# ADL Parameter Store - AI Agent Context

## Library Information
**Package**: `@avaldigitallabs/adl-commons-lib-node-pp-parameter-store`  
**Purpose**: Load AWS Parameter Store values into Node.js environment variables  
**Compatibility**: Node.js 16+  
**Team**: DevOps ADL  

## Installation Command
```bash
npm install @avaldigitallabs/adl-commons-lib-node-pp-parameter-store
```

## Required Prerequisites
- Git installed
- Node.js 16+ installed  
- NPM package manager
- JFROG credentials configured

## Core Functionality
This library automatically:
1. Connects to AWS Systems Manager Parameter Store
2. Retrieves parameters based on configuration
3. Sets them as environment variables in Node.js runtime
4. Supports both static .env files and dynamic Parameter Store values

## Implementation Pattern (CRITICAL - USE EXACTLY)
```javascript
// MUST be the first line in your entry point file
require("@avaldigitallabs/adl-commons-lib-node-pp-parameter-store");

exports.handler = async (event, context) => {
    // Now all Parameter Store values are available in process.env
    console.log(process.env["YOUR-PARAMETER-NAME"]);
    
    // Your application logic here
};
```

## Environment Configuration (REQUIRED)
Set these environment variables to configure the library:

### Core Configuration
| Variable | Required | Description | Example Value |
|----------|----------|-------------|---------------|
| `adl_parameter_store_enabled` | YES | Enable/disable library | `"true"` |
| `adl_parameter_store_prefix` | NO | Common path prefix | `"/YOUR_PROJECT"` |
| `adl_parameter_store_path` | YES | Parameter paths | `"/YOUR_NAMESPACE"` |
| `adl_parameter_store_parameters` | NO | Specific parameter names | `"/YOUR_NAMESPACE/VAR1"` |

### Configuration Rules (IMPORTANT)
**WITH PREFIX**: If you set `adl_parameter_store_prefix`:
- Path should contain only namespace: `"/YOUR_NAMESPACE"`
- Parameters should be relative: `"/YOUR_NAMESPACE/VAR_NAME"`

**WITHOUT PREFIX**: If prefix is empty:
- Path must contain full path: `"/YOUR_PROJECT/YOUR_NAMESPACE"`
- Parameters must be absolute: `"/YOUR_PROJECT/YOUR_NAMESPACE/VAR_NAME"`

### Multiple Values Support
Use comma separation for multiple paths or parameters:
```yaml
adl_parameter_store_path: "/NAMESPACE1,/NAMESPACE2"
adl_parameter_store_parameters: "/NAMESPACE1/VAR1,/NAMESPACE2/VAR2"
```

## Lambda Configuration Example
```yaml
lambdas:
  - name: "example-lambda"
    handler: "src/index.handler"
    runtime: "nodejs16.x"
    stacks:
      - stack: "pro"
        environment:
          - adl_parameter_store_prefix: "/YOUR_PROJECT"
          - adl_parameter_store_path: "/YOUR_NAMESPACE"
          - adl_parameter_store_parameters: "/YOUR_NAMESPACE/VAR1"
          - adl_parameter_store_enabled: "true"
```

## Static Environment Variables (.env file)
You can combine with static .env file:
```env
STATIC_VAR=hello
COMBINED_VAR=${STATIC_VAR}:world
```

## Required IAM Permissions (CRITICAL)
Add these permissions to your Lambda/service policy:

```yaml
# Get parameters by path
- Effect: Allow
  Action:
    - ssm:GetParametersByPath
  Resource: 
    - "arn:aws:ssm:REGION:ACCOUNT:parameter/PROJECT/NAMESPACE"

# Get individual parameters  
- Effect: Allow
  Action:
    - ssm:GetParameter
  Resource: 
    - "arn:aws:ssm:REGION:ACCOUNT:parameter/PROJECT/NAMESPACE/*"

# Decrypt KMS-encrypted parameters (if needed)
- Effect: Allow
  Action:
    - kms:Decrypt
  Resource: 
    - "arn:aws:kms:REGION:ACCOUNT:key/KEY_ID"
```

## Parameter Store Structure Format
```yaml
version: 1
path: '/PROJECT/NAMESPACE/'
params:
  'PARAMETER_NAME_1': 'value1'
  'PARAMETER_NAME_2': 'value2'
  'DATABASE_URL': 'postgres://...'
```

