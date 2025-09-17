# Node.js Manual para Aplicaciones Backend

<big style="margin-top: 2em">
Node.js es un entorno de ejecución para JavaScript en el servidor, ideal para construir aplicaciones backend rápidas, escalables y eficientes.
</big>

Node.js es open-source y cuenta con una comunidad activa y un vasto ecosistema de librerías. Permite crear APIs, servidores web, microservicios y más, usando JavaScript puro.

**¿Quieres ver código?** Revisa la sección [Esenciales](#esenciales) para un vistazo rápido, o sigue el [Tutorial](#tutorial) para una guía paso a paso.

## Características principales
- Asincronía y modelo orientado a eventos
- Gran rendimiento gracias a V8
- Ecosistema npm con miles de paquetes
- Multiplataforma
- Ideal para APIs REST, microservicios y servidores HTTP

## Instalación y requisitos

### Prerrequisitos
- **Node.js** - [v20.x o superior](https://nodejs.org/)
- **Editor de texto** - Recomendado: [Visual Studio Code](https://code.visualstudio.com/)
- **Terminal**

### Instalación
Descarga Node.js desde [nodejs.org](https://nodejs.org/) y sigue las instrucciones para tu sistema operativo.

Verifica la instalación:
```sh
node -v
npm -v
```

## Crear un nuevo proyecto

1. Crea una carpeta para tu proyecto y accede a ella:
   ```sh
   mkdir mi-backend-node
   cd mi-backend-node
   ```
2. Inicializa el proyecto:
   ```sh
   npm init -y
   ```
3. Crea el archivo principal:
   ```sh
   touch index.js
   ```

## Estructura recomendada de proyecto

```
mi-backend-node/
├─ src/
│  ├─ controllers/
│  ├─ routes/
│  ├─ services/
│  ├─ middlewares/
│  ├─ utils/
│  └─ index.js
├─ tests/
├─ package.json
├─ .env
└─ README.md
```

- **src/**: Código fuente de la aplicación
- **controllers/**: Lógica de negocio
- **routes/**: Definición de rutas HTTP
- **services/**: Servicios reutilizables (acceso a datos, lógica de dominio)
- **middlewares/**: Funciones intermedias para peticiones
- **utils/**: Utilidades y helpers
- **tests/**: Pruebas automatizadas

## Guía de estilo y buenas prácticas

### Nombres significativos
- Usa nombres descriptivos para variables, funciones y archivos.
- Evita abreviaturas innecesarias.

### Estructura limpia
- Mantén funciones pequeñas y con una sola responsabilidad.
- Separa la lógica de negocio de la de infraestructura (por ejemplo, no mezcles acceso a base de datos con controladores HTTP).

### Manejo de dependencias
- Usa `require`/`import` solo para lo necesario.
- Mantén actualizado el `package.json`.

### Manejo de errores
- Usa bloques `try/catch` para código asíncrono.
- Centraliza el manejo de errores en middlewares o funciones específicas.

### Variables de entorno
- Usa el paquete `dotenv` para gestionar configuraciones sensibles.

### Testing
- Escribe pruebas unitarias y de integración (por ejemplo, usando `jest` o `mocha`).
- Coloca las pruebas en la carpeta `tests/`.

### Documentación
- Documenta funciones y módulos con JSDoc o comentarios claros.
- Mantén actualizado el `README.md`.

## Ejemplo mínimo de servidor HTTP

```js
// src/index.js
const http = require('http');

const server = http.createServer((req, res) => {
  if (req.url === '/saludo' && req.method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ mensaje: '¡Hola desde Node.js!' }));
  } else {
    res.writeHead(404);
    res.end();
  }
});

server.listen(3000, () => {
  console.log('Servidor escuchando en http://localhost:3000');
});
```

## Uso de módulos y npm

- Instala paquetes con `npm install <paquete>`
- Usa `require('<paquete>')` para importar módulos
- Mantén las dependencias organizadas en `package.json`

## Integración con librerías externas

Ejemplo: uso de `dotenv` para variables de entorno:
```js
require('dotenv').config();
const puerto = process.env.PORT || 3000;
```

## Seguridad y despliegue
- Nunca subas archivos `.env` o credenciales al repositorio
- Usa herramientas como `helmet` para mejorar la seguridad HTTP
- Considera PM2 o Docker para producción

## Recursos adicionales
- [Documentación oficial Node.js](https://nodejs.org/en/docs)
- [npmjs.com](https://www.npmjs.com/)
- [Guía de estilo JavaScript de Google](https://google.github.io/styleguide/jsguide.html)

---

Este manual cubre los fundamentos para crear aplicaciones backend robustas con Node.js puro. Adapta la estructura y prácticas según las necesidades de tu proyecto.
