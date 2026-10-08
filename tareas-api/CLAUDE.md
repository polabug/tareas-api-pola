# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Gestor de tareas pendientes (todo list manager) in Java 17. Practice project for Generation México's technical mentorship program (CH70 Java, Oct 2026). In-memory persistence only—no database or REST API.

## Essential Commands

### Testing
```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=TareaServicioTest
mvn test -Dtest=TareaRepositorioTest

# Run specific test method
mvn test -Dtest=TareaServicioTest#completarMarcaLaTarea
mvn test -Dtest=TareaRepositorioTest#buscarPorTituloEncuentraCoincidenciaExacta

# Run tests with verbose output
mvn test -X

# Run tests and generate report
mvn surefire-report:report
```

### Building & Running
```bash
# Compile only
mvn compile

# Clean and compile
mvn clean compile

# Package as JAR
mvn package

# Run demo console app (quiet mode)
mvn -q exec:java

# Run demo with full Maven output
mvn exec:java

# Clean everything (removes target/)
mvn clean
```

### Verification
```bash
# Compile and run tests
mvn verify

# Check for compilation errors only
mvn compiler:compile

# Validate POM structure
mvn validate
```

### Dependency & Plugin Info
```bash
# Show dependency tree
mvn dependency:tree

# List all plugins
mvn help:describe -Dplugin=compiler

# Show effective POM (with all defaults)
mvn help:effective-pom
```

## Architecture

3-layer architecture (top to bottom):

1. **App.java** — Console demo (`main()` entry point)
2. **TareaServicio** — Business logic / use cases (create, complete, list, report)
3. **TareaRepositorio** — In-memory persistence using `LinkedHashMap<Integer, Tarea>`

**Entity model:**
- `Tarea` — Core entity (id, titulo, descripcion, prioridad, fechaLimite, completada)
- `Prioridad` — Enum: BAJA, MEDIA, ALTA
- `TareaNoEncontradaException` — RuntimeException for missing IDs

**Key patterns:**
- IDs are auto-assigned sequentially starting at 1 when saved
- `TareaRepositorio.buscar()` returns `null` for missing IDs
- `TareaServicio.completar()` throws exception for missing IDs (calls private `obtener()`)

## Known Issues (Intentional TODOs)

This is a learning project with documented bugs for students to discover:

1. **TareaServicio:51** — `listarPorPrioridadMinima()` uses `>` instead of `>=`, excluding the minimum priority itself
2. **TareaServicio:67** — `diasRestantes()` has inverted `DAYS.between()` arguments (returns negative when should be positive)
3. **TareaRepositorio:44** — `buscarPorTitulo()` is case-sensitive despite comment claiming otherwise (test disabled at TareaRepositorioTest:33)
4. **Tarea:19** — Missing validation for empty/blank titles (TODO comment)

**Test coverage gaps (documented in tests):**
- TareaServicioTest:39 — Missing test for `completar(999)` throwing exception
- TareaServicioTest:102 — No tests for `eliminar(int)` method

## Testing Notes

- JUnit 5 (Jupiter) used for all tests
- `TareaServicioTest` uses fixed date: `LocalDate.of(2026, 10, 8)`
- One test is `@Disabled`: `TareaRepositorioTest.buscarPorTituloIgnoraMayusculas()` due to known bug
- Pattern: `@BeforeEach` creates fresh `TareaServicio` with new `TareaRepositorio` for isolation

## Maven Configuration Notes

- Java 17 required (uses `LocalDate`, modern features)
- `maven-compiler-plugin` version explicitly set to 3.13.0 to fix Ubuntu/Debian Maven 3.8 compatibility issue (see pom.xml:32-33 comment)
- Single dependency: JUnit 5.10.2 (test scope only)
- Main class: `mx.generation.tareas.App`

## Development Philosophy

This is a pedagogical project designed to teach **Java fundamentals without frameworks**. When working on this codebase:

- Maintain simplicity—no Spring, Hibernate, or external libraries beyond JUnit
- Keep using in-memory persistence (don't add a database)
- Document bugs with TODO comments rather than immediately fixing (unless instructed otherwise)
- Follow existing patterns: verbose code over abstractions (see `generarReporte()` string concatenation)
