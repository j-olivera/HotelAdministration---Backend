# Convetions
- Reglas y convenciones a respetar OBLIGATORIAMENTE a la hora de trabajar en el proyecto.

### Escritura de código
- No emojis
- Código escrito en Inglés 
- Comentarios escritos en ESPAÑOL

### Escritura de archivos y clases
- camelCase para atributos, variables, funciones, etc.
- PascalCase para archivos

### Clases 
- Constructor y atributos privados
- Factory Method para la generacion de instancias

### Excepciones (Ejemplo)
```java
public class InvalidStatusTransitionException extends RuntimeException {

    public InvalidStatusTransitionException(UUID orderId, OrderStatus from, OrderStatus to) {
        super(String.format(
            "Transición inválida para pedido %s: %s → %s", orderId, from, to
        ));
}
```
### Interfaces
- Port Entrada
```java
public interface EjemploUseCase{
    void execute(Atributos...);
}
```
- Port Salida
```java
public interface EjemploRepository{
    Typo action(Atributos...);
}
```