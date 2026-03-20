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

- Edge-Optimized: for global clients
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
  - Custom Authorizer
- Custom Domain Name through integration with AWS Certificate Manager (ACM)

## Deployment Stages

- Making changes in the API gateway does not mean they're effective
- You need to make a "deployment" for them to be in effect
- It's a common source of confusion
- Changes are deployed to "Stages"
- Use the naming you like for stages (dev, test, prod)
- Each stage has its own configuration parameters
- Can be rolled back because of the history of deployments

## Stage variables

- Like environment variables for API gateway
- Use them to change often changing configuration values

### & Lambda Aliases

- Stage variable to indicate the corresponding Lambda alias
- API gateway will automatically invoke the right lambda function

## Canary Deployment

- Possibility to enable canary deployments for any stage (usually prod)
- Choose the % of traffic the canasy channel receives
- Metrics & Logs are separate (for better monitoring)
- Possiblity to override stage variables for canasy
- Blue / Green deployment with Lambda and API Gateway

## Integration Types

- Integration type MOCK
  - returns a response without sending the request to the backend
- HTTP / AWS
  - you must configure both the integration request and integration response
  - setup data mapping using mapping templates for the request and response
- AWS_PROXY (Lambda Proxy)
  - incoming request from the client is input to Lambda
  - The funtion is responsible for the logic of request / response
  - No mapping template
  - Headers, query params etc are passed as arguments to Lambda
- HTTP_PROXY
  - No mapping template
  - HTTP request is passed to the backend
  - HTTP response from the backend is forwarded by API gateway
  - Possibility to add HTTP headers if need be

### Mapping Templates

- Can be used to modify request / responses
- Modify query string params, body content, headers etc
- Uses Velocity Template Language (VTL)
