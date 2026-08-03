# API Reference — HotelAdministration

Base URL: `/api/v1`
Autenticación: `Authorization: Bearer <jwt_token>` en todos los endpoints excepto `/auth/**`

## Convenciones generales

- Todos los IDs son `UUID` en formato string.
- Fechas en formato ISO 8601: `2024-01-15T10:30:00Z`
- Respuestas de error siempre incluyen `{ "message": "...", "timestamp": "..." }`
- Paginación con `?page=0&size=20&sort=createdAt,desc`

## Auth

### POST /auth/register
Registra un nuevo usuario o administrador.

**Request**
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "user@gmail.com",
  "password": "password123",
  "role": "USER"
}
```

**Response 201 Created**
```json
{
  "userId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
  "email": "user@gmail.com",
  "role": "USER",
  "verification" : false
}
```

### POST /auth/login
Auténtica un usuario y retorna el JWT.

**Request**
```json
{
  "email": "user@gmail.com",
  "password": "password123"
}
```

**Response 200 OK**
```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "role": "ADMIN",
  "userId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
  "verification" : false | true
}
```

## Rooms

### GET /rooms
Lista y filtra habitaciones según estado, rango de precios o capacidad.

**Parameters**
- `status`: estado de la habitación (e.g. `AVAILABLE`, `OCCUPIED`)
- `priceMin`: precio mínimo
- `priceMax`: precio máximo
- `capacity`: cantidad de personas

**Response 200 OK**
```json
[
  {
    "id": "e5f6g7h8-a1b2-3c4d-5e6f-7g8h9i0j1k2l",
    "roomNumber": "101",
    "type": "DOUBLE",
    "capacity": 2,
    "pricePerNight": 120.00,
    "status": "AVAILABLE"
  }
]
```

## Reservations

### POST /reservations
Crea una nueva reserva de habitación.

**Request**
```json
{
  "roomId": "e5f6g7h8-a1b2-3c4d-5e6f-7g8h9i0j1k2l",
  "checkIn": "2026-08-10T14:00:00Z",
  "checkOut": "2026-08-15T10:00:00Z"
}
```

**Response 201 Created**
```json
{
  "reservationId": "c3d4e5f6-7a8b-9c0d-1e2f-3a4b5c6d7e8f",
  "roomId": "e5f6g7h8-a1b2-3c4d-5e6f-7g8h9i0j1k2l",
  "userId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
  "checkIn": "2026-08-10T14:00:00Z",
  "checkOut": "2026-08-15T10:00:00Z",
  "totalPrice": 600.00,
  "status": "CONFIRMED"
}
```

### DELETE /reservations/{id}
Cancela una reserva activa del usuario actual.

**Response 204 No Content**

### GET /reservations/my-room
Obtiene la habitación actualmente reservada por el usuario autenticado.

**Response 200 OK**
```json
{
  "reservationId": "c3d4e5f6-7a8b-9c0d-1e2f-3a4b5c6d7e8f",
  "roomNumber": "101",
  "checkIn": "2026-08-10T14:00:00Z",
  "checkOut": "2026-08-15T10:00:00Z",
  "status": "CONFIRMED"
}
```

## Admin

### GET /admin/rooms/occupancy
Lista las habitaciones ocupadas detallando quién las está usando.

**Response 200 OK**
```json
[
  {
    "roomId": "e5f6g7h8-a1b2-3c4d-5e6f-7g8h9i0j1k2l",
    "roomNumber": "101",
    "status": "OCCUPIED",
    "currentGuest": {
      "userId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
      "fullName": "John Doe",
      "email": "user@gmail.com"
    },
    "reservationId": "c3d4e5f6-7a8b-9c0d-1e2f-3a4b5c6d7e8f"
  }
]
```

### DELETE /admin/reservations/{id}
Cancela forzadamente una reserva por motivos administrativos (desencadena envío de email).

**Response 204 No Content**

