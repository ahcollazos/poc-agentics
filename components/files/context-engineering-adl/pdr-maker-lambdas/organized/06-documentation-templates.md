# ADL Documentation Templates - OBLIGATORIO

## Archivos de Documentación Requeridos

Los agentes IA **DEBEN** generar estos 5 archivos de documentación:

## 1. README.md Template

```markdown
# [Service Name] - ADL Payment Service

## Description
[Brief description of what this service does]

## Architecture
- **Runtime**: AWS Lambda (Node.js)
- **Pattern**: Hexagonal Architecture (Clean Architecture)
- **IoC**: Inversify Container
- **Validation**: Joi schemas
- **Testing**: Jest with 85%+ coverage
- **Security**: E2E Cypher integration

## ADL Libraries Used

| Library | Version | Purpose |
|---------|---------|---------|
| `@avaldigitallabs/adl-commons-lib-e2e-cypher` | ^1.1.1 | End-to-End encryption (RSA+AES) |
| `@avaldigitallabs/adl-commons-lib-node-pp-parameter-store` | ^1.1.13 | AWS Parameter Store integration |
| `@avaldigitallabs/bocc-pagos-lib-admin-token` | ^1.4.0 | Token management and caching |
| `@avaldigitallabs/bocc-pagos-lib-aws-kms` | ^1.0.1 | AWS KMS encryption services |
| `@avaldigitallabs/bocc-pagos-lib-aws-persistence` | ^1.0.3 | DynamoDB persistence utilities |
| `@avaldigitallabs/bocc-pagos-lib-axios` | ^1.0.1 | Enhanced HTTP client with retry logic |
| `@avaldigitallabs/bocc-pagos-lib-aws-sqs` | ^1.0.2 | SQS messaging for analytics |

## Installation

### Prerequisites
- Node.js 18+
- AWS CLI configured
- JFROG credentials configured

### Setup
\`\`\`bash
npm install
npm run build
\`\`\`

### Environment Variables
See [DEPLOYMENT.md](./DEPLOYMENT.md) for complete configuration.

## Basic Usage

### Request Example
\`\`\`bash
curl -X POST https://api.example.com/[endpoint] \\
  -H "Content-Type: application/json" \\
  -H "X-RqUID: 12345-67890" \\
  -H "X-Channel: WEB" \\
  -d '{"field": "value"}'
\`\`\`

### Response Example
\`\`\`json
{
  "statusCode": 200,
  "messageId": "SUCCESS",
  "message": "Operation completed successfully",
  "data": {
    "result": "success"
  }
}
\`\`\`

## Development

### Running Tests
\`\`\`bash
npm test                # Run all tests
npm run test:coverage   # Run with coverage report
\`\`\`

### Code Quality
\`\`\`bash
npm run lint           # Check linting
npm run prettier       # Check formatting
npm run audit          # Security audit
\`\`\`

### Build for Deployment
\`\`\`bash
npm run build          # Creates dist/[service-name].zip
\`\`\`

## Documentation
- [API Documentation](./API.md)
- [Deployment Guide](./DEPLOYMENT.md) 
- [E2E Cypher Setup](./E2E-CYPHER.md)
- [Architecture Details](./ARCHITECTURE.md)

## Support
- **Team**: ADL Digital Labs
- **Email**: support@avaldigitallabs.com
- **Issues**: [Project Issues URL]
```

## 2. API.md Template

```markdown
# [Service Name] API Documentation

## Overview
This document describes the REST API endpoints for [Service Name].

**Base URL**: `https://api.adl.example.com/[service]`

## Authentication
All requests require:
- **API Key**: `x-api-key` header
- **E2E Encryption**: Requests/responses are encrypted
- **HMAC Validation**: For backend-to-backend calls

## Standard Headers

### Required Headers
| Header | Type | Description |
|--------|------|-------------|
| `X-RqUID` | string | Unique request identifier |
| `X-Channel` | string | Request channel (WEB, MOBILE, API) |
| `X-CompanyId` | string | Company identifier |
| `X-IdentSerialNum` | string | Identity serial number |
| `X-GovIssueIdentType` | string | Government identity type |
| `X-IPAddr` | string | Client IP address |
| `X-ClientDt` | string | Client timestamp (ISO 8601) |
| `X-FirstName` | string | User first name |
| `X-LastName` | string | User last name |
| `X-CustIdentType` | string | Customer identity type |
| `X-CustIdentNum` | string | Customer identity number |
| `X-TypeTransaction` | string | Transaction type |
| `X-CameraReference` | string | Camera reference |
| `User-Agent` | string | User agent string |

## Endpoints

### POST /[operation-name]

**Description**: [Operation description]

