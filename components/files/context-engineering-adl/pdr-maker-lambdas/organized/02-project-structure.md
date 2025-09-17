# ADL Project Structure - OBLIGATORIA

## Estructura de Directorios Estándar

```
src/
├── index.js                 # Entry point de la Lambda (OBLIGATORIO)
├── assembler.js            # Contenedor IoC con Inversify (OBLIGATORIO)
├── application/            # Capa de aplicación - Lógica de negocio
│   ├── [service-name]-service-impl.js  # Implementación del servicio
│   └── [service-name]-service.js       # Interface del servicio
├── domain/                 # Capa de dominio - Entidades y modelos
│   ├── entities/          # Entidades de dominio
│   │   ├── object-mapper-impl.js       # Mapper de objetos (OBLIGATORIO)
│   │   ├── validate-object-impl.js     # Validador de objetos (OBLIGATORIO)
│   │   └── interfaces/                 # Interfaces de entidades
│   └── models/            # Modelos de datos
│       ├── body-request.js            # Schema del body (OBLIGATORIO)
│       ├── headers-model.js           # Schema de headers (OBLIGATORIO)
│       ├── dictionaries/              # Constantes y enums
│       └── enums/                     # Enumeraciones
└── infrastructure/        # Capa de infraestructura - Servicios externos
    ├── [service-name]-controller.js    # Controlador principal (OBLIGATORIO)
    ├── token-service-impl.js          # Servicio de tokens (OBLIGATORIO)
    ├── external-service-impl.js       # Servicio externo específico
    ├── data-retrieval-service-impl.js # Recuperación de datos
    ├── sqs-manager-impl.js            # Manager de SQS (OBLIGATORIO)
    └── error-logger.js                # Logger de errores (OBLIGATORIO)
```

## Archivos de Testing

```
test/
├── application/
│   └── [service-name]-service-impl.test.js
├── domain/
│   ├── object-mapper.test.js
│   └── validate-object-impl.test.js
├── infrastructure/
│   ├── data-retrieval-service-impl.test.js
│   ├── external-service-impl.test.js
│   ├── token-service-impl.test.js
│   └── [service-name]-controller.test.js
└── mocks/
    ├── [external-service]-mock.js
    ├── sqs-manager-mock.js
    └── [service-name]-service-mock.js
```

## Archivos de Configuración

```
./
├── package.json           # Usar template estándar ADL
├── tsconfig.json         # Configuración TypeScript
├── jest.config.js        # Configuración Jest
├── .eslintrc.js         # ESLint config
├── .prettierrc          # Prettier config
└── serverless.yml       # Configuración Serverless (opcional)
```

## Separación de Responsabilidades

### Capa de Dominio (`domain/`)
**Propósito**: Contiene la lógica de negocio pura, independiente de frameworks.

- **entities/**: Objetos de dominio y lógica de validación
- **models/**: Estructuras de datos y schemas de validación
- **interfaces/**: Contratos y abstracciones

### Capa de Aplicación (`application/`)
**Propósito**: Orquesta el flujo de la aplicación, coordina entre dominio e infraestructura.

- Servicios de aplicación que implementan casos de uso
- Lógica de negocio específica del servicio
- Coordinación entre diferentes servicios

### Capa de Infraestructura (`infrastructure/`)
**Propósito**: Implementa detalles técnicos, integraciones externas.

- Controladores que manejan requests HTTP
- Servicios que se conectan a APIs externas
- Implementaciones de persistencia
- Logging y monitoreo

## Convenciones de Nombrado

### Archivos y Clases
- **Controladores**: `[ServiceName]Controller`
- **Servicios**: `[ServiceName]ServiceImpl` + `[ServiceName]Service` (interface)
- **Mappers**: `ObjectMapperImpl` + `ObjectMapper` (interface)
- **Validators**: `ValidateObjectImpl` + `ValidateObject` (interface)

### Directorios
- **Minúsculas con guiones**: `service-name-controller.js`
- **Plurales para contenedores**: `entities/`, `models/`, `interfaces/`

### Tests
- **Unitarios**: `[filename].test.js`
- **Mocks**: `[service-name]-mock.js`

## Archivos Obligatorios Mínimos

Para que un servicio Lambda ADL sea válido debe tener como mínimo:

### Core Files (OBLIGATORIO)
1. `src/index.js` - Entry point con e2e.setHandler()
2. `src/assembler.js` - Contenedor IoC
3. `src/infrastructure/[service]-controller.js` - Controlador principal
4. `src/application/[service]-service-impl.js` - Lógica de negocio
5. `src/domain/entities/object-mapper-impl.js` - Mapper de objetos
6. `src/domain/entities/validate-object-impl.js` - Validador
7. `src/infrastructure/error-logger.js` - Error handling

### Schemas (OBLIGATORIO)  
8. `src/domain/models/headers-model.js` - Schema de headers
9. `src/domain/models/body-request.js` - Schema del body

### Services (OBLIGATORIO)
10. `src/infrastructure/token-service-impl.js` - Servicio de tokens
11. `src/infrastructure/sqs-manager-impl.js` - Analytics SQS

## Ejemplo de Creación

```bash
mkdir -p src/{application,domain/{entities,models/{dictionaries,enums},interfaces},infrastructure}
mkdir -p test/{application,domain,infrastructure,mocks}

# Crear archivos base
touch src/index.js src/assembler.js
touch src/infrastructure/error-logger.js
touch src/domain/models/headers-model.js
touch src/domain/models/body-request.js
```
