# Ejemplo de peticiones OAuth2 (token) — para compartir con AON/Innovac

Este documento describe **cómo estamos preparando** la obtención del `access_token` (`client_credentials`) según la documentación SecureToken Travel v21.

- **No incluimos** el campo `scope` en el cuerpo JSON (solo `grant_type`, `client_id`, `client_secret`).
- Sustituye `CLIENT_ID` y `CLIENT_SECRET` por las credenciales que os hayan facilitado.
- **No commitear** secretos reales: usar variables de entorno o un `body.json` local ignorado por git.

---

## Entorno QC — verificado (abril 2026)

Para **calidad / preproducción**, el token OAuth2 responde correctamente contra el host **`qc.aon.es`** (no contra `www.aon.es` en las pruebas realizadas).

**Endpoint**

```text
POST https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token
```

**Postman (equivalente)**

- Método: `POST`
- URL: `https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token`
- Headers: `Content-Type: application/json` (opcional: `Accept: application/json`)
- Body (raw JSON):

```json
{
  "grant_type": "client_credentials",
  "client_id": "CLIENT_ID",
  "client_secret": "CLIENT_SECRET"
}
```

> Las cookies tipo `__cf_bm` (Cloudflare) que Postman pueda mostrar suelen ser **incidentales** del navegador; la obtención del token debe basarse en el JSON anterior. Si hay bloqueos, revisar firewall y políticas de Cloudflare.

**curl**

```bash
curl --location --request POST 'https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token' \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --data-raw "{
    \"grant_type\": \"client_credentials\",
    \"client_id\": \"${CLIENT_ID}\",
    \"client_secret\": \"${CLIENT_SECRET}\"
}"
```

---

## Variables (bash / Git Bash)

```bash
export CLIENT_ID="tu_usuario_portal"
export CLIENT_SECRET="tu_password"
```

---

## Entorno de pruebas (documentación histórica — `www.aon.es`)

Algunos PDFs listan el host **`www.aon.es`** para pruebas. En nuestro caso, el flujo útil de token en QC fue con **`qc.aon.es`** (sección anterior). La URL siguiente se mantiene como referencia por si AON unifica hosts.

**Endpoint**

```text
POST https://www.aon.es/pruservices/elinperservicesRest/oauth2/token
```

**curl**

```bash
curl --location --request POST 'https://www.aon.es/pruservices/elinperservicesRest/oauth2/token' \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --data-raw "{
    \"grant_type\": \"client_credentials\",
    \"client_id\": \"${CLIENT_ID}\",
    \"client_secret\": \"${CLIENT_SECRET}\"
}"
```

---

## Producción

**Endpoint**

```text
POST https://www.aon.es/services/elinperservicesRest/oauth2/token
```

**curl**

```bash
curl --location --request POST 'https://www.aon.es/services/elinperservicesRest/oauth2/token' \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --data-raw "{
    \"grant_type\": \"client_credentials\",
    \"client_id\": \"${CLIENT_ID}\",
    \"client_secret\": \"${CLIENT_SECRET}\"
}"
```

---

## Cuerpo JSON (equivalente)

Sin `scope`:

```json
{
  "grant_type": "client_credentials",
  "client_id": "CLIENT_ID",
  "client_secret": "CLIENT_SECRET"
}
```

---

## Respuesta esperada (según documentación)

```json
{
  "access_token": "…",
  "token": "Bearer",
  "expire_time": 3600
}
```

El valor de `access_token` es el que debe usarse como **`SECURETOKEN`** en las peticiones XML posteriores.

---

## Windows (PowerShell) — alternativa con fichero

Para evitar problemas de escape, se puede guardar el cuerpo en `body.json` (sin commitear credenciales) y ejecutar:

**QC (recomendado si vuestro convenio apunta a `qc.aon.es`)**

```powershell
curl.exe -sS -i -X POST "https://qc.aon.es/pruservices/elinperservicesRest/oauth2/token" `
  -H "Content-Type: application/json" `
  -H "Accept: application/json" `
  --data-binary "@body.json"
```

**Pruebas (`www.aon.es` — referencia)**

```powershell
curl.exe -sS -i -X POST "https://www.aon.es/pruservices/elinperservicesRest/oauth2/token" `
  -H "Content-Type: application/json" `
  -H "Accept: application/json" `
  --data-binary "@body.json"
```

**Producción**

```powershell
curl.exe -sS -i -X POST "https://www.aon.es/services/elinperservicesRest/oauth2/token" `
  -H "Content-Type: application/json" `
  -H "Accept: application/json" `
  --data-binary "@body.json"
```

Contenido de ejemplo de `body.json`:

```json
{
  "grant_type": "client_credentials",
  "client_id": "CLIENT_ID",
  "client_secret": "CLIENT_SECRET"
}
```

---

## Notas para soporte AON (opcional)

- Con **`qc.aon.es`** y credenciales QC activas, el token OAuth2 puede obtenerse con el cuerpo JSON sin `scope` (verificado internamente).
- Si en otros hosts aparece **HTTP 520** (origen/proxy) o **401**, confirmar `client_id` exacto, activación de la cuenta, **lista blanca de IP** si aplica, y qué host de pruebas debe usarse (`qc` vs `www`).