**Headers**: All standard headers required

**Request Body**:
\`\`\`json
{
  "FirstName": "string (required)",
  "LastName": "string (required)",
  "NumDoc": "string (required)",
  "TypeDoc": "string (required, enum: CC|CE|TI|PP)",
  "RefInfo": {
    "RefType": "string (required)",
    "RefId": "string (required)"
  },
  "Email": "string (optional)",
  "Phone": "string (optional)",
  "[additional fields based on service]": "..."
}
\`\`\`

**Response Success (200)**:
\`\`\`json
{
  "statusCode": 200,
  "messageId": "SUCCESS",
  "message": "Operation completed successfully",
  "timestamp": "2023-12-01T10:30:00.000Z",
  "data": {
    "[service-specific response fields]": "..."
  }
}
\`\`\`

**Response Error (4xx/5xx)**:
\`\`\`json
{
  "statusCode": 400,
  "messageId": "VALIDATION_ERROR",
  "message": "Input validation failed",
  "timestamp": "2023-12-01T10:30:00.000Z",
  "details": [
    {
      "field": "fieldName",
      "message": "validation error message"
    }
  ]
}
\`\`\`

## Error Codes

| Status Code | Message ID | Description |
|-------------|------------|-------------|
| 200 | SUCCESS | Operation completed successfully |
| 400 | VALIDATION_ERROR | Input validation failed |
| 401 | AUTH_ERROR | Authentication failed |
| 422 | BUSINESS_ISSUE | Business logic error |
| 500 | INTERNAL_ERROR | Internal server error |
| 503 | EXTERNAL_SERVICE_ERROR | External service unavailable |

## Rate Limits
- **Requests per minute**: 1000
- **Concurrent requests**: 100

## Examples

### cURL Example
\`\`\`bash
curl -X POST https://api.adl.example.com/[service]/[operation] \\
  -H "Content-Type: application/json" \\
  -H "x-api-key: YOUR_API_KEY" \\
  -H "X-RqUID: $(uuidgen)" \\
  -H "X-Channel: API" \\
  -d '{
    "FirstName": "John",
    "LastName": "Doe",
    "NumDoc": "12345678",
    "TypeDoc": "CC",
    "RefInfo": {
      "RefType": "PAYMENT",
      "RefId": "PAY-001"
    }
  }'
\`\`\`

### JavaScript Example
\`\`\`javascript
const axios = require('axios');

const response = await axios.post(
  'https://api.adl.example.com/[service]/[operation]',
  {
    FirstName: 'John',
    LastName: 'Doe',
    NumDoc: '12345678',
    TypeDoc: 'CC',
    RefInfo: {
      RefType: 'PAYMENT',
      RefId: 'PAY-001'
    }
  },
  {
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': 'YOUR_API_KEY',
      'X-RqUID': crypto.randomUUID(),
      'X-Channel': 'API'
    }
  }
);
\`\`\`

### Python Example
\`\`\`python
import requests
import uuid

response = requests.post(
    'https://api.adl.example.com/[service]/[operation]',
    json={
        'FirstName': 'John',
        'LastName': 'Doe', 
        'NumDoc': '12345678',
        'TypeDoc': 'CC',
        'RefInfo': {
            'RefType': 'PAYMENT',
            'RefId': 'PAY-001'
        }
    },
    headers={
        'Content-Type': 'application/json',
        'x-api-key': 'YOUR_API_KEY',
        'X-RqUID': str(uuid.uuid4()),
        'X-Channel': 'API'
    }
)
\`\`\`

## Testing
Use the provided test data in `/scripts/insert-test-data.sh` for development and testing.
```

## 3. DEPLOYMENT.md Template

