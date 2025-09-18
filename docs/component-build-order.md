# Orden recomendado para construir componentes

Esta guía resume la secuencia sugerida para preparar todos los artefactos necesarios al crear un nuevo flow dentro del repositorio. Sigue cada fase en orden para garantizar que los componentes se alineen entre sí y aprovechen los activos existentes.

## 1. Configura el `manifest.yaml`

1. Define metadatos del conjunto de componentes (nombre, versión y proveedor) y registra las configuraciones de modelos que estarán disponibles para goals y squads. Puedes usar `components/manifest.yaml` como referencia para la estructura de `metadata` y `llm_configs`. Asegúrate de que cada entrada incluya `name`, `model_client` y la configuración necesaria para la plataforma de IA elegida.
2. Confirma que los nombres declarados en el manifest se correspondan con los usados posteriormente por los squads (por ejemplo, `azure:gpt-4.1-mini` y `azure:gpt-4.1` se reutilizan dentro del squad `angular-code-plan`).

> **Dependencias aguas abajo:** Sin manifest válido los componentes posteriores no podrán resolver los alias de modelo especificados en squads y goals.

## 2. Prepara los archivos de contexto en `components/files`

1. Organiza los documentos, imágenes o plantillas dentro de subcarpetas temáticas y decláralos en `components/files/control.json`. Cada entrada debe indicar el nombre, la ruta relativa dentro de `components/files`, el `mime_type` y la carpeta de destino. El bloque dedicado al flujo Angular muestra cómo mapear múltiples fuentes de contexto. 【F:components/files/control.json†L1-L184】
2. Verifica que las rutas declaradas coincidan con lo que esperas consumir en los flows; por ejemplo, la etapa `read_ui_design_spec` del flow Angular lee `/source_code_files/1-context-engineering/input/ui-design-specs.md`, ruta suministrada por `control.json`. 【F:components/files/control.json†L3-L40】【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L5-L18】

> **Dependencias aguas abajo:** Los transformadores `get_file_content` y las instrucciones del goal asumirán que estos archivos están disponibles en `/source_code_files/...` con los mismos nombres declarados aquí.

## 3. Diseña la meta en `components/goals`

1. Crea una carpeta con el nombre del goal y una subcarpeta por versión (`v0.0.1`, etc.). Dentro, redacta `goal.yaml` definiendo `name`, `version`, `display_name`, instrucciones (`objective`) y variables esperadas. El goal `angular-code-plan` sirve como ejemplo de cómo estructurar instrucciones detalladas y referenciar el contexto preparado en la fase anterior. 【F:components/goals/angular-code-plan/v0.0.1/goal.yaml†L1-L64】
2. Declara claramente las variables que se recibirán (por ejemplo, `prd_content`, `figma_design_specifications`, `user_story`) y asegúrate de que los nombres coincidan con lo que enviará el flow.

> **Dependencias aguas abajo:** El squad que invoque el goal debe conocer estas variables y cualquier archivo de salida esperado.

## 4. Construye el squad en `components/squads`

1. Replica la estructura de carpetas (`nombre/vX.Y.Z/squad.yaml`) y define los agentes, sus modelos y herramientas. El squad `angular-code-plan` muestra cómo un planner delega tareas a especialistas y utiliza los modelos registrados en el manifest. 【F:components/squads/angular-code-plan/v0.0.1/squad.yaml†L1-L154】
2. Asegúrate de que cada agente utilice alias de modelo disponibles en el manifest y de que el squad incluya roles, instrucciones y herramientas compatibles con el objetivo declarado.
3. Incluye instrucciones claras sobre cuándo finalizar el trabajo y qué artefactos deben guardarse.

> **Dependencias aguas abajo:** El group referenciará este squad por nombre y versión; cualquier cambio deberá reflejarse en el group correspondiente.

## 5. Define el group en `components/groups`

