# Respuesta de configuracion AON (QC) - Productos, destinos, duraciones y coberturas

Documento generado a partir de `Apuntes/seguros/ejemplo-respuesta-configuracion-qc.xml` (endpoint XMLCallRequest en entorno QC).

## Resumen ejecutivo
- Modalidades (productos): **15**
- Destinos (total sumado en todas las modalidades): **20**
- Duraciones (total sumado en todas las modalidades): **227**
- Observacion: cada modalidad puede incluir 0..N destinos y cada destino 0..N duraciones (con precio).

## Como interpretar \"Destinos\" y \"Duraciones\"

- **Destinos (columna de tabla por modalidad)**: numero de nodos `DESTINO` con informacion real (nombre/codigo y/o duraciones) dentro de esa `MODALIDAD`.
- **Duraciones (columna de tabla por modalidad)**: suma de `DURACION` dentro de los destinos anteriores (cada `DURACION` suele incluir dias y precio).
- **Importante**: no todas las modalidades devuelven precios en la respuesta de configuracion. En esas modalidades, el XML incluye coberturas y metadatos, pero no incluye destinos/duraciones; por eso veras **Destinos=0 y Duraciones=0**.

### Destinos reales detectados en esta respuesta (QC)

| Codigo destino | Nombre | Modalidades donde aparece | Duraciones totales (sumadas) |
|---:|---|---:|---:|
| 24 | Europa y Ribereños del Mediterráneo | 7 | 78 |
| 3 | Mundo | 7 | 78 |
| 1 | España | 5 | 64 |
| 7 | Andorra | 1 | 7 |

## Detalle de modalidades (tabla)

| Codigo | Nombre | Cia | Ramo | Categoria | Destinos | Duraciones | TAEDSOK | Comisionable |
|---:|---|---|---|---|---:|---:|---|---|
| 30 | Anulación Estancias, Circuitos Y Aereo Arag | Arag | Cancelación | VACACIONAL | 0 | 0 | No |  |
| 1139 | Radiales Caser | Caser | Asistencia | GRUPOS | 0 | 0 | No |  |
| 1158 | Vacacional Covid | Caser | Asistencia y cancelación | VACACIONAL | 0 | 0 | No |  |
| 1170 | Aon Travel 2020 | Axa | Asistencia y cancelación | VACACIONAL | 0 | 0 | No |  |
| 1195 | Aon Vacacional Grupos | Caser | Asistencia y cancelación | GRUPOS | 3 | 15 | No | No |
| 1221 | Aon Vip 2021 | Axa | Modular Mixto | VACACIONAL | 0 | 0 | No |  |
| 1232 | Vacacional A La Carta | Arag | Modular Mixto | VACACIONAL | 3 | 21 | No | No |
| 1233 | Cruceros A La Carta | Arag | Modular Mixto | CRUCERO | 2 | 14 | No | No |
| 1243 | Aon Grupos Inclusión | Axa | Asistencia | GRUPOS | 0 | 0 | No |  |
| 1244 | Aon Grupos Opcional | Axa | Asistencia y cancelación | GRUPOS | 0 | 0 | No |  |
| 1280 | AON Premium 2022 | Caser | Asistencia y cancelación | VACACIONAL | 3 | 12 | No | No |
| 1281 | Aon Estancias Covid | Caser | Asistencia y cancelación | COSTAS Y ESTANCIAS | 0 | 0 | No |  |
| 1283 | Multiviajes Premium | Caser | Asistencia y cancelación | VACACIONAL | 3 | 3 | No | No |
| 1415 | Aon Infinity 2025 | Iris Global Solutions | Modular Mixto | VACACIONAL | 3 | 81 | No | No |
| 1480 | Aon Infinity 2025 New | Iris Global Solutions | Modular Mixto | VACACIONAL | 3 | 81 | No | No |

## Anexo A - Fichas por modalidad

### Anulación Estancias, Circuitos Y Aereo Arag (codigo 30)

- **Aseguradora (cia)**: Arag
- **Ramo**: Cancelación
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/CCGGgyacpg30.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/Viajes/Arag/IPID_GYACPG30.pdf`
- **Notas importantes**: La garantía de anulación únicamente tendrá validez si se contrata en el mismo momento que el viaje objeto del seguro, o como máximo en los 7 días siguientes.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 0030001 | Cancelación | hasta 6.000€ |
| 0030002 | Asistencia médica | No tiene |
| 0030003 | Equipajes | No tiene |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Radiales Caser (codigo 1139)

- **Aseguradora (cia)**: Caser
- **Ramo**: Asistencia
- **Categoria**: GRUPOS
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/asistenciaviaje/gyacas1139.pdf`

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 001139001 | Gastos médicos en el extranjero | 950 € |
| 001139002 | Gastos médicos en el país de domicilio habitual del Asegurado | 650 € |
| 001139003 | Pérdida, daños y robo de equipaje | 75 € |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Vacacional Covid (codigo 1158)