```markdown
# [Service Name] - Deployment Guide

## Prerequisites

### Required Tools
- [Node.js 18+](https://nodejs.org/)
- [AWS CLI](https://aws.amazon.com/cli/) configured
- [Serverless Framework](https://www.serverless.com/) (if applicable)
- JFROG credentials configured

### Required Permissions
- Lambda function deployment
- API Gateway configuration  
- Parameter Store access
- SQS queue access
- CloudWatch logs access

## Environment Configuration

### Environment Variables

#### Core Configuration
\`\`\`bash
# Service endpoints
url_token_service=https://auth-service.example.com/token
url_external_service=https://external-service.example.com/api

# Authentication
api_key=your-api-key-here
client_id=your-client-id
client_secret=your-client-secret

# Token caching
ttl_cache=3600
table_cache=token-cache-table
\`\`\`

#### E2E Cypher Configuration (OBLIGATORY)
\`\`\`bash
# E2E Cipher settings
adl_rest_cipher_enabled=true
adl_rest_cipher_enableEncrypt=true
adl_rest_cipher_timeout=40
adl_rest_cipher_serverTimeout=1800

# Cryptographic keys (store in Parameter Store)
adl_rest_cipher_rsaPrivateKey=/adl/[service]/rsa/private
adl_rest_cipher_rsaPublicKey=/adl/[service]/rsa/public
adl_rest_cipher_hmacKey=/adl/[service]/hmac/key
\`\`\`

#### Certificate Configuration (if applicable)
\`\`\`bash
cert_pfx=/opt/certificates/client.pfx
passphrase=certificate-passphrase
\`\`\`

### AWS Parameter Store Setup

#### Store RSA Keys
\`\`\`bash
# Generate RSA key pair (3072 bits minimum)
openssl genpkey -algorithm RSA -out private.pem -pkeyopt rsa_keygen_bits:3072
openssl rsa -pubout -in private.pem -out public.pem

# Convert to base64
PRIVATE_B64=$(cat private.pem | base64 -w 0)
PUBLIC_B64=$(cat public.pem | base64 -w 0)
HMAC_KEY=$(openssl rand -base64 32)

# Store in Parameter Store
aws ssm put-parameter \\
  --name "/adl/[service]/rsa/private" \\
  --value "$PRIVATE_B64" \\
  --type "SecureString"

aws ssm put-parameter \\
  --name "/adl/[service]/rsa/public" \\
  --value "$PUBLIC_B64" \\
  --type "SecureString"

aws ssm put-parameter \\
  --name "/adl/[service]/hmac/key" \\
  --value "$HMAC_KEY" \\
  --type "SecureString"
\`\`\`

## Deployment Steps

### Development Environment

1. **Install Dependencies**
   \`\`\`bash
   npm install
   \`\`\`

2. **Run Tests**
   \`\`\`bash
   npm test
   npm run test:coverage
   \`\`\`

3. **Code Quality Checks**
   \`\`\`bash
   npm run lint
   npm run prettier
   npm audit
   \`\`\`

4. **Build Package**
   \`\`\`bash
   npm run build
   \`\`\`

### Staging Environment

1. **Configure Environment Variables**
   \`\`\`bash
   export STAGE=staging
   export REGION=us-east-1
   \`\`\`

2. **Deploy Lambda Function**
   \`\`\`bash
   # Using AWS CLI
   aws lambda update-function-code \\
     --function-name [service-name]-staging \\
     --zip-file fileb://dist/[service-name].zip

   # Or using Serverless
   serverless deploy --stage staging
   \`\`\`

3. **Configure API Gateway** (if not using Serverless)
   \`\`\`bash
   aws apigateway update-integration \\
     --rest-api-id [api-id] \\
     --resource-id [resource-id] \\
     --http-method POST \\
     --patch-ops op=replace,path=/uri,value=arn:aws:apigateway:[region]:lambda:path/2015-03-31/functions/[function-arn]/invocations
   \`\`\`

### Production Environment

1. **Security Validation**
   \`\`\`bash
   # Verify Parameter Store keys exist
   aws ssm get-parameter --name "/adl/[service]/rsa/private" --with-decryption
   
   # Check function configuration
   aws lambda get-function-configuration --function-name [service-name]-prod
   \`\`\`

2. **Deploy with Blue/Green**
   \`\`\`bash
   # Create alias for new version
   aws lambda publish-version --function-name [service-name]-prod
   aws lambda update-alias --function-name [service-name]-prod --name live --function-version $LATEST
   \`\`\`

3. **Health Check**
   \`\`\`bash
   curl -X GET https://api.prod.example.com/[service]/health
   \`\`\`

## Monitoring Setup

### CloudWatch Alarms
\`\`\`bash
# Error rate alarm
aws cloudwatch put-metric-alarm \\
  --alarm-name "[service-name]-error-rate" \\
  --alarm-description "Lambda error rate > 5%" \\
  --metric-name Errors \\
  --namespace AWS/Lambda \\
  --statistic Sum \\
  --period 300 \\
  --threshold 5 \\
  --comparison-operator GreaterThanThreshold

# Duration alarm  
aws cloudwatch put-metric-alarm \\
  --alarm-name "[service-name]-duration" \\
  --alarm-description "Lambda duration > 25s" \\
  --metric-name Duration \\
  --namespace AWS/Lambda \\
  --statistic Average \\
  --period 300 \\
  --threshold 25000 \\
  --comparison-operator GreaterThanThreshold
\`\`\`

### Custom Metrics
The service automatically sends analytics to SQS for business monitoring.

## Rollback Procedures

### Quick Rollback
\`\`\`bash
# Revert to previous version
aws lambda update-alias \\
  --function-name [service-name]-prod \\
  --name live \\
  --function-version [previous-version]
\`\`\`

### Complete Rollback
\`\`\`bash
# Restore previous deployment package
aws lambda update-function-code \\
  --function-name [service-name]-prod \\
  --zip-file fileb://backup/[service-name]-previous.zip
\`\`\`

## Troubleshooting

### Common Issues

**Issue**: "RSA key format error"
\`\`\`bash
# Verify key format
openssl rsa -in private.pem -text -noout
\`\`\`

**Issue**: "Token service timeout"
\`\`\`bash
# Check network connectivity
aws lambda invoke \\
  --function-name [service-name] \\
  --payload '{"httpMethod":"GET","path":"/health"}' \\
  response.json
\`\`\`

**Issue**: "SQS send failed"
\`\`\`bash
# Verify SQS permissions
aws sts get-caller-identity
aws sqs get-queue-attributes --queue-url [queue-url]
\`\`\`

### Logs Analysis
\`\`\`bash
# View recent logs
aws logs tail /aws/lambda/[service-name] --follow

# Filter error logs
aws logs filter-log-events \\
  --log-group-name /aws/lambda/[service-name] \\
  --filter-pattern "ERROR"
\`\`\`

## Performance Tuning

### Lambda Configuration
- **Memory**: 512MB (adjust based on load testing)
- **Timeout**: 30 seconds
- **Concurrent executions**: 100

### Optimization Tips
1. Keep Lambda warm with scheduled pings
2. Optimize dependency bundle size
3. Use connection pooling for external services
4. Cache tokens appropriately (TTL: 1 hour)

## Security Checklist

- ✅ RSA keys stored in Parameter Store
- ✅ API keys secured  
- ✅ HTTPS only communication
- ✅ Input validation enabled
- ✅ Error logging without PII
- ✅ Rate limiting configured
- ✅ CORS properly configured
```