## Common Implementation Patterns

### 1. Basic Lambda with Parameter Store
```javascript
require("@avaldigitallabs/adl-commons-lib-node-pp-parameter-store");

exports.handler = async (event, context) => {
    const dbUrl = process.env.DATABASE_URL;
    const apiKey = process.env.API_KEY;
    
    // Use parameters in your logic
    return { statusCode: 200, body: 'Success' };
};
```

### 2. Express.js Application
```javascript
require("@avaldigitallabs/adl-commons-lib-node-pp-parameter-store");
const express = require('express');

const app = express();
const port = process.env.PORT || 3000;

app.listen(port, () => {
    console.log(`Server running with DB: ${process.env.DATABASE_URL}`);
});
```

### 3. Microservice Configuration
```javascript
require("@avaldigitallabs/adl-commons-lib-node-pp-parameter-store");

class ConfigService {
    static getDbConfig() {
        return {
            host: process.env.DB_HOST,
            port: process.env.DB_PORT,
            username: process.env.DB_USER,
            password: process.env.DB_PASSWORD
        };
    }
}
```

## Error Handling Patterns
```javascript
require("@avaldigitallabs/adl-commons-lib-node-pp-parameter-store");

exports.handler = async (event, context) => {
    try {
        // Check if required parameters are loaded
        if (!process.env.REQUIRED_PARAM) {
            throw new Error('Required parameter not loaded from Parameter Store');
        }
        
        // Your logic here
        
    } catch (error) {
        console.error('Parameter Store error:', error);
        return { statusCode: 500, body: 'Configuration error' };
    }
};
```

## Troubleshooting Guidelines

### Common Issues and Solutions
1. **Parameters not loading**: Check IAM permissions and parameter paths
2. **KMS decrypt errors**: Verify KMS key permissions and ARNs
3. **Environment variables undefined**: Ensure library import is first line
4. **Path not found**: Verify parameter exists in AWS Parameter Store

### Debug Mode
Enable debug logging:
```bash
export DEBUG=adl-parameter-store:*
```

## Best Practices for AI Agents

### When generating code using this library:
1. **ALWAYS** put the require statement as the very first line
2. **VERIFY** environment variable configuration is complete
3. **INCLUDE** proper IAM permissions in deployment templates
4. **USE** prefix configuration for better organization
5. **VALIDATE** parameter existence before using in critical paths
6. **ENCRYPT** sensitive parameters using KMS
7. **SEPARATE** environments using different namespaces

### Security Considerations:
- Never log parameter values in production
- Use KMS encryption for sensitive data
- Follow principle of least privilege for IAM permissions
- Use different Parameter Store paths for different environments

### Performance Notes:
- Parameters are loaded once at container initialization
- Use caching mechanisms for frequently accessed values
- Consider cold start impact in serverless environments

## Integration with Other ADL Libraries
This library works seamlessly with:
- `@avaldigitallabs/adl-commons-lib-e2e-cypher`
- `@avaldigitallabs/bocc-pagos-lib-admin-token`
- Other ADL libraries that require configuration parameters

## Example Deployment Templates

### Serverless.yml
```yaml
service: my-service
provider:
  name: aws
  runtime: nodejs16.x
  environment:
    adl_parameter_store_enabled: "true"
    adl_parameter_store_prefix: "/myproject"
    adl_parameter_store_path: "/config"
  iamRoleStatements:
    - Effect: Allow
      Action:
        - ssm:GetParametersByPath
        - ssm:GetParameter
      Resource: 
        - "arn:aws:ssm:${aws:region}:${aws:accountId}:parameter/myproject/config*"
```

### CDK Example
```typescript
const lambda = new Function(this, 'MyFunction', {
    code: Code.fromAsset('dist'),
    handler: 'index.handler',
    runtime: Runtime.NODEJS_16_X,
    environment: {
        adl_parameter_store_enabled: 'true',
        adl_parameter_store_prefix: '/myproject',
        adl_parameter_store_path: '/config'
    }
});

lambda.addToRolePolicy(new PolicyStatement({
    effect: Effect.ALLOW,
    actions: ['ssm:GetParametersByPath', 'ssm:GetParameter'],
    resources: [`arn:aws:ssm:${this.region}:${this.account}:parameter/myproject/config*`]
}));
```

This context provides AI agents with comprehensive information to correctly implement and use the ADL Parameter Store library in Node.js applications.
