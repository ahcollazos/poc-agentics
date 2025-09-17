# ADL Domain Models & Validations - OBLIGATORIO

## 1. Headers Schema Estándar (OBLIGATORIO)

**Archivo**: `src/domain/models/headers-model.js`

```javascript
const joi = require("joi");

// Schema de headers obligatorio para todos los servicios ADL
const standardHeadersSchema = joi.object({
    'X-RqUID': joi.string().required().description('Unique request identifier'),
    'X-Channel': joi.string().required().description('Channel identifier'),
    'X-CompanyId': joi.string().required().description('Company identifier'),
    'X-IdentSerialNum': joi.string().required().description('Identity serial number'),
    'X-GovIssueIdentType': joi.string().required().description('Government identity type'),
    'X-IPAddr': joi.string().required().description('Client IP address'),
    'X-ClientDt': joi.string().required().description('Client datetime'),
    'X-FirstName': joi.string().required().description('User first name'),
    'X-LastName': joi.string().required().description('User last name'),
    'X-CustIdentType': joi.string().required().description('Customer identity type'),
    'X-CustIdentNum': joi.string().required().description('Customer identity number'),
    'X-TypeTransaction': joi.string().required().description('Transaction type'),
    'X-CameraReference': joi.string().required().description('Camera reference'),
    'User-Agent': joi.string().required().description('User agent string'),
}).unknown(true); // Permite headers adicionales

// Headers permitidos para normalización
const allowedHeaders = Object.keys(standardHeadersSchema.describe().keys);

module.exports = {
    standardHeadersSchema,
    allowedHeaders
};
```

## 2. Body Request Schema Base (OBLIGATORIO)

**Archivo**: `src/domain/models/body-request.js`

```javascript
const joi = require("joi");

// Schema base que debe extenderse según necesidades específicas
const baseBodyRequestSchema = joi.object({
    // Identificación obligatoria
    FirstName: joi.string().min(1).max(50).required().description('First name'),
    LastName: joi.string().min(1).max(50).required().description('Last name'),
    NumDoc: joi.string().min(5).max(20).required().description('Document number'),
    TypeDoc: joi.string().valid('CC', 'CE', 'TI', 'PP').required().description('Document type'),
    
    // Referencia obligatoria
    RefInfo: joi.object({
        RefType: joi.string().required().description('Reference type'),
        RefId: joi.string().required().description('Reference identifier'),
    }).required().description('Reference information'),
    
    // Campos opcionales comunes
    Email: joi.string().email().optional().description('Email address'),
    Phone: joi.string().pattern(/^[0-9+\-\s()]+$/).optional().description('Phone number'),
    IpAddress: joi.string().ip().optional().description('IP address'),
    Amount: joi.string().pattern(/^\d+(\.\d{1,2})?$/).optional().description('Amount'),
    
    // Campos de auditoría
    Timestamp: joi.string().isoDate().optional().description('Request timestamp'),
    Channel: joi.string().optional().description('Request channel'),
});

// Ejemplo de extensión para servicio específico
const paymentBodyRequestSchema = baseBodyRequestSchema.keys({
    // Campos específicos de pagos
    PaymentMethod: joi.string().valid('CARD', 'TRANSFER', 'CASH').required(),
    Currency: joi.string().valid('COP', 'USD').default('COP'),
    Description: joi.string().max(200).optional(),
    
    // Información de tarjeta (si aplica)
    CardInfo: joi.when('PaymentMethod', {
        is: 'CARD',
        then: joi.object({
            CardNumber: joi.string().creditCard().required(),
            ExpiryMonth: joi.number().integer().min(1).max(12).required(),
            ExpiryYear: joi.number().integer().min(new Date().getFullYear()).required(),
            CVV: joi.string().pattern(/^\d{3,4}$/).required(),
        }).required(),
        otherwise: joi.forbidden()
    })
});

module.exports = {
    baseBodyRequestSchema,
    paymentBodyRequestSchema, // Ejemplo
};
```

## 3. Object Mapper Estándar (OBLIGATORIO)

**Archivo**: `src/domain/entities/object-mapper-impl.js`

