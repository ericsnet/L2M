# ADR-0002: paquete de modelo y uso de ONNX

- **Estado:** Aceptado para el diseño del MVP
- **Fecha:** 2026-11-12
- **Contexto:** el modelo debe poder versionarse, verificarse y ejecutarse localmente sin obligar a todos los algoritmos a usar el mismo runtime.
- **Decisión:** cada modelo será un paquete con manifiesto versionado, artefacto, esquema de entrada/salida, preprocesamiento, procedencia, licencia y checksums. El primer predictor lineal podrá usar un formato declarativo nativo sencillo. ONNX será un formato de intercambio opcional cuando el modelo y sus operadores estén soportados; no será requisito universal ni el único formato interno.
- **Alternativas consideradas:** ONNX obligatorio; serialización nativa Python como formato principal; formatos específicos por modelo sin manifiesto común.
- **Consecuencias:** L2M debe mantener validadores de manifiesto y adaptadores de runtime. No se cargarán archivos pickle/joblib de fuentes no confiables porque su deserialización puede ejecutar código. La compatibilidad ONNX se probará por modelo y runtime.
- **Fuentes:** [PyTorch ONNX export](https://docs.pytorch.org/docs/stable/onnx.html), [ONNX Runtime Execution Providers](https://onnxruntime.ai/docs/execution-providers/).
- **Revisión:** antes de integrar un runtime Rust concreto, realizar prueba de exportación, inferencia, operadores, empaquetado y licencia.
