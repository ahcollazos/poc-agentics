# ADL Entry Point Patterns - OBLIGATORIO

## 1. Entry Point Estándar (index.js)

**CRÍTICO**: Todos los servicios ADL DEBEN usar este patrón exacto.

```javascript
const assembler_1 = require("./assembler");
const [ServiceController] = require("./infrastructure/[service-name]-controller");
const error_logger_1 = require("./infrastructure/error-logger");
const e2e = require('@avaldigitallabs/adl-commons-lib-e2e-cypher');

const yourHandler = async (event) => {
    try {
        // 1. Inicializar logger (OBLIGATORIO)
        (0, error_logger_1.InitLogger)();
        
        // 2. Resolver dependencias via IoC (OBLIGATORIO)
        const controller = assembler_1.AppContainer.get([ServiceController]);
        
        // 3. Ejecutar operación principal (OBLIGATORIO)
        return await controller.execute[Operation](event);
    } catch (err) {
        // 4. Manejo global de errores (OBLIGATORIO)
        return JSON.stringify(err);
    }
};

// 5. Wrapper E2E Cypher (OBLIGATORIO)
exports.handler = e2e.setHandler(yourHandler);
```

### Variables a Reemplazar
- `[ServiceController]` → Nombre real del controlador (ej: `PaymentController`)
- `[service-name]` → Nombre del servicio (ej: `payment-processor`)
- `[Operation]` → Nombre de la operación (ej: `ProcessPayment`)

## 2. Contenedor IoC (assembler.js)

**CRÍTICO**: Configuración obligatoria de Inversify.

```javascript
const inversify_1 = require("inversify");
const [ServiceController] = require("./infrastructure/[service-name]-controller");
const [ServiceNameImpl] = require("./application/[service-name]-service-impl");
const TokenServiceImpl = require("./infrastructure/token-service-impl");
const ExternalServiceImpl = require("./infrastructure/external-service-impl");
const DataRetrievalServiceImpl = require("./infrastructure/data-retrieval-service-impl");
const ObjectMapperImpl = require("./domain/entities/object-mapper-impl");
const ValidateObjectImpl = require("./domain/entities/validate-object-impl");
const SqsManagerImpl = require("./infrastructure/sqs-manager-impl");
const TYPES = require("./domain/models/dictionaries/types");

// Crear contenedor (OBLIGATORIO)
const container = new inversify_1.Container();

// Bindings obligatorios - Patrón estándar ADL
container.bind([ServiceController]).toSelf();
container.bind(TYPES.APPLICATION.[ServiceName]).to([ServiceNameImpl]);
container.bind(TYPES.INFRASTRUCTURE.TokenService).to(TokenServiceImpl);
container.bind(TYPES.INFRASTRUCTURE.ExternalService).to(ExternalServiceImpl);
container.bind(TYPES.INFRASTRUCTURE.DataRetrievalService).to(DataRetrievalServiceImpl);
container.bind(TYPES.ENTITIES.ObjectMapper).to(ObjectMapperImpl);
container.bind(TYPES.ENTITIES.ValidateObject).to(ValidateObjectImpl);
container.bind(TYPES.INFRASTRUCTURE.SqsManager).to(SqsManagerImpl).inSingletonScope();

exports.AppContainer = container;
```

## 3. Dictionary de Tipos (OBLIGATORIO)

**Archivo**: `src/domain/models/dictionaries/types.js`

```javascript
const TYPES = {
    APPLICATION: {
        [ServiceName]: Symbol.for("[ServiceName]"),
    },
    INFRASTRUCTURE: {
        TokenService: Symbol.for("TokenService"),
        ExternalService: Symbol.for("ExternalService"),
        DataRetrievalService: Symbol.for("DataRetrievalService"),
        SqsManager: Symbol.for("SqsManager"),
    },
    ENTITIES: {
        ObjectMapper: Symbol.for("ObjectMapper"),
        ValidateObject: Symbol.for("ValidateObject"),
    },
};

module.exports = TYPES;
```

## 4. Error Logger (OBLIGATORIO)

**Archivo**: `src/infrastructure/error-logger.js`

