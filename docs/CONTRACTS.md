# L2M — Contratos iniciales

**Estado:** especificación conceptual de Fase 0. Los ejemplos son ilustrativos y aún no constituyen schemas validados por código.

## 1. Reglas de versionado

- Todo documento intercambiado entre componentes incluye `schema_version`.
- Cambios compatibles pueden añadir campos opcionales.
- Cambios incompatibles requieren nueva versión mayor del contrato y migración explícita.
- Los lectores deben rechazar versiones no soportadas con un error identificable.
- Los campos desconocidos no deben alterar silenciosamente la semántica.

## 2. Dataset manifest

Ejemplo conceptual:

```json
{
  "schema_version": "1.0",
  "dataset_id": "example-regression",
  "dataset_version": "1",
  "format": "csv",
  "file": "data.csv",
  "sha256": "<64-character-hex-digest>",
  "columns": [
    {"name": "feature_a", "logical_type": "float64", "role": "feature", "nullable": false},
    {"name": "target", "logical_type": "float64", "role": "target", "nullable": false}
  ],
  "problem": {"task": "regression", "target": "target"},
  "provenance": {
    "source": "user-provided",
    "license": "user-declared",
    "created_at": "2026-01-01T00:00:00Z"
  }
}
```

Reglas:
- Los nombres de columnas son únicos.
- El target debe existir y tener un tipo compatible con la tarea.
- El hash se calcula sobre los bytes exactos del archivo.
- El origen y la licencia no se inventan; pueden quedar como desconocidos hasta que el usuario los indique.
- El parser debe definir explícitamente codificación, separador, comillas y tratamiento de valores ausentes.
- Los límites de filas, columnas y tamaño deben configurarse y registrarse.

## 3. Experiment specification

Campos mínimos previstos:
- `experiment_id`
- `schema_version`
- `dataset_ref` (ID, versión y hash)
- `task`
- `target`
- `feature_selection`
- `split_strategy`
- `seed`
- `model_spec`
- `training_config`
- `environment`

La especificación es inmutable una vez iniciada una ejecución. Una modificación crea otro experimento.

## 4. Split strategies

Para el MVP:
- Aleatorio reproducible para datos IID, con semilla fija y proporciones explícitas.
- Temporal cronológico para series ordenadas por tiempo.
- No implementar agrupación, estratificación avanzada ni walk-forward hasta definir casos de uso y pruebas específicas.

El usuario debe poder indicar la columna temporal cuando se use split temporal. Los registros con timestamps inválidos se rechazan o se tratan según una política explícita; nunca se reordenan silenciosamente.

## 5. Model manifest

Ejemplo conceptual:

```json
{
  "schema_version": "1.0",
  "model_id": "linear-regression-demo",
  "model_version": "1.0.0",
  "task": "regression",
  "artifact": {
    "path": "model.json",
    "format": "l2m-linear-v1",
    "sha256": "<64-character-hex-digest>"
  },
  "input_schema": [
    {"name": "feature_a", "logical_type": "float64"}
  ],
  "output_schema": [
    {"name": "prediction", "logical_type": "float64"}
  ],
  "preprocessing": [],
  "runtime": {"name": "l2m-native", "version": "1"},
  "provenance": {
    "experiment_id": "experiment-identifier",
    "dataset_ref": "dataset-version-and-hash"
  },
  "license": "MIT"
}
```

El campo `license` del ejemplo es ficticio y no debe copiarse como licencia real de pesos o datos. El manifiesto debe declarar la licencia real del artefacto.

## 6. Prediction request/result

Una predicción debe validar:
- ID y versión de modelo.
- Nombres y tipos de features.
- Ausencia de features requeridas.
- Valores no finitos cuando el tipo no los admita.
- Dimensiones y tamaño de lote.

La respuesta debe identificar modelo y versión, estado, predicciones y advertencias. No debe devolver una cifra como si fuese certeza: la semántica de la salida depende de la tarea y del modelo.

## 7. Error envelope

Formato común conceptual:
- `code`: identificador estable legible por máquina.
- `message`: explicación segura y útil.
- `component`: origen del error.
- `retryable`: booleano.
- `details`: opcional, sin secretos ni datos sensibles.
- `correlation_id`: identificador para correlacionar logs.

No exponer stack traces, rutas privadas, claves ni contenido completo del dataset en mensajes de error de usuario.

## 8. Compatibilidad

La compatibilidad se declara explícitamente:
- Versión del contrato.
- Versión del productor.
- Versiones mínimas/máximas del runtime.
- Operadores y tipos soportados.
- Plataforma/arquitectura si el artefacto lo requiere.

No se debe inferir compatibilidad únicamente por extensión de archivo.
