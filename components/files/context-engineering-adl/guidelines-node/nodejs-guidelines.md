# Lineamientos Node.js para Agentes de IA

## 📚 Documentación Oficial
- [Node.js Docs](https://nodejs.org/en/docs)
- [Node.js API Reference](https://nodejs.org/dist/latest-v18.x/docs/api/)

## 🏗️ Estructura de Proyecto Recomendada
- `/src` — Código fuente principal
- `/test` — Pruebas automatizadas
- `package.json` — Metadatos, scripts y dependencias
- `.env` — Variables de entorno (no subir a git)
- `.eslintrc` — Configuración de estilo de código

## 🧑‍💻 Buenas Prácticas de Código
- Usa la versión LTS de Node.js
- Prefiere `async/await` para asincronía
- Maneja errores con try/catch y callbacks (err, result)
- No expongas información sensible en logs ni código
- Usa módulos ES6 (`import/export`) o CommonJS (`require/module.exports`) según el proyecto
- Documenta funciones y módulos con JSDoc
- Mantén funciones pequeñas y de única responsabilidad
- Evita el uso de variables globales

## 🛠️ Herramientas y Dependencias
- Instala dependencias con `npm install` o `yarn`
- Usa scripts en `package.json` para tareas comunes (`start`, `test`, `lint`, etc.)
- Usa ESLint y Prettier para mantener el estilo de código
- Usa dotenv para variables de entorno

## 🧪 Testing
- Escribe pruebas unitarias y de integración (Jest, Mocha, etc.)
- Coloca las pruebas en `/test` o junto al código fuente
- Usa mocks para dependencias externas
- Automatiza pruebas en CI/CD

## 🔒 Seguridad
- Valida y sanitiza toda entrada del usuario
- Usa `helmet` y `cors` en aplicaciones web
- No subas secretos ni contraseñas a git
- Actualiza dependencias regularmente (`npm audit`)

## 📦 Publicación y Deploy
- Usa `.env` para configuración sensible
- No subas archivos innecesarios (`.gitignore`)
- Documenta cómo instalar y correr el proyecto en el README
- Usa PM2 o Docker para producción

## 📝 Ejemplo de Comentario JSDoc
```js
/**
 * Suma dos números
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function suma(a, b) {
  return a + b;
}
```

## 💡 Recursos Útiles
- [Node.js Best Practices (GitHub)](https://github.com/goldbergyoni/nodebestpractices)
- [Awesome Node.js (GitHub)](https://github.com/sindresorhus/awesome-nodejs)

---

> Usa estos lineamientos como contexto para agentes de IA que generen, revisen o documenten código Node.js.
