# Features principales

## 1. AutenticaciÃ³n de Usuarios

- Los usuarios deben poder registrarse e iniciar sesion usando un correo electronico y contraseña.
- Solo los usuarios con una sesion activa pueden visualizar la disponibilidad completa y realizar reservas.
- Los usuarios solo pueden gestionar sus propias reservas, no las de terceros.

## 2. exploracion y Seleccion de Canchas

- El sistema debe listar estaticamente las 5 canchas disponibles: Cancha Laureles, Cancha El Poblado, Cancha BelÃ©n, Cancha Robledo, y Cancha Envigado.
- El usuario debe poder seleccionar una fecha especi­fica en un calendario para ver la disponibilidad.
- Para la fecha seleccionada, el sistema debe mostrar la grilla de horarios de 24 horas (en bloques de 1 hora) para la cancha elegida.
- El sistema debe indicar claramente quÃ© bloques horarios estan "Disponibles" y cuales estan "Reservados".
- Un usuario solo puede tener una reserva activa a la vez

## 3. Creacion de Reservas

- El usuario puede seleccionar un bloque horario disponible y confirmar su reserva.
- **Regla Cri­tica (Prevencion de colision):** Antes de confirmar, el sistema debe re-validar que el bloque siga disponible. Si otro usuario tomÃ³ el turno en ese lapso de tiempo, la reserva debe ser rechazada con un mensaje de error claro.
- Las reservas solo pueden hacerse en bloques enteros (ej. 14:00 a 15:00).
- No se pueden realizar reservas en fechas u horarios que ya hayan transcurrido (pasado).

## 4. Gestion Mis Reservas

- El usuario debe tener un panel donde pueda ver la lista de sus reservas futuras y su historial de reservas pasadas.
- Cada Ã­tem de la lista debe mostrar: Nombre de la cancha, Fecha y Hora.
- El usuario puede cancelar una reserva futura seleccionandola en su panel y confirmando la accion.

## Lo que NO se va a construir en esta iteracion (Non-Goals)

- NO habra pasarela de pagos integrados (el pago se manejara presencialmente en el club).
- NO habra panel de administrador web para agregar, editar o eliminar canchas (el listado de 5 canchas es inmutable en el cÃ³digo).
- NO habra notificaciones externas (ni correos transaccionales de confirmacion, ni mensajes SMS/WhatsApp).
- NO habra reservas de mas de 1 hora continua en un solo clic (si alguien quiere 2 horas, debe hacer 2 reservas independientes).
- NO habra sistema de "matchmaking" para buscar compañeros de juego o torneos.
