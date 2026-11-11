# L2M — Planificación y especificación técnica

**Estado:** diseño inicial.  
**Licencia del proyecto:** MIT.  
**Idiomas:** español e inglés.  
**Modalidad:** online y local/offline.  
**Arquitectura:** núcleo común con módulos especializados.

## 1. Visión

L2M será una plataforma modular para construir, entrenar, evaluar, versionar y ejecutar modelos predictivos. La primera especialidad será la predicción general. En una fase posterior se añadirá un módulo de análisis financiero avanzado de carteras y riesgos, sin operaciones reales.

El objetivo es avanzar de forma incremental: primero un MVP pequeño, medible y reproducible; después ampliar capacidades sin romper contratos ni perder trazabilidad.

## 2. Decisiones aprobadas

| Área | Decisión |
|---|---|
| Nombre | L2M |
| Licencia | MIT |
| Arquitectura | Núcleo común + módulos especializados |
| Primera especialidad | Predicción general |
| Especialidad posterior | Análisis financiero de carteras y riesgos |
| Finanzas | Solo análisis, simulación y backtesting; sin operaciones reales |
| Entrenamiento | Sistema completo de entrenamiento, evaluación y exportación |
| Aprendizaje | Ciclo automatizado de self-training con validación independiente |
| Datos | Fuentes públicas y propias |
| Inferencia | Online y local |
| Offline | La inferencia local funcionará sin Internet una vez instalados modelos y dependencias |
| Tecnologías | Sin restricciones rígidas; Rust cuando aporte ventajas |
| Precisión/velocidad | Perfiles configurables |
| Interfaz | Modo sencillo y avanzado |
| Idiomas | Español e inglés |
| Distribución local | Aplicación de escritorio |
| APIs externas | Opcionales; nunca obligatorias para la inferencia local |
| API pública para terceros | Fuera del alcance inicial |

## 3. Principios de diseño

1. Modularidad: cada componente tiene responsabilidades y contratos claros.
2. Reproducibilidad: cada experimento debe poder reconstruirse con sus datos, configuración y versiones.
3. Evaluación honesta: ningún modelo se promociona por una única métrica aislada.
4. Local-first: el usuario debe poder trabajar localmente sin depender de servicios externos.
5. Portabilidad: formatos y manifiestos documentados para mover modelos entre entornos.
6. Seguridad: datos y artefactos se validan antes de procesarse.
7. Transparencia: métricas, supuestos, limitaciones y procedencia visibles.
8. Evolución gradual: cada fase debe cerrar con criterios verificables.

## 4. Arquitectura lógica

### 4.1 Componentes comunes

- **L2M Core:** tipos, configuración, contratos, errores, capacidades y orquestación.
- **L2M Data Engine:** importación, validación, limpieza, transformación, particionado y trazabilidad.
- **L2M Model Engine:** interfaces y adaptadores para familias de modelos.
- **L2M Learning Engine:** entrenamiento, experimentos, self-training controlado y promoción.
- **L2M Evaluation Engine:** métricas, validación, calibración, comparación y regresiones.
- **L2M Model Registry:** manifiestos, artefactos, versiones, compatibilidad y procedencia.
- **L2M Studio:** UI, historial, estadísticas, configuración y visualización.

### 4.2 Módulos especializados

**L2M General — primera fase funcional**
- Predicción de variables numéricas y categóricas según el tipo de problema.
- Baselines y modelos clásicos antes de añadir complejidad.
- Soporte progresivo para datos tabulares y temporales.

**L2M Finance — fase posterior**
- Análisis de series históricas, activos y carteras.
- Volatilidad, correlaciones, diversificación, beta y drawdown.
- VaR, CVaR, escenarios y backtesting.
- Sin ejecución de órdenes, trading automático ni recomendaciones financieras personalizadas.

## 5. Tecnologías candidatas

La selección final debe verificarse durante las fases correspondientes; esta lista no autoriza a introducir todas las tecnologías de golpe.

| Área | Candidatos |
|---|---|
| Core y procesamiento | Rust |
| Entrenamiento/experimentación | Python |
| Modelos | PyTorch, scikit-learn |
| Intercambio de modelos | ONNX cuando sea compatible |
| Inferencia Rust | rten u otro runtime validado |
| Frontend | TypeScript |
| Escritorio | Tauri |
| Online | Cloudflare Pages y Workers |
| Artefactos online | Cloudflare R2 |
| Persistencia local inicial | SQLite |

La compatibilidad de operadores ONNX, licencias, rendimiento, tamaño de artefactos y límites de Cloudflare debe probarse. No se presupone que un modelo entrenable en Python pueda ejecutarse directamente en Workers.

## 6. Motor predictivo

