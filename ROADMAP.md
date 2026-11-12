# L2M — Roadmap

**Estado actual:** Fase 0 en curso. El repositorio contiene planificación y especificaciones; no hay componentes funcionales implementados todavía.

| Fase | Nombre | Estado | Criterio de salida |
|---:|---|---|---|
| 0 | Planificación y diseño | En curso | Contratos, ADRs, estructura, MVP, riesgos y criterios de aceptación cerrados |
| 1 | L2M Core | Pendiente | Core compilable, interfaces y tests |
| 2 | Data Engine | Pendiente | Importación y validación reproducibles |
| 3 | Primeros modelos | Pendiente | Baseline y primer modelo comparables |
| 4 | Evaluation Engine | Pendiente | Métricas y validación sin leakage |
| 5 | Learning Engine | Pendiente | Entrenamiento reproducible y promoción controlada |
| 6 | L2M Finance | Pendiente | Análisis y backtesting sin ejecución de órdenes |
| 7 | L2M Studio | Pendiente | UI sencilla y avanzada |
| 8 | Optimización de inferencia | Pendiente | Benchmarks y formatos validados |
| 9 | Aplicación local | Pendiente | App de escritorio con inferencia offline |
| 10 | Plataforma online | Pendiente | Frontend y servicios compatibles con límites del proveedor |
| 11 | Estabilización | Pendiente | Seguridad, documentación y pruebas de calidad |

## Fase 0 — tareas

| Tarea | Estado | Evidencia |
|---|---|---|
| Revisar objetivos, alcance y restricciones | Completada | `PLAN.md`, `AGENTS.md` |
| Definir arquitectura y límites de módulos | Completada | `ARCHITECTURE.md` |
| Definir contratos iniciales de dataset, experimento y modelo | Completada (diseño) | `docs/CONTRACTS.md` |
| Registrar decisiones técnicas y alternativas | Completada (diseño) | `docs/decisions/ADR-0001..0004` |
| Definir política inicial de contribución y seguridad | Completada | `CONTRIBUTING.md`, `SECURITY.md` |
| Cerrar alcance exacto del MVP y sus criterios de aceptación | Pendiente de revisión | `PLAN.md` |
| Verificar dependencias/licencias/versiones antes de Fase 1 | Pendiente | Requiere selección concreta de dependencias |
| Revisar y aprobar el diseño final | Pendiente | Revisión de propietario |

## Criterios de salida de Fase 0

La fase solo se marcará como completada cuando:
1. El alcance del MVP y lo que queda fuera estén inequívocos.
2. Los contratos iniciales tengan campos obligatorios, reglas de validación y versionado.
3. Los límites entre Core, datos, aprendizaje, evaluación, registro e inferencia estén documentados.
4. Las decisiones que condicionan la implementación tengan ADR aceptado.
5. Existan criterios verificables para pruebas y aceptación de Fase 1.
6. Se hayan identificado riesgos técnicos, dependencias externas y elementos que requieren una prueba de concepto.
7. El propietario haya revisado el diseño y no queden decisiones de producto bloqueantes.

## Reglas de seguimiento

- Cambiar el estado solo cuando exista evidencia verificable.
- Estados permitidos: Pendiente, En curso, Bloqueada, Completada.
- Registrar bloqueos y dependencias en la fase correspondiente.
- No marcar una fase como completada si faltan pruebas o documentación.
- Toda modificación de alcance debe actualizar también [PLAN.md](PLAN.md).
- Los agentes deben consultar [AGENTS.md](AGENTS.md) antes de trabajar.

## Registro de progreso

- 2026-11-12: revisión inicial del repositorio y documentación.
- 2026-11-12: arquitectura, contratos iniciales, cuatro ADRs, contribución y política de seguridad añadidos.
- No se ha creado código ni se han ejecutado tests de software; esta fase es de diseño.