- **Aseguradora (cia)**: Caser
- **Ramo**: Asistencia y cancelación
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/asistenciaviaje/CCGGgyacas1158.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/Viajes/CASER/IPID_gyacas1158.pdf`
- **Notas importantes**: Este seguro puede emitirse en cualquier momento hasta el día antes del inicio del viaje. Seguro válido para Extranjeros. Contratación permitida para un máximo de 10 asegurados para las mismas fechas y destino del viaje

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1158001 | Asistencia médica | hasta 4.500 € (incluye cobertura Covid19) |
| 1158002 | Equipajes | hasta 300 € |
| 1158003 | Cancelación | hasta 1.500 € (incluye cobertura Covid19) |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Aon Travel 2020 (codigo 1170)

- **Aseguradora (cia)**: Axa
- **Ramo**: Asistencia y cancelación
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyaaxa1170.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/services/documentos/ipid/Viajes/AXA/IPID_gyaxa1170.pdf`
- **Notas importantes**: El seguro debe contratarse en el momento de la confirmación del viaje, si se contrata con posterioridad se aplicará un período de carencia de 72h. La tarifa de España incluye también los viajes a Andorra. La tarifa Europa incluye los paises Ribereños del Mediterráneo. Solo válido para residentes en España. Válido para Grupos.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 001170001 | Cancelación | Hasta 1.500 € (Incluye cobertura Covid19) |
| 001170002 | Asistencia médica | Hasta 4.500 € (Incluye cobertura Covid19) |
| 001170003 | Equipajes | Hasta 300 € |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Aon Vacacional Grupos (codigo 1195)

- **Aseguradora (cia)**: Caser
- **Ramo**: Asistencia y cancelación
- **Categoria**: GRUPOS
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/asistenciaviaje/gyacas1195.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/Viajes/CASER/IPID_gyacas1195.pdf`
- **Notas importantes**: Esta modalidad podrá contratarse con posterioridad a la confirmación de la reserva, aplicando un período de carencia de 72h. Seguro válido para residentes en el extranjero. NO es válido para residentes de origen Mundo que viajen a España o Europa. Válido para grupos.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1195001 | Cancelación | hasta 1.500 € |
| 1195002 | Asistencia médica | hasta 10.000 € |
| 1195003 | Equipajes | Hasta 300 € |

**Destinos y duraciones (con precio)**

- **España** (codigo 1)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 4 días | 4 | 4 | 14.84 | 14.84 | 24 |
| 9 días | 9 | 9 | 18.91 | 18.91 | 24 |
| 16 días | 16 | 16 | 23.1 | 23.1 | 24 |
| 24 días | 24 | 24 | 30.24 | 30.24 | 24 |
| 32 días | 32 | 32 | 33.68 | 33.68 | 24 |

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 4 días | 4 | 4 | 14.84 | 14.84 | 24 |
| 9 días | 9 | 9 | 18.91 | 18.91 | 24 |
| 16 días | 16 | 16 | 23.1 | 23.1 | 24 |
| 24 días | 24 | 24 | 30.24 | 30.24 | 24 |
| 32 días | 32 | 32 | 33.68 | 33.68 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 4 días | 4 | 4 | 14.84 | 14.84 | 24 |
| 9 días | 9 | 9 | 18.91 | 18.91 | 24 |
| 16 días | 16 | 16 | 23.1 | 23.1 | 24 |
| 24 días | 24 | 24 | 30.24 | 30.24 | 24 |
| 32 días | 32 | 32 | 33.68 | 33.68 | 24 |

---

### Aon Vip 2021 (codigo 1221)

- **Aseguradora (cia)**: Axa
- **Ramo**: Modular Mixto
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyaaxa1221.pdf`
- **Notas importantes**: El seguro de solo Asistencia en viaje puede contratarse hasta el día antes del inicio del viaje. La modalidad con cobertura de Cancelación del viaje si se contrata con posterioridad a la confirmación de la reserva, se aplicará un período de carencia de 72h. Solo válido para residentes en España. Válido para cruceros.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 001163001 | Cancelación | Cancelación en Resto del Mundo Hasta 15.000.-€ |
| 001163002 | Asistencia médica | ILIMITADA |
| 001163003 | Equipajes | hasta 3.000€ |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Vacacional A La Carta (codigo 1232)

