# Ajustes en base de datos para gestión de seguros (Innovac/AON)

## Contexto actual (DB)

Hoy la selección del seguro ya queda persistida en:

- `Reservation`
- `ReservationTraveler`
- `ReservationTravelerActivity`
  - Relaciona `ReservationTravelerId` con `ActivityId` seleccionado por el viajero.
  - `ActivityId` representa el seguro configurado en TK (Básico / Comfort / Comfort Plus / Room interno) mediante el modelo de actividades.

Lo que **falta** para la gestión de seguros completa:

- Guardar la información de emisión/contratación devuelta por Innovac/AON (`CODIGO_REFERENCIA`, `LOCALIZADOR`, `NUMERO_POLIZA`, `MODALIDAD`, etc.).
- Guardar el estado del ciclo de vida (pendiente, contratado, emitido, anulado, error).
- Guardar y relacionar la documentación emitida (PDF/HTML/url/condiciones si aplica) con el **seguro emitido** (no solo con la reserva).
- Guardar logs de integración con el proveedor para auditoría y troubleshooting.

## Objetivo del ajuste

1. Mantener la selección del usuario por viajero usando `ReservationTravelerActivity`.
2. Emitir según la regla validada por negocio:
  - Se emite el **Seguro Básico** para todos los viajeros, salvo que hayan elegido **Comfort Plan** o **Comfort Plan Plus**.
  - En condiciones de re-cálculo coherente en un único paso (sin cambios posteriores), como máximo se emiten 2 certificados por reserva (uno `BASIC` y/o uno `COMFORT`).
  - En cambios posteriores tras emisión parcial, si se aplica estrategia incremental sin anular/re-emitir, el número total de certificados por reserva puede llegar a ser **1..n** (ver Escenario 17 en `Casuisticas_emision_seguros_Innovac.md`).
3. Guardar en DB el/los certificados emitidos y asociarlos a las actividades seleccionadas que participaron en la emisión.

## Regla imperativa a reflejar en emisión
- Todos los viajeros deben tener como mínimo el **Seguro Básico**, salvo que elijan **Comfort Plan** o **Comfort Plan Plus**.
- En consecuencia, al agrupar emisión por tipo:
  - el certificado `BASIC` debe incluir únicamente a los viajeros que **no** eligieron Comfort/Plus
  - el certificado `COMFORT` incluye a los viajeros que eligieron Comfort/Plus.

## Modelo propuesto (tablas nuevas)

### 1) `ReservationInsuranceCertificate`

Representa un “certificado emitido” por la integración con Innovac/AON.

Relación:

- FK `ReservationId` -> `Reservation`
- (Derivará su tipo de seguro a partir de la emisión: `BASIC` o `COMFORT`)

Campos recomendados:

- `Id` (PK, BaseEntityId)
- `ReservationId` (FK)
- `InsuranceType` (int o enum): `BASIC` | `COMFORT`
- `Status` (int o enum): `PENDING` | `CONTRACTING` | `CONTRACTED` | `ISSUED` | `CANCELLED` | `FAILED`
- `FailedReason` (nullable string)
- `CreatedAt`, `UpdatedAt` (audit si ya usáis)

Campos de Innovac/AON (según `Documentacion_Servicio_Web_Seguros_de_Viaje_SecureToken_Travel_v21.md`):

- `CodigoReferencia` (`CODIGO_REFERENCIA`)
- `Localizador` (`LOCALIZADOR`)
- `NumeroPoliza` (`NUMERO_POLIZA`)
- `Modalidad` (`MODALIDAD`)
- `Destino` (`DESTINO`)
- `Precio` (`PRECIO`)
- `CertificadoUrl` (si devuelve `CERTIFICADO_URL`)

Metadatos de emisión (para auditoría):

- `Idioma` (normalmente `ES`)
- `OrigenCodigo` (`ORIGEN codigo`)
- `ModalidadCodigo` (`MODALIDAD codigo`)
- `FechaInicioViaje` (DD/MM/YYYY enviada, ideal como Date)
- `FechaFinViaje` (Date)
- `NumeroAsegurados` (`NASEGURADOS`)

Índices/uniques recomendados:

- `Unique(ReservationId, InsuranceType, CertificateVersion)`  
  - Permite múltiples certificados por reserva/tipo cuando hay re-emisiones/incidencias (versionando).
- `Index(Status, ReservationId)`

### 2) `ReservationInsuranceCertificateTravelerActivity` (tabla join)

Asocia qué actividades de seguro (por viajero) participaron en un certificado.

Relación:

- FK `ReservationInsuranceCertificateId` -> `ReservationInsuranceCertificate`
- FK `ReservationTravelerActivityId` -> `ReservationTravelerActivity`

Campos:

- `Id` (PK)
- `ReservationInsuranceCertificateId`
- `ReservationTravelerActivityId`

Reglas/constraints:

- `Unique(ReservationInsuranceCertificateId, ReservationTravelerActivityId)`
- `Index(ReservationTravelerActivityId)`

