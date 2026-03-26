# Documentación Servicio Web Seguros de Viaje (SecureToken Travel v21)

**Versión 21.0 – Conexión XML**

**Documento original:** Documentación Servicio Web Seguros de Viaje v21 – SecureToken Travel  
**Proveedor:** AON – Mercado Turismo  
**Fecha:** Enero 2026  
**Versión:** 21.0 – Conexión XML

## Índice

1. Introducción
2. Seguridad – Obtención de Token de Identificación
3. Petición Inicial Productos XML
4. Mensajes de Respuesta
   1. Respuesta de información
5. Petición Certificado XML
6. Respuesta Certificado XML
   1. Ejemplo de seguro normal
7. Petición XML Certificado HTML
   1. Ejemplo de petición
8. Respuesta XML Certificado HTML
9. Petición XML Anulación Certificado
   1. Ejemplo de petición
10. Respuesta XML de Anulación
11. Tabla de Errores
   1. Ejemplo de error

## 1. Introducción

La aplicación de seguros de **AON** permite realizar la emisión de seguros de asistencia en viaje. AON proporciona a las agencias un **usuario y contraseña** para utilizar el servicio web y emitir certificados, una vez dadas de alta y con modalidades contratadas.

La nueva interfaz **XML** permite integrar los seguros en aplicaciones propias mediante conexión **SSL** segura.

### URLs del servicio

**Producción**

```text
https://www.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx
```

**Entorno de pruebas (con trazas)**

```text
https://www.aon.es/pruservices/elinper/viajes/servicios/pruebaXmlCallRequest.aspx
```

## 2. Seguridad – Obtención de Token de Identificación

Todas las invocaciones se realizan bajo protocolo seguro (**HTTPS**), incluyendo:

- Activación por IP
- Control de credenciales
- Token cifrado
- Opcionalmente certificado digital

Se utiliza **OAuth2 Bearer Token** para identificar el punto de venta. Las credenciales **no** se envían en el cuerpo del XML, sino en la cabecera mediante el token.

### Credenciales

- `client_id`: `nombreUsuario_Portal`
- `client_secret`: `Password`

### Endpoint OAuth2

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

```json
{
  "grant_type": "client_credentials",
  "client_id": "string",
  "client_secret": "string",
  "scope": "string"
}
```

### Respuesta

```json
{
  "access_token": "string",
  "token": "Bearer",
  "expire_time": 3600
}
```

## 3. Petición Inicial Productos XML

Permite obtener la información necesaria para contratar seguros.

### XML base

```xml
<?xml version="1.0" encoding="UTF-8"?>
<PETICION_CONFIGURACION>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
</PETICION_CONFIGURACION>
```

### Ejemplo con origen

```xml
<PETICION_CONFIGURACION>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
  <ORIGEN codigo="24"></ORIGEN>
</PETICION_CONFIGURACION>
```

> El campo `SECURETOKEN` debe contener el token OAuth previamente generado.

## 4. Mensajes de Respuesta

La respuesta devuelve las **modalidades disponibles**, destinos, duraciones, precios y coberturas.

### Estructura general

```xml
<RESPUESTA_CONFIGURACION>
  <MODALIDADES>
    <MODALIDAD 
      nombre="AON Premium"
      codigo="1182"
      AONOK="No"
      Comisionable="No"
      valorGlobal="No"
      isAccident="No"
      fechafin="No"
      maxPax=""
    >
```

Incluye:

- Imágenes
- URLs de condiciones generales
- IPID
- Destinos
- Duraciones y precios
- Coberturas detalladas

## 4.1. Respuesta de información – Campos principales

### Modalidad

| Atributo     | Descripción |
| ------------ | ----------- |
| `nombre`     | Nombre del producto |
| `codigo`     | Código del producto |
| `Comisionable` | Neto o comisionable |
| `maxPax`     | Nº máximo de asegurados |

### Destino

| Atributo | Descripción |
| -------- | ----------- |
| `nombre` | Nombre del destino |
| `codigo` | Código del destino |

### Duración

| Atributo | Descripción |
| -------- | ----------- |
| `nombre` | Texto descriptivo |
| `codigo` | Código de tarifa |
| `dies` | Nº de días |
| `precio` | Precio |
| `precioC` | Precio comisionable |

## 5. Petición Certificado XML

Se utiliza para emitir un certificado de seguro.

### Petición base

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

### Idiomas soportados

- `ES` – Castellano (por defecto)
- `EN` – Inglés
- `CA` – Catalán

## 6. Respuesta Certificado XML

Devuelve la información final del seguro emitido.

### Ejemplo

```xml
<RESPUESTA_CERTIFICADO>
  <PERIODO>
    <DESDE>02/11/04</DESDE>
    <HASTA>02/11/04</HASTA>
  </PERIODO>

  <CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA>
  <LOCALIZADOR>109000</LOCALIZADOR>
  <NUMERO_POLIZA>0769345</NUMERO_POLIZA>
  <MODALIDAD>AON Premium</MODALIDAD>
  <DESTINO>España</DESTINO>
  <PRECIO>12.53</PRECIO>

  <CERTIFICADO_URL>
    https://www.aon.es/.../obtenerCertificado.aspx
  </CERTIFICADO_URL>
</RESPUESTA_CERTIFICADO>
```

## 7. Petición XML Certificado HTML

Permite obtener el certificado en formato HTML.

```xml
<PETICION_CERTIFICADOHTML>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
  <CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA>
  <IDIOMA>ES</IDIOMA>
</PETICION_CERTIFICADOHTML>
```

### Ejemplo de petición

```xml
<PETICION_CERTIFICADOHTML>
  <AUTENTICACION SECURETOKEN="xxxxx"></AUTENTICACION>
  <CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA>
  <IDIOMA>ES</IDIOMA>
</PETICION_CERTIFICADOHTML>
```

## 8. Respuesta XML Certificado HTML

```xml
<RESPUESTA_CERTIFICADOHTML>
  <CONTENIDO>
    <HTML>...</HTML>
  </CONTENIDO>
  <CERTIFICADO_URL>
    https://www.aon.com/.../certificado.aspx
  </CERTIFICADO_URL>
</RESPUESTA_CERTIFICADOHTML>
```

## 9. Petición XML Anulación Certificado

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

### Ejemplo de petición

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

## 10. Respuesta XML de Anulación

```xml
<RESPUESTA_OK>
  <DESC_OK>Anulación realizada</DESC_OK>
</RESPUESTA_OK>
```

## 11. Tabla de Errores

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

### Formato de error

```xml
<RESPUESTA_ERROR>
  <CODIGO_ERROR>10</CODIGO_ERROR>
  <DESC_ERROR>XML mal formado</DESC_ERROR>
</RESPUESTA_ERROR>
```

