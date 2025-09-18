# Flows (`components/flows`)

## Propósito
Los flows orquestan transformadores y grupos para preparar contexto, delegar objetivos y producir entregables. Son el punto de entrada operativo del sistema: definen el orden de ejecución, las variables de entrada y cómo se conectan con goals y squads.

## Estructura YAML
```yaml
flow:
  id: <uuid>
  name: <slug>
  version: <semver>
  description: <opcional>
  variables:
    - name: <variable>
      type: <file|string|...>
  pre_stages: []        # opcional
  stages:
    - name: <etapa>
      comment: <opcional>
      target_type: transformer|group
      target_name: <componente>
      target_version: <semver>
      iterator: <ref>              # opcional
      iterator_parallelism: <n>
      input_values:
        - <clave>: <valor|*_ref>
      output_values: []            # opcional
  post_stages: []       # opcional
```
Ejemplos completos: `components/flows/angular-code-plan/v0.0.1/flow.yaml` y `components/flows/generate-angular-code/v0.0.1/flow.yaml`. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L1-L95】【F:components/flows/generate-angular-code/v0.0.1/flow.yaml†L1-L18】

## Campos críticos
- `name` y `version` determinan la carpeta y las referencias en otras piezas.
- `variables` declara cada entrada dinámica que el runtime debe inyectar (rutas, strings, etc.). Deben alinearse con los marcadores `{{variable}}` utilizados en el goal asociado.
- `stages` define la secuencia: las primeras etapas suelen leer archivos (`get_file_content`) y preparar strings (`escape_angular_template`) antes de invocar al grupo.
- `target_type: group` es obligatorio en la etapa que delega el objetivo principal. El `target_name` debe coincidir con `components/groups/<nombre>/<versión>/group.yaml`.

## Relaciones con otros componentes
- Las variables declaradas se convierten en insumos que el goal espera. Ejemplo: el flow `angular-code-plan` envía `prd_content`, `figma_design_specifications`, etc., que el goal usa dentro de su `objective`. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L80-L88】【F:components/goals/angular-code-plan/v0.0.1/goal.yaml†L14-L63】
- Cada flow invoca un `group` específico (`target_type: group`). Ese group referencia un `goal` y los `squads` que ejecutarán la meta. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L80-L88】【F:components/groups/angular-code-plan/v0.0.1/group.yaml†L1-L6】
- Los flows dependen de los archivos listados en `components/files/control.json`; las etapas de lectura deben apuntar a rutas válidas. 【F:components/files/control.json†L1-L184】

## Listas de verificación
### Creación / nueva versión
- [ ] Crear carpeta `components/flows/<nombre>/<versión>/` siguiendo semántica de versionado.
- [ ] Generar `id` nuevo (UUID) si se publica una versión mayor.
- [ ] Definir variables de entrada y confirmar que los goals asociados referencian exactamente esos nombres.
- [ ] Documentar cada etapa con comentarios si agrega transformaciones no triviales.
- [ ] Incluir etapa final con `target_type: group` apuntando al group vigente.

### Mantenimiento
- [ ] Actualizar rutas absolutas cuando cambien archivos en `components/files` y sincronizar `control.json`.
- [ ] Incrementar versión al introducir cambios incompatibles (nuevas variables, reordenamiento de stages, etc.). Mantén la versión previa para compatibilidad.
- [ ] Revisar que cualquier cambio en `target_version` de transformadores o grupos exista en el repositorio.
