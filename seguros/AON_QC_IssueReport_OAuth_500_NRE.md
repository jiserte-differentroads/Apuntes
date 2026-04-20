# Reporte incidencia — AON QC OAuth devuelve 500 (NRE)

## Resumen
El endpoint OAuth2 de **QC** devuelve **HTTP 500** con el mensaje:
`Object reference not set to an instance of an object.`

Esto impide obtener `access_token` y, por tanto, bloquea cualquier operación posterior (configuración/emisión/html/anulación).

## Fecha/hora
- 2026-04-09 (hora local)

## Endpoint afectado
- `POST https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token`

## Request (redactado)
```http
POST /pruservices/elinperservicesRest/oauth2/token
Content-Type: application/json
Accept: application/json

{"grant_type":"client_credentials","client_id":"6003_invc","client_secret":"***REDACTED***"}
```

## Response (completa)
```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/json; charset=utf-8
status-message: Object reference not set to an instance of an object.

{"MsgError":"Object reference not set to an instance of an object.","Code":500}
```

## Observaciones
- El error parece **interno del servidor** (NullReferenceException).
- No se recibe `access_token` en ningún caso, por lo que no es posible probar el servicio XML.

## Impacto
- Bloqueo total de pruebas E2E en QC para SecureToken Travel v21.

