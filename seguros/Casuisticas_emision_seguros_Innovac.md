# Casuísticas Emisión Seguros Innovac (Básico vs Comfort)

## Regla de emisión (según validación negocio)
- Regla imperativa: todos los viajeros tienen que tener como mínimo el **Seguro Básico**, a menos que elijan **Comfort Plan** o **Comfort Plan Plus**.
- Si un viajero selecciona **Comfort Plan** o **Comfort Plan Plus**, **no** se emite el **Seguro Básico para ese mismo viajero**.
- Se agrupa la emisión por tipo de seguro para construir certificados:
- Emitimos un **certificado Básico** que incluye a los viajeros que **no** han elegido Comfort/Plus.
- Emitimos un **certificado Comfort** que incluye a los viajeros que han elegido Comfort Plan o Comfort Plan Plus.
- **Como máximo** se emiten **2 certificados por reserva** (1 de Básico y 1 de Comfort) cuando el flujo re-calcula y mantiene coherencia.
- Si existe un cambio posterior tras una emisión parcial y se aplica estrategia incremental sin anular certificados previos (ver Escenario 17), pueden emitirse certificados adicionales.
- Si todos los viajeros seleccionan Comfort (incluyendo mezcla Comfort/Comfort Plus), **solo** se emite Comfort y **no** se emite Básico.

## Escenario 1
- Viajero 1: selecciona el seguro básico
- Viajero 2: selecciona el seguro básico
Resultado:
- Emitimos 1 `Básico` (para 1,2) => 1 certificado por reserva

## Escenario 2
- Viajero 1: selecciona el seguro básico
- Viajero 2: selecciona el seguro Comfort Plan
Resultado:
- Emitimos 1 `Básico` (para el Viajero 1)
- Emitimos 1 `Comfort` (para el Viajero 2)

## Escenario 3
- Viajero 1: selecciona el seguro Comfort Plan
- Viajero 2: selecciona el seguro Comfort Plan
Resultado:
- Emitimos 1 `Comfort` (para 1,2) => 1 certificado por reserva

## Escenario 4
- Viajero 1: selecciona el seguro básico
- Viajero 2: selecciona el seguro Comfort Plan
- Viajero 3: selecciona el seguro básico
- Viajero 4: selecciona el seguro Comfort Plan
Resultado:
- Emitimos 1 `Básico` (para V1 y V3)
- Emitimos 1 `Comfort` (para V2 y V4)

## Escenario 5
- Viajero 1: selecciona Comfort Plan
- Viajero 2: selecciona Básico
Resultado:
- Emitimos 1 `Básico` (para el Viajero 2)
- Emitimos 1 `Comfort` (para el Viajero 1)

## Escenario 6
- 3 viajeros, todos básicos
- Viajero 1: Básico
- Viajero 2: Básico
- Viajero 3: Básico
Resultado:
- Emitimos 1 `Básico` (para 1,2,3) => 1 certificado por reserva

## Escenario 7
- 3 viajeros, todos comfort
- Viajero 1: Comfort Plan
- Viajero 2: Comfort Plan Plus
- Viajero 3: Comfort Plan
Resultado:
- Emitimos 1 `Comfort` (para 1,2,3) => 1 certificado por reserva
- No emitimos `Básico`

## Escenario 8
- 3 viajeros, 2 básicos + 1 comfort
- Viajero 1: Básico
- Viajero 2: Básico
- Viajero 3: Comfort Plan
Resultado:
- Emitimos 1 `Básico` (para V1 y V2)
- Emitimos 1 `Comfort` (para V3)
- Total: 2 certificados

## Escenario 9
- 3 viajeros, 1 básico + 2 comfort
- Viajero 1: Básico
- Viajero 2: Comfort Plan Plus
- Viajero 3: Comfort Plan
Resultado:
- Emitimos 1 `Básico` (para V1)
- Emitimos 1 `Comfort` (para V2 y V3)
- Total: 2 certificados

## Escenario 10
- 4 viajeros, 3 básicos + 1 comfort
- Viajero 1: Básico
- Viajero 2: Básico
- Viajero 3: Básico
- Viajero 4: Comfort Plan
Resultado:
- Emitimos 1 `Básico` (para V1,V2,V3)
- Emitimos 1 `Comfort` (para V4)
- Total: 2 certificados

## Escenario 11
- 4 viajeros, 1 básico + 3 comfort
- Viajero 1: Básico
- Viajero 2: Comfort Plan
- Viajero 3: Comfort Plan Plus
- Viajero 4: Comfort Plan
Resultado:
- Emitimos 1 `Básico` (para V1)
- Emitimos 1 `Comfort` (para V2,V3,V4)
- Total: 2 certificados

## Escenario 12
- 2 viajeros, comfort vs comfort plus
- Viajero 1: Comfort Plan
- Viajero 2: Comfort Plan Plus
Resultado:
- Emitimos 1 solo `Comfort` para V1 y V2
- No emitimos `Básico`

## Escenario 13
- 1 viajero, comfort
- Viajero 1: Comfort Plan
Resultado:
- Emitimos 1 `Comfort` (para V1)
- No emitimos `Básico`

## Escenario 14
- 1 viajero, básico
- Viajero 1: Básico
Resultado:
- Emitimos 1 `Básico` (para V1)

## Escenario 15
- Con “Room” interno seleccionado, pero no contratado
- Viajero 1: Básico + Room (interno)
- Viajero 2: Básico + Room (interno)
Resultado:
- Se emite solo lo contratado con Innovac.
- Emitimos 1 `Básico` (para V1 y V2).
- El “Room” interno no afecta a Innovac

## Escenario 16 (cambio de selección posterior)
- 3 viajeros
- Estado inicial (antes del cambio):
- Viajero 1: Básico
- Viajero 2: Comfort Plan
- Viajero 3: Básico
- Cambio posterior permitido:
- Se cambia el Viajero 3 de Básico -> Comfort Plan

Resultado (estado final):
- Emitimos 1 `Básico` (para V1).
- Emitimos 1 `Comfort` (para V2 y V3).

Nota operativa:
- Si el certificado no se ha emitido aún, se recalcula y se emite con la selección final.
- Si el certificado ya se emitió, el flujo correcto es cancelar (anular) y re-emitir con la nueva composición (Básico/Comfort) según aplique.

## Escenario 17 (emitido parcial y cambio posterior)
- 3 viajeros
- Estado inicial:
- Viajero 1: Básico
- Viajero 2: Comfort Plan
- Viajero 3: Básico
- Emisión previa:
- Ya se ha emitido el certificado `Comfort` para el Viajero 2.
- Cambio posterior:
- Se cambia el Viajero 3 de Básico -> Comfort Plan.

Resultado esperado (estrategia incremental, si aplicáis re-cálculo sin anular el `Comfort` ya emitido):
- Emitimos un nuevo certificado `Comfort` para el Viajero 3.
- El certificado `Básico` incluirá al Viajero 1.

Nota:
- Si preferís mantener “1 certificado Comfort por reserva” en todo momento, el flujo alternativo sería anular y re-emitir el certificado Comfort ya emitido para incluir también al Viajero 3.

