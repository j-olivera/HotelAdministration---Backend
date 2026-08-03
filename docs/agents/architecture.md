# Arquitectura del Proyecto | HotelAdministration

El proyecto seguirá los lineamientos de **Clean Architecture** (Arquitectura Limpia) y **Screaming Architecture**. El objetivo es separar las responsabilidades en capas concéntricas y organizar el código por dominio (features), no por tipos técnicos.

## Diagrama de Capas (Regla de Dependencia)

Las dependencias siempre apuntan hacia adentro. Las capas exteriores (Presentación e Infraestructura) dependen de las interiores (Aplicación y Core). El Core **nunca** depende de nada externo (ni siquiera de Spring).

```
┌─────────────────────────────────────┐
│           Presentation              │  Controllers REST, WebSocket
│  ┌──────────────────────────────┐   │
│  │        Infrastructure        │   │  JPA, Security, WebSocket config
│  │  ┌───────────────────────┐   │   │
│  │  │      Application      │   │   │  Use Cases, Ports (interfaces)
│  │  │  ┌────────────────┐   │   │   │
│  │  │  │     Domain     │   │   │   │  Entidades, Enums, Excepciones
│  │  │  └────────────────┘   │   │   │
│  │  └───────────────────────┘   │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘
```
## Definición de Capas

1. **Capa CORE**
   - El corazón inviolable de la aplicación.
   - Contiene Entidades puras de negocio (POJOs), Enums y Excepciones de dominio.
   - **Regla inquebrantable:** CERO anotaciones de Spring Boot, base de datos o librerías externas.

2. **Capa APPLICATION**
   - Orquesta los flujos (Casos de Uso).
   - Contiene Casos de Uso, DTOs de dominio (Requests/Responses) y **Ports** (interfaces que la infraestructura debe implementar).
   - No sabe si la app se consume por HTTP o consola, ni qué base de datos se usa.

3. **Capa INFRASTRUCTURE**
   - Implementa los detalles técnicos.
   - Contiene los **Adapters** (implementaciones de los Ports), Configuración de Spring, Entidades JPA (`@Entity`), Repositorios (`@Repository`), Mappers, Seguridad (JWT) y configuración de WebSockets.

4. **Capa PRESENTATION**
   - El punto de entrada.
   - Contiene los Controladores (`@RestController`), el manejo global de errores (`@ControllerAdvice`) y filtros HTTP.

## Organización por Dominio ("Screaming Architecture")

Dentro de cada capa, no agrupamos todas las clases por su tipo técnico (ej. todos los repositorios juntos), sino por su **contexto de negocio**. Así, al ver la carpeta, el proyecto "grita" que trata sobre reservas de hotel:

```text
src/main/java/com/hotel/backend
├── core
│   ├── auth        (Reglas puras, Excepciones de auth)
│   ├── room        (Room, RoomStatus)
│   └── reservation (Reservation, Excepciones)
├── application
│   ├── auth        (LoginUseCase, RegisterUseCase)
│   ├── room        (SearchRoomsUseCase, RoomPort)
│   └── reservation (BookRoomUseCase, ReservationPort)
├── infrastructure
│   ├── persistence
│   │   ├── room        (RoomEntity, JpaRoomRepository, RoomAdapter)
│   │   └── reservation (ReservationEntity, JpaReservationRepo, Adapter)
│   └── config          (SecurityConfig, WebSocketConfig)
└── presentation
    ├── rest
    │   ├── auth        (AuthController)
    │   └── room        (RoomController, RoomDTOs)
    └── common          (GlobalExceptionHandler)
```
