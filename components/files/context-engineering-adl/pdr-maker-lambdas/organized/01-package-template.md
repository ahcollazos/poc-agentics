# ADL Standard Package.json Template

## OBLIGATORIO - Template Exacto

**CRÍTICO**: Los agentes IA DEBEN usar exactamente este template para todos los servicios ADL.

```json
{
    "name": "[service-name]",
    "version": "1.0.0",
    "description": "Lambda for [service description]",
    "main": "src/index.js",
    "files": [
        "/src"
    ],
    "scripts": {
        "test": "jest",
        "test:coverage": "jest --coverage",
        "audit": "npm audit",
        "sonar": "sonar-scanner -Dsonar.organization=$SONAR_ORGANIZATION -Dsonar.host.url=$SONAR_URL -Dsonar.login=$SONAR_TOKEN",
        "lint:fix": "eslint --fix .",
        "lint": "eslint .",
        "prettier:list": "prettier --list-different \"./{test,src}/**/*.ts\" || ECHO .",
        "prettier:write": "prettier --write  \"./{test,src}/**/*.ts\"",
        "prettier": "prettier --check  \"./{test,src}/**/*.ts\"",
        "clean": "rimraf dist && rimraf coverage && rimraf [service-name].zip",
        "build": "npm run clean && npm run build-app",
        "build-app": "tsc -p tsconfig.json",
        "postbuild": "npm run copy-pck-files && npm run copy-mdl-files && npm run zip && mv [service-name].zip dist/",
        "copy-pck-files": "copyfiles -u 1 src/package.json dist/src",
        "copy-mdl-files": "cp package.json dist && cp .npmrc dist && cd dist && npm i --production && cd ../",
        "zip": "cd dist/ && bestzip  ../[service-name].zip *"
    },
    "devDependencies": {
        "@types/aws-lambda": "^8.10.149",
        "@types/jest": "^27.0.0",
        "@types/joi": "^17.2.3",
        "@types/node": "^16.3.0",
        "@types/qs": "^6.9.9",
        "@typescript-eslint/eslint-plugin": "^5.9.0",
        "@typescript-eslint/parser": "^5.9.0",
        "bestzip": "^2.2.0",
        "copyfiles": "^2.4.1",
        "eslint": "^7.20.0",
        "eslint-config-google": "^0.14.0",
        "eslint-config-prettier": "^8.0.0",
        "eslint-plugin-import": "^2.22.1",
        "eslint-plugin-prettier": "^3.3.1",
        "jest": "^29.5.0",
        "jshint": "^2.12.0",
        "nyc": "^15.1.0",
        "prettier": "^2.2.1",
        "prettier-eslint": "^12.0.0",
        "repack-zip": "^0.2.8",
        "ts-jest": "^29.1.0",
        "ts-node": "^10.9.2",
        "tslint": "^6.1.3",
        "typescript": "^4.3.5"
    },
    "dependencies": {
        "@avaldigitallabs/adl-commons-lib-e2e-cypher": "^1.1.1",
        "@avaldigitallabs/adl-commons-lib-node-pp-parameter-store": "^1.1.13",
        "@avaldigitallabs/bocc-pagos-lib-admin-token": "^1.4.0",
        "@avaldigitallabs/bocc-pagos-lib-aws-kms": "^1.0.1",
        "@avaldigitallabs/bocc-pagos-lib-aws-persistence": "^1.0.3",
        "@avaldigitallabs/bocc-pagos-lib-axios": "^1.0.1",
        "@avaldigitallabs/bocc-pagos-lib-aws-sqs": "^1.0.2",
        "inversify": "^6.0.1",
        "joi": "^17.4.0",
        "qs": "^6.11.2",
        "reflect-metadata": "^0.2.2"
    }
}
```

## Descripción de Dependencias ADL

### Dependencias de Producción (OBLIGATORIAS)

| Librería | Propósito | Versión |
|----------|-----------|---------|
| `@avaldigitallabs/adl-commons-lib-e2e-cypher` | Cifrado End-to-End RSA+AES | ^1.1.1 |
| `@avaldigitallabs/adl-commons-lib-node-pp-parameter-store` | AWS Parameter Store | ^1.1.13 |
| `@avaldigitallabs/bocc-pagos-lib-admin-token` | Gestión de tokens | ^1.4.0 |
| `@avaldigitallabs/bocc-pagos-lib-aws-kms` | Cifrado AWS KMS | ^1.0.1 |
| `@avaldigitallabs/bocc-pagos-lib-aws-persistence` | DynamoDB utilities | ^1.0.3 |
| `@avaldigitallabs/bocc-pagos-lib-axios` | HTTP cliente mejorado | ^1.0.1 |
| `@avaldigitallabs/bocc-pagos-lib-aws-sqs` | SQS messaging | ^1.0.2 |
| `inversify` | IoC Container | ^6.0.1 |
| `joi` | Validación de schemas | ^17.4.0 |
| `qs` | Query strings | ^6.11.2 |
| `reflect-metadata` | Metadata para decoradores | ^0.2.2 |

### Scripts de Build Críticos

**Flujo de Build**:
1. `npm run clean` - Limpia directorios
2. `npm run build-app` - Compila TypeScript  
3. `npm run copy-pck-files` - Copia package internos
4. `npm run copy-mdl-files` - Instala dependencias de producción
5. `npm run zip` - Crea ZIP para Lambda

## Instrucciones para Agentes IA

### Reemplazo de Placeholders
```javascript
// Reemplazar estas variables:
"[service-name]" → nombre real del servicio
"[service description]" → descripción específica
```

### Generación Automática
```javascript
const generatePackageJson = (serviceName, description) => {
    const template = /* template de arriba */;
    return template
        .replace(/\[service-name\]/g, serviceName)
        .replace(/\[service description\]/g, description);
};
```