Implementar progresivamente familias de modelos según el problema:
- Baselines constantes y reglas simples.
- Regresión lineal y logística.
- Árboles de decisión.
- Random Forest.
- Gradient Boosting.
- Modelos probabilísticos y de incertidumbre.
- Redes neuronales MLP cuando aporten valor.
- Modelos específicos de series temporales cuando exista una necesidad demostrada.

La selección de modelo debe considerar tipo de datos, volumen, latencia, memoria, interpretabilidad, precisión y coste de entrenamiento. Una red neuronal no es automáticamente la mejor opción.

## 7. Perfiles de ejecución

- **Rápido:** menor latencia y consumo; puede sacrificar precisión.
- **Equilibrado:** compromiso configurable.
- **Preciso:** permite modelos y configuraciones más costosos.

Los perfiles deben mostrar sus límites y no prometer una precisión concreta. El usuario podrá ajustar parámetros en modo avanzado.

## 8. Aprendizaje y self-training

El self-training debe automatizar el ciclo de mejora sin convertir las predicciones del propio modelo en verdad.

Flujo:
1. Recopilar datos nuevos o ejemplos etiquetados.
2. Validar esquema, calidad, permisos y procedencia.
3. Detectar duplicados, anomalías y cambios de distribución.
4. Preparar y particionar datos evitando leakage.
5. Entrenar un candidato aislado.
6. Evaluarlo con datos independientes y pruebas de regresión.
7. Compararlo con el modelo actual y baselines.
8. Promoverlo solo si supera criterios predefinidos.
9. Guardar métricas, artefactos, versiones y configuración.
10. Permitir rollback y detener el ciclo ante degradación.

Las pseudoetiquetas deben registrar su origen y confianza. No deben tratarse como etiquetas verificadas sin controles adicionales.

## 9. Datos y formatos

Formatos iniciales previstos:
- CSV.
- JSON.
- Parquet cuando se incorpore soporte robusto.

Cada dataset tendrá un manifiesto con:
- Identificador y versión.
- Origen y licencia.
- Fecha de incorporación.
- Esquema y tipos.
- Objetivo/target, si existe.
- Transformaciones aplicadas.
- Particiones utilizadas.
- Restricciones y notas de calidad.

La importación debe validar columnas, tipos, valores ausentes, dimensiones, tamaños y codificación. Los datos no confiables no podrán ejecutar código.

## 10. Evaluación y métricas

Métricas candidatas, elegidas según el problema:
- Regresión: MAE, RMSE, R² y MAPE solo cuando sea matemáticamente apropiado.
- Clasificación: precision, recall, F1, ROC-AUC y log loss.
- Probabilidad: Brier score y calibración.
- Incertidumbre: cobertura de intervalos y calidad de predicción probabilística.

Requisitos:
- Separación train/validation/test.
- Validación temporal para series temporales.
- Prevención explícita de data leakage.
- Comparación con baselines.
- Registro de semillas, configuración, entorno y versiones.
- Evaluación independiente antes de promocionar modelos.

## 11. L2M Finance

Se incorporará después de consolidar el núcleo y el motor de evaluación.

Capacidades previstas:
- Rendimiento histórico y análisis de series.
- Volatilidad y correlaciones.
- Diversificación y exposición.
- Beta y drawdown.
- VaR y CVaR.
- Simulación de escenarios.
- Backtesting con costes y supuestos documentados.

Debe distinguir hechos históricos, estimaciones y escenarios hipotéticos. No ejecutará operaciones reales ni presentará resultados como garantías.

## 12. Aplicación e interfaz

La interfaz tendrá dos niveles:
- **Sencillo:** importar datos, elegir objetivo, entrenar, predecir y consultar métricas.
- **Avanzado:** configuración de modelos, particiones, métricas, hiperparámetros, experimentos y artefactos.

Funciones previstas:
- Historial de ejecuciones.
- Estadísticas de rendimiento.
- Visualización del funcionamiento del modelo.
- Configuración avanzada.
- Importación y exportación de datos.
- Gestión de modelos y versiones.
- Evaluación de precisión.
- Comparación de resultados.
- Entrenamiento con ejemplos.

La API pública para integración externa no forma parte del alcance inicial.

## 13. Ejecución online y local

### Local
- Inferencia offline después de descargar modelos y dependencias.
- Almacenamiento local inicial con SQLite.
- Aplicación de escritorio basada en Tauri como objetivo.
- Entrenamiento local cuando el hardware y el modelo lo permitan.

### Online
- GitHub como repositorio y documentación.
- Cloudflare Pages para frontend.
- Cloudflare Workers para endpoints ligeros y coordinación.
- R2 para artefactos cuando sea necesario y viable.

No se debe asumir que Workers es adecuado para entrenar modelos grandes. El entrenamiento pesado debe ejecutarse en entornos específicos o localmente; el despliegue online se limitará a tareas compatibles con los límites reales de la plataforma.

