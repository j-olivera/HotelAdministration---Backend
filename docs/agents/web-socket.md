# Comunicación en Tiempo Real (WebSockets) | HotelAdministration
 
## Rol y Objetivo

El rol principal de los WebSockets en el backend es **notificar a los clientes en tiempo real** sobre cambios críticos en el estado de las habitaciones, sin que el cliente (frontend) tenga que hacer peticiones constantes al servidor (polling).

Esto brinda una experiencia fluida e instantánea: si un usuario reserva una habitación, el resto de los usuarios (y los administradores) verán cómo esa habitación deja de estar disponible inmediatamente en sus pantallas.

## Casos de Uso (Eventos en Vivo)

1. **Cambio de Estado de Habitación:**
   - De `AVAILABLE` a `RESERVED` (cuando alguien confirma una reserva).
   - De `RESERVED` a `OCCUPIED` (cuando se registra el check-in).
   - De `RESERVED`/`OCCUPIED` a `AVAILABLE` (cuando se cancela una reserva o se hace check-out).

2. **Panel de Administración en Vivo:**
   - Los administradores reciben actualizaciones instantáneas en su dashboard cuando los huéspedes ingresan o cancelan.

## Arquitectura Recomendada

En Spring Boot, se recomienda utilizar **WebSockets con STOMP** (Simple Text Oriented Messaging Protocol).

### Flujo Típico:
1. **Conexión:** El frontend establece una conexión WebSocket al endpoint `/ws`.
2. **Suscripción (Sub):** El cliente se suscribe a un tópico público, por ejemplo `/topic/rooms`.
3. **Publicación (Pub):** Cuando ocurre un evento de negocio en el backend (ej. un usuario llama a `POST /reservations`), el backend guarda la reserva en la base de datos y, si es exitoso, envía un mensaje JSON al tópico `/topic/rooms` con el nuevo estado de la habitación.
4. **Recepción:** Todos los clientes suscritos reciben el JSON y actualizan su interfaz (UI) inmediatamente.

## Ejemplo de Payload de Evento

Cuando una habitación cambia de estado, el servidor emitirá un mensaje similar a este:

```json
{
  "type": "ROOM_STATUS_CHANGED",
  "payload": {
    "roomId": "e5f6g7h8-a1b2-3c4d-5e6f-7g8h9i0j1k2l",
    "roomNumber": "101",
    "newStatus": "RESERVED",
    "timestamp": "2026-08-03T20:30:00Z"
  }
}
```