```javascript
const inversify_1 = require("inversify");
const { allowedHeaders } = require("../models/headers-model");
const https = require('https');

class ObjectMapperImpl {
    constructor() {
        this.TIMEOUT_SERVICE = 30000;
    }

    // Normalización de headers (OBLIGATORIO)
    normalizeHeaders(headers) {
        const lowerCaseHeaders = Object.fromEntries(
            Object.entries(headers || {}).map(([k, v]) => [k.toLowerCase(), v])
        );
        
        return Object.fromEntries(
            allowedHeaders.map((key) => [
                key, 
                lowerCaseHeaders[key.toLowerCase()] || ''
            ])
        );
    }

    // Construcción de info de token (OBLIGATORIO)
    buildTokenInfo() {
        return {
            config: {
                method: 'POST',
                maxBodyLength: Infinity,
                url: process.env.url_token_service,
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded',
                    'Accept': 'application/json',
                    'X-api-key': process.env.api_key,
                },
                data: `grant_type=client_credentials&client_id=${process.env.client_id}&client_secret=${process.env.client_secret}`,
                timeout: this.TIMEOUT_SERVICE,
            },
            tableCache: process.env.table_cache,
            ttlCache: parseInt(process.env.ttl_cache, 10),
            applicationName: 'API_GATEWAY',
        };
    }

    // Construcción de opciones de request (OBLIGATORIO)
    buildOptions(headers, body, token, pfx = null) {
        const options = {
            url: process.env.url_external_service,
            method: 'POST',
            headers: {
                ...this.mapStandardHeaders(headers),
                'Authorization': `Bearer ${token}`,
                'x-api-key': process.env.api_key,
                'Content-Type': 'application/json',
            },
            data: body,
            timeout: this.TIMEOUT_SERVICE,
        };

        // Configuración de certificado (si aplica)
        if (pfx) {
            options.httpsAgent = new https.Agent({
                pfx,
                passphrase: process.env.passphrase,
                rejectUnauthorized: false,
            });
        }

        return options;
    }

    // Mapeo de headers estándar (OBLIGATORIO)
    mapStandardHeaders(headers) {
        return {
            'X-RqUID': headers['X-RqUID'],
            'X-Channel': headers['X-Channel'],
            'X-CompanyId': headers['X-CompanyId'],
            'X-IPAddr': headers['X-IPAddr'],
            'X-ClientDt': headers['X-ClientDt'],
            'X-TypeTransaction': headers['X-TypeTransaction'],
        };
    }

    // Construcción de respuesta estándar (OBLIGATORIO)
    buildResponse(statusCode, response, data = null) {
        const body = {
            statusCode,
            messageId: response.messageId || 'unknown',
            message: response.message || 'Success',
            timestamp: new Date().toISOString(),
            data: data,
        };

        return {
            statusCode,
            headers: {
                'Access-Control-Allow-Origin': '*',
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(body),
            isBase64Encoded: false,
        };
    }

    // Fusión de datos desde múltiples fuentes (OBLIGATORIO)
    fromSources(decryptedPayload, headers, body) {
        return {
            ...decryptedPayload,
            ...body,
            metadata: {
                correlationId: headers['X-RqUID'],
                channel: headers['X-Channel'],
                timestamp: new Date().toISOString(),
            }
        };
    }

    // Construcción de evento analítico (OBLIGATORIO)
    buildEventAnalytic(headers, detail, isSuccess = true) {
        return {
            correlationId: headers['X-RqUID'],
            channel: headers['X-Channel'],
            companyId: headers['X-CompanyId'],
            timestamp: new Date().toISOString(),
            eventType: headers['X-TypeTransaction'],
            success: isSuccess,
            details: detail,
            userInfo: {
                firstName: headers['X-FirstName'],
                lastName: headers['X-LastName'],
                identityType: headers['X-CustIdentType'],
                identityNumber: headers['X-CustIdentNum'],
            }
        };
    }

    // Detalles de evento para éxito (OBLIGATORIO)
    getEventDetails(headers) {
        return {
            operation: headers['X-TypeTransaction'],
            channel: headers['X-Channel'],
            timestamp: headers['X-ClientDt'],
            status: 'SUCCESS'
        };
    }

    // Detalles de evento para fallo (OBLIGATORIO)
    getEventDetailsFail(message, messageId) {
        return {
            status: 'FAILED',
            error: {
                message,
                messageId,
                timestamp: new Date().toISOString()
            }
        };
    }

    // Detalles de fallo de servicio externo (OBLIGATORIO)
    getEventDetailsFailService(errorResponse) {
        return {
            status: 'EXTERNAL_SERVICE_ERROR',
            externalError: errorResponse,
            timestamp: new Date().toISOString()
        };
    }
}

// IoC decorators
inversify_1.decorate(inversify_1.injectable(), ObjectMapperImpl);

module.exports = ObjectMapperImpl;
```

## 4. Validador de Objetos (OBLIGATORIO)

