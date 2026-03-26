# Mejora de Seguros (Innovac)

## Objetivo
Definir el proceso completo para gestionar la contratación de seguros con Innovac desde la **Middle**, incluyendo:
- Cómo **mostrar y filtrar** los seguros en la Middle (pendientes vs realizados).
- Cómo **marcar** cuándo un seguro está **contratado** con Innovac.
- Cómo **gestionar y mostrar la documentación** del seguro.
- Qué hacer para casos **manuales (RQ Issue)** y cómo pasar el resultado a **Issue en TK**.
- Cómo encajar todo el flujo con el estado de la reserva (**confirm/paid**).

## Alcance
Incluye:
- Seguros contratados con Innovac (Seguro Básico, Comfort Plan, Comfort Plan Plus).
- Seguro interno “Room” (no se contrata, se gestiona internamente).
- Integración con TourKnife para sincronizar / reflejar actividades de seguro (producto/actividad TK).

Fuera de alcance (por definir aparte, si aplica):
- UI/UX de pantallas no relacionadas con seguros.
- Cambios en el cálculo de elegibilidad de destino (clasificación Europa vs Mundo+Noruega+Islandia).

## Referencias
- Web de Innovac (proveedor): [https://www.innovac.es/innovac/home.aspx](https://www.innovac.es/innovac/home.aspx)
- Especificación del proveedor (SecureToken Travel v21): *según PDF entregado por Innovac (no incluido aquí en detalle de campos/payloads).*

## Terminología
- **TK (TourKnife)**: sistema origen donde se materializan Issue/actividades relacionadas con el seguro.
- **Middle**: aplicación donde se visualiza el estado, se contrata el seguro y se muestra documentación.
- **RQ Issue**: caso en el que el flujo de contratación NO es automático y debe hacerse manualmente.
- **Contratado (Innovac)**: el proveedor ya ha emitido y/o ha devuelto la confirmación/póliza (ver criterio de marcado abajo).

## Primera Fase: seguros en Tour/TourKnife (como hasta ahora)
En la primera fase, el alta/introducción de los seguros se mantiene tal y como se hace hoy en **Tour/TourKnife**:
- Los seguros se configuran en TK como productos de tipo “Seguros”.
- Se exponen en el checkout a través de la API de Tour como **actividades** (`Activity`).
- La Middle consume esas actividades para mostrar y gestionar la selección del usuario (pero sin cambiar el origen en TK).

### Configuración en TK (identificación)
Según `seguros/SEGUROS_TK_Y_WEB.md`, en TK cada seguro se crea con:
- `Product Type`: `Seguros`
- `Service Type`: `Others`
- `Sub type`:
  - Seguro Básico: `Basic Assistance insurance`
  - Seguro Comfort Plan: `Assistance + Cancellation Insurance`
  - Seguro Comfort Plan Plus: `Assistance + Cancellation Insurance`
  - Seguro Comfort Room: `Other insurance`
- `Opcional`: `Sí` en todos los seguros (crítico para que el checkout permita “elegir uno” en competition group y “varios” en adicionales).

Además:
- Los que pertenecen a un `competition group` se muestran en el checkout como **elección exclusiva** (radio: uno excluye a los demás).
- Los que **no** pertenecen a un `competition group` se muestran como **seguros adicionales** (multi-select).

### Nota clave de mapeo TK -> Middle
En esta arquitectura, la Middle no “inventa” los seguros: los seguros aparecen en Middle porque previamente se sincronizan desde TK hacia la capa de Tour como actividades.
Para la sincronización de “seguro activities” se filtra por `product_type_id = 82` (actividades de seguro en TK).

## En la Middle: seguros como Activities
En la Middle, los seguros se gestionan como actividades (`Activity`) filtradas por:
- `ActivityType` = `Seguros` (en el front se busca por nombre; en el servicio se contempla código `ACTTYPE82` como tipo seguro).
- `itineraryId` (itinerario asociado a la departure/período).
- `isVisibleOnWeb = true`.

Además, el front distingue:
- Planes (Básico/Comfort/Comfort Plus) por que `activityCompetitionGroupId` **tiene valor**.
- El “Room” (adicional) por que `activityCompetitionGroupId` es **null**.

Endpoints (API de Tour / Middle) usados para construir la selección:
- `GET /api/Activity?itineraryId={itineraryId}&isVisibleOnWeb=true&activityTypeId={activityTypeIdSeguros}`
- `GET /api/DepartureActivity?departureId={departureId}` (para quedarnos solo con actividades asociadas al departure del periodo)
- `GET /api/ActivityPrice?ActivityId={...}&DepartureId={departureId}` (para precios de planes)
- `GET /api/ActivityCompetitionGroup/{id}` (para construir grupos de elección)

Para resolver IDs a partir de códigos/nombres (y soportar diferenciación por subtipo si es necesario):
- `GET /api/ActivityType` (resolver `ActivityType` con nombre `Seguros` / code `ACTTYPE82`)
- `GET /api/ActivitySubType` (resolver `ActivitySubtypeId` desde el subtipo configurado en TK)

## Tipos de seguros y reglas de selección

### 1) Seguro Básico
- Corresponde a: `INNOVAC GRUPOS INCLUSION`
- Condiciones:
  - Disponible **solo para residentes en España**.
  - Solo se puede gestionar/contratar cuando el **pago esté completo** (**paid**).

### 2) Comfort Plan
- Producto según destino:
  - Si es **Europa**: usar `escapadas premium`
  - Si es **Mundo + Noruega + Islandia**: usar `INNOVAC SELECT`

### 3) Comfort Plan Plus
- A efectos de contratar son **el mismo** que Comfort Plan.
- Regla de producto según destino:
  - Europa: `escapadas premium`
  - Mundo + Noruega + Islandia: `INNOVAC SELECT`

### 4) Seguro “Room” (interno)
- No se contrata con Innovac.
- Se gestiona **directamente por nosotros**.
- En Middle se mostrará en un apartado diferenciado o con etiqueta `Interno (Room)`.

## Estado y disparadores de gestión (Middle)

### Cuándo se puede gestionar el seguro
- El seguro se puede gestionar desde la Middle a partir de estado:
  - `confirm`
  - o `paid`
- Reglas:
  - Si aún NO está en `confirm/paid`, la Middle mostrará “Pendiente (esperando estado)” y/o deshabilitará acciones de contratación.

### Regla especial del Seguro Básico
- El Seguro Básico **solo** se puede hacer cuando el **pago esté completo**.
- Regla imperativa de negocio: **todos los viajeros** deben tener como mínimo Seguro Básico, excepto cuando **elijan Comfort Plan o Comfort Plan Plus**.
- Por tanto:
 - Para los viajeros que **no** hayan elegido Comfort/Plus, si está en `confirm` pero no en `paid`, el Seguro Básico debe quedar como `Pendiente (pago incompleto)`.

## Flujo general (no-RQ)

### Paso 1: Sincronización/lectura desde TK (base para la Middle)
- En la **primera fase**, la Middle “lee” los seguros desde la capa de Tour, que ya han sido introducidos/sincronizados desde TK como `Activity`.
- En TK, las actividades de seguro se identifican por `product_type_id = 82`.
- Para sincronizar actividades de seguro desde TK:
  - `PUT /api/sync/Activities/insurance/itinerary/{itineraryTkId}`

### Paso 2: Mostrar en Middle y clasificar (pendientes vs realizados)
En la Middle debe existir un **apartado de Seguros** con:
- Filtro `Pendientes`:
  - seguros detectados pero aún no contratados con Innovac
  - o con restricciones no cumplidas (ej. pago incompleto para básico, aplicable a viajeros que no elijan Comfort/Plus)
- Filtro `Realizados`:
  - seguros ya contratados y con documentación asociada (según criterio de “contratado”)

### Paso 3: Contratación con Innovac
Para contratar:
- Es necesario el `DNI o Pasaporte` del **titular de la reserva**.
- La Middle llama al **servicio web** del proveedor (SecureToken Travel v21) según el producto determinado por reglas de destino/tipo.

Resultado esperado:
- La respuesta debe permitir marcar:
  - `contratado` (ver criterio)
  - referencias (póliza/booking/identificador del proveedor)
  - datos para documentación

### Paso 4: Persistencia y marcado “Contratado con Innovac”
La Middle debe marcar el seguro como:
- `REALIZADO - CONTRATADO INNOVAC`
cuando el proveedor haya confirmado emisión/contratación.

**Criterio recomendado (a concretar contra respuesta real del proveedor):**
- Existencia de:
  - identificador de contrato/póliza en Innovac
  - y/o URL/ID de documentación emitida
- y ausencia de errores en el flujo de contratación.

Cuando esté “contratado”, se debe habilitar:
- descarga/visualización de documentación
- visualización de datos principales (compañía, producto, fechas, referencia)

## Flujo RQ Issue (manual)

### 1) Detección
Si el seguro viene como **RQ Issue**, la Middle:
- No debe intentar contratar automáticamente.
- Debe mostrar `Pendiente - RQ (manual)`.

### 2) Acción manual
- La operación se hace manualmente (procedimiento interno).
- El resultado manual debe generar los datos necesarios para actualizar TK y/o continuar el flujo de documentación.

### 3) Paso a Issue en TK
Una vez realizado el proceso manual:
- Hay que “pasarlo a Issue en TK”.
- Se debe definir desde Middle:
  - cómo se identifica el Issue resultante en TK
  - cómo se vuelve a sincronizar/reflejar en el apartado de seguros

### 4) Continuación en Middle
Después de actualizar TK:
- La sincronización debe reflejar el estado final para que la Middle pueda marcar:
  - `Realizado` (si ya existe documentación/confirmación)
  - o `Pendiente - documentación` (si falta adjuntar documentos o esperar emisión)

> Nota: aquí es clave definir el mapeo exacto entre “RQ en Middle” y “Issue en TK” (IDs/estado/evento). En este MD queda como punto a cerrar con el equipo técnico.

## Gestión de documentación del seguro

### Qué documentación se debe mostrar
En la Middle, por cada seguro:
- Identificador de Innovac (póliza/contrato si aplica)
- Producto contratado (ej. `INNOVAC GRUPOS INCLUSION`, `escapadas premium`, `INNOVAC SELECT`)
- Fechas de cobertura (si aplica en respuesta del proveedor)
- Archivos/documentos del seguro emitidos por Innovac (principalmente PDF o links)

### Cómo mostrarla
En el apartado `Realizados` y/o en la ficha del seguro:
- Una sección “Documentación” con:
  - lista de documentos
  - botón/enlace `Descargar`
  - posible preview si existe soporte

### Persistencia / fuente de verdad
Recomendación:
- La Middle debe guardar (o referenciar) los documentos una vez se reciban del proveedor o una vez queden asociados vía TK.
- Evitar depender únicamente de reconsultas si:
  - existen límites de consulta
  - o el proveedor no mantiene accesos indefinidamente

## UI Middle - Requisitos mínimos

### Filtros y secciones
- Sección principal: `Seguros`
- Filtros:
  - `Pendientes`
  - `Realizados`
- (Opcional) Filtro adicional:
  - `Interno (Room)`
  - `Tipo` (Básico / Comfort / Comfort Plus)

### Información visible por fila (pendiente/realizado)
Para cada seguro:
- Tipo (Seguro Básico / Comfort Plan / Comfort Plan Plus / Room interno)
- Producto Innovac esperado (cuando aplique)
- Estado (texto corto):
  - `Pendiente - esperando confirm/paid`
  - `Pendiente - pago incompleto (básico)`
  - `Pendiente - RQ (manual)`
  - `Pendiente - falta DNI/Pasaporte titular`
  - `Realizado - contratado Innovac`
  - `Realizado - documentación pendiente` (si aplica)
- Compañía (Innovac / Interno)
- Referencia (si existe): póliza/contrato o ID de Innovac
- Fecha (si está disponible): confirmación/contratación

### Acciones
- En `Pendientes`:
  - `Contratar` (solo si es elegible y datos completos)
  - `Generar/Continuar RQ` (solo si aplica)
- En `Realizados`:
  - `Ver documentación`
  - `Descargar PDF`

## Validaciones de negocio (obligatorias)
1. Para contratar cualquier seguro Innovac:
   - DNI/Pasaporte del titular obligatorio.
2. Para Seguro Básico:
   - solo residente España
   - solo cuando esté `paid` (pago completo)
   - además: **aplica solo a los viajeros que no elijan Comfort/Comfort Plus** (es imperativo como mínimo).
3. Para Comfort Plan / Plus:
   - selección de producto por destino:
     - Europa -> `escapadas premium`
     - Mundo + Noruega + Islandia -> `INNOVAC SELECT`
4. Si el caso es RQ Issue:
   - no automático
   - requiere paso manual y luego actualización en TK.

## Sincronización con TourKnife (para soportar el estado en Middle)
- Actividades de seguro en TK:
  - identificar por `product_type_id = 82`
- Medio de sincronización:
  - `PUT /api/sync/Activities/insurance/itinerary/{itineraryTkId}`
- La Middle debe usar la sincronización para:
  - actualizar el listado de seguros
  - reflejar “Realizado” tras contratación/doc emitida
  - reflejar “RQ” tras la acción manual y posterior Issue en TK

## Consultar el seguro de una reserva (reservations API)
Cuando necesitemos ver el seguro que tiene una reserva ya creada:
1. Se consultan los viajeros de la reserva:
   - `GET /api/ReservationTraveler?ReservationId={reservationId}`
2. Para cada viajero (o para los `ReservationTravelerId` conocidos) se consultan sus actividades asignadas:
   - `GET /api/ReservationTravelerActivity?ReservationTravelerId={reservationTravelerId}`

En `ReservationTravelerActivity`, las actividades asignadas incluyen las de tipo “seguro”, ya que los seguros seleccionados se guardan como actividades por viajero.

## Checklist de cierre (para completar implementación)
- Definir el “criterio exacto de contratado” según la respuesta real del proveedor (campos presentes).
- Definir cómo Middle obtiene/valida `DNI/Pasaporte` del titular.
- Definir cómo Middle detecta “residente en España”.
- Definir el mapeo exacto entre:
  - `RQ Issue` en Middle
  - y “Issue en TK” (IDs, estados, evento de sincronización).
- Definir dónde y cómo se guardan los PDFs (almacenamiento y permisos).

## Peticiones Curl (endpoints de pruebas - TourKnife sync)
Basado en `core.differentroads.tourknife/core.differentroads.tourknife.API/ActivitySync_Test.http`.

> Sustituye `{{baseUrl}}` por la URL base del servicio y `78350` por el `itineraryTkId`.

### Sincronizar actividades de seguro para un itinerario
```bash
curl --location --request PUT '{{baseUrl}}/api/sync/Activities/insurance/itinerary/78350' \
  --header 'Content-Type: application/json'
```

### Sincronizar todas las actividades de un itinerario (modo general)
```bash
curl --location --request PUT '{{baseUrl}}/api/sync/Activities/itinerary/78350' \
  --header 'Content-Type: application/json'
```

### Sincronizar grupos de competencia de un itinerario (modo complementario)
```bash
curl --location --request PUT '{{baseUrl}}/api/sync/Activities/competitionGroup/itinerary/78350' \
  --header 'Content-Type: application/json'
```

