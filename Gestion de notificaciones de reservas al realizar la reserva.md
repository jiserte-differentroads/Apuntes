# Gestion de notificaciones de reservas al realizar la reserva

## Reserva por transferencia

### Descripción

Siempre que se haga una reserva por transferencia, hay que enviar el email de que tiene que pagar la reserva, indistintamente si ha dado OK (Ha entrado en TK) o ha dado KO (no ha entrado en TK).

### Flujo

1. Se hace la reserva.
2. Se selecciona la transferencia como método de pago y se le da a pagar.
3. Se envía el email de solicitud de transferencia que tiene que pagar la reserva y se muestra en pantalla la misma información que se envía por email.
4. Se espera a que el cliente suba el justificante de pago.
5. Si la reserva esta confirmada, se envía el email de confirmación de tu viaje. Si la reserva esta en verificacion, se espera 24 horas y se vuelve a evaluar si la reserva esta confirmada; si sigue en verificacion, se envía el email de que la reserva esta en verificacion.

## Reserva pago por tarjeta/scalapay

### Descripción

Siempre que se haga una reserva por tarjeta/scalapay, hay que confirmar el pago que sea correcto y enviar el email de confirmación de tu viaje o el email de que la reserva esta en verificacion.

## Textos email / Página final

### Texto de email solicitando transferencia

CONFIRMA TU SOLICITUD MEDIANTE TRANSFERENCIA

Referencia: {reference} | Viaje: {tourName} | Salida: {departureDate} | Vuelo: {flight}

Estimado/a viajero/a,
Hemos registrado tu solicitud de reserva nº {reference} para el viaje {tourName}.
Para continuar con la tramitación, es necesario que realices la transferencia correspondiente antes de la fecha y hora límite indicadas. Este paso nos permitirá avanzar con el proceso de gestión de tu solicitud.
La transferencia deberá efectuarse antes de las {limitDate} h del día {limitDate}, utilizando cualquiera de las cuentas bancarias que se indican a continuación.
Una vez realizada, deberás subir el justificante de pago desde tu perfil de Viajero o haciendo clic en el botón rojo “Subir justificante”, siempre dentro del plazo establecido.
Si no se registra el justificante antes de la hora límite, la solicitud se cancelará automáticamente.
Si tienes cualquier duda o necesitas ayuda durante el proceso, nuestro equipo de Atención al Cliente estará encantado de ayudarte a través del correo info@differentroads.es o en el teléfono 965 02 71 04.
Gracias por confiar en Different Roads.


### Texto de email de confirmación de tu viaje

CONFIRMACIÓN DE TU VIAJE

Reserva: {reference} | Viaje: {tourName} | Salida: {departureDate} | Vuelo: {flight}

Estimado/a viajero/a,
Gracias por confiar en Different Roads.
Te confirmamos que tu reserva para el viaje {tourName} está confirmada.
Si tienes cualquier duda, puedes contactar con nuestro equipo en info@differentroads.es o en el 965 02 71 04.
Gracias por confiar en Different Roads.


### Texto de email de que la reserva esta en verificacion

ESTADO DE TU RESERVA: EN VERIFICACION

Reserva: {reference} | Viaje: {tourName} | Salida: {departureDate} | Vuelo: {flight}

Estimado/a viajero/a,
Hemos recibido correctamente el pago asociado a tu reserva nº {reference} para el viaje {tourName}.
En este momento, tu reserva se encuentra en verificacion.
En este momento, nuestro equipo se encuentra verificando la disponibilidad de todos los servicios incluidos en el viaje. Estamos realizando las comprobaciones necesarias y te informaremos en cuanto tengamos la confirmación completa.
Durante las próximas 24/48 horas, el equipo de Atención al Cliente se pondrá en contacto contigo con información actualizada sobre el estado de tu reserva.
La confirmación definitiva del viaje quedará sujeta a la disponibilidad final de los servicios.
Si una vez completadas las comprobaciones fuese necesario aplicar algún ajuste de precio o suplemento, te lo comunicaremos previamente. En ese caso, podrás cancelar la reserva sin compromiso y recibir el reembolso íntegro, o continuar con la reserva aceptando las condiciones indicadas.
Si tienes cualquier duda o necesitas ayuda, puedes contactar con nuestro equipo de Atención al Cliente escribiendo a info@differentroads.es o llamando al 965 02 71 04.
Gracias por confiar en Different Roads.
