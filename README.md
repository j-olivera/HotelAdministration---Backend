# Hotel Administration | Backend

### Descripcion | Objetivo

- App Web que proporciona comodidad para el usuario a la hora de escojer la habitacion del hotel, proporcionandole infomracion acerca de la disponibilidad de las habitaciones, duracion de estadia, proxima habitacion disponible, mejores precios, tipos de habitaciones y demas.
- Además de contar con información e imagenes acerca de las habitaciones disponibles

### Funciones disponibles para USUARIO | Backend
#### Autenticacion
- Registrarse
- Logearse
#### Funciones administrativas
- Ver cuartos disponibles, próximos a liberar
- Ver cuartos en un rango de precio
- Ver cuartos según la cantidad de personas 
- Reservar un cuarto
- Cancelar la reservación de un cuarto
- Ver su cuarto reservado
- (Funciones a agregar)

### Funciones disponibles para ADMIN | Backend
#### Autenticacion
- Registrarse
- Logearse
#### Funciones administrativas
- Ver cuartos libres
- Ver cuartos ocupados y por quien
- Cancelar una reserva por tal motivo (Envía email al usuario)

### Foco del Proyecto: Endpoints y Excepciones Clave
*(Las especificaciones técnicas y contratos detallados se definirán en documentos posteriores)*

#### Autenticación (`/api/v1/auth`)
- `POST /auth/register` - Registro de nuevos usuarios y administradores.
- `POST /auth/login` - Inicio de sesión.

#### Habitaciones (`/api/v1/rooms`)
- `GET /rooms` - Listar y filtrar habitaciones (por precio, capacidad, disponibilidad).
- **Excepción clave:** `RoomNotFoundException` (cuando la habitación no existe).

#### Reservas (`/api/v1/reservations`)
- `POST /reservations` - Reservar una habitación libre.
- `DELETE /reservations/{id}` - Cancelar una reserva activa.
- `GET /reservations/my-room` - Ver detalles del cuarto reservado por el usuario.
- **Excepciones clave:**
  - `RoomAlreadyBookedException` (la habitación ya está reservada en esas fechas).
  - `ReservationNotFoundException` (reserva inexistente).
  - `InvalidReservationDateException` (fechas inconsistentes o en el pasado).

#### Administración (`/api/v1/admin`)
- `GET /admin/rooms/occupancy` - Ver habitaciones ocupadas y detalles del huésped.
- `DELETE /admin/reservations/{id}` - Cancelación forzada de reservas con envío de email.
  - **Excepción clave:** `ReservationCancellationDeniedException` (intentos de cancelación inválidos).