## 4. E2E-CYPHER.md Template

```markdown
# E2E Cypher Configuration - [Service Name]

## Overview
This document describes the End-to-End encryption setup using the ADL commons library `@avaldigitallabs/adl-commons-lib-e2e-cypher`.

> 🚨 **IMPORTANT**: This library provides RSA + AES encryption for secure communication between frontend and backend services.

## Prerequisites
- [OpenSSL](https://www.openssl.org/) for key generation
- AWS Parameter Store access
- RSA keys of **3072 bits or higher**

## Key Generation

### Generate RSA Key Pair
\`\`\`bash
# Generate 3072-bit private key
openssl genpkey -algorithm RSA -out PRIVATE.pem -pkeyopt rsa_keygen_bits:3072

# Extract public key
openssl rsa -pubout -in PRIVATE.pem -out PUBLIC.pem

# Generate HMAC key for message signing
openssl rand -base64 32 > HMAC_KEY.txt
\`\`\`

### Convert to Base64 for Storage
\`\`\`bash
# Convert keys to base64 (single line)
cat PRIVATE.pem | base64 -w 0 > PRIVATE_BASE64.txt
cat PUBLIC.pem | base64 -w 0 > PUBLIC_BASE64.txt
cat HMAC_KEY.txt | base64 -w 0 > HMAC_BASE64.txt
\`\`\`

### Store in AWS Parameter Store
\`\`\`bash
# Store private key
aws ssm put-parameter \\
    --name "/adl/[service-name]/rsa/private" \\
    --value "$(cat PRIVATE_BASE64.txt)" \\
    --type "SecureString" \\
    --description "RSA private key for [service-name] E2E encryption"

# Store public key
aws ssm put-parameter \\
    --name "/adl/[service-name]/rsa/public" \\
    --value "$(cat PUBLIC_BASE64.txt)" \\
    --type "SecureString" \\
    --description "RSA public key for [service-name] E2E encryption"

# Store HMAC key
aws ssm put-parameter \\
    --name "/adl/[service-name]/hmac/key" \\
    --value "$(cat HMAC_BASE64.txt)" \\
    --type "SecureString" \\
    --description "HMAC key for [service-name] message signing"
\`\`\`

## Lambda Environment Configuration

### Required Environment Variables
\`\`\`bash
# E2E Cypher Configuration
adl_rest_cipher_enabled=true
adl_rest_cipher_enableEncrypt=true
adl_rest_cipher_timeout=40
adl_rest_cipher_serverTimeout=1800

# Cryptographic Keys (reference Parameter Store)
adl_rest_cipher_rsaPrivateKey=\${ssm:/adl/[service-name]/rsa/private}
adl_rest_cipher_rsaPublicKey=\${ssm:/adl/[service-name]/rsa/public}
adl_rest_cipher_hmacKey=\${ssm:/adl/[service-name]/hmac/key}
\`\`\`

### Optional Configuration (for public key distribution)
\`\`\`bash
# For services that need to distribute their public key
adl_rest_cipher_publicKeyPath=/public-key
adl_rest_cipher_enableServer=true
\`\`\`

## Implementation Patterns

### 1. Standard Lambda Proxy Pattern (RECOMMENDED)
\`\`\`javascript
const e2e = require('@avaldigitallabs/adl-commons-lib-e2e-cypher');

// Your business logic handler
const yourHandler = async (event) => {
    // Requests are automatically decrypted
    // Responses are automatically encrypted
    
    // Your business logic here...
    return {
        statusCode: 200,
        body: JSON.stringify({
            message: "Success",
            data: result
        })
    };
};

// Wrap with E2E handler (MANDATORY)
exports.handler = e2e.setHandler(yourHandler);
\`\`\`

### 2. Public Key Distribution Pattern
\`\`\`javascript
const e2e = require('@avaldigitallabs/adl-commons-lib-e2e-cypher');

exports.handler = async (event, context) => {
    // Handle GET requests for public key distribution
    if (event.httpMethod === 'GET' && event.path === '/public-key') {
        return e2e.publicKeyHandler(event.queryStringParameters?.timestamp);
    }
    
    // Handle other requests with E2E encryption
    return e2e.setHandler(yourBusinessHandler)(event, context);
};

const yourBusinessHandler = async (event) => {
    // Business logic with encrypted requests/responses
};
\`\`\`

### 3. Backend-to-Backend Communication
\`\`\`javascript
const e2e = require('@avaldigitallabs/adl-commons-lib-e2e-cypher');
const axios = require('axios');

class ExternalServiceImpl {
    async callAnotherADLService(requestData) {
        // Sign the request for HMAC validation
        const hmacSignature = e2e.signMessage(requestData);
        
        const response = await axios({
            url: process.env.url_external_adl_service,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'HMAC': hmacSignature,  // Required for ADL services
                'x-api-key': process.env.api_key
            },
            data: JSON.stringify(requestData)
        });
        
        return response.data;
    }
}
\`\`\`

### 4. Manual Encryption/Decryption (ADVANCED)
\`\`\`javascript
const e2e = require('@avaldigitallabs/adl-commons-lib-e2e-cypher');

exports.handler = async (event) => {
    try {
        // Manual decryption
        const decryptedBody = e2e.decrypt(event);
        const requestData = JSON.parse(decryptedBody);
        
        // Process business logic
        const result = await processRequest(requestData);
        
        // Manual encryption
        const encryptedResponse = e2e.encrypt(JSON.stringify(result));
        
        return {
            statusCode: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                data: encryptedResponse
            })
        };
    } catch (error) {
        // Handle encryption/decryption errors
        throw error;
    }
};
\`\`\`

## Public Key Distribution Response

When a client requests the public key, the service returns:

\`\`\`json
{
    "publicKey": "LS0tLS1CRUdJTi...BASE64_PUBLIC_KEY",
    "timestamp": {
        "server": 1701387041706,
        "client": 1701387041897,
        "hash": "a1b2c3d4e5f6g7h8..."
    }
}
\`\`\`

## Security Considerations

### Key Management
- ✅ Use RSA keys of **3072 bits minimum**
- ✅ Store keys in AWS Parameter Store (SecureString)
- ✅ Implement key rotation policies
- ✅ Never hardcode keys in source code

### Operational Security
- ✅ Enable CloudTrail logging for Parameter Store access
- ✅ Monitor failed decryption attempts
- ✅ Set up alerts for HMAC validation failures
- ✅ Regular security audits of encryption implementation

### Development Security
- ✅ Use separate keys for dev/staging/production
- ✅ Never commit keys to version control
- ✅ Implement proper error handling for encryption failures
- ✅ Log security events (without exposing sensitive data)

## Testing E2E Encryption

### Unit Tests
\`\`\`javascript
const e2e = require('@avaldigitallabs/adl-commons-lib-e2e-cypher');

describe('E2E Encryption', () => {
    test('should encrypt and decrypt correctly', () => {
        const testData = { message: 'test' };
        const encrypted = e2e.encrypt(JSON.stringify(testData));
        const decrypted = e2e.decrypt({ body: encrypted });
        
        expect(JSON.parse(decrypted)).toEqual(testData);
    });
    
    test('should generate valid HMAC signature', () => {
        const testData = { message: 'test' };
        const signature = e2e.signMessage(testData);
        
        expect(signature).toBeDefined();
        expect(typeof signature).toBe('string');
    });
});
\`\`\`

### Integration Tests
\`\`\`javascript
const request = require('supertest');

describe('E2E Integration', () => {
    test('should handle encrypted request', async () => {
        const testPayload = { 
            FirstName: 'Test',
            LastName: 'User',
            NumDoc: '12345678',
            TypeDoc: 'CC'
        };
        
        // Encrypt payload (in real scenario, client would do this)
        const encryptedPayload = e2e.encrypt(JSON.stringify(testPayload));
        
        const response = await request(app)
            .post('/api/endpoint')
            .send({ data: encryptedPayload })
            .expect(200);
        
        // Response should be encrypted
        expect(response.body.data).toBeDefined();
    });
});
\`\`\`

## Troubleshooting

### Common Issues

**Error: "Invalid RSA key format"**
\`\`\`bash
# Verify key format
openssl rsa -in PRIVATE.pem -text -noout

# Check base64 encoding
cat PRIVATE_BASE64.txt | base64 -d | openssl rsa -text -noout
\`\`\`

**Error: "HMAC validation failed"**
\`\`\`javascript
// Debug HMAC generation
const testData = { test: 'data' };
const signature = e2e.signMessage(testData);
console.log('Generated HMAC:', signature);
console.log('Request data:', JSON.stringify(testData));
\`\`\`

**Error: "Decryption timeout"**
\`\`\`bash
# Increase timeout in environment variables
adl_rest_cipher_timeout=60
adl_rest_cipher_serverTimeout=3600
\`\`\`

**Error: "Parameter Store access denied"**
\`\`\`bash
# Check IAM permissions
aws sts get-caller-identity
aws ssm describe-parameters --max-items 10
\`\`\`

### Debug Mode
\`\`\`bash
# Enable detailed logging
export NODE_ENV=development
export ADL_CIPHER_DEBUG=true
\`\`\`

### Health Checks
\`\`\`bash
# Test public key endpoint
curl -X GET "https://api.example.com/[service]/public-key?timestamp=$(date +%s)000"

# Test encrypted endpoint (requires encrypted payload)
curl -X POST https://api.example.com/[service]/endpoint \\
    -H "Content-Type: application/json" \\
    -d '{"data": "ENCRYPTED_PAYLOAD_HERE"}'
\`\`\`

## Performance Considerations

### Optimization Tips
1. **Key Caching**: Keys are automatically cached by the library
2. **Connection Pooling**: Use persistent connections for external calls
3. **Payload Size**: Large payloads increase encryption/decryption time
4. **Concurrent Requests**: Monitor Lambda concurrent execution limits

### Monitoring Metrics
- **E2E Latency**: Time spent on encryption/decryption
- **HMAC Validation Success Rate**: Monitor authentication success
- **Key Rotation Events**: Track key management operations
- **Decryption Failures**: Alert on anomalous patterns

## Key Rotation Procedure

### Manual Rotation
\`\`\`bash
# 1. Generate new key pair
openssl genpkey -algorithm RSA -out PRIVATE_NEW.pem -pkeyopt rsa_keygen_bits:3072
openssl rsa -pubout -in PRIVATE_NEW.pem -out PUBLIC_NEW.pem

# 2. Update Parameter Store
aws ssm put-parameter \\
    --name "/adl/[service-name]/rsa/private" \\
    --value "$(cat PRIVATE_NEW.pem | base64 -w 0)" \\
    --type "SecureString" \\
    --overwrite

# 3. Restart Lambda function
aws lambda update-function-configuration \\
    --function-name [service-name] \\
    --description "Key rotation $(date)"
\`\`\`

### Automated Rotation (Recommended)
Implement automated key rotation using AWS Lambda and EventBridge for production environments.
```

