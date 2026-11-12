# L2M — Arquitectura inicial

**Estado:** propuesta técnica de Fase 0; no describe componentes ya implementados.  
**Alcance:** MVP de predicción general. L2M Finance, UI final y plataforma online quedan fuera de la primera entrega funcional.

## 1. Principios

1. **Núcleo pequeño:** el Core define contratos y coordina; no contiene algoritmos de cada dominio.
2. **Módulos explícitos:** datos, entrenamiento, evaluación, inferencia y registro tienen responsabilidades separadas.
3. **Local-first:** la ruta local debe funcionar sin servicios remotos una vez instalados runtime, dependencias y artefactos.
4. **Formatos versionados:** ningún módulo debe depender de estructuras internas no documentadas de otro.
5. **Evaluación independiente:** el componente que entrena un candidato no puede aprobarlo por sí mismo.
6. **Portabilidad gradual:** primero un formato nativo simple y verificable; formatos de intercambio como ONNX se añaden cuando exista un caso probado.
7. **Seguridad por defecto:** datasets y modelos importados son entradas no confiables.

## 2. Vista lógica

```text
                 ┌──────────────────────────┐
                 │       L2M Studio         │
                 │    (futuro: UI)          │
                 └────────────┬─────────────┘
                              │
                 ┌────────────▼─────────────┐
                 │         L2M Core         │
                 │ contratos, config, jobs  │
                 └───┬────────┬────────┬────┘
                     │        │        │
          ┌──────────▼─┐ ┌────▼─────┐ ┌▼────────────┐
          │ Data Engine│ │ Learning │ │ Evaluation  │
          │            │ │ Engine   │ │ Engine      │
          └─────┬──────┘ └────┬─────┘ └─────┬───────┘
                │             │             │
                │        ┌────▼─────┐       │
                └───────►│ Model    │◄──────┘
                         │ Registry │
                         └────┬─────┘
                              │
                      ┌───────▼────────┐
                      │ Inference /    │
                      │ Model Engine   │
                      └────────────────┘
```

El diagrama expresa límites conceptuales, no dependencias de compilación ya existentes.

## 3. Responsabilidades y dependencias

| Componente | Responsabilidad | No debe hacer |
|---|---|---|
| Core | Tipos compartidos, configuración, errores, IDs, capacidades y orquestación | Importar PyTorch, conocer detalles de UI o contener modelos concretos |
| Data Engine | Leer CSV, validar esquema, transformar y describir datasets | Entrenar o promocionar modelos |
| Learning Engine | Crear experimentos, ejecutar entrenamiento y generar candidatos | Aprobar por sí solo sus propios candidatos |
| Evaluation Engine | Ejecutar métricas, validación, comparaciones y gates | Modificar el dataset de test para mejorar resultados |
| Model Engine | Resolver capacidades y ejecutar predictores compatibles | Suponer que todo modelo es ONNX o ejecutable en Workers |
| Registry | Guardar manifiestos, versiones, checksums y artefactos | Ejecutar artefactos al importarlos |
| Studio | Configurar y presentar operaciones y resultados | Contener lógica de negocio duplicada |
| Especialidades | Definir objetivos, métricas y modelos de dominio | Alterar contratos comunes sin versionarlos |

Dependencias permitidas: las especialidades usan contratos del Core y capacidades de los motores; el Core no depende de las especialidades. Evaluation consume resultados y artefactos, pero no depende de la UI. Registry almacena y verifica; no ejecuta.

## 4. Límites entre Rust y Python

- Rust será el lenguaje del Core y de los componentes de ejecución donde aporte seguridad, distribución o rendimiento.
- Python será un entorno de entrenamiento/experimentación aislado, no una dependencia de la inferencia local básica.
- En el MVP, la frontera entre ambos será explícita mediante archivos de manifiesto JSON versionados y artefactos declarativos. No se introduce todavía un RPC ni un binding nativo.
- Los procesos Python se invocarán como procesos controlados por el orquestador cuando llegue esa fase, con timeout, límites de recursos, directorio de trabajo aislado y salida estructurada.
- Los mensajes de error y resultados se serializan con un esquema versionado. Los logs humanos no serán el protocolo.

## 5. Contratos de datos

### 5.1 Dataset