- **Aseguradora (cia)**: Arag
- **Ramo**: Modular Mixto
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyacpg1232.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/services/documentos/ipid/Viajes/ARAG/IPID_gyacpg789.pdf`
- **Notas importantes**: La garantía de anulación debe contratarse el día de la confirmación de la reserva o MÁXIMO dentro de los 7 días posteriores a la misma. Válido para NO residentes en ESPAÑA. Válido para actividades turísticas deportivas y/o de aventura. (Consultar condicionado)

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1232001 | Cancelación | hasta 10.000€ |
| 1232002 | Asistencia médica | hasta 500.000€ |
| 1232003 | Equipajes | hasta 4.000€ |

**Destinos y duraciones (con precio)**

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 9 días | 9 | 9 | 29.32 | 29.32 | 24 |
| 17 días | 17 | 17 | 47.23 | 47.23 | 24 |
| 22 días | 22 | 22 | 68.55 | 68.55 | 24 |
| 34 días | 34 | 34 | 102.81 | 102.81 | 24 |
| 64 días | 64 | 64 | 137.08 | 137.08 | 24 |
| 94 días | 94 | 94 | 209.51 | 209.51 | 24 |
| 120 días | 120 | 120 | 281.77 | 281.77 | 24 |

- **Andorra** (codigo 7)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 9 días | 9 | 9 | 19.02 | 19.02 | 24 |
| 17 días | 17 | 17 | 27.1 | 27.1 | 24 |
| 22 días | 22 | 22 | 40.69 | 40.69 | 24 |
| 34 días | 34 | 34 | 47.92 | 47.92 | 24 |
| 64 días | 64 | 64 | 55.14 | 55.14 | 24 |
| 94 días | 94 | 94 | 104.89 | 104.89 | 24 |
| 120 días | 120 | 120 | 154.56 | 154.56 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 9 días | 9 | 9 | 19.02 | 19.02 | 24 |
| 17 días | 17 | 17 | 27.1 | 27.1 | 24 |
| 22 días | 22 | 22 | 40.69 | 40.69 | 24 |
| 34 días | 34 | 34 | 47.92 | 47.92 | 24 |
| 64 días | 64 | 64 | 55.14 | 55.14 | 24 |
| 94 días | 94 | 94 | 104.89 | 104.89 | 24 |
| 120 días | 120 | 120 | 154.56 | 154.56 | 24 |

---

### Cruceros A La Carta (codigo 1233)

- **Aseguradora (cia)**: Arag
- **Ramo**: Modular Mixto
- **Categoria**: CRUCERO
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyacpg1233.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/services/documentos/ipid/Viajes/ARAG/IPID_gyacpg804.pdf`
- **Notas importantes**: La garantía de anulación debe contratarse el día de la confirmación de la reserva o MÁXIMO dentro de los 7 días posteriores a la misma. Válido para NO residentes en ESPAÑA.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1233001 | Cancelación | hasta 10.000€ |
| 1233002 | Asistencia médica | hasta 100.000€ |
| 1233003 | Equipajes | hasta 4.000€ |

**Destinos y duraciones (con precio)**

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 9 días | 9 | 9 | 43.98 | 43.98 | 24 |
| 17 días | 17 | 17 | 70.85 | 70.85 | 24 |
| 22 días | 22 | 22 | 102.83 | 102.83 | 24 |
| 34 días | 34 | 34 | 154.22 | 154.22 | 24 |
| 64 días | 64 | 64 | 205.62 | 205.62 | 24 |
| 94 días | 94 | 94 | 314.27 | 314.27 | 24 |
| 120 días | 120 | 120 | 422.66 | 422.66 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 9 días | 9 | 9 | 28.53 | 28.53 | 24 |
| 17 días | 17 | 17 | 40.65 | 40.65 | 24 |
| 22 días | 22 | 22 | 61.04 | 61.04 | 24 |
| 34 días | 34 | 34 | 71.88 | 71.88 | 24 |
| 64 días | 64 | 64 | 82.71 | 82.71 | 24 |
| 94 días | 94 | 94 | 157.34 | 157.34 | 24 |
| 120 días | 120 | 120 | 231.84 | 231.84 | 24 |

---

### Aon Grupos Inclusión (codigo 1243)

- **Aseguradora (cia)**: Axa
- **Ramo**: Asistencia
- **Categoria**: GRUPOS
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyaaxa1243.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/Viajes/AXA/IPID_gyaaxa1243.pdf`
- **Notas importantes**: El seguro debe contratarse con anterioridad a la fecha de salida del viaje. La prima para el destino España incluye también Andorra, Portugal y Sur Francia hasta 150 km desde la frontera. Sólo válido para residentes en España. Válido para grupos.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 0012420001 | Cancelación | No tiene |
| 0012420002 | Equipajes | Hasta 200€ |
| 0012420003 | Asistencia médica | Hasta 3.000€ |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Aon Grupos Opcional (codigo 1244)

- **Aseguradora (cia)**: Axa
- **Ramo**: Asistencia y cancelación
- **Categoria**: GRUPOS
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyaaxa1244.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/Viajes/AXA/IPID_gyaaxa1244.pdf`
- **Notas importantes**: La garantía de anulación debe contratarse el día de la confirmación de la reserva, si se realizara después de este día, se aplicará un período de carencia de 72 horas. La prima con destino España incluye también Andorra. Sólo válido para residentes en España. Válido para grupos. Válido para cruceros con recargo del 50 %.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 0012440001 | Cancelación | Hasta 3.000€ |
| 0012440002 | Equipajes | Hasta 300€ |
| 0012440003 | Asistencia médica | Hasta 12.000€ |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### AON Premium 2022 (codigo 1280)