## 5. ARCHITECTURE.md Template

```markdown
# [Service Name] - Architecture Documentation

## System Overview

### Purpose
[Brief description of the service's business purpose and role in the broader system]

### Architecture Pattern
This service implements **Hexagonal Architecture** (Clean Architecture) with the following characteristics:
- **Domain-driven design** with clear separation of concerns
- **Dependency inversion** using Inversify IoC container
- **Clean interfaces** between layers
- **Testable architecture** with dependency injection

## High-Level Architecture

\`\`\`
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   API Gateway   │────│   Lambda Proxy   │────│  External APIs  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │
                       ┌────────▼────────┐
                       │  E2E Cypher     │
                       │  (Encryption)   │
                       └────────┬────────┘
                                │
                    ┌───────────▼───────────┐
                    │   Controller Layer    │
                    │  (Infrastructure)     │
                    └───────────┬───────────┘
                                │
                    ┌───────────▼───────────┐
                    │  Application Layer    │
                    │  (Business Logic)     │
                    └───────────┬───────────┘
                                │
                    ┌───────────▼───────────┐
                    │   Domain Layer        │
                    │  (Models & Rules)     │
                    └───────────────────────┘
\`\`\`

## Layer Architecture

### 1. Infrastructure Layer
**Location**: `src/infrastructure/`
**Responsibility**: External concerns and technical details

Components:
- **Controllers**: Handle HTTP requests and responses
- **External Service Adapters**: Integrate with external APIs
- **Token Service**: Manage authentication tokens
- **SQS Manager**: Handle analytics messaging
- **Error Logger**: Centralized error handling

\`\`\`
infrastructure/
├── [service-name]-controller.js      # Main request handler
├── token-service-impl.js            # Authentication service
├── external-service-impl.js         # External API integration
├── data-retrieval-service-impl.js   # Data fetching service
├── sqs-manager-impl.js              # Analytics messaging
└── error-logger.js                  # Error management
\`\`\`

### 2. Application Layer
**Location**: `src/application/`
**Responsibility**: Orchestrate business workflows

Components:
- **Service Implementations**: Coordinate between domain and infrastructure
- **Use Case Handlers**: Implement specific business scenarios
- **Workflow Orchestrators**: Manage complex business processes

\`\`\`
application/
├── [service-name]-service-impl.js   # Main business logic
├── [service-name]-service.js        # Service interface
└── workflows/                       # Complex business workflows
\`\`\`

### 3. Domain Layer
**Location**: `src/domain/`
**Responsibility**: Core business logic and rules

Components:
- **Entities**: Business objects with behavior
- **Models**: Data structures and validation schemas
- **Value Objects**: Immutable business concepts
- **Domain Services**: Business logic that doesn't belong to entities

\`\`\`
domain/
├── entities/
│   ├── object-mapper-impl.js        # Data transformation
│   ├── validate-object-impl.js      # Business validation
│   └── interfaces/                  # Entity contracts
├── models/
│   ├── body-request.js             # Request models
│   ├── headers-model.js            # Header validation
│   ├── dictionaries/               # Constants and enums
│   └── enums/                      # Business enumerations
└── services/                       # Domain-specific services
\`\`\`

## Data Flow

### Request Processing Flow
\`\`\`
1. API Gateway → Lambda Proxy
2. E2E Cypher → Decrypt Request
3. Controller → Receive HTTP Event
4. Application Service → Process Business Logic
5. Domain Entities → Validate & Transform
6. Infrastructure Services → External API Calls
7. Application Service → Aggregate Results
8. Controller → Format Response
9. E2E Cypher → Encrypt Response
10. API Gateway → Return to Client
\`\`\`

### Detailed Processing Steps

#### 1. Request Reception & Decryption
```javascript
// Automatic E2E decryption via wrapper
exports.handler = e2e.setHandler(yourHandler);
```

#### 2. Validation Pipeline
```javascript
// Concurrent validation of headers and body
Promise.all([
    validateObject.executeValidateObject(headers, headersSchema),
    validateObject.executeValidateObject(body, bodyRequestSchema)
]);
```

#### 3. Authentication Flow
```javascript
// Token retrieval with caching
const token = await tokenService.getTokenFromSession(tokenInfo);
```

#### 4. External Service Integration
```javascript
// Secure API call with certificates (if needed)
const response = await externalService.sendRequest(requestOptions);
```

#### 5. Analytics Pipeline
```javascript
// Business analytics via SQS
const analyticsEvent = mapper.buildEventAnalytic(headers, details, success);
await sqsManager.sendMessage(analyticsEvent);
```

## Integration Patterns

### External Service Integration
```javascript
class ExternalServiceImpl {
    async sendRequest(options) {
        // Retry logic with exponential backoff
        // Certificate-based authentication
        // Response validation and error handling
    }
}
```

### Token Management Pattern
```javascript
class TokenServiceImpl {
    async getTokenFromSession(tokenInfo) {
        // Check cache first
        // Request new token if expired
        // Handle authentication failures
    }
}
```

### Analytics Pattern
```javascript
class SqsManagerImpl {
    async sendMessage(eventData) {
        // Asynchronous analytics
        // Non-blocking business flow
        // Error resilience
    }
}
```

## Security Architecture

### End-to-End Encryption
- **RSA 3072+ bit keys** for asymmetric encryption
- **AES encryption** for payload security
- **HMAC validation** for message integrity
- **Key rotation** capabilities

### Authentication & Authorization
- **OAuth2 client credentials** flow
- **Token caching** with TTL
- **API key validation**
- **Certificate-based mutual TLS** (when required)

### Input Validation
- **Joi schema validation** for all inputs
- **Header sanitization**
- **Business rule validation**
- **XSS/Injection prevention**

## Performance Considerations

### Optimization Strategies
1. **Token Caching**: Reduce authentication overhead
2. **Connection Pooling**: Reuse HTTP connections
3. **Concurrent Operations**: Parallel validation and processing
4. **Efficient Error Handling**: Fail-fast principles

### Scaling Characteristics
- **Stateless design** for horizontal scaling
- **Lambda auto-scaling** based on demand
- **SQS buffering** for analytics spikes
- **Circuit breaker pattern** for external dependencies

## Monitoring & Observability

### Logging Strategy
```javascript
// Structured logging with correlation IDs
const logger = {
    info: (message, context) => console.log(JSON.stringify({
        level: 'INFO',
        message,
        correlationId: context.correlationId,
        timestamp: new Date().toISOString(),
        service: '[service-name]'
    }))
};
```

### Key Metrics
- **Request latency** (p50, p95, p99)
- **Error rates** by type
- **External service response times**
- **Token cache hit rates**
- **E2E encryption/decryption latency**

### Health Checks
```javascript
// Service health endpoint
app.get('/health', (req, res) => {
    res.json({
        service: '[service-name]',
        status: 'healthy',
        timestamp: new Date().toISOString(),
        version: process.env.SERVICE_VERSION
    });
});
```

## Error Handling Strategy

### Error Classification
1. **Validation Errors** (400): Client input issues
2. **Authentication Errors** (401): Token/credential issues  
3. **Business Errors** (422): Business rule violations
4. **External Service Errors** (503): Dependency failures
5. **System Errors** (500): Internal failures

### Error Response Format
```json
{
    "statusCode": 400,
    "messageId": "VALIDATION_ERROR",
    "message": "Input validation failed",
    "timestamp": "2023-12-01T10:30:00.000Z",
    "correlationId": "12345-67890-abcdef",
    "details": [
        {
            "field": "fieldName",
            "message": "validation error description"
        }
    ]
}
```

## Deployment Architecture

### Environment Separation
- **Development**: Local development with mocked services
- **Staging**: Full integration testing environment
- **Production**: Live environment with monitoring

### Infrastructure Components
- **AWS Lambda**: Serverless compute
- **API Gateway**: HTTP routing and throttling
- **Parameter Store**: Secure configuration management
- **SQS**: Asynchronous analytics processing
- **CloudWatch**: Logging and monitoring

## Testing Strategy

### Testing Pyramid
```
┌─────────────────┐
│  E2E Tests      │  5%
├─────────────────┤
│ Integration     │  15%  
├─────────────────┤
│  Unit Tests     │  80%
└─────────────────┘
```

### Test Types
1. **Unit Tests**: Individual component testing
2. **Integration Tests**: Service interaction testing
3. **Contract Tests**: API contract validation
4. **End-to-End Tests**: Full workflow validation

## Quality Gates

### Code Quality Requirements
- **Test Coverage**: Minimum 85%
- **Linting**: ESLint compliance
- **Formatting**: Prettier standards
- **Security**: npm audit clean
- **Performance**: Load testing validation

### Deployment Gates
- ✅ All tests passing
- ✅ Code quality checks
- ✅ Security vulnerability scan
- ✅ Performance benchmarks met
- ✅ Documentation updated

## Future Considerations

### Scalability Roadmap
1. **Read Replicas**: For high-read scenarios
2. **Caching Layer**: Redis for frequently accessed data
3. **Event-Driven Architecture**: Microservices communication
4. **Multi-Region Deployment**: Global availability

### Technology Evolution
- **Container Migration**: From Lambda to containers if needed
- **GraphQL Integration**: For complex data requirements
- **Streaming Analytics**: Real-time business insights
- **ML Integration**: Intelligent business logic
```

## Instructions for AI Agents

### When to Use Each Template
1. **README.md**: Always generate - provides overview and setup
2. **API.md**: For services with HTTP endpoints
3. **DEPLOYMENT.md**: Always generate - deployment is crucial
4. **E2E-CYPHER.md**: Always generate - security is mandatory
5. **ARCHITECTURE.md**: For complex services or when requested

### Customization Guidelines
- Replace all `[service-name]` placeholders with actual service name
- Update dependency versions if newer stable versions available
- Add service-specific endpoints and schemas to API.md
- Include actual environment variable values in DEPLOYMENT.md
- Customize architecture diagrams based on specific service needs

### Quality Requirements
- All documentation must be complete and accurate
- Code examples must be syntactically correct
- All required sections must be included
- Links and references must be valid
