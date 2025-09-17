# Ejemplo de prompt para generación de código con adl-commons-lib-node-pp-parameter-store

## Prompt sugerido para LLM

"""
Quiero que generes código Node.js que use la librería @avaldigitallabs/adl-commons-lib-node-pp-parameter-store para cargar parámetros de AWS Parameter Store como variables de entorno. El código debe:

- Incluir el require de la librería como la primera línea del entrypoint.
- Configurar las variables de entorno necesarias (adl_parameter_store_enabled, adl_parameter_store_prefix, adl_parameter_store_path, etc.).
- Acceder a los parámetros usando process.env.
- Incluir manejo de errores si un parámetro requerido no está presente.
- Incluir ejemplo de permisos IAM necesarios para el acceso a Parameter Store.
- Seguir las mejores prácticas de seguridad (no loggear valores sensibles, usar KMS para parámetros críticos, separar entornos por namespace).

Referencia técnica:

[INCLUIR AQUÍ EL CONTENIDO DE adl-commons-lib-node-pp-parameter-store.md]
"""

Puedes adaptar este prompt para otros escenarios: Express.js, microservicios, integración con otras librerías ADL, etc. Solo cambia los requisitos específicos y mantén el contexto técnico como referencia.
