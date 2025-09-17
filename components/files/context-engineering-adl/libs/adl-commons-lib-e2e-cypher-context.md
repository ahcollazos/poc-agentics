# Contexto técnico: adl-commons-lib-e2e-cypher

Librería Node.js para cifrado extremo a extremo (E2E) usando RSA (mínimo 3072 bits) y AES. Permite cifrar/descifrar mensajes entre frontend y backend, y firmar mensajes para comunicación segura entre servicios.

## Instalación y prerequisitos

- Node.js, NPM y Git instalados
- Credenciales JFROG configuradas
- Instala con:  
  `npm install @avaldigitallabs/adl-commons-lib-e2e-cypher`

## Generación de llaves RSA

```sh
openssl genpkey -algorithm RSA -out PRIVATE.pem -pkeyopt rsa_keygen_bits:3072
openssl rsa -pubout -in PRIVATE.pem -out PUBLIC.pem
```
Las llaves deben ser de 3072 bits o más.

## Variables de entorno requeridas

- adl_rest_cipher_enabled: "true"
- adl_rest_cipher_enableEncrypt: "true"
- adl_rest_cipher_timeout: "40"
- adl_rest_cipher_serverTimeout: "1800"
- adl_rest_cipher_rsaPrivateKey: "<PRIVATE_KEY_BASE64>"
- adl_rest_cipher_rsaPublicKey: "<PUBLIC_KEY_BASE64>"
- adl_rest_cipher_hmacKey: "<HMAC_KEY_BASE64>"

Opcional: puedes usar llaves sin encabezados o gestionarlas con Parameter Store (recomendado).

## Uso en AWS Lambda

1. Importa la librería:
   ```js
   const { publicKeyHandler, setHandler } = require("@avaldigitallabs/adl-commons-lib-e2e-cypher")
   ```

2. Expón el public key vía GET:
   ```js
   exports.handler = async (event, context) => {
     return publicKeyHandler(event.queryStringParameters.timestamp)
   }
   ```

3. Para cifrado/descifrado automático:
   ```js
   const yourHandler = async (event, context) => {
     // lógica de negocio
   }
   exports.handler = setHandler(yourHandler)
   ```

## Comunicación servicio a servicio (Back2Back)

```js
const { signMessage } = require("@avaldigitallabs/adl-commons-lib-e2e-cypher")
const axios = require("axios")
exports.handler = async (event, context) => {
  const myRequest = {...}
  const sign = signMessage(myRequest)
  const myResponse = await axios({
    url: "LA_URL_DE_TU_SERVICIO",
    method: "POST",
    headers: { "HMAC": sign },
    body: JSON.stringify(myRequest)
  })
  return { statusCode: 200 }
}
```
En Swagger/OpenAPI, declara el header HMAC como requerido.

## Uso en microservicios

- Variables extra:
  - adl_rest_cipher_publicKeyPath: "PATH_DEL_METODO_GET"
  - adl_rest_cipher_enableServer: "true"

- Importa y usa:
  ```js
  const { decrypt, encrypt } = require("@avaldigitallabs/adl-commons-lib-e2e-cypher")
  // decrypt(event) y encrypt(data)
  ```

- El mensaje cifrado de respuesta debe ser:
  ```json
  { "data": "ENCRYPTED_DATA" }
  ```

## Notas de seguridad y operación

- Nunca expongas llaves privadas en código o variables públicas.
- Usa Parameter Store para gestionar secretos.
- La integración debe ser Lambda Proxy para funcionar correctamente.