Cada dataset lógico tendrá un manifiesto que identifique:
- `schema_version`, `dataset_id` y `dataset_version`.
- Archivo(s), formato, tamaño y SHA-256.
- Columnas, tipos lógicos, roles y nulabilidad.
- Variable objetivo, si existe.
- Origen, licencia, fecha de captura y transformaciones.
- Política de particionado y semilla, si procede.

Los datos tabulares se representan como filas y columnas con nombres estables. No se inferirá que una columna es target solo por su nombre.

### 5.2 Experimento

Un experimento registra:
- ID, estado y timestamps.
- Dataset y versión exactos.
- Especificación del problema y del modelo.
- Configuración de entrenamiento y semilla.
- Identificador de particiones.
- Versiones de L2M, runtime y dependencias relevantes.
- Métricas, logs resumidos y referencias a artefactos.
- Resultado de evaluación y decisión de promoción.

### 5.3 Modelo

Un paquete de modelo contendrá un manifiesto, pesos/artefactos declarativos, metadatos de preprocesamiento y checksums. El manifiesto declara tareas, entradas, salidas, versión de contrato, runtime requerido, licencia y procedencia.

El cargador validará tamaño, hash, esquema y compatibilidad antes de abrir el artefacto. No se permitirá deserialización que ejecute código arbitrario como parte de la carga normal.

## 6. Evaluación y prevención de leakage

- Los splits se definen antes de ajustar transformaciones aprendidas.
- Imputación, escalado, selección de variables y codificación se ajustan solo con train y luego se aplican a validation/test.
- Datos temporales usan cortes cronológicos; nunca se mezclan observaciones futuras en train para evaluar pasado.
- Test queda reservado para evaluación final y no se usa para seleccionar hiperparámetros.
- Un candidato solo puede promocionarse si supera baselines y umbrales versionados, además de no activar regresiones críticas.
- La evaluación registra incertidumbre y tamaño de muestra cuando sea relevante.

## 7. Almacenamiento y ejecución

### Local
- Archivos de datasets y modelos en directorios administrados por L2M.
- SQLite para metadatos e historial inicial.
- Checksums para detectar corrupción.
- Inferencia local sin red después de instalar runtime y descargar el modelo.

### Online (futuro)
- Pages para frontend estático.
- Workers para endpoints ligeros, autenticación y coordinación.
- R2 para almacenar artefactos cuando se justifique.
- El entrenamiento pesado no se presupone en Workers. Se ejecutará localmente o en un entorno de entrenamiento apropiado.
- Las capacidades online deben ser opcionales para el flujo local.

## 8. Seguridad y privacidad

- No ejecutar código de datasets, modelos o plugins importados.
- No aceptar rutas arbitrarias fuera de los directorios autorizados.
- Aplicar límites de tamaño, filas, columnas, memoria, CPU y duración.
- Mantener claves fuera del frontend, repositorio y logs.
- No transmitir datasets ni métricas a servicios externos sin acción explícita del usuario.
- Documentar licencia y procedencia de código, datasets, pesos y dependencias por separado.

## 9. Estructura inicial prevista

Se adoptará un monorepo, pero se crearán carpetas cuando exista código real:

```text
/
├── Cargo.toml              # workspace Rust virtual
├── Cargo.lock
├── crates/
│   └── l2m-core/
├── training/
│   ├── common/
│   └── general/
├── docs/
│   └── decisions/
├── tests/
└── README.md
```

Los crates de Data, Learning, Evaluation, Registry e Inference se separarán cuando sus contratos y dependencias justifiquen su extracción. No se crearán crates vacíos para aparentar modularidad.

## 10. Fuentes técnicas consultadas

- [Cargo Workspaces — The Cargo Book](https://doc.rust-lang.org/cargo/reference/workspaces.html)
- [PyTorch ONNX export](https://docs.pytorch.org/docs/stable/onnx.html)
- [ONNX Runtime Execution Providers](https://onnxruntime.ai/docs/execution-providers/)
- [Cloudflare Workers Limits](https://developers.cloudflare.com/workers/platform/limits/)
- [Tauri Architecture](https://v2.tauri.app/concept/architecture/)

Estas fuentes respaldan capacidades y límites de las herramientas; no sustituyen pruebas de integración en L2M.