## 14. Privacidad y seguridad

- No subir datasets privados sin autorización explícita.
- No incluir claves API en código, commits, frontend ni logs.
- Validar archivos, tamaños, esquemas y artefactos.
- Aislar procesos de entrenamiento y limitar recursos.
- Documentar licencias de modelos y datasets por separado de la licencia del código.
- Proporcionar controles de borrado y exportación de datos cuando se implemente almacenamiento persistente.
- No añadir telemetría oculta.

## 15. Licencia

El código del proyecto utilizará MIT. Esta licencia permite uso, modificación y redistribución bajo sus condiciones. La licencia MIT del código no concede automáticamente derechos sobre datasets, pesos de modelos, dependencias ni contenido externo. Cada recurso debe conservar su licencia y atribuciones aplicables.

## 16. Estructura prevista del repositorio

La estructura es una propuesta objetivo; crear directorios solo cuando una fase los necesite.

```text
L2M/
├── README.md
├── LICENSE
├── AGENTS.md
├── PLAN.md
├── ROADMAP.md
├── ARCHITECTURE.md
├── CONTRIBUTING.md
├── SECURITY.md
├── docs/
│   ├── overview.md
│   ├── architecture.md
│   ├── data.md
│   ├── learning.md
│   ├── evaluation.md
│   ├── finance.md
│   ├── deployment.md
│   └── decisions/
├── apps/
│   ├── web/
│   └── desktop/
├── crates/
│   ├── l2m-core/
│   ├── l2m-data/
│   ├── l2m-inference/
│   ├── l2m-learning/
│   ├── l2m-evaluation/
│   ├── l2m-registry/
│   └── l2m-finance/
├── training/
│   ├── common/
│   ├── general/
│   ├── finance/
│   ├── experiments/
│   └── export/
├── models/
│   ├── manifests/
│   └── benchmarks/
├── workers/
│   └── api/
├── datasets/
│   ├── examples/
│   └── manifests/
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── performance/
│   ├── learning/
│   └── finance/
└── scripts/
```

## 17. Roadmap

1. **Fase 0 — Planificación y diseño:** contratos, ADRs, estructura, criterios de aceptación y alcance del MVP.
2. **Fase 1 — L2M Core:** tipos, configuración, errores, interfaces y pruebas.
3. **Fase 2 — Data Engine:** importación CSV, validación, limpieza y particionado.
4. **Fase 3 — Primeros modelos:** baselines y modelos predictivos iniciales.
5. **Fase 4 — Evaluation Engine:** métricas, comparación y validación sin leakage.
6. **Fase 5 — Learning Engine:** experimentos y ciclo inicial de entrenamiento/self-training controlado.
7. **Fase 6 — L2M Finance:** análisis financiero y backtesting analítico.
8. **Fase 7 — L2M Studio:** interfaz sencilla y avanzada.
9. **Fase 8 — Inferencia y optimización:** formatos, rendimiento y consumo.
10. **Fase 9 — Aplicación local:** empaquetado de escritorio y modo offline.
11. **Fase 10 — Plataforma online:** Pages, Workers y almacenamiento de artefactos si procede.
12. **Fase 11 — Estabilización:** seguridad, documentación, rendimiento y expansión.

Las fases pueden desglosarse en tareas. No se debe saltar una fase sin justificar dependencias y actualizar el roadmap.

## 18. MVP

El MVP debe demostrar un flujo predictivo reproducible:
- L2M Core en Rust.
- Importación de CSV.
- Validación básica de datos.
- Predictor de regresión inicial y baseline.
- Métricas MAE y RMSE.
- Comparación de resultados.
- Registro de experimentos.
- Tests y documentación.

Quedan fuera del MVP: self-training completo, módulo financiero completo, aplicación de escritorio final, despliegue online y API pública.

## 19. Criterios generales de aceptación

Una fase se considera terminada cuando:
- Sus objetivos y entregables están implementados.
- Los contratos están documentados.
- Los tests pertinentes pasan.
- Los errores y límites están cubiertos.
- No existen secretos ni datos no autorizados en el repositorio.
- La documentación refleja el comportamiento real.
- Se registran decisiones y limitaciones pendientes.

## 20. Reglas para agentes de IA

Las instrucciones operativas están en [AGENTS.md](AGENTS.md). En resumen:
- Inspeccionar antes de modificar.
- Trabajar dentro de la fase actual.
- No inventar resultados ni compatibilidades.
- Mantener pruebas y documentación.
- No desplegar ni cambiar recursos externos sin autorización explícita.
- No convertir el módulo financiero en un sistema de trading.
- No permitir que el self-training apruebe sus propios resultados sin evaluación independiente.