Con esto, aunque el certificado “sea por reserva”, mantenéis trazabilidad 1:1 contra los viajeros/actividades que lo dispararon.

### 3) `ReservationInsuranceCertificateDocument` (relación de documentación)

Permite relacionar documentos emitidos (PDF/HTML/url/otros) con el certificado, reutilizando el modelo existente `Document`.

Relación:

- FK `ReservationInsuranceCertificateId`
- FK `DocumentId` -> `Document`

Campos:

- `Id` (PK)
- `ReservationInsuranceCertificateId`
- `DocumentId`
- `DocumentKind` (nullable string/enum): `CERTIFICATE_PDF` | `CERTIFICATE_HTML` | `GENERAL_CONDITIONS` | `OTHER`

Constraints:

- `Unique(ReservationInsuranceCertificateId, DocumentId, DocumentKind)` si quieres evitar duplicados

Nota:

- No eliminamos `DocumentReservation` si ya lo usáis, pero para “documentación del seguro” suele ser más correcto colgarlo del certificado.

### 4) `ReservationInsuranceProviderLog` (logs proveedor AON)

Auditoría de cada intento de emisión/contratación/anulación contra Innovac.

Relación:

- FK `ReservationInsuranceCertificateId`
- FK opcional a `ReservationId` derivable, pero normalmente con la FK del certificado es suficiente.

Campos:

- `Id` (PK)
- `ReservationInsuranceCertificateId` (FK)
- `Operation` (enum/string): `CONFIGURATION` | `ISSUE` | `CERT_HTML` | `CANCEL`
- `SentAt`, `ResponseAt`
- `HttpStatusCode`
- `RequestPayload` (nullable string)
  - Recomendación: redaccionar `SECURETOKEN` antes de guardar.
- `ResponsePayload` (nullable string)
- `ErrorCode`, `ErrorMessage`
- `CreatedAt`

Índices:

- `Index(Operation, CreatedAt)`
- `Index(HttpStatusCode, CreatedAt)`

## Estado del certificado (máquina de estados)

Propuesta alineada con el flujo:

- `PENDING`
  - No se ha contratado/emitido todavía.
  - Activable cuando la reserva está en `confirm/paid` (y Basic solo cuando `paid`).
- `CONTRACTED` / `ISSUED`
  - Se marcan tras recibir `Respuesta Certificado XML` (contrato emitido) y/o tras obtener HTML y asociar doc.
- `CANCELLED`
  - Tras ejecutar `PETICION_ANULACION` y obtener respuesta OK.
- `FAILED`
  - Si AON responde error (ver tabla de errores) o timeout/invalid payload.

## Relación con “actividades de seguro”

Cómo se conecta el modelo propuesto con la selección existente:

1. Middle identifica las asignaciones del usuario como `ReservationTravelerActivity`.
2. A partir de esas actividades, agrupa por tipo:
  - Actividades de “Básico” -> viajeros a incluir en `InsuranceType = BASIC`
  - Actividades de “Comfort/Plus” -> viajeros a incluir en `InsuranceType = COMFORT`
3. Crea/actualiza un `ReservationInsuranceCertificate` por tipo (unique por `ReservationId, InsuranceType`).
4. Crea la tabla join `ReservationInsuranceCertificateTravelerActivity` para trazabilidad.

## Consideraciones para “Room” interno

- El “Room” interno existe como `Activity` en TK, pero tu regla indica:
  - no se emite con Innovac.
- Por tanto:
  - No debe crear `ReservationInsuranceCertificate` para `Room`.
  - Tampoco participa en la join certificate->travelerActivity para certificados Innovac.

## Qué datos necesitas para poder poblar el certificado

Desde la Middle (input):

- Travelers seleccionados (vía join con `ReservationTravelerActivity`)
- Titular (obligatorio DNI/pasaporte) para construir el XML de `PETICION_CERTIFICADO`
- Fechas del viaje (de reserva o contexto)
- Códigos de `MODALIDAD` y `DESTINO/DURACION` (saldrán de “configuración inicial” XML)

## Migración / compatibilidad

- No se elimina nada existente.
- Se añade:
  - tablas nuevas de certificado/join/document/log
- La selección actual seguirá funcionando:
  - `ReservationTravelerActivity` sigue siendo la fuente para “qué pidió el usuario”.
- La nueva funcionalidad de Innovac añade:
  - “qué se emitió” y “qué documento está asociado”.

## Próximas decisiones (para cerrar diseño)

1. Confirmar que efectivamente AON emite “1 certificado por reserva y tipo” tal y como reflejan los escenarios validados.
2. Confirmar el/los formatos de documentación que queréis guardar:
  - solo `CERTIFICADO_URL` o también descargar y almacenar el PDF/HTML como `Document`.
3. Confirmar si necesitáis soporte de re-emisión (por ejemplo si cambia el pago/fechas) y cómo versionar:
  - o bien sobreescribes el certificado `Status`
  - o creas una nueva “versión” (requiere cambiar `Unique(ReservationId, InsuranceType)`).

