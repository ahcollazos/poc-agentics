# Groups (`components/groups`)

## Propósito
Un group es el puente entre un flow y los squads que ejecutan el goal. Define cómo se coordina la conversación (tipo de chat, planner, límites), referencia la meta (`goal_ref`) y lista los escuadrones que participarán (`squads`). 【F:components/groups/generate-angular-code/v0.0.1/group.yaml†L1-L27】

## Estructura YAML
```yaml
group:
  id: <uuid>
  name: <slug>
  version: <semver>
  display_name: <opcional>
  description: <opcional>
  chat_type: round-robin|broadcast|...
  chat_allow_repeat_speakers: <bool>
  planner_role: <mensaje>
  planner_guidance: |-
    ...
  goal_ref: <goal>@<versión>
  squads:
    - squad_ref: <squad>@<versión>
  max_rounds: <n>
  term_regexes:
    - ^\s*TERMINATE\s*$
```
La definición puede incluir configuraciones adicionales como `capabilities`, `input_keys`, `enable_memory` o `term_regex_flags` dependiendo de las necesidades del orquestador.

## Campos críticos
- `goal_ref`: enlace directo al goal que describe la misión. Debe coincidir con una carpeta existente en `components/goals/<goal>/<versión>/`.
- `squads`: lista de referencias `squad_ref` que participarán; cada entrada usa el formato `<nombre>@<versión>`.
- `planner_guidance` y `planner_role`: instrucciones con las que inicia el agente planner; deben estar alineadas con el `startpoint` del goal.
- `max_rounds` y `term_regexes`: controlan la finalización de la sesión.

## Relaciones con otros componentes
- Los flows llaman al group mediante `target_type: group`, por lo que `name` y `version` deben coincidir con lo declarado en la etapa correspondiente. 【F:components/flows/generate-angular-code/v0.0.1/flow.yaml†L9-L16】
- El group consume el goal indicado en `goal_ref`; cualquier cambio de versión requiere actualizar flows y squads asociados. 【F:components/goals/generate-angular-code/v0.0.1/goal.yaml†L1-L27】
- Cada `squad_ref` debe existir y contener agentes configurados para cumplir el objetivo. 【F:components/squads/generate-angular-code/v0.0.1/squad.yaml†L1-L146】

## Listas de verificación
### Creación / nueva versión
- [ ] Crear carpeta `components/groups/<nombre>/<versión>/group.yaml` y generar un `id` único.
- [ ] Establecer `goal_ref` apuntando a la versión del goal que utilizará el flow.
- [ ] Enumerar todos los `squad_ref` necesarios y verificar que existan.
- [ ] Ajustar `planner_guidance` para reflejar el `objective` y `startpoint` del goal.
- [ ] Definir reglas de terminación (`term_regexes`) acordes al comportamiento esperado.

### Mantenimiento
- [ ] Sincronizar `goal_ref` y `squad_ref` cuando se publiquen nuevas versiones de goals o squads.
- [ ] Actualizar `max_rounds`, roles o instrucciones si cambian los tiempos de ejecución o protocolos de colaboración.
- [ ] Versionar el group cuando los cambios impacten compatibilidad (por ejemplo, añadir/quitar squads o modificar drásticamente la guía del planner).