- **Aseguradora (cia)**: Caser
- **Ramo**: Asistencia y cancelación
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/asistenciaviaje/CCGGgyacas1121.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/services/documentos/ipid/Viajes/CASER/IPID_gyacas1182.pdf`
- **Notas importantes**: Esta modalidad podrá contratarse con posterioridad a la confirmación de la reserva, aplicando un período de carencia de 72h. Solo válido para residentes en España. Producto válido para CRUCEROS sin coste adicional.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1280000 | <span style="color:red">Incluye WIN WAY</b></span> |  |
| 1280001 | Cancelación(fuerza mayor) hasta 10.000€ | . |
| 1280002 | Asistencia médica enfermedades preexistentes. | hasta 10.000 € |
| 1280003 | Asistencia médica | hasta 700.000 € |

**Destinos y duraciones (con precio)**

- **España** (codigo 1)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 29.63 | 29.63 | 24 |
| 10 días | 10 | 10 | 39.83 | 39.83 | 24 |
| 17 días | 17 | 17 | 54.34 | 54.34 | 24 |
| 32 días | 32 | 32 | 65.79 | 65.79 | 24 |

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 43.31 | 43.31 | 24 |
| 10 días | 10 | 10 | 64.61 | 64.61 | 24 |
| 17 días | 17 | 17 | 92.97 | 92.97 | 24 |
| 32 días | 32 | 32 | 135.23 | 135.23 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 29.63 | 29.63 | 24 |
| 10 días | 10 | 10 | 39.83 | 39.83 | 24 |
| 17 días | 17 | 17 | 54.34 | 54.34 | 24 |
| 32 días | 32 | 32 | 65.79 | 65.79 | 24 |

---

### Aon Estancias Covid (codigo 1281)

- **Aseguradora (cia)**: Caser
- **Ramo**: Asistencia y cancelación
- **Categoria**: COSTAS Y ESTANCIAS
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/asistenciaviaje/gyacas1140.pdf`

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1281001 | Anulación de reservas. | Hasta 100% de la reserva |
| 1281002 | Vacaciones no disfrutadas | Hasta 100% de la reserva |
| 1281003 | Indemnización por demora en la entrega del equipaje (mínimo 6 horas) | Hasta 750€ |

**Destinos y duraciones (con precio)**

> Esta modalidad no incluye destinos/duraciones en la respuesta de configuracion (no hay precios por destino/dias en este XML).

---

### Multiviajes Premium (codigo 1283)