```javascript
const InitLogger = () => {
    console.log("Logger initialized for ADL service");
};

const ErrorLogger = (error) => {
    console.error("ADL Service Error:", {
        message: error.message,
        stack: error.stack,
        timestamp: new Date().toISOString(),
        correlationId: error.correlationId || 'unknown'
    });
};

module.exports = {
    InitLogger,
    ErrorLogger
};
```

## 5. Controlador Base (OBLIGATORIO)

**Archivo**: `src/infrastructure/[service-name]-controller.js`

```javascript
const inversify_1 = require("inversify");
const TYPES = require("../domain/models/dictionaries/types");
const { ErrorLogger } = require("./error-logger");

class [ServiceName]Controller {
    constructor(service) {
        this.service = service;
    }

    async execute[Operation](event) {
        try {
            return await this.service.execute[Operation](event);
        } catch (error) {
            ErrorLogger(error);
            throw error;
        }
    }
}

// Decorador para inyección de dependencias
inversify_1.decorate(inversify_1.injectable(), [ServiceName]Controller);
inversify_1.decorate(
    inversify_1.inject(TYPES.APPLICATION.[ServiceName]), 
    [ServiceName]Controller, 
    0
);

module.exports = [ServiceName]Controller;
```

## 6. Servicio de Aplicación Base (OBLIGATORIO)

**Archivo**: `src/application/[service-name]-service-impl.js`

```javascript
const inversify_1 = require("inversify");
const TYPES = require("../domain/models/dictionaries/types");
const { ErrorLogger } = require("../infrastructure/error-logger");

class [ServiceName]ServiceImpl {
    constructor(
        tokenService,
        externalService,
        dataRetrievalService,
        mapper,
        validateObject,
        sqsManager
    ) {
        this.tokenService = tokenService;
        this.externalService = externalService;
        this.dataRetrievalService = dataRetrievalService;
        this.mapper = mapper;
        this.validateObject = validateObject;
        this.sqsManager = sqsManager;
    }

    async execute[Operation](event) {
        // Implementar lógica de negocio aquí
        // Ver 07-application-layer.md para detalles
    }
}

// Decoradores para IoC
inversify_1.decorate(inversify_1.injectable(), [ServiceName]ServiceImpl);
inversify_1.decorate(inversify_1.inject(TYPES.INFRASTRUCTURE.TokenService), [ServiceName]ServiceImpl, 0);
inversify_1.decorate(inversify_1.inject(TYPES.INFRASTRUCTURE.ExternalService), [ServiceName]ServiceImpl, 1);
inversify_1.decorate(inversify_1.inject(TYPES.INFRASTRUCTURE.DataRetrievalService), [ServiceName]ServiceImpl, 2);
inversify_1.decorate(inversify_1.inject(TYPES.ENTITIES.ObjectMapper), [ServiceName]ServiceImpl, 3);
inversify_1.decorate(inversify_1.inject(TYPES.ENTITIES.ValidateObject), [ServiceName]ServiceImpl, 4);
inversify_1.decorate(inversify_1.inject(TYPES.INFRASTRUCTURE.SqsManager), [ServiceName]ServiceImpl, 5);

module.exports = [ServiceName]ServiceImpl;
```

## Características Obligatorias

### 1. E2E Cypher Integration
- **SIEMPRE** usar `e2e.setHandler()` para wrappear el handler
- **NUNCA** exponer handler directamente

### 2. IoC Pattern  
- **SIEMPRE** usar Inversify para inyección de dependencias
- **SIEMPRE** definir símbolos únicos en dictionary
- **SIEMPRE** usar decoradores para binding

### 3. Error Handling
- **SIEMPRE** inicializar logger al inicio
- **SIEMPRE** manejar errores globalmente
- **SIEMPRE** loggear errores con contexto

### 4. Separation of Concerns
- **Controller**: Manejo de HTTP requests
- **Service**: Lógica de negocio 
- **Infrastructure**: Integraciones externas
- **Domain**: Modelos y validaciones

## Validación de Implementation

Un entry point ADL válido debe:
- ✅ Usar template exacto de index.js
- ✅ Configurar IoC con Inversify
- ✅ Tener error logger inicializado
- ✅ Usar e2e.setHandler() wrapper
- ✅ Tener dictionary de tipos definido
- ✅ Implementar patrón controller/service
