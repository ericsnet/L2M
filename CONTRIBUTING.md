# Contribuir a L2M

L2M está en Fase 0. Actualmente el repositorio contiene planificación y documentación; todavía no hay una base de código funcional.

## Antes de contribuir

1. Lee [AGENTS.md](AGENTS.md), [PLAN.md](PLAN.md), [ROADMAP.md](ROADMAP.md) y [ARCHITECTURE.md](ARCHITECTURE.md).
2. Consulta los ADRs en `docs/decisions/`.
3. Comprueba la fase actual y limita el cambio a su alcance.
4. Para cambios de arquitectura, contratos, formatos o dependencias, explica alternativas y registra un ADR.

## Reglas de contribución

- No introduzcas dependencias sin justificar mantenimiento, licencia, tamaño y necesidad.
- Mantén contratos explícitos y versionados.
- Añade tests y documentación con cada cambio de comportamiento.
- No incluyas datasets privados, secretos ni artefactos de origen/licencia desconocidos.
- No cargues modelos o serializaciones no confiables que puedan ejecutar código.
- No hagas despliegues, releases ni operaciones externas sin autorización del responsable del proyecto.
- No presentes benchmarks o métricas sin reproducirlos y documentar el entorno.

## Estilo

- Rust: `cargo fmt`, `cargo clippy` y tests del workspace cuando existan crates.
- Python: formatter, linter y tests se fijarán al iniciar la parte de entrenamiento.
- Documentación: Markdown claro, enlaces a fuentes primarias y estado explícito (propuesta, aceptado, implementado).

## Pull requests

Incluye objetivo, alcance, decisiones, riesgos, archivos afectados, pruebas ejecutadas y limitaciones pendientes. Si no se ejecutó una prueba, indícalo expresamente.
