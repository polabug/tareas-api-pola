# tareas-api

Gestor de tareas pendientes en Java 17. Proyecto de práctica de la mentoría
**«Programar con Claude»** (Generation México · CH70 Java · 8-oct-2026).

Sistema simple de gestión de tareas con prioridades, fechas límite y persistencia en memoria. Diseñado para aprender fundamentos de Java sin frameworks complejos.

## 📋 Características

- ✅ Crear tareas con título, descripción, prioridad (BAJA/MEDIA/ALTA) y fecha límite
- ✅ Marcar tareas como completadas
- ✅ Listar tareas pendientes
- ✅ Filtrar por prioridad mínima
- ✅ Buscar tareas por título
- ✅ Calcular días restantes hasta vencimiento
- ✅ Generar reporte diario con estadísticas
- ✅ Eliminar tareas

## 🛠️ Requisitos

- Java 17 o superior (`java -version`)
- Maven 3.8+ (`mvn -v`)

## 🚀 Comandos

```bash
# Compilar el proyecto
mvn compile

# Ejecutar todas las pruebas
mvn test

# Ejecutar una clase de prueba específica
mvn test -Dtest=TareaServicioTest

# Ejecutar un método de prueba específico
mvn test -Dtest=TareaServicioTest#completarMarcaLaTarea

# Ejecutar la demo de consola
mvn -q exec:java

# Limpiar y compilar
mvn clean compile

# Empaquetar como JAR
mvn package
```

## 🏗️ Arquitectura

El proyecto sigue una arquitectura de 3 capas:

```
┌─────────────────────────────────┐
│      App.java (Demo CLI)        │  ← Punto de entrada
└─────────────────────────────────┘
              ↓
┌─────────────────────────────────┐
│   TareaServicio (Casos de uso)  │  ← Lógica de negocio
└─────────────────────────────────┘
              ↓
┌─────────────────────────────────┐
│ TareaRepositorio (Persistencia) │  ← Almacenamiento en memoria
└─────────────────────────────────┘
              ↓
┌─────────────────────────────────┐
│      Tarea (Entidad)            │  ← Modelo de dominio
└─────────────────────────────────┘
```

### Componentes principales

| Clase | Responsabilidad |
|-------|----------------|
| **Tarea** | Entidad de dominio con id, título, descripción, prioridad, fecha límite y estado |
| **Prioridad** | Enum con valores BAJA, MEDIA, ALTA |
| **TareaRepositorio** | Persistencia en memoria usando `LinkedHashMap` con asignación automática de IDs |
| **TareaServicio** | Casos de uso: crear, completar, listar, filtrar, buscar, eliminar y reportes |
| **TareaNoEncontradaException** | Excepción personalizada cuando no existe una tarea |
| **App** | Demo de consola que muestra el uso del sistema |

## 📝 Ejemplo de uso

```java
// Crear servicio
TareaServicio servicio = new TareaServicio(new TareaRepositorio());

// Crear tareas
Tarea t1 = servicio.crear(
    "Entregar informe mensual", 
    "Enviar a finanzas", 
    Prioridad.ALTA, 
    LocalDate.now().plusDays(2)
);

Tarea t2 = servicio.crear(
    "Revisar PR del equipo", 
    "", 
    Prioridad.MEDIA, 
    LocalDate.now().plusDays(1)
);

// Completar tarea
servicio.completar(t2.getId());

// Listar pendientes
List<Tarea> pendientes = servicio.listarPendientes();

// Generar reporte
String reporte = servicio.generarReporte(LocalDate.now());
System.out.println(reporte);
```

**Salida esperada:**
```
=== REPORTE DE TAREAS ===
Total: 2
Completadas: 1
Pendientes: 1
Vencidas: 0
Alta prioridad pendientes: 1
Estado: BIEN (50%)
--- Pendientes ---
* Entregar informe mensual (vence 2026-10-10)
```

## 🧪 Pruebas

El proyecto incluye pruebas unitarias con JUnit 5:

- **TareaRepositorioTest**: 5 tests (4 activos, 1 deshabilitado)
- **TareaServicioTest**: 8 tests

```bash
mvn test
```

### Cobertura de pruebas

✅ **Probado:**
- Guardar y buscar tareas
- Eliminar tareas
- Completar tareas
- Listar tareas pendientes
- Filtrar por prioridad
- Cálculo de días restantes
- Generación de reportes

⚠️ **Pendiente de probar:**
- Excepción al completar tarea inexistente
- Método `eliminar(int)` en TareaServicio

## 🐛 Problemas conocidos

Este es un proyecto pedagógico que contiene bugs documentados intencionalmente para que los estudiantes los descubran:

1. **`TareaServicio:51`** — `listarPorPrioridadMinima()` usa `>` en lugar de `>=`, excluyendo la prioridad mínima solicitada
2. **`TareaServicio:67`** — `diasRestantes()` tiene los argumentos de `DAYS.between()` invertidos (devuelve negativo cuando debería ser positivo)
3. **`TareaRepositorio:44`** — `buscarPorTitulo()` es case-sensitive a pesar de que el comentario indica lo contrario (test deshabilitado con `@Disabled`)
4. **`Tarea:19`** — Falta validación para títulos vacíos o en blanco (comentario TODO)

## 📚 Tecnologías

- **Java 17** — Características modernas (LocalDate, var, etc.)
- **Maven** — Gestión de dependencias y construcción
- **JUnit 5** — Framework de pruebas unitarias

**Sin frameworks complejos:**
- ❌ No usa Spring Boot
- ❌ No usa bases de datos (H2, PostgreSQL)
- ❌ No usa Hibernate/JPA
- ❌ No expone API REST

## 🎓 Propósito pedagógico

Este proyecto está diseñado para enseñar:

- ✅ Fundamentos de Java puro
- ✅ Arquitectura en capas
- ✅ Patrones de diseño básicos (Repository, Service)
- ✅ Pruebas unitarias con JUnit 5
- ✅ Gestión de proyectos con Maven
- ✅ Manejo de fechas con LocalDate
- ✅ Excepciones personalizadas
- ✅ Enumeraciones (enum)

La simplicidad es intencional para enfocarse en los fundamentos antes de introducir frameworks complejos.

## 📄 Licencia

Proyecto educativo de Generation México.
