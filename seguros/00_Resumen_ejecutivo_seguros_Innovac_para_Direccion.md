# Resumen ejecutivo – Gestión de Seguros (Innovac/AON) en Middle

## Contexto y objetivo
Actualmente los seguros se ofrecen al cliente como parte del checkout y la selección queda registrada por viajero. El objetivo de esta iniciativa es **industrializar la gestión completa de seguros** (selección, contratación, seguimiento y documentación) desde la **Middle**, con integración con **Innovac/AON** para los seguros contratables y manteniendo la convivencia con nuestros “seguros internos”.

## Qué entendemos por “nuestros seguros” vs “seguros Innovac”
En nuestro ecosistema, “seguro” puede significar dos cosas:

- **Seguro como producto/actividad (nuestro catálogo)**:
  - En **TourKnife (TK)** los seguros se configuran como productos de tipo **Seguros** y se exponen al checkout.
  - En la arquitectura actual, la Middle **no crea** seguros: los **consume como `Activities`** provenientes de TK (sincronizadas a Tour) y permite al usuario seleccionar por viajero.
  - Esto es “nuestro modelo” de oferta/selección y persistencia (por ejemplo, vía `ReservationTravelerActivity`).

- **Seguro contratado con proveedor (Innovac/AON)**:
  - Para los productos contratables (Básico/Comfort/Comfort Plus), la contratación real se ejecuta contra el servicio de **Innovac/AON (SecureToken Travel v21)**.
  - Innovac devuelve identificadores de emisión/contrato (p. ej. `CODIGO_REFERENCIA`, `LOCALIZADOR`, `NUMERO_POLIZA`) y la **documentación** asociada (HTML/PDF o URL según modalidad).
  - El “seguro Innovac” es, por tanto, el **resultado de una emisión** (certificado/póliza) que debe quedar trazado y auditable.

## Tipos de seguro (visión negocio)
- **Seguro Básico**: corresponde a `INNOVAC GRUPOS INCLUSION`.
  - Restricciones clave: **residentes en España** y **solo cuando el pago está completo**.
- **Comfort Plan / Comfort Plan Plus**: a efectos de contratación son equivalentes y el producto Innovac depende del destino:
  - **Europa**: `escapadas premium`
  - **Mundo + Noruega + Islandia**: `INNOVAC SELECT`
- **Room (interno)**: es un seguro **propio**, se gestiona internamente y **no se contrata** con Innovac.

## Regla imperativa (garantía mínima por viajero)
- Todos los viajeros deben tener como mínimo **Seguro Básico**, **salvo** que elijan **Comfort Plan** o **Comfort Plan Plus**.
- Consecuencia operativa: se emite Básico solo para viajeros que **no** hayan elegido Comfort/Plus, y Comfort para quienes sí lo hayan elegido.

## Qué cambia: apartado específico en la Middle
Se implementará un **apartado específico de Seguros** en la Middle, con foco operativo y de control:

- **Visión unificada por reserva**:
  - Qué ha elegido cada viajero (catálogo/Activities).
  - Qué está contratado con Innovac (estado y referencias devueltas por el proveedor).
  - Qué documentación está disponible y dónde se encuentra.

- **Estados y seguimiento**:
  - Diferenciar **Pendiente** vs **Contratado/Emitido** vs **Anulado/Error**.
  - Cumplimiento de reglas por estado de reserva (`confirm/paid`) y restricción especial del Básico (solo `paid`).

- **Operación y excepciones**:
  - Identificación de casos manuales (**RQ Issue**) y su gestión fuera del flujo automático, con trazabilidad hacia TK (Issue).
  - Auditoría y troubleshooting (logs de integración y reintentos controlados).

## Enfoque por fases (alto nivel)
- **Fase 1 (continuidad operativa)**:
  - Los seguros siguen originándose/configurándose en **Tour/TK** como hasta ahora.
  - La Middle los consume como **Activities** y centraliza el seguimiento/visualización.

- **Fase 2 (gestión completa)**:
  - La Middle orquesta la **contratación real** con Innovac/AON y persiste:
    - referencias de emisión/contrato,
    - estado del ciclo de vida,
    - documentación asociada,
    - auditoría de llamadas al proveedor.

## Beneficios para Dirección
- **Control y trazabilidad end-to-end**: selección por viajero ↔ emisión con proveedor ↔ documentación.
- **Reducción de manualidad** y mejor gestión de excepciones (RQ) con proceso estándar.
- **Mejor experiencia de cliente y operaciones**: acceso centralizado a certificados y estados.
- **Auditoría y cumplimiento**: evidencias de emisión, anulación y documentación por reserva.

## Riesgos / puntos de decisión
- **Cambios posteriores a emisión**: si un viajero cambia su selección tras una emisión parcial, hay dos estrategias:
  - **Anular y re-emitir** (mantener 1 certificado por tipo).
  - **Incremental** (emitir certificados adicionales): el total por reserva puede ser **1..n**.
- **Documento/almacenamiento**: decidir si guardamos solo `CERTIFICADO_URL` o si descargamos y almacenamos PDF/HTML como documento interno.

## Indicadores recomendados (para seguimiento)
- % reservas `confirm/paid` con seguros en estado correcto (sin pendientes improcedentes).
- Tiempo medio de contratación (desde `paid` hasta `issued`).
- Ratio de errores / reintentos por operación (issue/cert/html/cancel).
- Volumen de excepciones RQ y tiempo de resolución.

