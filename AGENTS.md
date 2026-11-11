# AGENTS.md — Instrucciones para agentes de IA

Este archivo es la guía operativa principal para cualquier agente de IA que trabaje en L2M. Léelo completo antes de inspeccionar, planificar o modificar el proyecto. El documento de diseño maestro es [PLAN.md](PLAN.md) y el estado/orden de fases está en [ROADMAP.md](ROADMAP.md).

## 1. Misión del proyecto

L2M es una plataforma modular de predicción y aprendizaje automático. Debe ofrecer:
- Un núcleo común reutilizable.
- Modelos especializados por dominio, no un único modelo universal.
- Entrenamiento, evaluación, versionado e inferencia reproducibles.
- Ejecución online y una aplicación local capaz de funcionar offline después de instalar dependencias y descargar modelos.
- Interfaz en español e inglés, con modo sencillo y avanzado.

La primera especialidad es **predicción general**. La especialidad financiera llegará después y se limitará a análisis, simulación y evaluación de riesgos; nunca ejecutará operaciones reales.

## 2. Fuentes de verdad y prioridades

En caso de conflicto, aplica este orden:
1. Instrucciones explícitas recientes del propietario del proyecto.
2. Decisiones aprobadas en `PLAN.md` y ADRs aceptados.
3. Este archivo.
4. `ROADMAP.md`.
5. Código y comportamiento actual (que pueden contener errores o deuda técnica).

No conviertas una propuesta técnica en una decisión aprobada sin documentarlo. Si falta una decisión importante, explica las alternativas y pregunta antes de realizar cambios irreversibles.

## 3. Reglas de trabajo obligatorias

1. Inspecciona primero el repositorio, sus instrucciones, estructura, dependencias, tests y estado de Git.
2. Identifica la fase actual en `ROADMAP.md`. No implementes fases futuras por iniciativa propia.
3. Antes de editar, presenta un plan breve cuando el cambio afecte a varios archivos, arquitectura, datos, modelos o despliegue.
4. Haz cambios pequeños, coherentes y verificables. Evita reescrituras completas si una corrección localizada basta.
5. No inventes resultados de tests, benchmarks, compatibilidad, métricas ni despliegues. Ejecuta las comprobaciones y comunica exactamente qué se ejecutó y qué no.
6. Actualiza documentación y tests junto con el código cuando cambie un contrato o comportamiento.
7. Mantén separadas las responsabilidades de core, datos, entrenamiento, evaluación, inferencia, registro y UI.
8. No añadas dependencias sin justificar su necesidad, mantenimiento, licencia, tamaño y compatibilidad con los objetivos local/offline.
9. No introduzcas servicios externos obligatorios para que funcione la inferencia local.
10. Mantén español e inglés en la interfaz y documentación de usuario cuando corresponda.
11. No añadas telemetría, analítica, envío de datasets ni llamadas externas ocultas.
12. No publiques secretos, tokens, claves API, datos privados, datasets con licencia dudosa ni información personal.

## 4. Límites de autorización

- No despliegues a Cloudflare, GitHub Pages ni otro proveedor sin autorización explícita para ese despliegue.
- No crees, borres, hagas públicos/privados ni renombres repositorios o recursos sin autorización explícita.
- No añadas secretos, cambies DNS, dominios, facturación, permisos, reglas de acceso o configuración de cuenta sin autorización explícita.
- No realices operaciones financieras reales. El módulo financiero es exclusivamente analítico y educativo.
- Las APIs externas pueden ser opcionales, pero nunca requisito para ejecutar los modelos locales.
- No hagas commits, pushes, releases ni merges si el usuario no ha autorizado la operación correspondiente.

## 5. Arquitectura objetivo

La arquitectura aprobada es **núcleo común + módulos/modelos especializados**:

- **L2M Core:** contratos, configuración, tipos comunes, errores y orquestación.
- **L2M Data Engine:** importación, validación, limpieza, transformación, particionado y trazabilidad de datasets.
- **L2M Model Engine:** catálogo y adaptadores de familias de modelos.
- **L2M Learning Engine:** preparación, entrenamiento, self-training controlado, experimentos y promoción de modelos.
- **L2M Evaluation Engine:** métricas, validación, comparación, calibración y detección de degradación.
- **L2M Model Registry:** artefactos, manifiestos, versiones, procedencia y compatibilidad.
- **L2M Studio:** interfaz sencilla/avanzada, historial, estadísticas y visualizaciones.
- **L2M General:** primera especialidad de predicción general.
- **L2M Finance:** especialidad posterior para carteras y riesgos, sin trading ni ejecución de órdenes.

No acoples el núcleo a una familia de modelos concreta. Los módulos deben integrarse mediante contratos explícitos y versionados.

## 6. Stack tecnológico: orientación, no dogma

- Rust para componentes de core, procesamiento e inferencia cuando aporte ventajas reales.
- Python para experimentación y entrenamiento cuando el ecosistema lo justifique.
- TypeScript para frontend.
- PyTorch y scikit-learn como candidatos para entrenamiento/modelos.
- ONNX como formato de intercambio cuando el modelo y las operaciones sean compatibles.
- rten u otro runtime Rust como candidato de inferencia; validar madurez y cobertura antes de adoptarlo.
- Tauri como candidato para escritorio.
- Cloudflare Pages/Workers/R2 para la parte online, respetando límites reales de CPU, memoria, tamaño y duración.
- SQLite para almacenamiento local inicial.

