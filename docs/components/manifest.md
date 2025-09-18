# Manifest de componentes

## Propósito
El manifiesto centraliza los metadatos del paquete de componentes (nombre, autoría, versión) y declara las configuraciones de modelos disponibles para squads y transformadores. Sirve como punto único para sincronizar identificadores de modelos y credenciales utilizadas en `groups`, `squads` o transformadores que hagan referencia a `llm_config`. 【F:components/manifest.yaml†L1-L63】

## Estructura YAML
```yaml
components:
  manifest_version: "0.1"
  metadata:
    name: <slug>
    version: <semver>
    author: <equipo>
    description: <resumen>
    llm_configs:
      - name: <proveedor:modelo>
        model_client: <clase>
        configuration:
          <clave>: <valor>
```

- La raíz `components` agrupa toda la definición. 【F:components/manifest.yaml†L1-L3】
- `metadata` describe el paquete y se alinea con la convención de versionado por carpeta usada en `components/<tipo>/<nombre>/<versión>/`. 【F:components/manifest.yaml†L4-L8】
- `llm_configs` lista cada modelo utilizable por squads; el campo `name` se usa como referencia en `llm_config` dentro de stages o agentes. 【F:components/manifest.yaml†L9-L63】

## Campos críticos
- `manifest_version`: controla compatibilidad con el runtime de orquestación. Actualiza sólo cuando cambien las reglas del esquema.
- `metadata.version`: debe reflejar la versión global del paquete y mantenerse alineado con carpetas de versión de goals, flows, groups y squads.
- `llm_configs[*].name`: referencia utilizada en `components/flows/angular-code-plan/v0.0.1/flow.yaml` al invocar transformadores (`llm_config: azure:gpt-4o`). Cualquier cambio requiere actualizar todas las referencias. 【F:components/flows/angular-code-plan/v0.0.1/flow.yaml†L34-L47】

## Relaciones con otros componentes
- Los `llm_configs` proveen las etiquetas que squads usan en los agentes (`model: azure:gpt-4.1-mini` en `components/squads/generate-angular-code/v0.0.1/squad.yaml`). 【F:components/squads/generate-angular-code/v0.0.1/squad.yaml†L64-L66】
- Cambios en `metadata.version` o `llm_configs` pueden requerir regenerar IDs en goals/grupos si se versiona el paquete completo.

## Listas de verificación
### Creación / versión inicial
- [ ] Definir `metadata` con nombre único, autoría y descripción resumida.
- [ ] Registrar todos los modelos que usarán agentes o transformadores del paquete.
- [ ] Validar credenciales y campos obligatorios (`api_key`, `azure_endpoint`, etc.) según cada `model_client`.

### Mantenimiento
- [ ] Incrementar `metadata.version` cuando se publique una versión que incluya cambios incompatibles o relevantes en cualquier componente.
- [ ] Añadir nuevos `llm_configs` antes de referenciarlos en squads o flows; eliminar sólo tras reemplazar sus usos.
- [ ] Revisar periódicamente que los nombres declarados sigan disponibles en los entornos cloud correspondientes.