**Archivo**: `src/domain/entities/validate-object-impl.js`

```javascript
const inversify_1 = require("inversify");

class ValidateObjectImpl {
    
    // Validación principal (OBLIGATORIO)
    async executeValidateObject(object, schema) {
        try {
            const { error, value } = schema.validate(object, {
                abortEarly: false,    // Reportar todos los errores
                allowUnknown: false,  // No permitir propiedades extra
                stripUnknown: true    // Remover propiedades no definidas
            });

            if (error) {
                const validationError = new ValidationError(
                    'Validation failed',
                    error.details
                );
                return Promise.reject(validationError);
            }

            return Promise.resolve(value);
        } catch (err) {
            return Promise.reject(new ValidationError(
                'Validation processing error',
                [{ message: err.message }]
            ));
        }
    }

    // Validación con transformación (OPCIONAL)
    async validateAndTransform(object, schema, transformer = null) {
        try {
            const validatedObject = await this.executeValidateObject(object, schema);
            
            if (transformer && typeof transformer === 'function') {
                return transformer(validatedObject);
            }
            
            return validatedObject;
        } catch (error) {
            throw error;
        }
    }

    // Validación batch (OPCIONAL)
    async validateMultiple(objects, schemas) {
        if (objects.length !== schemas.length) {
            throw new ValidationError('Objects and schemas count mismatch');
        }

        const validationPromises = objects.map((object, index) => 
            this.executeValidateObject(object, schemas[index])
        );

        return Promise.all(validationPromises);
    }
}

// Custom Error Class
class ValidationError extends Error {
    constructor(message, details = []) {
        super(message);
        this.name = 'ValidationError';
        this.statusCode = 400;
        this.messageId = 'VALIDATION_ERROR';
        this.details = details;
        this.timestamp = new Date().toISOString();
    }
}

// IoC decorators
inversify_1.decorate(inversify_1.injectable(), ValidateObjectImpl);

module.exports = ValidateObjectImpl;
module.exports.ValidationError = ValidationError;
```

## 5. Enums y Constantes (OBLIGATORIO)

**Archivo**: `src/domain/models/enums/status-codes.js`

```javascript
const StatusCode = {
    OK: 200,
    CREATED: 201,
    BAD_REQUEST: 400,
    UNAUTHORIZED: 401,
    FORBIDDEN: 403,
    NOT_FOUND: 404,
    CONFLICT: 409,
    UNPROCESSABLE_ENTITY: 422,
    INTERNAL_SERVER_ERROR: 500,
    SERVICE_UNAVAILABLE: 503,
    BUSINESS_ISSUE: 422, // Para errores de negocio del servicio externo
};

const STATUS_ID = {
    SUCCESS: {
        messageId: 'SUCCESS',
        message: 'Operation completed successfully'
    },
    ERROR_VALIDATION: {
        messageId: 'VALIDATION_ERROR', 
        message: 'Input validation failed'
    },
    ERROR_AUTHENTICATION_SERVICE: {
        messageId: 'AUTH_ERROR',
        message: 'Authentication service unavailable'
    },
    ERROR_EXTERNAL_SERVICE: {
        messageId: 'EXTERNAL_SERVICE_ERROR',
        message: 'External service error'
    },
    ERROR_INTERNAL: {
        messageId: 'INTERNAL_ERROR',
        message: 'Internal server error'
    }
};

const HTTP_METHODS = {
    GET: 'GET',
    POST: 'POST',
    PUT: 'PUT',
    DELETE: 'DELETE',
    PATCH: 'PATCH'
};

module.exports = {
    StatusCode,
    STATUS_ID,
    HTTP_METHODS
};
```

## Patrones de Validación Obligatorios

### 1. Headers SIEMPRE Validar
```javascript
await this.validateObject.executeValidateObject(headers, standardHeadersSchema)
```

### 2. Body SIEMPRE Validar  
```javascript
await this.validateObject.executeValidateObject(body, bodyRequestSchema)
```

### 3. Validación Concurrente (RECOMENDADO)
```javascript
await Promise.all([
    this.validateObject.executeValidateObject(headers, headersSchema),
    this.validateObject.executeValidateObject(body, bodyRequestSchema)
]);
```

### 4. Manejo de Errores de Validación
```javascript
.catch(validationError => {
    if (validationError.name === 'ValidationError') {
        return Promise.reject({
            statusCode: StatusCode.BAD_REQUEST,
            ...STATUS_ID.ERROR_VALIDATION,
            details: validationError.details
        });
    }
    throw validationError;
});
```
