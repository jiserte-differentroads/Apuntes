# Cancelación automática de reservas KO sin pago (post-`PREBOOKED`)

## Objetivo

Cancelar automáticamente las **reservas formalizadas** que se han quedado en **KO** y **no están confirmadas**, cuando **no** exista:

- **ningún pago aceptado** (pago con estado `COMPLETED`), y
- **ningún pago pendiente** (estado `PENDING`) que tenga **justificante adjunto** (`AttachmentUrl` informado).

> Nota: En este documento se considera que una reserva está **formalizada** cuando pasa al estado `PREBOOKED`.

## Fuentes y referencias

- **Estados de reserva** (Swagger): `https://reservations.differentroads.es/api/ReservationStatus?useExactMatchForStrings=false`
- **Formalización** en la API: en `ReservationService.UpdateAsync`, cuando `toStatus == "PREBOOKED"` se setea `ReservedAt = UtcNow`.
- **Pagos**: `ReservationPaymentService` y entidad `ReservationPaymentResponse` (incluye `PaymentStatusId` y `AttachmentUrl`).

## Definiciones

- **Reserva formalizada**: `ReservationStatus.Code == "PREBOOKED"` o cualquier estado “posterior” en el flujo de reserva (ver apartado de estados).
- **Reserva confirmada**: `ReservationStatus.Code` en `CONFIRMED` o `PAID`.
- **Pago aceptado**: pago cuyo `PaymentStatus.Code == "COMPLETED"`.
- **Pago pendiente con justificante**: pago cuyo `PaymentStatus.Code == "PENDING"` y `ReservationPayment.AttachmentUrl` no es nulo/vacío.
- **KO**: resultado KO del proceso de reserva/pago en checkout (p.ej. fallo en TPV/Scalapay o no se completa el flujo) que deja una reserva formalizada pero sin pagos aceptados.

## Estados de reserva (clasificación operativa)

Según el Swagger, los estados actuales son:

- **Pre-formalización (no se considera “reserva” todavía)**:
  - `PRECHECKOUT`, `DRAFT`, `CART`, `BUDGET`, `CART_ABANDONED`
- **Formalizada (a partir de aquí aplica la regla de cancelación)**:
  - `PREBOOKED` (estado visible de “pendiente de confirmación / error técnico”)
  - `ERROR` (técnico)
  - `RQ`, `RQ_ISSUED`, `RQ_REOPEN` (pendientes/gestión en TK)
  - `BOOKED` (registrada sin pagos)
  - `CONFIRMED` (confirmada con pagos parciales) → **excluida** de cancelación automática por esta regla
  - `PAID` (pagada completamente) → **excluida**
  - `CANCELLED`, `DELETED`, `EXPIRED`, `SUSPENDED`
  - `PREBOOKEDTK` (reservas de guías) → **excluida** de cancelación automática por esta regla

## Reglas de cancelación

### 1) Alcance (qué reservas se evalúan)

Se evalúan reservas **formalizadas** (estado `PREBOOKED` o posterior) que:

- **no** estén en `CONFIRMED` ni `PAID`
- **no** estén ya en `CANCELLED` o `DELETED`
- **no** estén en `PREBOOKEDTK` (reservas de guías)

### 2) Condición de “mantener viva” la reserva

Una reserva **NO** se cancela si cumple **al menos una**:

- tiene **algún pago aceptado** (`COMPLETED`) asociado a la reserva, o
- tiene **algún pago pendiente** (`PENDING`) con **`AttachmentUrl` informado** (caso típico: transferencias con justificante subido)

### 3) Condición de cancelación automática

Se cancela la reserva si:

- cumple el alcance del punto 1, y
- **no** cumple ninguna condición del punto 2, y
- ha pasado el cierre de día desde su formalización.

#### Momento de cancelación

- **Después de las 23:59** del día en que se formaliza la reserva (cuando pasa a `PREBOOKED`, usando `ReservedAt`).

> Importante: concretar la **zona horaria** usada para “día” y “23:59” (recomendado: la zona horaria de negocio; no UTC).

## Algoritmo (pseudoflujo)

1. Seleccionar reservas con:
   - `ReservedAt` en una fecha **anterior al día actual** (es decir, reservas formalizadas en días pasados)
   - `ReservationStatus.Code` en conjunto “formalizadas y no confirmadas” (por ejemplo: `PREBOOKED`, `ERROR`, `RQ*`, `BOOKED`, etc.)
   - excluyendo: `CONFIRMED`, `PAID`, `CANCELLED`, `DELETED`, `PREBOOKEDTK`
2. Para cada reserva:
   - leer pagos asociados (`ReservationPayment` por `ReservationId`)
   - si existe pago `COMPLETED` → **NO cancelar**
   - si existe pago `PENDING` con `AttachmentUrl` informado → **NO cancelar**
   - en caso contrario → **cambiar estado a `CANCELLED`**
3. (Opcional recomendado) Registrar auditoría/telemetría: motivo “auto-cancel KO sin pago/justificante”.

## Casos borde a contemplar

- **Pago pendiente sin justificante**: se cancela si supera \(T\).
- **Pago completado después de cancelar**: definir comportamiento (ideal: no debería ocurrir; si ocurre, tratar como incidencia).
- **Reservas en `BOOKED` sin pagos**: se cancelarían si superan \(T\), salvo que exista `PENDING` con justificante (transferencia).
- **`ERROR` vs `PREBOOKED`**: el Swagger indica que `PREBOOKED` puede representar internamente `ERROR`; se recomienda tratar ambos como “en verificación/pendiente”.

## Implementación sugerida (alto nivel)

- **Job/worker** periódico (cada 15–60 min) que ejecute el algoritmo anterior.
- Reutilizar:
  - `IReservationService` para leer/actualizar estado (teniendo en cuenta validación de transiciones).
  - `IReservationPaymentService`/repositorio para listar pagos.
  - `IPaymentStatusService` para resolver IDs de `COMPLETED` / `PENDING` por `Code`.

