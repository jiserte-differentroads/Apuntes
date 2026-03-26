# Peticiones (curl) - Innovac/AON SecureToken Travel v21

Flujo recomendado para gestionar un seguro (Innovac/AON) con **SecureToken Travel v21 – Conexión XML**.

> Nota importante: el PDF describe las estructuras XML y las URLs. Dependiendo de cómo acepte el servicio AON el payload XML, puede requerir:
> 1) XML como body `text/xml`, o
> 2) XML como parámetro de formulario (p.ej. `xml`/`XML`).
>
> Aquí dejo un ejemplo “body raw” (el más habitual) y una variante “form” por si vuestro entorno lo pide.

## 0) Variables / prerequisitos

- `CLIENT_ID`: `nombreUsuario_Portal`
- `CLIENT_SECRET`: `Password`
- `MODE`: `prod` o `prueba`
- `MODALIDAD codigo`: se obtiene desde la **respuesta de configuración**
- `ORIGEN codigo`: ejemplo del PDF `24` (ajustar al vuestro)

Antes de emitir:
- Determinar por viajero si requiere `BASIC` o `COMFORT`:
  - Todos requieren como mínimo `BASIC` salvo que hayan elegido `Comfort Plan` o `Comfort Plan Plus`.

## 1) Obtener OAuth2 token (SecureToken)

### Endpoint

**Pruebas**

```text
POST https://www.aon.es/pruservices/elinperservicesRest/oauth2/token
```

**Producción**

```text
POST https://www.aon.es/services/elinperservicesRest/oauth2/token
```

### Content-Type

```http
application/json
```

### Petición

```bash
curl --location --request POST 'https://www.aon.es/services/elinperservicesRest/oauth2/token' \
  --header 'Content-Type: application/json' \
  --data-raw '{
    "grant_type": "client_credentials",
    "client_id": "'"${CLIENT_ID}"'",
    "client_secret": "'"${CLIENT_SECRET}"'",
    "scope": "string"
  }'
```

### Respuesta (campos típicos)

```json
{
  "access_token": "string",
  "token": "Bearer",
  "expire_time": 3600
}
```

> Usa `access_token` como `SECURETOKEN` dentro de los XML:
>
> `SECURETOKEN="${ACCESS_TOKEN}"`

## 2) Llamar al servicio XML (configuración / emisión / etc.)

### URL del servicio XML

**Producción**

```text
https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx
```

**Entorno de pruebas (con trazas)**

```text
https://www.aon.es/pruservices/elinper/viajes/servicios/pruebaXmlCallRequest.aspx
```

### Variante A: XML como body (raw)

Ejemplo genérico (ajusta el XML en `--data-raw`):

```bash
curl --location --request POST 'https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx' \
  --header 'Content-Type: text/xml; charset=utf-8' \
  --header 'Accept: application/xml,text/xml,*/*' \
  --data-raw '<PETICION_...>...</PETICION_...>'
```

### Variante B: XML como parámetro de formulario

```bash
curl --location --request POST 'https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode "xml=<PETICION_...>...</PETICION_...>"
```

> Si al probar con Variante A no funciona, intentad Variante B (y viceversa).

## 3) Petición Inicial: Productos XML (configuración)

### Objetivo

Obtener modalidades disponibles (productos), destinos, duraciones, precios y coberturas para poder emitir después el certificado.

### XML base (del PDF)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_CONFIGURACION>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
</PETICION_CONFIGURACION>
```

### Ejemplo con origen (del PDF)

```xml
<PETICION_CONFIGURACION>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
  <ORIGEN codigo="24"></ORIGEN>
</PETICION_CONFIGURACION>
```

### curl (body raw)

```bash
XML_CONFIG="$(cat <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_CONFIGURACION>
  <AUTENTICACION SECURETOKEN="${ACCESS_TOKEN}"></AUTENTICACION>
  <ORIGEN codigo="24"></ORIGEN>
</PETICION_CONFIGURACION>
EOF
)"

curl --location --request POST 'https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx' \
  --header 'Content-Type: text/xml; charset=utf-8' \
  --data-raw "$XML_CONFIG"
```

> De la respuesta, idempotentemente, se extrae el `MODALIDAD codigo` (y los códigos de destino/duración) para el siguiente paso.

## 4) Petición Certificado XML (emitir seguro)

### Petición base (del PDF)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_CERTIFICADO>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>

  <MODALIDAD codigo="m">
    <DESTINO codigo="x">
      <DURACION codigo="n"></DURACION>
    </DESTINO>
  </MODALIDAD>

  <ORIGEN codigo="x"></ORIGEN>

  <DATOS>
    <FECHA_INICIO_VIAJE>DD/MM/YYYY</FECHA_INICIO_VIAJE>
    <FECHA_FINAL_VIAJE>DD/MM/YYYY</FECHA_FINAL_VIAJE>
    <NASEGURADOS>N</NASEGURADOS>
    <ASEGURADOS>
      <ASEGURADO>
        <NOMBRE>Nombre Apellidos</NOMBRE>
        <DNI></DNI>
      </ASEGURADO>
    </ASEGURADOS>
    <LOCALIDAD>Ciudad</LOCALIDAD>
    <REF_AGENCIA>Referencia</REF_AGENCIA>
  </DATOS>

  <IDIOMA>ES</IDIOMA>
</PETICION_CERTIFICADO>
```

