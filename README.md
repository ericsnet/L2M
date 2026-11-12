# L2M

Plataforma modular de predicción y aprendizaje automático, con ejecución local y online y módulos especializados.

> **Estado:** Fase 0 — planificación y diseño. No existe todavía una versión funcional.

## Objetivo

Construir una plataforma reproducible para importar datos, entrenar modelos, evaluarlos, versionarlos y ejecutar predicciones. La primera especialidad será predicción general. El módulo financiero llegará más adelante y se limitará a análisis y simulación, sin ejecutar operaciones.

## Principios

- Núcleo común en Rust y entrenamiento especializado en Python.
- Arquitectura modular con contratos versionados.
- Ejecución local/offline como capacidad de primera clase.
- Evaluación independiente, trazabilidad y reproducibilidad.
- Sin envío oculto de datos ni servicios externos obligatorios para inferencia local.

## Documentación

- [Instrucciones para agentes](AGENTS.md)
- [Plan y alcance](PLAN.md)
- [Roadmap y estado](ROADMAP.md)
- [Arquitectura](ARCHITECTURE.md)
- [Contratos iniciales](docs/CONTRACTS.md)
- [Decisiones de arquitectura (ADRs)](docs/decisions/)
- [Contribuir](CONTRIBUTING.md)
- [Seguridad](SECURITY.md)

## Licencia

El código se publica bajo licencia MIT. Los datasets, pesos de modelos y dependencias de terceros mantienen sus propias licencias y condiciones.
