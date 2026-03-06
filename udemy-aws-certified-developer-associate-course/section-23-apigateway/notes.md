# AWS API Gateway

- AWS Lambda + API Gateway = fully serverless application
- Support for Websocket Protocol
- Handle API versioning
- Handle different environments
- Handle security
- Create API keys
- Swagger / OpenAPI import
- Transform and validate requests and responses
- Generate SDK and API specs
- Cache API responses

## Integrations

- Lamdba Function
  - Invoke Lambda function
  - Easy way to expose REST API backed by AWS Lambda
- HTTP
  - Expose HTTP endpoints in the backend
- AWS Service

## Endpoint Types

- Edge-Optimized: for gloabl clients
  - Requests are routed through the CloudFront Edge locations
  - API Gateway still lives in only one region
- Regional: for clients within the same region
  - Could manually combine with CloudFront (more control over the caching strategies and the distribution)
- Private
  - Can only be accessed from your VPC using an interface VPC endpoint (ENI)

## Security

- User authentication through:
  - IAM Roles
  - Cognito (identity for external users)
  - Custom Authori
