# CLAUDE.md

Instrucciones para Claude Code al trabajar en este repositorio.

## Stack
- Java 17 (con `LocalDate` y APIs modernas)
- Maven 3.x, JUnit 5.10.2 (única dependencia)
- Persistencia en memoria con `LinkedHashMap` (sin base de datos)

## Comandos esenciales
```bash
mvn compile              # Compilar
mvn test                 # Ejecutar todas las pruebas
mvn -q exec:java         # Ejecutar la demo de consola (modo silencioso)
mvn clean package        # Limpiar y empaquetar
```

## Convenciones del proyecto
1. **Idioma**: Todo el código y las pruebas están en español (clases, métodos, variables, comentarios)
2. **Pruebas**: Una prueba por comportamiento, usando JUnit 5 con nombres descriptivos en español
3. **Dependencias**: No agregar nuevas dependencias sin consultarme primero
4. **Aislamiento**: Cada test crea su propio `TareaServicio` y `TareaRepositorio` en `@BeforeEach`

## Regla de oro
**Cualquier cambio debe dejar `mvn test` en verde.** Si una prueba falla, corrígela antes de continuar.

## Arquitectura en 3 capas
- **App** → Demo de consola (punto de entrada)
- **TareaServicio** → Lógica de negocio (crear, completar, listar, reportes)
- **TareaRepositorio** → Persistencia en memoria (CRUD con ids autogenerados desde 1)

## Filosofía
Proyecto de aprendizaje sin frameworks. Mantén la simplicidad: código verboso sobre abstracciones, sin Spring ni Hibernate.
