# AWS CLI, SDK, IAM Roles, Policies

## AWS CLI Profiles

- Enable being able to use multiple AWS accounts from the CLI

## AWS CLI with MFA

- Add MFA device via AWS Console
- Send API request to create a temporary session with a session token

## AWS SDK

- Used from code within an application
- AWS CLI uses the python SDK
- If no default region is specified, us-east-1 will be chosen

## AWS Limits (Quotas)

### API Rate Limits

- For intermittent errors, implement Exponential Backoff
- For consistent errors, request an API throttling limit increase

### Service Limits

- How many resources you can run at once
- Request a service limit increase by opening a ticket
- Request a service quota increase by using the Service Quotas API

### Exponential Backoff

- If you get a ThrottlingException intermittently, use exponential backoff
- Retry mechanism already included in AWS SDK
- Must implement yourself if using AWS API directly
  - Must only implement the retries on 5xx server errors and throttling
  - Do not implement on the 4xx client errors
- The more we retry, the longer we wait before retrying

## AWS CLI Credentials Provider Chain

- Command line options
- Environment variables
- CLI credentials file
- CLI configuration file
- Container credentials
  - for ECS tasks
- Instance profile credentials
  - for EC2 instance profiles

### AWS Credentials Best Practice

- If working within AWS, use IAM roles:
  - EC2 instance roles for EC2 instances
  - ECS roles for ECS tasks
  - Lambda roles for Lambda functions
- If working outside of AWS, use environment variables or named profiles

## Signing AWS Requests

- Uses SigV4 to sign requests
- Built into the CLI and SDKs
- Using either a HTTP Header or Query String