- **Aseguradora (cia)**: Caser
- **Ramo**: Asistencia y cancelación
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/asistenciaviaje/CCGGgyacas1121.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/services/documentos/ipid/Viajes/CASER/IPID_gyacas1182.pdf`
- **Notas importantes**: Esta modalidad podrá contratarse con posterioridad a la confirmación de la reserva, aplicando un período de carencia de 72h. Solo válido para residentes en España. Producto válido para CRUCEROS sin coste adicional.

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1280001 | Cancelación(fuerza mayor) hasta 10.000€ | hasta 7.000 € |
| 1280002 | Asistencia médica enfermedades preexistentes. | hasta 1.500 € |
| 1280003 | Asistencia médica | hasta 700.000 € |

**Destinos y duraciones (con precio)**

- **España** (codigo 1)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 365 días | 365 | 365 | 39.83 | 39.83 | 24 |

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 365 días | 365 | 365 | 64.61 | 64.61 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 365 días | 365 | 365 | 39.83 | 39.83 | 24 |

---

### Aon Infinity 2025 (codigo 1415)

- **Aseguradora (cia)**: Iris Global Solutions
- **Ramo**: Modular Mixto
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/AsistenciaViaje/gyairi1381.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/Viajes/IRIS/IPID_gyairi1381.pdf`
- **Notas importantes**: <p class="MsoNormal" style="text-align:justify"><span style="font-size: 11px; background: rgb(250, 251, 252);">El seguro de solo Asistencia en viaje puede contratarse hasta el día antes del inicio del viaje. La modalidad con cobertura de Cancelación del viaje si se contrata con posterioridad a la confirmación de la reserva, se aplicará un período de carencia de 72h. </span><span style="font-size: 11px;"> Válido para cruceros. Válido para actividades turísticas deportivas y/o de aventura.</span><o:p></o:p></p>

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 999991001 | Asistencia médica | hasta 5.000.000€ |
| 999991002 | Anulación | hasta 30.000 € |
| 999991003 | Equipajes | hasta 2.500 € |
| tm000100 | PÉRDIDA DE SERVICIOS Y DEMORAS | ***** |
| tm0001001 | Fuerza mayor | Anulacion y prolongacion 7 días 150 € día |
| tm0001002 | Pérdida de visitas y excursiones |  |
| tm0001003 | Gastos de anulación del crucero por retraso del vuelo |  |
| tm0001004 | Reembolso del crucero no disfrutado por retraso o cancelación del vuelo |  |
| tm0001005 | Gastos de anulación de servicios por anulación del crucero |  |
| tm0001006 | Gastos por overbooking en el vuelo. | > Gastos ocasionados transporte alternativo no previsto: Hasta 360€ > Gastos ocasionados por el cambio de alojamiento: Hasta 600€ |
| tm0001007 | Reembolso de servicios contratados por cancelación del vuelo. | 1.000 € |
| tm0001008 | Pérdida de servicios |  |
| tm0001009 | Pérdida de conexiones | 800 € |
| tm0001010 | Gastos de prolongación de estancia en hotel por causa de Fuerza Mayor. | 150€ por día, máximo 7 días |
| tm0001011 | Gastos de secuestro del medio de transporte público |  |
| tm0001012 | Incidencias Aereas |  |
| tm0001013 | Responsabilidad civil privada | 60.000 € |
| tm0001014 | Responsabilidad civil privada responsables de grupo | 60.000 € |
| tm000110 | INDEMNIZACIÓN POR ACCIDENTE | Hasta 60.000 € |
| tm0001101 | En caso de fallecimiento o invalidez permanente por accidente en el viaje 24 horas | 60.000 € |
| tm0001102 | En caso de fallecimiento o invalidez permanente por accidente en medio de transporte público | 60.000 € |
| tm000120 | MASCOTAS | No incluido |
| tm0001201 | Regreso anticipado del asegurado a causa de fallecimiento u hospitalización de animal de compañía |  |
| tm0001202 | Asistencia Veterinaria urgente |  |
| tm0001203 | Repatrición por fallecimiento |  |
| tm0001204 | Estancia mascotas por hospitalizacion del Asegurado |  |
| tm0001205 | Cuidado de animal domestico (perros o gatos) |  |
| tm000130 | ACTIVIDADES DEPORTIVAS | SI |
| tm000140 | CRUCERO | SI |
| tm000150 | RESTRICCIÓN DE CONTRATACIÓN | 72 horas |
| tm000160 | GASTOS DE ANULACION DE VIAJE NO INICIADO. Restriciones | Agravamientos crónicas, mentales Asegurado, 4 acompañantes, imposibilidad total de poder realizar las actividades previstas en el viaje. |
| tm00030 | Individual | SI |
| tm00040 | Grupos | NO |
| tm00050 | Aseguradora | Iris Global |
| tm00060 | ASISTENCIA | hasta 5.000.000€ |
| tm000601 | Gastos médicos en España | 5.000.000 € |
| tm000602 | Gastos médicos en Europa | 5.000.000 € |
| tm000603 | Gastos médicos en Mundo | 5.000.000 € |
| tm000604 | Gastos médicos enfermedades preexistentes | 100.000 € |
| tm000605 | Gastos médicos a bordo del crucero | 5.000.000 € |
| tm000606 | Teleconsulta y videoconsulta |  |
| tm000607 | Transporte o repatriación sanitaria de heridos y enfermos | Incluido |
| tm000608 | Transporte o repatriación de los Asegurados y/o Acompañantes Asegurados | Incluido |
| tm000609 | Transporte o repatriación de fallecidos | Incluido |
| tm000610 | Repatriación o transporte de menores o discapacitados | Incluido |
| tm000611 | Repatriación por quiebra de aerolinea |  |
| tm000612 | Gastos de prolongación del visado | Incluido |
| tm000613 | Gastos de prolongación de estancia en hotel por prescripción médica | 200 € por dia/maximo 20 |
| tm000614 | Gastos de prolongación de estancia en hotel del acompañante por prescripción médica | 200 € por dia/maximo 20 |
| tm000615 | Desplazamiento de acompañante por hospitalización del Asegurado | Incluido (superior 2 noches) |
| tm000616 | Gastos de hotel para un acompañante por hospitalización del Asegurado | 200 € por dia/maximo 20 |
| tm000617 | Regreso anticipado por fallecimiento de un familiar | Incluido |
| tm000618 | Regreso anticipado por hospitalización de un familiar | Incluido >1 noche |
| tm00070 | EQUIPAJES | hasta 2.500 € |
| tm000771 | Equipajes en España | 2.500 € |
| tm000772 | Equipajes en Europa | 2.500 € |
| tm000773 | Equipajes en Mundo | 2.500 € |
| tm000774 | Hurto |  |
| tm000775 | Demora en la entrega del equipaje | 400 € 24 horas |
| tm00080 | GASTOS ANULACION VIAJE NO INICIADO | hasta 30.000 € |
| tm000801 | Gastos de anulación en España | 5.000 € |
| tm000802 | Gastos de anulación en Europa | 15.000 € |
| tm000803 | Gastos de anulación en Mundo | 30.000 € |
| tm000804 | Cambio de condiciones de viaje |  |
| tm00090 | REEMBOLSO VACACIONES NO DISFRUTADAS | hasta 30.000 € |
| tm000901 | Vacaciones no disfrutadas en España | 5.000 € |
| tm000902 | Vacaciones no disfrutadas en Europa | 15.000 € |
| tm000903 | Vacaciones no disfrutadas en Mundo | 30.000 € |

