# Goals (`components/goals`)

## Propósito
Los goals encapsulan la misión que deberán cumplir los squads: definen instrucciones, contexto y variables que recibirán los agentes. Funcionan como contrato entre los flows (que preparan los datos) y los grupos/squads (que ejecutan la tarea). 【F:components/goals/angular-code-plan/v0.0.1/goal.yaml†L1-L69】

## Estructura YAML
```yaml
goal:
  id: <uuid>
  name: <slug>
  version: <semver>
  display_name: <nombre legible>
  description: <opcional>
  objective: |-
    ... instrucciones con {{variables}}
  startpoint: |-
    ... sugerencia inicial para el planner
```
- `objective` contiene el prompt principal con marcadores `{{variable}}` que deben coincidir con los nombres enviados desde el flow.
- `startpoint` describe el primer paso sugerido para el planner del squad.

## Campos críticos
- `objective`: debe enumerar claramente contexto, entradas, pasos y criterios de aceptación. Ejemplo: `angular-code-plan` lista PRD, especificaciones y ruta de salida. 【F:components/goals/angular-code-plan/v0.0.1/goal.yaml†L14-L63】
- `version`: se alinea con la carpeta `components/goals/<nombre>/<versión>/` y con referencias `goal_ref` en grupos.
- `name`: usado por flows y groups para enlazar el goal (`goal_ref: <name>@<versión>`).

## Relaciones con otros componentes
- El flow es responsable de poblar cada `{{variable}}` antes de invocar al group; si faltan, el goal quedará con marcadores sin resolver. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L80-L88】
- Los groups enlazan el goal mediante `goal_ref`, asegurando que los squads trabajen con la versión correcta. 【F:components/groups/generate-angular-code/v0.0.1/group.yaml†L9-L24】
- Los squads reciben el `objective` como parte del contexto operativo cuando el group los activa, por lo que cualquier cambio debe comunicarse en sus instrucciones si afecta el flujo de trabajo. 【F:components/squads/generate-angular-code/v0.0.1/squad.yaml†L1-L126】

## Listas de verificación
### Creación / nueva versión
- [ ] Crear carpeta `components/goals/<nombre>/<versión>/` y generar `id` nuevo si es una versión incompatible.
- [ ] Redactar `objective` con secciones claras: contexto, entradas, pasos, criterios de aceptación y rutas de salida.
- [ ] Confirmar que todas las variables declaradas en el texto (`{{variable}}`) tengan equivalentes en el flow correspondiente.
- [ ] Actualizar `startpoint` para guiar al planner acorde a la complejidad del objetivo.

### Mantenimiento
- [ ] Incrementar versión cuando cambien instrucciones o variables de forma incompatible con flows existentes.
- [ ] Revisar que `goal_ref` de los groups apunten a la versión correcta tras cualquier actualización. 【F:components/groups/generate-angular-code/v0.0.1/group.yaml†L9-L24】
- [ ] Alinear instrucciones de squads con nuevos pasos o criterios definidos en el goal.
