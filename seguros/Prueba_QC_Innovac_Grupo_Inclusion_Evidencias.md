# Evidencias de pruebas — QC Innovac (Aon Grupos Inclusión)

## Contexto
- **Fecha**: 2026-04-09
- **Entorno proveedor**: QC (`qc.aon.es`)
- **Proveedor**: AON SecureToken Travel v21 (conexión XML)
- **API local**:
  - Base URL (HTTP): `http://localhost:5269`
  - Base URL (HTTPS): `https://localhost:7290`
- **Caso**: contratación modalidad **Aon Grupos Inclusión** (`modalidadCode = "1243"`)

## Redacción de secretos (obligatorio)
En este documento, cualquier aparición de:
- `client_secret` se sustituye por `***REDACTED***`
- `access_token` / `SECURETOKEN` se sustituye por `***REDACTED***`

## Paso 0 — Configuración (appsettings / env)
### Valores esperados (QC)
- `Innovac:OAuth:TokenUrl`: `https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token`
- `Innovac:TravelXml:ServiceUrl`: `https://qc.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx`
- `Innovac:TravelXml:XmlPayloadMode`: `FormUrlEncodedPXml`
- `Innovac:TravelXml:DefaultOriginCode`: `24`

## Paso 1 — Sanity check proveedor: OAuth token directo (QC)
### Request (cURL) — directo a AON QC
```bash
curl -s -i -X POST "https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"grant_type":"client_credentials","client_id":"6003_invc","client_secret":"***REDACTED***"}'
```

### Response — directo a AON QC
```http
HTTP/1.1 500 Internal Server Error
status-message: Object reference not set to an instance of an object.

{"MsgError":"Object reference not set to an instance of an object.","Code":500}
```

**Resultado**: KO (el proveedor falla en OAuth en QC con NRE).

## Paso 1 — Obtener configuración (modalidades/destinos/duraciones)
### Request (cURL) — nuestra API
```bash
curl -s -X GET "http://localhost:5269/InnovacConfiguration/seguros" -H "Accept: application/json"
```

### Response (JSON) — nuestra API
```json
{"modalities":[],"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}
```

### Request/Response saliente (RestSharp) — OAuth + PETICION_CONFIGURACION
```text
[Innovac] OAuth request: POST https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token (body omitido; contiene secretos).
[Innovac] OAuth response: Status OK. Body: {"access_token":"***REDACTED***","token":"Bearer","token_type":"Bearer","expire_time":1775732352}
[Innovac] XML request (Configuration): POST https://qc.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx. Mode: FormUrlEncodedPXml. Payload: <PETICION_CONFIGURACION><AUTENTICACION SECURETOKEN="***REDACTED***" /><ORIGEN codigo="24" /></PETICION_CONFIGURACION>
[Innovac] XML response (Configuration): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

## Paso 2 — Selección de producto
### Modalidad objetivo
- `modalidadCode = "1243"`

### Destino/Duración elegidos
- `destinationCode = "1"` (España)
- `durationCode = "5"` (5 días)
- `originCode = "24"`

## Paso 3 — Emitir certificado
### Request (cURL) — nuestra API
```bash
curl -s -X POST "http://localhost:5269/InnovacCertificate" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "modalidadCode": "1243",
    "destinationCode": "1",
    "durationCode": "5",
    "originCode": "24",
    "startDate": "10/04/2026",
    "endDate": "15/04/2026",
    "insuredPeople": [{ "name": "Nombre Apellidos", "documentNumber": "12345678Z" }],
    "city": "Madrid",
    "agencyReference": "PRUEBA-QC-001",
    "language": "ES"
  }'
```

### Response (JSON) — nuestra API
```json
{"referenceCode":null,"locator":null,"policyNumber":null,"modalidad":null,"destination":null,"price":null,"certificateUrl":null,"periodFrom":null,"periodTo":null,"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}
```

### Datos a reutilizar
- `referenceCode`: `___`
- `locator`: `___`
- `policyNumber`: `___`
- `price`: `___`
- `certificateUrl`: `___`

### Request/Response saliente (RestSharp) — PETICION_CERTIFICADO
```text
[Innovac] XML request (Certificate): POST https://qc.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx. Mode: FormUrlEncodedPXml. Payload: <PETICION_CERTIFICADO><AUTENTICACION SECURETOKEN="***REDACTED***" /><MODALIDAD codigo="1243"><DESTINO codigo="1"><DURACION codigo="5" /></DESTINO></MODALIDAD><ORIGEN codigo="24" /><DATOS><FECHA_INICIO_VIAJE>10/04/2026</FECHA_INICIO_VIAJE><FECHA_FINAL_VIAJE>15/04/2026</FECHA_FINAL_VIAJE><NASEGURADOS>1</NASEGURADOS><ASEGURADOS><ASEGURADO><NOMBRE>Nombre Apellidos</NOMBRE><DNI>12345678Z</DNI></ASEGURADO></ASEGURADOS><LOCALIDAD>Madrid</LOCALIDAD><REF_AGENCIA>PRUEBA-QC-001</REF_AGENCIA></DATOS><IDIOMA>ES</IDIOMA></PETICION_CERTIFICADO>
[Innovac] XML response (Certificate): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

## Paso 4 — Obtener certificado HTML
### Request (cURL) — nuestra API
```bash
curl -s -X POST "http://localhost:5269/InnovacCertificateHtml" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{ "referenceCode": "109000", "language": "ES" }'
```

### Response (JSON) — nuestra API
```json
{"html":null,"certificateUrl":null,"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}
```

### Request/Response saliente (RestSharp) — PETICION_CERTIFICADOHTML
```text
[Innovac] XML request (CertificateHtml): POST https://qc.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx. Mode: FormUrlEncodedPXml. Payload: <PETICION_CERTIFICADOHTML><AUTENTICACION SECURETOKEN="***REDACTED***" /><CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA><IDIOMA>ES</IDIOMA></PETICION_CERTIFICADOHTML>
[Innovac] XML response (CertificateHtml): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

## Paso 5 — Anular certificado
### Request (cURL) — nuestra API
```bash
curl -s -X POST "http://localhost:5269/InnovacCancellation" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "referenceCode": "109000",
    "withoutReplacement": true,
    "reasons": { "dateError": false, "durationError": false, "others": "Error administrativo" }
  }'
