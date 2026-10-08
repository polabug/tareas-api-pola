# Mapa del Proyecto: tareas-api

## 1. ¿Qué hace el proyecto?

Gestor de tareas pendientes (todo list manager) en Java 17 con persistencia en memoria. Permite crear tareas con prioridad y fecha límite, marcarlas como completadas, listar por prioridad y generar reportes de estado sin base de datos ni API REST.

## 2. Tabla de clases

| Clase | Responsabilidad | Depende de |
|-------|----------------|------------|
| `App` | Punto de entrada con demo de consola que crea tareas de ejemplo y muestra el reporte | TareaServicio, TareaRepositorio |
| `Tarea` | Entidad que representa una tarea con id, título, descripción, prioridad, fecha límite y estado de completada | Prioridad |
| `Prioridad` | Enum que define los niveles de prioridad: BAJA, MEDIA, ALTA | - |
| `TareaNoEncontradaException` | RuntimeException que se lanza cuando se solicita una tarea con un id inexistente | - |
| `TareaRepositorio` | Persistencia en memoria usando LinkedHashMap, asigna ids consecutivos y provee operaciones CRUD | Tarea |
| `TareaServicio` | Lógica de negocio y casos de uso (crear, completar, eliminar, listar, generar reportes) | TareaRepositorio, Tarea, Prioridad, TareaNoEncontradaException |

## 3. Cómo se compila y se prueba

```bash
# Compilar el proyecto
mvn compile

# Ejecutar todas las pruebas
mvn test

# Ejecutar una clase de prueba específica
mvn test -Dtest=TareaServicioTest
mvn test -Dtest=TareaRepositorioTest

# Ejecutar la aplicación de demo (modo silencioso)
mvn -q exec:java

# Limpiar y empaquetar como JAR
mvn clean package

# Verificar (compilar + probar)
mvn verify
```

## 4. Tres cosas sospechosas o incompletas

### 1. TareaServicio.java:51 - Filtro incorrecto de prioridad mínima
```java
if (t.getPrioridad().ordinal() > minima.ordinal()) {
```
Usa `>` en lugar de `>=`, excluyendo la prioridad especificada. Si pides prioridad MEDIA, solo devuelve las ALTA, cuando debería incluir las MEDIA también según el comentario del método.

### 2. TareaServicio.java:67 - Cálculo invertido de días restantes
```java
return ChronoUnit.DAYS.between(tarea.getFechaLimite(), hoy);
```
Los argumentos de `between()` están al revés: calcula desde la fecha límite hasta hoy, devolviendo valores negativos cuando deberían ser positivos y viceversa. Debería ser `between(hoy, tarea.getFechaLimite())`.

### 3. TareaServicio.java:26 - NullPointerException no manejado
```java
public Tarea completar(int id) {
    Tarea tarea = repositorio.buscar(id);
    tarea.marcarCompletada();
    return tarea;
}
```
Si `repositorio.buscar(id)` devuelve `null` (id inexistente), la línea siguiente lanzará NullPointerException en lugar de la TareaNoEncontradaException documentada. El método privado `obtener()` sí hace esta validación correctamente en otros lugares.
