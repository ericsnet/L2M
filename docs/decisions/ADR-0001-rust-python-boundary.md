# ADR-0001: frontera entre Rust y Python

- **Estado:** Aceptado para el diseño del MVP
- **Fecha:** 2026-11-12
- **Contexto:** L2M necesita un núcleo local/offline robusto y también aprovechar el ecosistema Python para entrenamiento.
- **Decisión:** Rust implementará el Core y la ejecución común. Python se reservará para entrenamiento y experimentación. La primera frontera entre ambos será mediante manifiestos JSON versionados y artefactos en disco, con procesos Python aislados cuando se implemente el Learning Engine.
- **Alternativas consideradas:**
  1. Todo en Python: simplifica ML inicial, pero no encaja tan bien con el objetivo de núcleo Rust y distribución local.
  2. Todo en Rust: reduce lenguajes, pero limita el acceso inicial a ecosistemas de entrenamiento maduros.
  3. PyO3/FFI desde el principio: añade acoplamiento y complejidad de ABI/packaging antes de demostrar necesidad.
- **Consecuencias:** hay que definir contratos serializables y versionados; los procesos de entrenamiento tendrán timeout, límites de recursos y salida estructurada. No se necesita Python para la inferencia del primer baseline nativo.
- **Revisión:** reconsiderar RPC o bindings solo si los benchmarks y el coste de procesos justifican esa complejidad.
