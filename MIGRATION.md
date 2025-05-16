# Migration from AWS SAM to Serverless Framework

This document explains the key differences and changes when migrating from AWS SAM to the Serverless Framework for this API project.

## Key Changes

### 1. Project Structure and Configuration

| AWS SAM | Serverless Framework | Notes |
|---------|---------------------|-------|
| `template.yaml` | `serverless.yml` | Main configuration file |
| `sam build` | Not required | Serverless Framework doesn't require a separate build step |
| SAM-specific properties | Serverless-specific syntax | Different YAML structure and property names |

### 2. Resource Definitions

#### Functions

**SAM:**
```yaml
LocationsFunction:
  Type: AWS::Serverless::Function
  Properties:
    Handler: src/api/locations.handler
    Events:
      GetLocations:
        Type: HttpApi
        Properties:
          Path: /locations
          Method: GET
```

**Serverless:**
```yaml
functions:
  locations:
    handler: src/api/locations.handler
    events:
      - httpApi:
          path: /locations
          method: GET
```

#### API Definition

**SAM:**
```yaml
HttpApi:
  Type: AWS::Serverless::HttpApi
  Properties:
    Auth:
      Authorizers:
        LambdaAuthorizer:
          FunctionArn: !GetAtt AuthorizerFunction.Arn
```

**Serverless:**
```yaml
provider:
  httpApi:
    authorizers:
      lambdaAuthorizer:
        type: request
        functionName: authorizer
```

#### IAM Permissions

**SAM:**
```yaml
Policies:
  - DynamoDBCrudPolicy:
      TableName: !Ref LocationsTable
```

**Serverless:**
```yaml
iamRoleStatements:
  - Effect: Allow
    Action:
      - dynamodb:Query
      - dynamodb:Scan
      - dynamodb:GetItem
      - dynamodb:PutItem
      - dynamodb:UpdateItem
      - dynamodb:DeleteItem
    Resource: !GetAtt LocationsTable.Arn
```

### 3. Added Dependencies

To support local development and deployment with Serverless Framework, two plugins were added:

- `serverless-dynamodb-local`: For local DynamoDB testing
- `serverless-offline`: For local API Gateway and Lambda emulation

Install with:
```
npm install --save-dev serverless-dynamodb-local serverless-offline
```

### 4. DynamoDB Table Definitions

In SAM, the `AWS::Serverless::SimpleTable` resource was used for the LocationsTable, which is a simplified CloudFormation resource. In Serverless Framework, we need to use the full `AWS::DynamoDB::Table` resource for all tables.

### 5. Resource Naming

Serverless Framework automatically prefixes resources with the service name and stage, following this pattern:
- Lambda Functions: `${service}-${stage}-${functionName}`
- DynamoDB Tables: Tables retain their CloudFormation logical names unless specified

This differs from SAM where resources retain their CloudFormation logical names by default.

### 6. Local Development

| AWS SAM | Serverless Framework | Notes |
|---------|---------------------|-------|
| `sam local start-api` | `serverless offline start` | Start local API |
| `sam local invoke` | `serverless invoke local` | Invoke function locally |
| Requires Docker | No Docker required | Serverless Offline doesn't require Docker |

### 7. Deployment

| AWS SAM | Serverless Framework | Notes |
|---------|---------------------|-------|
| `sam deploy --guided` | `serverless deploy` | Deploy stack |
| Step-by-step guided wizard | Configuration in `serverless.yml` | Less interactive deployment |
| Template parameter handling | Custom/CLI params | Different approaches for parameters |

### 8. Testing

No changes were required for the test files. Both unit and integration tests work with the Serverless Framework deployment as they did with SAM.

### 9. CloudWatch Integration

Both frameworks support CloudWatch Logs and Metrics. The configuration has been maintained in the migration, including dashboards and alarms.

## Code Changes Required

1. No changes to Lambda function code were required
2. No changes to the API implementation were required
3. No changes to the tests were required

The migration primarily involves configuration changes in how resources are defined and deployed, not changes to the application code itself.

## Benefits of Serverless Framework

1. Simplified local development with `serverless-offline`
2. Enhanced local testing with `serverless-dynamodb-local`
3. More plugins available for extending functionality
4. Support for multiple cloud providers (AWS, Azure, GCP, etc.)
5. Simplified deployment commands
6. Less verbose configuration

## Drawbacks of Serverless Framework

1. Different naming conventions for deployed resources
2. Some AWS-specific features may require custom CloudFormation in the resources section
3. Learning a new configuration format and commands
4. Some SAM features like `sam sync` don't have direct equivalents