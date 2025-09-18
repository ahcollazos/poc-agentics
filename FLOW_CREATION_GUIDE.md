# Guía para construir un flow

Esta guía describe el proceso completo para crear un nuevo *flow* dentro del repositorio, desde la ideación hasta la validación. Un flow es la pieza que orquesta transformadores y grupos de agentes para preparar contexto, activar metas y devolver artefactos finales.

## 1. Comprende el ecosistema

Antes de empezar confirma cómo se relacionan los componentes:

- Un **flow** reúne entradas y define las etapas secuenciales a ejecutar.
- Cada flow delega el objetivo principal en un **group**, el cual enlaza una meta (`goal`) y uno o varios escuadrones (`squads`). 【F:components/groups/figma-to-user-story/v0.0.1/group.yaml†L1-L6】
- La meta describe el objetivo, las instrucciones y las variables que recibirán los agentes. 【F:components/goals/figma-to-user-story/v0.0.1/goal.yaml†L1-L64】
- Los archivos de contexto que necesites deben existir en `components/files` y estar declarados en `control.json` para que los transformers `get_file_content` puedan montarlos en tiempo de ejecución. 【F:components/files/control.json†L1-L184】

## 2. Define el propósito del flow

1. Determina qué entregable quieres producir y qué goal (existente o nueva) lo cubrirá.
2. Lista la información que el squad necesitará (documentos, imágenes, rutas de salida, etc.).
3. Identifica qué transformadores o herramientas deben ejecutarse antes de llamar al grupo (por ejemplo, leer archivos, normalizar textos, analizar imágenes).

Documentar esta intención facilita diseñar la secuencia de etapas y detectar activos faltantes.

## 3. Prepara entradas y variables

- Si necesitas nuevos archivos, agrégalos a `components/files/<carpeta>` y actualiza `control.json` para que estén disponibles en `/source_code_files/...`.
- Define qué valores dinámicos recibirá el flow cuando se ejecute (por ejemplo, rutas de imagen o ubicaciones de salida). Cada valor será una variable del flow con un tipo (`file`, `string`, etc.). En `figma-to-user-story` se declaran tanto la ruta de la imagen Figma como la carpeta donde guardar la historia. 【F:components/flows/figma-to-user-story/v0.0.1/flow.yaml†L4-L8】

## 4. Crea la estructura de carpetas

1. Dentro de `components/flows` crea una carpeta con el nombre del flow (`kebab-case`).
2. Añade un subdirectorio para la versión (`v0.0.1`, `v0.1.0`, etc.).
3. Dentro de esa carpeta crea `flow.yaml`. El repositorio sigue un esquema de versionado por carpeta, por lo que futuros cambios convivirán en carpetas adicionales (`v0.0.2`, `v1.0.0`, ...).

## 5. Redacta el `flow.yaml`

### 5.1 Encabezado obligatorio

Incluye al menos el nombre y la versión. Puedes agregar un `id`, `description` o `display_name` si lo necesitas, como se hace en otros flujos. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L1-L4】【F:components/flows/generate-angular-code/v0.0.1/flow.yaml†L1-L8】

```yaml
flow:
  name: tu-flow
  version: v0.0.1
```

### 5.2 Variables de entrada

Bajo `flow.variables` declara cada input con su nombre y tipo. Estas variables podrán referenciarse dentro de los stages mediante claves `*_ref` (por ejemplo, `image_path_var_name`, `file_path_ref`). 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L90-L94】

### 5.3 Diseña la secuencia de stages

Cada elemento de `flow.stages` describe una acción en orden de ejecución:

- `name`: identificador único de la etapa.
- `target_type`: `transformer` para procesamientos automáticos o `group` para delegar en agentes.
- `target_name` y `target_version`: componente específico que se invoca.
- `input_values`: pares clave-valor que se entregan al target. Puedes combinar valores literales (como rutas absolutas) y referencias a variables o salidas previas.

Ejemplo de lectura de archivos y análisis de imagen antes de delegar al grupo: 【F:components/flows/figma-to-user-story/v0.0.1/flow.yaml†L9-L86】

Si necesitas enriquecer comentarios o documentar una etapa, puedes usar la clave opcional `comment`. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L72-L79】

### 5.4 Etapa final: llamar al grupo

Añade una etapa con `target_type: group` que pase al grupo toda la información preparada. Ese grupo debe existir y apuntar al goal/squad correctos. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L80-L88】【F:components/groups/angular-code-plan/v0.0.1/group.yaml†L1-L6】

### 5.5 Opciones avanzadas

- Usa `iterator` e `iterator_parallelism` si necesitas iterar sobre colecciones producidas por etapas previas, como se observa en el flow `generate-angular-code`. 【F:components/flows/generate-angular-code/v0.0.1/flow.yaml†L9-L16】
- `pre_stages`, `post_stages` y `output_values` están disponibles si requieres preparar o limpiar resultados alrededor de la lista principal de stages. 【F:components/flows/generate-angular-code/v0.0.1/flow.yaml†L6-L16】

## 6. Verifica la alineación con goal y squad

- Confirma que el grupo referenciado por el flow expone el goal correcto y que el goal espera exactamente las variables que estás enviando (nombres y tipos deben coincidir). 【F:components/flows/figma-to-user-story/v0.0.1/flow.yaml†L81-L86】【F:components/goals/figma-to-user-story/v0.0.1/goal.yaml†L4-L59】
- Revisa que el squad asociado cuente con los roles y herramientas necesarios para cumplir el objetivo. Ajusta sus instrucciones si introduces nuevos formatos de entrega.

## 7. Documenta y registra los artefactos generados

Asegúrate de que cualquier archivo producido por el flow tenga una ruta clara dentro del repositorio (por ejemplo, `2-angular-code-plan/output/angular-code-plan.md`). Si agregas nuevos archivos de entrada, mantén `control.json` actualizado para que el entorno de ejecución los monte correctamente. 【F:components/files/control.json†L1-L184】

## 8. Valida el flujo

1. Revisa la sintaxis YAML (puedes usar `yamllint` o tu editor con soporte YAML).
2. Ejecuta las pruebas o comandos que uses normalmente para validar componentes (por ejemplo, herramientas internas de orquestación si están disponibles).
3. Comprueba que las rutas de archivos y referencias a variables existan y coincidan con los nombres esperados por el goal y el squad.

Con estos pasos tendrás un flow alineado con la arquitectura del repositorio, listo para orquestar transformadores y equipos de agentes de forma reproducible.