```

### Response (JSON) — nuestra API
```json
{"descriptionOk":null,"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}
```

### Request/Response saliente (RestSharp) — PETICION_ANULACION
```text
[Innovac] XML request (Cancellation): POST https://qc.aon.es/pruservices/elinper/viajes/servicios/XMLCallRequest.aspx. Mode: FormUrlEncodedPXml. Payload: <PETICION_ANULACION><AUTENTICACION SECURETOKEN="***REDACTED***" /><CODIGO_REFERENCIA>109000</CODIGO_REFERENCIA><SIN_SUSTITUCION>Si</SIN_SUSTITUCION><MOTIVOS><ERROR_FECHA>No</ERROR_FECHA><ERROR_DURACION>No</ERROR_DURACION><OTROS>Error administrativo</OTROS></MOTIVOS></PETICION_ANULACION>
[Innovac] XML response (Cancellation): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

## Pruebas negativas (mínimo)
### Negativa 1 — Campo obligatorio vacío (esperado error proveedor 04 → HTTP 400)
```text
cURL:
curl -s -X POST "http://localhost:5269/InnovacCertificate" -H "Content-Type: application/json" -H "Accept: application/json" --data-binary "@C:\Repositorios\Apuntes\seguros\neg_cert_missing_dates.json"

Respuesta (nuestra API):
HTTP 502
{"referenceCode":null,"locator":null,"policyNumber":null,"modalidad":null,"destination":null,"price":null,"certificateUrl":null,"periodFrom":null,"periodTo":null,"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}

Logs (RestSharp):
[Innovac] XML request (Certificate): ... Payload: <PETICION_CERTIFICADO>...<FECHA_INICIO_VIAJE></FECHA_INICIO_VIAJE><FECHA_FINAL_VIAJE></FECHA_FINAL_VIAJE>...</PETICION_CERTIFICADO>
[Innovac] XML response (Certificate): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

### Negativa 2 — Error de parámetros (esperado error proveedor 03 → HTTP 400)
```text
cURL:
curl -s -X POST "http://localhost:5269/InnovacCertificateHtml" -H "Content-Type: application/json" -H "Accept: application/json" --data-binary "@C:\Repositorios\Apuntes\seguros\neg_html_empty_reference.json"

Respuesta (nuestra API):
HTTP 502
{"html":null,"certificateUrl":null,"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}

Logs (RestSharp):
[Innovac] XML request (CertificateHtml): ... Payload: <PETICION_CERTIFICADOHTML>...<CODIGO_REFERENCIA></CODIGO_REFERENCIA>...</PETICION_CERTIFICADOHTML>
[Innovac] XML response (CertificateHtml): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

### Negativa 3 — XML mal formado (esperado error proveedor 10 → HTTP 400) (si aplica)
```text
cURL:
curl -s -X POST "http://localhost:5269/InnovacCancellation" -H "Content-Type: application/json" -H "Accept: application/json" --data-binary "@C:\Repositorios\Apuntes\seguros\neg_cancel_empty_reference.json"

Respuesta (nuestra API):
HTTP 502
{"descriptionOk":null,"isSuccess":false,"rawXml":"<?xml version=\"1.0\" encoding=\"UTF-8\"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>","error":{"code":"0","description":"Object reference not set to an instance of an object."}}

Logs (RestSharp):
[Innovac] XML request (Cancellation): ... Payload: <PETICION_ANULACION>...<CODIGO_REFERENCIA></CODIGO_REFERENCIA>...</PETICION_ANULACION>
[Innovac] XML response (Cancellation): Status OK. Body: <?xml version="1.0" encoding="UTF-8"?><RESPUESTA_ERROR><CODIGO_ERROR>0</CODIGO_ERROR><DESC_ERROR>Object reference not set to an instance of an object.</DESC_ERROR></RESPUESTA_ERROR>
```