No afirmes que una combinación es viable hasta verificar versiones, licencias, formatos, límites del proveedor y pruebas de integración. Mantén las decisiones importantes en ADRs.

## 7. Predicción y evaluación

- Implementa primero baselines sencillos y reproducibles.
- Elige el modelo según tipo de datos, objetivo, coste, latencia e interpretabilidad; no presupongas que una red neuronal es siempre superior.
- Usa particiones train/validation/test sin contaminación entre conjuntos.
- Para datos temporales, utiliza validación temporal o walk-forward; nunca mezcles futuro en el entrenamiento de una evaluación pasada.
- Evita data leakage en preprocesamiento, selección de variables, normalización y ajuste de hiperparámetros.
- Selecciona métricas apropiadas al problema. Posibles métricas: MAE, RMSE, MAPE solo cuando tenga sentido, R², precision, recall, F1, ROC-AUC, log loss, Brier y calibración.
- Registra dataset/versiones, configuración, semillas, entorno, métricas y artefactos para reproducir experimentos.
- Compara contra baselines y modelos anteriores. Una mejora en una métrica no debe ocultar regresiones relevantes en otras.

## 8. Self-training y aprendizaje automático

“100 % self-training” significa automatizar el ciclo de aprendizaje, no aceptar automáticamente como verdad las predicciones del propio modelo.

Flujo mínimo:
1. Recibir datos nuevos o ejemplos etiquetados.
2. Validar esquema, calidad, procedencia, permisos y distribución.
3. Detectar duplicados, anomalías y posibles cambios de distribución.
4. Preparar datos sin contaminar evaluación.
5. Entrenar candidato en un experimento aislado.
6. Evaluar con un conjunto independiente y pruebas de regresión.
7. Comparar con el modelo vigente y baselines.
8. Promover solo si supera criterios explícitos.
9. Mantener historial, trazabilidad y rollback.
10. Monitorizar degradación y permitir detener el proceso.

Las pseudoetiquetas deben llevar confianza/procedencia y controles de calidad. No uses predicciones propias como etiquetas verificadas sin validación independiente. Nunca permitas que el modelo modifique sus propios criterios de aceptación para aprobarse.

## 9. Seguridad, privacidad y calidad de datos

- Trata datasets, modelos y archivos importados como entradas no confiables.
- Valida tipos, tamaños, dimensiones, codificaciones y límites de recursos.
- Evita ejecución de código arbitrario contenido en datasets o artefactos.
- Aísla procesos de entrenamiento y limita consumo de CPU, RAM, GPU y disco.
- Comprueba licencias y condiciones de uso de cada dataset y modelo.
- Documenta origen, fecha, transformaciones, restricciones y retención de datos.
- No registres datos sensibles en logs.
- Protege claves y credenciales mediante mecanismos de secretos; nunca las incluyas en frontend ni en el repositorio.

## 10. L2M Finance — límites obligatorios

El módulo futuro podrá analizar series históricas, volatilidad, correlaciones, diversificación, drawdown, beta, VaR, CVaR, escenarios y backtesting. Debe:
- Diferenciar datos históricos, estimaciones y escenarios simulados.
- Mostrar supuestos, ventanas temporales, incertidumbre y limitaciones.
- Evitar presentar resultados como garantías o predicciones seguras.
- No conectarse para ejecutar compras/ventas ni enviar órdenes.
- No ofrecer recomendaciones financieras personalizadas.

## 11. Tests y criterios de aceptación

Para cada cambio:
- Añade tests unitarios para lógica nueva o modificada.
- Añade tests de integración para contratos entre módulos.
- Incluye casos límite, entradas inválidas y errores esperados.
- Comprueba reproducibilidad cuando sea relevante.
- Ejecuta formatter, linter, type checks y tests disponibles.
- Para optimizaciones, aporta benchmark comparable antes/después y el mismo entorno.
- No reduzcas cobertura ni elimines tests para hacer pasar CI sin justificación aprobada.

Un cambio no está terminado solo porque compile: debe cumplir su contrato, tener pruebas y documentación adecuada.

## 12. Flujo recomendado para cada tarea

1. Leer `AGENTS.md`, `PLAN.md`, `ROADMAP.md` y los ADRs relevantes.
2. Inspeccionar código y estado actual; no asumir que la documentación describe perfectamente la implementación.
3. Resumir objetivo, alcance, riesgos y archivos afectados.
4. Implementar el cambio mínimo correcto.
5. Ejecutar pruebas y comprobaciones pertinentes.
6. Revisar diff buscando regresiones, secretos, dependencias innecesarias y cambios fuera de alcance.
7. Actualizar documentación y roadmap solo si el estado realmente cambió.
8. Entregar resumen, archivos modificados, comandos ejecutados, resultados y limitaciones pendientes.

## 13. Primera tarea del proyecto

El proyecto comienza en la **Fase 0 — planificación y diseño**. No inventes que existe código funcional. Primero revisa la documentación, confirma la estructura prevista y prepara las decisiones de arquitectura necesarias. Después, y solo cuando la Fase 0 esté cerrada, comienza la Fase 1: L2M Core.

---

Si una instrucción de este archivo contradice una limitación técnica real, no la ignores silenciosamente: explica el conflicto, aporta evidencia y propone una decisión documentada.