**Destinos y duraciones (con precio)**

- **España** (codigo 1)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 30.6 | 30.6 | 24 |
| 9 días | 9 | 9 | 44.1 | 44.1 | 24 |
| 16 días | 16 | 16 | 60.3 | 60.3 | 24 |
| 22 días | 22 | 22 | 65.25 | 65.25 | 24 |
| 34 días | 34 | 34 | 90 | 90 | 24 |
| 49 días | 49 | 49 | 134.1 | 134.1 | 24 |
| 64 días | 64 | 64 | 171 | 171 | 24 |
| 79 días | 79 | 79 | 175.61 | 175.61 | 24 |
| 94 días | 94 | 94 | 210.89 | 210.89 | 24 |
| 109 días | 109 | 109 | 246.17 | 246.17 | 24 |
| 124 días | 124 | 124 | 281.45 | 281.45 | 24 |
| 139 días | 139 | 139 | 320.26 | 320.26 | 24 |
| 154 días | 154 | 154 | 359.06 | 359.06 | 24 |
| 169 días | 169 | 169 | 397.87 | 397.87 | 24 |
| 184 días | 184 | 184 | 436.68 | 436.68 | 24 |
| 199 días | 199 | 199 | 475.49 | 475.49 | 24 |
| 214 días | 214 | 214 | 514.3 | 514.3 | 24 |
| 229 días | 229 | 229 | 553.1 | 553.1 | 24 |
| 244 días | 244 | 244 | 591.91 | 591.91 | 24 |
| 259 días | 259 | 259 | 630.72 | 630.72 | 24 |
| 274 días | 274 | 274 | 669.53 | 669.53 | 24 |
| 289 días | 289 | 289 | 708.34 | 708.34 | 24 |
| 304 días | 304 | 304 | 747.14 | 747.14 | 24 |
| 319 días | 319 | 319 | 785.95 | 785.95 | 24 |
| 334 días | 334 | 334 | 824.76 | 824.76 | 24 |
| 349 días | 349 | 349 | 863.57 | 863.57 | 24 |
| 366 días | 366 | 366 | 902.38 | 902.38 | 24 |

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 57.6 | 57.6 | 24 |
| 9 días | 9 | 9 | 66.15 | 66.15 | 24 |
| 16 días | 16 | 16 | 99 | 99 | 24 |
| 22 días | 22 | 22 | 123.3 | 123.3 | 24 |
| 34 días | 34 | 34 | 150.3 | 150.3 | 24 |
| 49 días | 49 | 49 | 224.1 | 224.1 | 24 |
| 64 días | 64 | 64 | 291.15 | 291.15 | 24 |
| 79 días | 79 | 79 | 297.86 | 297.86 | 24 |
| 94 días | 94 | 94 | 356.9 | 356.9 | 24 |
| 109 días | 109 | 109 | 415.94 | 415.94 | 24 |
| 124 días | 124 | 124 | 474.98 | 474.98 | 24 |
| 139 días | 139 | 139 | 539.93 | 539.93 | 24 |
| 154 días | 154 | 154 | 604.87 | 604.87 | 24 |
| 169 días | 169 | 169 | 669.82 | 669.82 | 24 |
| 184 días | 184 | 184 | 734.76 | 734.76 | 24 |
| 199 días | 199 | 199 | 799.7 | 799.7 | 24 |
| 214 días | 214 | 214 | 864.65 | 864.65 | 24 |
| 229 días | 229 | 229 | 929.59 | 929.59 | 24 |
| 244 días | 244 | 244 | 994.54 | 994.54 | 24 |
| 259 días | 259 | 259 | 1059.48 | 1059.48 | 24 |
| 274 días | 274 | 274 | 1124.42 | 1124.42 | 24 |
| 289 días | 289 | 289 | 1189.37 | 1189.37 | 24 |
| 304 días | 304 | 304 | 1254.31 | 1254.31 | 24 |
| 319 días | 319 | 319 | 1319.26 | 1319.26 | 24 |
| 334 días | 334 | 334 | 1384.2 | 1384.2 | 24 |
| 349 días | 349 | 349 | 1449.14 | 1449.14 | 24 |
| 366 días | 366 | 366 | 1514.09 | 1514.09 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 30.6 | 30.6 | 24 |
| 9 días | 9 | 9 | 44.1 | 44.1 | 24 |
| 16 días | 16 | 16 | 60.3 | 60.3 | 24 |
| 22 días | 22 | 22 | 65.25 | 65.25 | 24 |
| 34 días | 34 | 34 | 90 | 90 | 24 |
| 49 días | 49 | 49 | 134.1 | 134.1 | 24 |
| 64 días | 64 | 64 | 171 | 171 | 24 |
| 79 días | 79 | 79 | 175.61 | 175.61 | 24 |
| 94 días | 94 | 94 | 210.89 | 210.89 | 24 |
| 109 días | 109 | 109 | 246.17 | 246.17 | 24 |
| 124 días | 124 | 124 | 281.45 | 281.45 | 24 |
| 139 días | 139 | 139 | 320.26 | 320.26 | 24 |
| 154 días | 154 | 154 | 359.06 | 359.06 | 24 |
| 169 días | 169 | 169 | 397.87 | 397.87 | 24 |
| 184 días | 184 | 184 | 436.68 | 436.68 | 24 |
| 199 días | 199 | 199 | 475.49 | 475.49 | 24 |
| 214 días | 214 | 214 | 514.3 | 514.3 | 24 |
| 229 días | 229 | 229 | 553.1 | 553.1 | 24 |
| 244 días | 244 | 244 | 591.91 | 591.91 | 24 |
| 259 días | 259 | 259 | 630.72 | 630.72 | 24 |
| 274 días | 274 | 274 | 669.53 | 669.53 | 24 |
| 289 días | 289 | 289 | 708.34 | 708.34 | 24 |
| 304 días | 304 | 304 | 747.14 | 747.14 | 24 |
| 319 días | 319 | 319 | 785.95 | 785.95 | 24 |
| 334 días | 334 | 334 | 824.76 | 824.76 | 24 |
| 349 días | 349 | 349 | 863.57 | 863.57 | 24 |
| 366 días | 366 | 366 | 902.38 | 902.38 | 24 |

