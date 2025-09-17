# Ejemplo de prompt para generación de código con adl-commons-lib-e2e-cypher

## Prompt sugerido para LLM

"""
Quiero que generes código Node.js para una AWS Lambda que use la librería @avaldigitallabs/adl-commons-lib-e2e-cypher para cifrado extremo a extremo (E2E) entre frontend y backend. La lambda debe:

- Exponer el public key vía un endpoint GET que reciba un parámetro timestamp.
- Usar setHandler para cifrar/descifrar automáticamente los mensajes de entrada/salida.
- Configurar las variables de entorno requeridas para el cifrado (ver contexto adjunto).
- Asegurarse de que las llaves RSA sean de al menos 3072 bits y estén gestionadas de forma segura (idealmente con Parameter Store).
- El mensaje cifrado de respuesta debe tener la forma: { "data": "ENCRYPTED_DATA" }
- No expongas llaves privadas en el código.

Referencia técnica:

[INCLUIR AQUÍ EL CONTENIDO DE adl-commons-lib-e2e-cypher-context.md]
"""

Puedes adaptar este prompt para otros escenarios: comunicación servicio a servicio (firmando mensajes con signMessage), microservicios, etc. Solo cambia los requisitos específicos y mantén el contexto técnico como referencia.