1. Crea `group.yaml` enlazando explícitamente el goal y los squads necesarios. Debe declarar `name`, `version`, `goal_ref` y la lista de `squads`. 【F:components/groups/angular-code-plan/v0.0.1/group.yaml†L1-L6】
2. Valida que `goal_ref` coincida con el nombre y versión definidos en el paso 3 y que cada `squad_ref` apunte a squads existentes. Usa el mismo patrón de versionado para mantener trazabilidad.

> **Dependencias aguas abajo:** Los flows invocarán este group; si las referencias no coinciden, la ejecución fallará al intentar delegar en los agentes.

## 6. Orquesta el flow en `components/flows`

1. Diseña la secuencia de etapas dentro de `flow.yaml`, comenzando por reunir el contexto (lectura de archivos, transformaciones, análisis de imágenes) y terminando con la invocación del group creado. El flow `angular-code-plan` ilustra cómo preparar entradas y llamar al group `angular-code-plan@v0.0.1`. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L1-L94】
2. Declara las variables globales necesarias (`flow.variables`) y reutiliza los nombres definidos en el goal. Incluye comentarios (`comment`) cuando quieras documentar acciones específicas.
3. Comprueba que todas las rutas y variables referenciadas existan y provengan de los pasos previos.

> **Dependencias aguas abajo:** Este es el punto de integración final; cualquier inconsistencia en nombres, rutas o versiones se manifestará como errores de ejecución.

## 7. Relación con la guía de creación de flows

La guía `FLOW_CREATION_GUIDE.md` amplía cada fase anterior:

- Las secciones **1** y **2** coinciden con los pasos de comprensión del ecosistema y definición del propósito del flow, útiles tras preparar manifest y archivos de contexto. 【F:FLOW_CREATION_GUIDE.md†L1-L28】
- La sección **3** describe cómo preparar entradas y variables, reforzando la fase 2 de esta guía. 【F:FLOW_CREATION_GUIDE.md†L30-L37】
- Las secciones **4** y **5** detallan la estructura del flow y sus stages, alineándose con el paso 6. 【F:FLOW_CREATION_GUIDE.md†L39-L86】
- La sección **6** enfatiza la alineación entre flow, goal y squad, lo que corresponde directamente a los pasos 3, 4 y 5. 【F:FLOW_CREATION_GUIDE.md†L88-L95】
- Las secciones **7** y **8** proporcionan pautas de documentación y validación para cerrar el proceso descrito aquí. 【F:FLOW_CREATION_GUIDE.md†L97-L115】

Consulta la guía original para obtener ejemplos adicionales de flows como `figma-to-user-story` y `generate-angular-code` cuando necesites profundizar en opciones avanzadas.

## 8. Criterios de validación

Antes de cerrar cambios, verifica lo siguiente:

1. **Consistencia de nombres y versiones**: el `goal_ref` del group coincide con `goal.name@goal.version`, y los `squad_ref` apuntan a squads existentes con la misma versión. El flow debe referenciar el group usando el mismo patrón (`angular-code-plan@v0.0.1`). 【F:components/groups/angular-code-plan/v0.0.1/group.yaml†L1-L6】【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L80-L88】
2. **Disponibilidad de recursos**: cada ruta utilizada en el flow existe en `control.json` y los archivos asociados están en `components/files`. 【F:components/files/control.json†L1-L184】【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L5-L79】
3. **Cobertura de instrucciones**: el goal detalla objetivos, entradas y criterios de salida coherentes con las capacidades del squad. 【F:components/goals/angular-code-plan/v0.0.1/goal.yaml†L1-L64】【F:components/squads/angular-code-plan/v0.0.1/squad.yaml†L1-L154】
4. **Versionado alineado**: todas las carpetas siguen el esquema `vX.Y.Z` y cualquier actualización incremental se refleja en nuevas subcarpetas, evitando sobrescribir versiones anteriores.
5. **Validación sintáctica**: comprueba que todos los YAML sean válidos (usa linters o soporte del editor) y que no existan referencias circulares entre stages o grupos.

Seguir esta lista te permitirá detectar inconsistencias tempranas y facilitar la revisión de nuevos componentes.
