# ADR-0004: monorepo y workspace Rust

- **Estado:** Aceptado para la estructura inicial
- **Fecha:** 2026-11-12
- **Contexto:** los módulos comparten contratos y deben evolucionar de forma coordinada; el proyecto combina Rust y Python.
- **Decisión:** mantener un único repositorio. Cuando comience la Fase 1, Rust usará un Cargo virtual workspace en la raíz y los crates se añadirán según existan responsabilidades reales. El código Python vivirá en `training/` con dependencias y pruebas propias.
- **Alternativas:** repositorios separados por módulo; un único crate gigante; crear todos los crates vacíos desde el inicio.
- **Consecuencias:** se simplifican cambios coordinados y CI. El workspace tendrá un único Cargo.lock. No se crean crates vacíos para aparentar modularidad.
- **Fuente:** [Cargo Workspaces — The Cargo Book](https://doc.rust-lang.org/cargo/reference/workspaces.html).
- **Revisión:** al introducir el primer crate y configurar CI.
