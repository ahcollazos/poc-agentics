# Credit Cards Query API - Technical Requirements

## Overview
Build endpoint `/credit-cards` for credit card consultation and publish it in API Gateway.

## Field Tab Information
- **Cycle Time**: [To be defined]
- **Description**: Credit card query endpoint implementation

---

## Objective
Create a Lambda function to query a client's credit cards. The function should consume the service that returns credit cards (TC) for a client and persist this information in the payment items table.

---

## Technical Requirements

### Service Integration
- Modify the public services query Lambda to consume the service: `cardsManagement/v1/Administration_CreditCard/CreditCard/Cards`
- Send client's document type and number as request parameters

### Data Processing and Mapping
- Process the external service response
- Map data to two columns:
  - `Card`: Summary of information returned by the service
  - `Data`: Complete service response object
- Format information according to attached specifications

---

## Acceptance Criteria

### ✅ Service Consumption
- [ ] Lambda function modified to consume `cardsManagement/v1/Administration_CreditCard/CreditCard/Cards`
- [ ] Document type and number sent as request parameters
- [ ] Proper error handling for service calls

### ✅ Data Formatting and Mapping
- [ ] External service response processed correctly
- [ ] Information summarized and formatted per sspecifications
- [ ] `Card` column populated with summary data
- [ ] `Data` column populated with complete service response

### ✅ Database Integration
- [ ] Data persisted in `payment-item-client` DynamoDB table
- [ ] Proper JSON formatting for `Data` and `Card` fields
- [ ] Document ID used as hash key

---

## Database Schema

### DynamoDB Table: `payment-item-client`

```hcl
resource "aws_dynamodb_table" "payment_item_client" {
  name           = "payment-item-client"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "document_id"

  attribute {
    name = "document_id"
    type = "S"
  }

  attribute {
    name = "Client"
    type = "S"
  }

  # Data and Card fields stored as JSON strings
  attribute {
    name = "Data"
    type = "S"
  }

  attribute {
    name = "Card"
    type = "S"
  }
}
```

### Table Structure
| Field | Type | Description |
|-------|------|-------------|
| `documentKey` | String (S) | Hash key - Client document identifier |
| `clientDocument` | String (S) | Client information |
| `data` | String (S) | Complete service response (JSON format) |
| `card` | String (S) | Summarized card information (JSON format) |

---

## Implementation Tasks

### 1. Lambda Function Development
- [ ] Create/modify Lambda function for credit card queries
- [ ] Implement service client for `cardsManagement` API
- [ ] Add request/response logging
- [ ] Implement error handling and retries

### 2. Data Transformation
- [ ] Create mapping logic for service response
- [ ] Implement data summarization for `Card` field
- [ ] Ensure JSON serialization for DynamoDB storage

### 3. Database Operations
- [ ] Implement DynamoDB client
- [ ] Create item insertion/update logic
- [ ] Handle duplicate entries appropriately

### 4. API Gateway Integration
- [ ] Create API Gateway endpoint `/credit-cards`
- [ ] Configure Lambda integration
- [ ] Set up proper HTTP methods and responses
- [ ] Add authentication/authorization

### 5. Testing
- [ ] Unit tests for Lambda function
- [ ] Integration tests with external service
- [ ] DynamoDB operation tests
- [ ] End-to-end API testing

---

## Dependencies

### External Services
- `cardsManagement/v1/Administration_CreditCard/CreditCard/Cards`

### AWS Resources
- Lambda Function
- DynamoDB Table: `payment-item-client`
- API Gateway
- IAM Roles and Policies

---

## Notes for AI Agents

### Context Requirements
- Client document type and number are required inputs
- Service response must be mapped to both summary and complete formats
- DynamoDB table already exists with defined schema
- JSON serialization required for database storage

### Implementation Priorities
1. Service integration and error handling
2. Data mapping and transformation
3. Database persistence
4. API Gateway configuration
5. Testing and validation

### Key Considerations
- Handle service timeouts and failures gracefully
- Ensure data consistency in DynamoDB operations
- Validate input parameters before service calls
- Log all operations for debugging and monitoring