---

### Aon Infinity 2025 New (codigo 1480)

- **Aseguradora (cia)**: Iris Global Solutions
- **Ramo**: Modular Mixto
- **Categoria**: VACACIONAL
- **Condicionado (MODURL)**: `https://www.aon.es/pruservices/documentos/condicionados/services/documentos/condicionados/AsistenciaViaje/gyairi1454.pdf`
- **IPID (MODIPID)**: `https://www.aon.es/pruservices/documentos/ipid/services/documentos/ipid/Viajes/IRIS/IPID_gyairi1454.pdf`
- **Notas importantes**: <p class="MsoNormal" style="text-align:justify"><span style="font-size: 11px; background: rgb(250, 251, 252);">El seguro de solo Asistencia en viaje puede contratarse hasta el día antes del inicio del viaje. La modalidad con cobertura de Cancelación del viaje si se contrata con posterioridad a la confirmación de la reserva, se aplicará un período de carencia de 72h. </span><span style="font-size: 11px;"> Válido para cruceros. Válido para actividades turísticas deportivas y/o de aventura.</span><o:p></o:p></p>

**Coberturas (RESUMEN)**

| ID | Nombre | Valor |
|---|---|---|
| 1454001 | Asistencia médica | hasta 5.000.000€ |
| 1454002 | Anulación | hasta 30.000 € |
| 1454003 | Equipajes | hasta 2.500 € |

**Destinos y duraciones (con precio)**

- **España** (codigo 1)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 30.6 | 30.6 | 24 |
| 9 días | 9 | 9 | 44.1 | 44.1 | 24 |
| 16 días | 16 | 16 | 60.3 | 60.3 | 24 |
| 22 días | 22 | 22 | 65.25 | 65.25 | 24 |
| 34 días | 34 | 34 | 90 | 90 | 24 |
| 49 días | 49 | 49 | 134.1 | 134.1 | 24 |
| 64 días | 64 | 64 | 171 | 171 | 24 |
| 79 días | 79 | 79 | 175.61 | 175.61 | 24 |
| 94 días | 94 | 94 | 210.89 | 210.89 | 24 |
| 109 días | 109 | 109 | 246.17 | 246.17 | 24 |
| 124 días | 124 | 124 | 281.45 | 281.45 | 24 |
| 139 días | 139 | 139 | 320.26 | 320.26 | 24 |
| 154 días | 154 | 154 | 359.06 | 359.06 | 24 |
| 169 días | 169 | 169 | 397.87 | 397.87 | 24 |
| 184 días | 184 | 184 | 436.68 | 436.68 | 24 |
| 199 días | 199 | 199 | 475.49 | 475.49 | 24 |
| 214 días | 214 | 214 | 514.3 | 514.3 | 24 |
| 229 días | 229 | 229 | 553.1 | 553.1 | 24 |
| 244 días | 244 | 244 | 591.91 | 591.91 | 24 |
| 259 días | 259 | 259 | 630.72 | 630.72 | 24 |
| 274 días | 274 | 274 | 669.53 | 669.53 | 24 |
| 289 días | 289 | 289 | 708.34 | 708.34 | 24 |
| 304 días | 304 | 304 | 747.14 | 747.14 | 24 |
| 319 días | 319 | 319 | 785.95 | 785.95 | 24 |
| 334 días | 334 | 334 | 824.76 | 824.76 | 24 |
| 349 días | 349 | 349 | 863.57 | 863.57 | 24 |
| 366 días | 366 | 366 | 902.38 | 902.38 | 24 |

