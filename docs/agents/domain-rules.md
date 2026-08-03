# Reglas de Dominio - HotelAdministration

## Auth
- No puede haber dos cuentas con el mismo email.
- El email debe tener un formato valido.
- La contraseña debe tener una longitud no menor o igual a 8 caracteres.
- Solo se permiten caracteres ASCII

## JWT
- Tiempo de expiración: 7 dias.
- Deslogueo = Eliminacion del jwt

## User 
- Debe estar verificado para poder reservar una habitación
- Las cancelaciones deben hacerse hasta 24hs antes de la fecha reservada
- Si desea cancelar durante su periodo de reserva debe al menos haber pasado 1 dia, dejando la habitacion disponible 1 dia despues de la fecha de cancelacion

## Habitaciones
- No puede ser reservada por mas de 1 usuario
- Tiempo maximo de reservacion: 30 dias
- Estados: AVAILABLE (habitación sin pedido de reserva) | RESERVED (habitación reservada sin ocupar) | OCCUPIED (habitacion reservada y ocupada)

## Casos de Uso Principales

### 1. Registrar y Verificar Usuario
- **Flujo:** El usuario se registra, el sistema valida que el email no esté duplicado, que sea válido y que la contraseña cumpla con las reglas (ASCII, >= 8 chars). Se crea el usuario en estado `UNVERIFIED`. Para poder reservar, deberá completarse el flujo de verificación para transicionar el estado a `VERIFIED`.

### 2. Buscar Habitaciones Disponibles
- **Flujo:** Filtra habitaciones disponibles en un rango de fechas. Una habitación se considera disponible si no tiene ninguna reserva confirmada en dicho rango (`AVAILABLE` o sin reservas conflictivas en el calendario).

### 3. Crear Reserva (Booking)
- **Flujo:** Un usuario en estado `VERIFIED` solicita reservar una habitación para un rango de fechas. El sistema valida:
  - Que la habitación no esté reservada por otro usuario en ese rango.
  - Que la duración sea menor o igual a 30 días.
  - Que las fechas sean válidas (check-in antes que check-out, no en el pasado).
- **Resultado:** Se genera la reserva y la habitación cambia a estado `RESERVED` para las fechas seleccionadas.

### 4. Transición de Estado a Ocupada (Check-In)
- **Flujo:** Al llegar la fecha de check-in (o mediante acción del administrador), la habitación transiciona de `RESERVED` a `OCCUPIED`.

### 5. Cancelar Reserva
El comportamiento varía según el momento de la solicitud:
- **Cancelación Preventiva (Antes de ingresar):** Se puede realizar hasta 24 horas antes de la fecha de check-in. Libera la habitación inmediatamente (`AVAILABLE`) y la reserva queda en estado `CANCELLED`.
- **Cancelación Durante la Estadía (Checkout anticipado):** Solo se permite si ya pasó al menos 1 día desde el inicio de la reserva. El sistema actualiza la fecha de check-out al día siguiente de la fecha de cancelación (ej. si cancela el lunes, la habitación se libera el martes). Se recalcula el costo final cobrando los días transcurridos más el día de penalización.

### 6. Visualizar Ocupación de Habitaciones (Rol: ADMIN)
- **Flujo:** El administrador consulta el estado actual o futuro de las habitaciones. El sistema retorna el listado completo detallando si están disponibles (`AVAILABLE`), reservadas sin huésped (`RESERVED`) u ocupadas (`OCCUPIED`). En caso de no estar libres, se provee la identidad del huésped asociado y el ID de la reserva.

### 7. Cancelación Administrativa Forzada (Rol: ADMIN)
- **Flujo:** Por motivos de fuerza mayor o mantenimiento, el administrador cancela una reserva activa o futura ingresando un motivo obligatorio.
- **Resultado:** La habitación se libera inmediatamente, la reserva se marca como cancelada administrativamente y el sistema envía un email de notificación automática al usuario con el motivo detallado.
