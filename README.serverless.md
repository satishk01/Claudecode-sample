# Serverless REST API with HTTP API, Node.js and Serverless Framework

This project demonstrates how to build a serverless REST API using the Serverless Framework, AWS HTTP API Gateway, AWS Lambda with Node.js, and Amazon DynamoDB.

## Overview

This sample application was migrated from an AWS SAM implementation to use the Serverless Framework. It provides the same functionality as the original SAM version, but with the benefits of the Serverless Framework's ecosystem and simplified configuration.

The API provides endpoints for managing locations, resources, and bookings:

- Locations: Physical locations that contain resources
- Resources: Bookable resources within locations (e.g., meeting rooms)
- Bookings: User bookings of resources for specific time periods

The application uses Amazon Cognito for user authentication/authorization with two permission levels:
- Regular users: Can view locations, resources, and manage their own bookings
- Admin users: Can create, update, and delete locations and resources

## Files and Directories

- `src/api`: Lambda function handlers for the API endpoints
- `__tests__`: Unit and integration tests
- `serverless.yml`: Serverless Framework configuration file
- `DEPLOYMENT.md`: Instructions for deploying with Serverless Framework
- `MIGRATION.md`: Documentation of the migration from SAM to Serverless Framework

## Getting Started

### Prerequisites

- Node.js 20.x or later
- AWS CLI configured with appropriate permissions
- Serverless Framework installed globally (`npm install -g serverless`)
- An AWS account with permissions to create necessary resources

### Installation

1. Clone this repository
2. Install dependencies: `npm install`
3. Install development dependencies for Serverless: 
   ```
   npm install --save-dev serverless-dynamodb-local serverless-offline
   ```

### Deployment

See the detailed deployment instructions in [DEPLOYMENT.md](./DEPLOYMENT.md).

### Local Development

For local development, use the Serverless Offline plugin:

```bash
# Install DynamoDB local
serverless dynamodb install

# Start local API
serverless offline start
```

## Testing

Run unit tests:
```
npm run test:unit
```

Run integration tests (requires deployed stack):
```
npm run test:integration
```

## Authentication

This API uses Amazon Cognito for authentication. Users need to include their ID token in the `Authorization` header of API requests. See [DEPLOYMENT.md](./DEPLOYMENT.md) for instructions on setting up users.

## Migration from SAM

This project was migrated from AWS SAM to the Serverless Framework. For details about the migration process and differences between the implementations, see [MIGRATION.md](./MIGRATION.md).

## Architecture

The application's architecture remains the same as in the SAM implementation:

- AWS API Gateway (HTTP API) as the entry point
- Lambda functions for request handling
- DynamoDB tables for data storage
- Cognito for authentication/authorization
- CloudWatch for logging, metrics, and alerting

## License

MIT-0