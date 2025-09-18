# Squads (`components/squads`)

## Propósito
Los squads describen a los agentes que colaborarán para cumplir un goal: roles, modelos, herramientas, guías y restricciones. Actúan como plantilla operativa activada por un group. 【F:components/squads/generate-angular-code/v0.0.1/squad.yaml†L1-L146】

## Estructura YAML
```yaml
squad:
  id: <uuid>
  name: <slug>
  version: <semver>
  display_name: <opcional>
  description: <opcional>
  model: <llm-config-por-defecto>
  chat_type: <opcional>
  chat_allow_repeat_speakers: <bool>
  term_regexes:
    - ^WELL_DONE_TEAM$
  agents:
    - name: <rol>
      display_name: <visible>
      leader: <bool>
      model: <llm-config>
      guidance: |-
        ...
      tools:
        - save_file
      talk_with:
        - planner
      user_proxy: <bool>
```
Cada agente puede declarar `human_cost_per_hour`, `human_time_multiplyer`, `code_execution`, `term_regexes` y otras restricciones adicionales.

## Campos críticos
- `agents[*].model`: debe coincidir con un `llm_configs.name` definido en `components/manifest.yaml`.
- `leader: true` determina quién puede terminar la sesión; sus instrucciones deben alinearse con el `startpoint` del goal.
- `tools` habilitan acciones clave (por ejemplo `save_file`). Asegúrate de incluir las herramientas necesarias para cumplir los criterios del goal.
- `term_regexes` del squad controlan palabras clave de finalización; deben concordar con las instrucciones del planner (`WELL_DONE_TEAM`, etc.).

## Relaciones con otros componentes
- Los groups referencian squads mediante `squad_ref`. Cada versión nueva debe registrarse allí. 【F:components/groups/generate-angular-code/v0.0.1/group.yaml†L18-L24】
- Las instrucciones y roles deben respaldar el `objective` del goal; por ejemplo, el `planner` guía al equipo para generar código definido en el goal `generate-angular-code`. 【F:components/goals/generate-angular-code/v0.0.1/goal.yaml†L1-L27】【F:components/squads/generate-angular-code/v0.0.1/squad.yaml†L6-L110】
- Los flows dependen de que los squads manejen las variables entregadas a través del group, por lo que cualquier cambio en herramientas o comunicación debe reflejarse en la documentación del flow.

## Listas de verificación
### Creación / nueva versión
- [ ] Crear carpeta `components/squads/<nombre>/<versión>/squad.yaml` con `id` nuevo si hay cambios incompatibles.
- [ ] Definir roles necesarios (planner, especialistas, critic, user_proxy, etc.) y sus permisos.
- [ ] Asignar modelos disponibles en el manifiesto y verificar que soporten las herramientas declaradas.
- [ ] Redactar guías detalladas para cada agente, alineadas con el goal correspondiente.
- [ ] Configurar `term_regexes` y herramientas clave (`save_file`, `read_file`, `submit_question`, etc.).

### Mantenimiento
- [ ] Actualizar `guidance`, `tools` o `model` cuando cambien los procesos o capacidades requeridas.
- [ ] Incrementar versión y sincronizar `squad_ref` en groups si se modifica la composición del equipo.
- [ ] Revisar que el agente líder conserve la autoridad de terminación y que la palabra clave siga alineada con las instrucciones del goal.
