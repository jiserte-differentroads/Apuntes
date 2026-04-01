# Campos requeridos y datos de los viajeros (Middleweb vs TK)

Este documento define el contrato de **qué datos se piden a los viajeros** y **en qué momento** para poder realizar una reserva, con una regla clave: al implementar este documento se traslada la responsabilidad de la gestión/captura de datos desde TK hacia **Middleweb** mediante una configuración.

## 1) Cambio de responsabilidad (obligatorio al implementar)

1. Al implementar este documento, **se elimina la responsabilidad de TK** sobre la gestión de los datos que se le piden al usuario.
2. La captura de datos al usuario queda gestionada por **Middleweb**.
3. Middleweb trabaja con una **configuración por defecto** y permite ampliar/ajustar los campos requeridos en:
  - cada periodo,
  - cada tour,
  - y según el flujo (datos en el momento de la reserva o para introducir después).

## 2) Configuración por tour/periodo

Middleweb debe poder definir (por tour/periodo):

1. Qué campos son **mínimos** para permitir realizar una reserva.
2. Qué campos adicionales son requeridos:
  - **en el momento de la reserva**, o
  - **para introducir después** (mediante workflows y/o avisos).
3. Las reglas condicionales en función del tipo de salida y del perfil de los viajeros (ej. singles/mixtos, existencia de niños, destino fuera de Europa).

## 3) Datos mínimos para realizar una reserva

Para poder crear una reserva, los requisitos mínimos son:

1. `Nombre` y `Apellidos` de **todos los viajeros**.
2. `Email` (al menos 1) de **un viajero**.
3. `Teléfono` (al menos 1) de **un viajero**.

> Regla de asignación de “principal”: el viajero que aporta el `email` y el `teléfono` se considera el **viajero principal** a efectos de ciertos campos (ver seguros).
>
> Nota sobre emails: **todos los emails** que se introduzcan en el checkout **recibirán la documentación** y **podrán ver y gestionar la reserva**.

## 3.1 Regla de obligatoriedad en Checkout

En Checkout, los campos marcados como **obligatorios** deben ser únicamente los necesarios para **realizar la reserva**.

- Por defecto, mostrar en el formulario: `Nombre`, `Apellidos`, `Email` y `Teléfono`.
  - `Nombre` y `Apellidos`: **obligatorios** para **todos los viajeros**.
  - `Email` y `Teléfono`: **solo obligatorios en un viajero** (el **viajero principal**); para el resto, **opcionales**.
- El resto de campos son **opcionales**:
  - se pueden **completar después** mediante Middleweb/workflows y/o avisos (según se configure para ese tour/periodo), y/o
  - se pueden **mostrar en el propio checkout** como campos “extra” bajo la opción **`Ver más`** del formulario de cada viajero, para que el usuario pueda rellenarlos en el momento si lo desea.

## 4) Reglas condicionales por tipo de salida

### 4.1 Salida Single/Mixto

Si la salida de la reserva es de tipo `single` o `mixto`, entonces es obligatorio introducir:

1. `Nombre` y `Apellidos` de **todos los viajeros**.
2. `Sexo` de **todos los viajeros**.

Además:

1. En `singles`, la `fecha de nacimiento` es obligatoria para todos los viajeros (ver punto 4.2).

### 4.2 Existencia de niños

Si hay **algún niño** en la reserva, entonces es obligatorio introducir:

1. `Fecha de nacimiento` de **ese/los niño(s)** para verificación que es/son niño(s).

## 5) Reglas para seguros (momento y fuente del dato)

Para realizar los seguros de las reservas se necesita:

1. `DNI` de un viajero, que será el **viajero principal**.
2. El `DNI` del seguro se pedirá al mismo viajero que introduce `email` y `teléfono`.

> Importante: estos campos se consideran obligatorios en Checkout **solo si** la configuración del tour indica que son necesarios en el momento de la reserva/para avanzar en el flujo actual. Si no, se piden después mediante workflows.

## 6) Reglas por destino (Europa vs fuera de Europa)

Si el destino es `fuera de Europa`, entonces es necesario pedir por viajero (cuando aplique según las reglas del flujo configurado):

1. `Pasaporte`
2. `Fecha de caducidad del pasaporte`

> Importante: estos campos se consideran obligatorios en Checkout **solo si** la configuración del tour indica que son necesarios para realizar la reserva en ese destino/flujo. Si no, se completan después.

## 7) Resumen operativo (checklist rápido)

1. Reserva mínima: `Nombre + Apellidos` (de todos los viajeros) y al menos `Email + Teléfono` (de un viajero).
2. Single/Mixto: `Nombre + Apellidos + Sexo` para todos los viajeros.
3. Si hay niños: `Fecha de nacimiento` del/los niño(s) para verificación.
4. En `singles`: `Fecha de nacimiento` obligatoria.
5. Seguros: `DNI` del viajero principal (el mismo que pone `email` y `teléfono`), **si** la configuración del tour marca esto como obligatorio en Checkout.
6. Fuera de Europa: `Pasaporte + caducidad`, **si** la configuración del tour marca esto como obligatorio en Checkout.