- **Mundo** (codigo 3)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 57.6 | 57.6 | 24 |
| 9 días | 9 | 9 | 66.15 | 66.15 | 24 |
| 16 días | 16 | 16 | 99 | 99 | 24 |
| 22 días | 22 | 22 | 123.3 | 123.3 | 24 |
| 34 días | 34 | 34 | 150.3 | 150.3 | 24 |
| 49 días | 49 | 49 | 224.1 | 224.1 | 24 |
| 64 días | 64 | 64 | 291.15 | 291.15 | 24 |
| 79 días | 79 | 79 | 297.86 | 297.86 | 24 |
| 94 días | 94 | 94 | 356.9 | 356.9 | 24 |
| 109 días | 109 | 109 | 415.94 | 415.94 | 24 |
| 124 días | 124 | 124 | 474.98 | 474.98 | 24 |
| 139 días | 139 | 139 | 539.93 | 539.93 | 24 |
| 154 días | 154 | 154 | 604.87 | 604.87 | 24 |
| 169 días | 169 | 169 | 669.82 | 669.82 | 24 |
| 184 días | 184 | 184 | 734.76 | 734.76 | 24 |
| 199 días | 199 | 199 | 799.7 | 799.7 | 24 |
| 214 días | 214 | 214 | 864.65 | 864.65 | 24 |
| 229 días | 229 | 229 | 929.59 | 929.59 | 24 |
| 244 días | 244 | 244 | 994.54 | 994.54 | 24 |
| 259 días | 259 | 259 | 1059.48 | 1059.48 | 24 |
| 274 días | 274 | 274 | 1124.42 | 1124.42 | 24 |
| 289 días | 289 | 289 | 1189.37 | 1189.37 | 24 |
| 304 días | 304 | 304 | 1254.31 | 1254.31 | 24 |
| 319 días | 319 | 319 | 1319.26 | 1319.26 | 24 |
| 334 días | 334 | 334 | 1384.2 | 1384.2 | 24 |
| 349 días | 349 | 349 | 1449.14 | 1449.14 | 24 |
| 366 días | 366 | 366 | 1514.09 | 1514.09 | 24 |

- **Europa y Ribereños del Mediterráneo** (codigo 24)

| Duracion | Codigo | Dias | Precio | PrecioC | CodigoOrigen |
|---|---:|---:|---:|---:|---:|
| 5 días | 5 | 5 | 30.6 | 30.6 | 24 |
| 9 días | 9 | 9 | 44.1 | 44.1 | 24 |
| 16 días | 16 | 16 | 60.3 | 60.3 | 24 |
| 22 días | 22 | 22 | 65.25 | 65.25 | 24 |
| 34 días | 34 | 34 | 90 | 90 | 24 |
| 49 días | 49 | 49 | 134.1 | 134.1 | 24 |
| 64 días | 64 | 64 | 171 | 171 | 24 |
| 79 días | 79 | 79 | 175.61 | 175.61 | 24 |
| 94 días | 94 | 94 | 210.89 | 210.89 | 24 |
| 109 días | 109 | 109 | 246.17 | 246.17 | 24 |
| 124 días | 124 | 124 | 281.45 | 281.45 | 24 |
| 139 días | 139 | 139 | 320.26 | 320.26 | 24 |
| 154 días | 154 | 154 | 359.06 | 359.06 | 24 |
| 169 días | 169 | 169 | 397.87 | 397.87 | 24 |
| 184 días | 184 | 184 | 436.68 | 436.68 | 24 |
| 199 días | 199 | 199 | 475.49 | 475.49 | 24 |
| 214 días | 214 | 214 | 514.3 | 514.3 | 24 |
| 229 días | 229 | 229 | 553.1 | 553.1 | 24 |
| 244 días | 244 | 244 | 591.91 | 591.91 | 24 |
| 259 días | 259 | 259 | 630.72 | 630.72 | 24 |
| 274 días | 274 | 274 | 669.53 | 669.53 | 24 |
| 289 días | 289 | 289 | 708.34 | 708.34 | 24 |
| 304 días | 304 | 304 | 747.14 | 747.14 | 24 |
| 319 días | 319 | 319 | 785.95 | 785.95 | 24 |
| 334 días | 334 | 334 | 824.76 | 824.76 | 24 |
| 349 días | 349 | 349 | 863.57 | 863.57 | 24 |
| 366 días | 366 | 366 | 902.38 | 902.38 | 24 |

---

