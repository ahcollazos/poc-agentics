# Archivos de contexto (`components/files`)

## Propósito
Este componente empaqueta artefactos de apoyo (documentos, imágenes, guías) que los flows montan en el runtime. Cada archivo se versiona por carpeta funcional y queda disponible mediante rutas `/source_code_files/...` definidas en `control.json`. 【F:components/files/control.json†L1-L184】

## Estructura de carpetas
```
components/files/
  <colección>/
    input/
      <archivo>
    output/
      <artefactos-generados>
  control.json
```
- Cada colección agrupa entradas relacionadas con un flujo o contexto (por ejemplo `1-context-engineering` o `adl-user-story-to-implementation-plan`).
- Las carpetas `input` contienen fuentes que se copiarán al contenedor de ejecución; `output` sirve como destino cuando goals o squads guardan resultados (no se listan en `control.json`).

## `control.json`
El archivo `control.json` es un arreglo de objetos que describe cada recurso disponible.
```json
{
  "name": "angular-manual.md",
  "description": "Angular manual",
  "file": "1-context-engineering/input/angular-manual.md",
  "mime_type": "text/markdown",
  "rag": true,
  "dest_dir": "/source_code_files/1-context-engineering/input"
}
```
- `file` es la ruta relativa dentro de `components/files`. 【F:components/files/control.json†L1-L33】
- `dest_dir` indica la carpeta destino que verá el agente (usada por transformadores como `get_file_content`).
- `rag` activa si un archivo debe indexarse para recuperación aumentada.

## Relaciones con otros componentes
- Los flows leen estos archivos con transformadores; por ejemplo `angular-code-plan` consume PRD y especificaciones definidas aquí. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L5-L88】
- Los goals hacen referencia a rutas de salida que existen en estas colecciones (p.ej. `2-angular-code-plan/output/angular-code-plan.md`). 【F:components/goals/angular-code-plan/v0.0.1/goal.yaml†L42-L66】

## Listas de verificación
### Creación / incorporación de nuevos archivos
- [ ] Copiar los archivos fuente dentro de una carpeta funcional (`input/`).
- [ ] Registrar cada recurso en `control.json` con `dest_dir` coherente.
- [ ] Confirmar que los flows o goals hacen referencia a la ruta montada (`/source_code_files/...`).

### Mantenimiento
- [ ] Actualizar `description` y `rag` cuando cambie el propósito o la indexación requerida.
- [ ] Versionar colecciones creando nuevas carpetas (por ejemplo `v0.0.2/input/`) si la estructura de archivos cambia de forma incompatible.
- [ ] Eliminar entradas de `control.json` sólo después de remover todas las referencias en flows o goals.
