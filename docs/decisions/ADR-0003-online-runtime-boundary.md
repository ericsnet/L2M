# ADR-0003: límites de la ejecución online

- **Estado:** Aceptado como restricción de arquitectura
- **Fecha:** 2026-11-12
- **Contexto:** L2M contempla una experiencia online en Cloudflare, pero también exige inferencia local/offline y modelos potencialmente pesados.
- **Decisión:** Cloudflare Pages servirá el frontend; Workers se reservará inicialmente para API ligera, autenticación y coordinación; R2 podrá almacenar artefactos. No se diseñará el entrenamiento pesado para ejecutarse dentro de Workers. La inferencia en Workers será opcional y solo para modelos cuyo tamaño, memoria, latencia y CPU hayan sido medidos y encajen en los límites vigentes.
- **Motivo:** los límites documentados de Workers incluyen memoria de 128 MiB y límites de CPU distintos según plan; por tanto, no es razonable asumir que un Worker sea un runtime general de entrenamiento ML.
- **Consecuencias:** la arquitectura local será la ruta completa de referencia. Las capacidades online pueden ser menores que las locales. Se debe verificar la documentación vigente antes de cada implementación y despliegue.
- **Fuente:** [Cloudflare Workers Limits](https://developers.cloudflare.com/workers/platform/limits/).
- **Revisión:** al comenzar la Fase 10 o cuando se diseñe una inferencia remota concreta.