### curl (body raw)

```bash
XML_CERT="$(cat <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_CERTIFICADO>
  <AUTENTICACION SECURETOKEN="${ACCESS_TOKEN}"></AUTENTICACION>

  <MODALIDAD codigo="${MODALIDAD_COD}">
    <DESTINO codigo="${DESTINO_COD}">
      <DURACION codigo="${DURACION_COD}"></DURACION>
    </DESTINO>
  </MODALIDAD>

  <ORIGEN codigo="${ORIGEN_COD}"></ORIGEN>

  <DATOS>
    <FECHA_INICIO_VIAJE>${FECHA_INICIO}</FECHA_INICIO_VIAJE>
    <FECHA_FINAL_VIAJE>${FECHA_FIN}</FECHA_FINAL_VIAJE>
    <NASEGURADOS>${NASEGURADOS}</NASEGURADOS>
    <ASEGURADOS>
      <ASEGURADO>
        <NOMBRE>${NOMBRE_TITULAR}</NOMBRE>
        <DNI>${DNI_TITULAR}</DNI>
      </ASEGURADO>
    </ASEGURADOS>
    <LOCALIDAD>${LOCALIDAD}</LOCALIDAD>
    <REF_AGENCIA>${REF_AGENCIA}</REF_AGENCIA>
  </DATOS>

  <IDIOMA>ES</IDIOMA>
</PETICION_CERTIFICADO>
EOF
)"

curl --location --request POST 'https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx' \
  --header 'Content-Type: text/xml; charset=utf-8' \
  --data-raw "$XML_CERT"
```

### Respuesta Certificado XML (campos típicos)

El PDF indica que devuelve, entre otros:

- `CODIGO_REFERENCIA`
- `LOCALIZADOR`
- `NUMERO_POLIZA`
- `MODALIDAD`
- `DESTINO`
- `PRECIO`
- `CERTIFICADO_URL` (URL de consulta/descarga)

## 5) Petición Certificado HTML (XML Certificado HTML)

### Petición (del PDF)

```xml
<PETICION_CERTIFICADOHTML>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
  <CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA>
  <IDIOMA>ES</IDIOMA>
</PETICION_CERTIFICADOHTML>
```

### curl (body raw)

```bash
XML_CERT_HTML="$(cat <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_CERTIFICADOHTML>
  <AUTENTICACION SECURETOKEN="${ACCESS_TOKEN}"></AUTENTICACION>
  <CODIGO_REFERENCIA>${CODIGO_REFERENCIA}</CODIGO_REFERENCIA>
  <IDIOMA>ES</IDIOMA>
</PETICION_CERTIFICADOHTML>
EOF
)"

curl --location --request POST 'https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx' \
  --header 'Content-Type: text/xml; charset=utf-8' \
  --data-raw "$XML_CERT_HTML"
```

> De la respuesta se obtiene:
> - `CONTENIDO` con `HTML`
> - `CERTIFICADO_URL` (según el PDF)

## 6) Petición XML Anulación Certificado (cancelar seguro)

### Petición (del PDF)

```xml
<PETICION_ANULACION>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
  <CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA>
  <SIN_SUSTITUCION>Si</SIN_SUSTITUCION>
  <MOTIVOS>
    <ERROR_FECHA>No</ERROR_FECHA>
    <ERROR_DURACION>No</ERROR_DURACION>
    <OTROS>Error administrativo</OTROS>
  </MOTIVOS>
</PETICION_ANULACION>
```

### curl (body raw)

```bash
XML_CANCEL="$(cat <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_ANULACION>
  <AUTENTICACION SECURETOKEN="${ACCESS_TOKEN}"></AUTENTICACION>
  <CODIGO_REFERENCIA>${CODIGO_REFERENCIA}</CODIGO_REFERENCIA>
  <SIN_SUSTITUCION>Si</SIN_SUSTITUCION>
  <MOTIVOS>
    <ERROR_FECHA>No</ERROR_FECHA>
    <ERROR_DURACION>No</ERROR_DURACION>
    <OTROS>${MOTIVO}</OTROS>
  </MOTIVOS>
</PETICION_ANULACION>
EOF
)"

curl --location --request POST 'https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx' \
  --header 'Content-Type: text/xml; charset=utf-8' \
  --data-raw "$XML_CANCEL"
```

## 7) Tabla de errores (resumen del PDF)

| Código | Descripción |
| ------ | ----------- |
| 01 | Usuario o contraseña incorrectos |
| 02 | Agencia desactivada |
| 03 | Error de parámetros |
| 04 | Campo obligatorio vacío |
| 05 | Sistema no disponible |
| 10 | XML mal formado |
| 16 | Certificado ya anulado |
| 17 | Precios no encontrados |

Ejemplo de error:

```xml
<RESPUESTA_ERROR>
  <CODIGO_ERROR>10</CODIGO_ERROR>
  <DESC_ERROR>XML mal formado</DESC_ERROR>
</RESPUESTA_ERROR>
```

---

## Siguiente paso (si quieres afinarlo)

Si me confirmas qué formato exacto espera vuestro backend AON (body raw vs form-param `xml`), te dejo estas `curl` ya “cerradas” sin variantes y con headers finales.

