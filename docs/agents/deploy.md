 # Despliegue e Infraestructura (Docker) | HotelAdministration

## Estrategia de Contenedores

La aplicación y sus dependencias se orquestarán utilizando **Docker** y **Docker Compose**. Esto garantiza un entorno idéntico entre desarrollo, pruebas y producción.

## Componentes

### 1. Aplicación Backend (Spring Boot)
- Se creará un `Dockerfile` multi-etapa (multi-stage) para compilar el proyecto usando Maven y luego empaquetar el `.jar` en una imagen ligera de Java (ej. `eclipse-temurin:17-jre-alpine`).
- Se expondrá el puerto `8080`.

### 2. Base de Datos (PostgreSQL)
- Se utilizará la imagen oficial de `postgres`.
- Los datos se persistirán utilizando un volumen de Docker (`pgdata`).
- Las credenciales y configuración se inyectarán vía variables de entorno.

## Ejemplo de Arquitectura (docker-compose.yml)

```yaml
version: '3.8'

services:
  backend:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/hoteldb
      - SPRING_DATASOURCE_USERNAME=hoteladmin
      - SPRING_DATASOURCE_PASSWORD=secret
    depends_on:
      - db
    networks:
      - hotel-network

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=hoteldb
      - POSTGRES_USER=hoteladmin
      - POSTGRES_PASSWORD=secret
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - hotel-network

networks:
  hotel-network:
    driver: bridge

volumes:
  pgdata:
```

## Variables de Entorno y Seguridad
- En entornos productivos, nunca se deben codificar contraseñas en el `docker-compose.yml`. Se deben utilizar archivos `.env` (ignorados en git) o un gestor de secretos.
