# AWS Lambda

## What is serverless?

- Serverless is when developers don't have to manage servers
- Developers just deploy code
- Serverless was pioneered by AWS Lambda but now includes anything that's managed:
  - databases
  - messaging
  - storage
- Serverless does not mean no servers, it means no server management

## What is serverless in AWS?

- AWS Lambda
- DynamoDB
- AWSCognito
- AWS API Gateway
- Amazon S3
- AWS SNS & SQS
- AWS Kinesis Data Firehose
- Aurora Serverless
- Step Functions
- Fargate

## Why AWS Lambda?

- No server management
- Limited by time - short executions
- Run on-demand
- Scaling is automated

### Benefits of AWS Lambda

- Easy pricing
  - Pay per request and compute time
  - Free tier of 1M requests and 400,000 GB-seconds per month
- Integrated with the whole AWS suite
- Integrated with many programming languages
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per function
- Increasing RAM will improve CPU and network

### Lambda Language Support

- Node.js
- Python
- Ruby
- Java
- Python
- C#
- Custom Runtime API
- Lambda Container Image
  - The container image must implement the Lambda Runtime API
  - ECS / Fargate is preferred for running arbitrary Docker images

## Synchronous Invocations

- CLI, SDK, API Gateway, Application Load Balancer
  - Results is returned right away
  - Error handling must happen client-side
- User invoked, service invoked

### Lambda Integration with ALB

- To expose a Lambda function as an HTTP(S) endpoint, use an Application Load Balancer or API Gateway
- The Lambda function must be registered in a target group
- ALB converts the HTTP request to JSON and sends it to the Lambda function
- Then the ALB converts the JSON response to HTTP
- ALB Multi-Value Headers are supported
  - Can send multiple values for the same header
  - Can be set in the settings of the ALB target group

## Asynchronous Invocations

- S3, SNS, SES, CloudWatch Events, CodeCommit, Cognito, CloudFormation, etc.
  - Results are not returned right away
  - Error handling can happen asynchronously

### Cloudwatch Events / EventBridge

- Trigger the Lambda function on a schedule
- Trigger the Lambda function in response to an event or state change

### S3 Event Notifications

- Trigger the Lambda function in response to an S3 event

## Event Source Mapping

- Kinesis Data Streams
- DynamoDB Streams
- SQS & SQS FIFO
- Records need to be polled from the source
- The Lambda function is invoked synchronously

### Streams

- Event source mapping creates an iterator for each shard, processes items in order
- Start with new items, from the beginning or from timestamp
- Processed items aren't removed from the stream (so other consumers can read them)
- Low traffic: use batch window to accumulate records before processing
- You can process multiple batches in parallel
  - up to 10 batches per shard
  - in-order processing is still guaranteed for each partition key

#### Error Handling

- The entire batch is reprocessed until the function succeeds, or the items in the batch expire
- Processing for the affected shard is paused until the error is resolved
- Can configure the event source mapping to:
  - discard old events
  - restrict the number of retries
  - split the batch on error
- Discarded events can go to a destination

### SQS & SQS FIFO

- Lambda polls the SQS queue
- Specify batch size
- Set the queue visibility timeout to 6x the timeout of your Lambda function
- Setup the DLQ in SQS to capture failed messages

#### Queues and Lambda

- Supports in-order processing for FIFO queues, scaling up to the number of active message groups
- For standard queues, items aren't necessarily processed in order
- Lambda scales up to process a standard queue as quickly as possible
- When an error occurs, batches are returned to the queue as individual items and might be processed in a different grouping than the original batch
- Occasionally, the event source mapping might receive the same item from the queue twice, even if no error occurred
- Lambda deletes items from the queue after they are processed successfully

### Scaling

#### Kinesis Data Streams & DynamoDB Streams

- One lambda invocation per stream shard
- If using parallelisation, up to 10 batches processed per shard simultaneously

#### SQS Standard

- Lambda adds 60 more instances per minute to scale up
- Up to 1000 batches of messages processed simultaneously

#### SQS FIFO

- Messages with the same GroupId will be processed in order
- Lambda function scales to the number of active message groups

## Lambda Event & Context Objects

- Event: object from EventBridge
  - contains data for the function to process
  - information from the invoking service
  - Lambda runtime converts the event to an object
- Context: object from AWS Lambda about the Lambda function
  - Provides methods and properties that provide information about the invocation, function, and execution environment
  - Passed to the handler function by Lambda at runtime

## Destinations

- Lambda Destinations
  - Send the result of an asynchronous invocation to a destination
  - Can be another Lambda function, SNS topic, SQS queue, EventBridge event bus
  - Can be configured for success or failure
  - Can be configured for both success and failure
  - Can be configured for both success and failure with different destinations
  - AWS recommends using destinations for asynchronous invocations instead of DLQ
- Send discarded event batches to SQS or SNS

## Lambda Execution Role

- Grants the Lambda function permissions to AWS services / resources
- Best practice: create one Lambda execution role per function

### Resource-Based Policies

- Use resource-based policies to grant other accounts and AWS services permission to use your Lambda resources
- Similar to S3 bucket policies

## Lambda Environment Variables

- Key-value pairs that you can pass to your Lambda function
- Lambda Service adds its own system environment variables
- Helpful to store secrets (encrypted with KMS)
- Secrets can be encrypted by the Lambda service or your own CMK

## Lambda Logging and Monitoring

- Lambda execution logs are stored in AWS CloudWatch Logs (assuming the function has permission to write to CloudWatch Logs)
- Lambda metrics are displayed in AWS CloudWatch Metrics
- Tracing with X-Ray
  - Enable in Lambda configuration (Active Tracing)
  - Runs the X-Ray daemon for you
  - Use AWS X-Ray SDK to instrument your code
  - Ensure Lambda function has a correct IAM execution role
  - Environment variables can be used to communicate with X-Ray
    - AWS_XRAY_DAEMON_ADDRESS
    - AWS_XRAY_CONTEXT_MISSING
    - \_X_AMZN_TRACE_ID

## Lamdba@Edge & CloudFront Functions

- CloudFront Functions
  - Lightweight JavaScript functions
  - For high-scale, latency-sensitive CDN customizations
  - Used to change Viewer requests and responses
  - Native feature of CloudFront (manage code entirely within CloudFront)
  - Use cases:
    - URL rewrites
    - HTTP header manipulation
    - Cache key normalization
    - Access authorization and authentication
- Lamdba@Edge
  - Functions written in Node.js or Python
  - Used to change CloudFront Viewer and/or Origin requests and responses
  - Author functions in one AWS region, then CloudFront replicates them to AWS locations globally
  - Use cases:
    - Same as CloudFront Functions
    - But also anything a regular lambda function can do

## Lambda in VPC

- Lambda functions are deployed in an AWS-owned VPC
- The lambda can access the internet but nothing in a private VPC
- Deploy the lambda function in a VPC to access resources in the VPC
- The Lambda function does not have internet access by default
- Deploying a lambda function in a public subnet does not give it internet access or a public IP address
- Instead the lambda needs to use a NAT Gateway in a public subnet to access the internet
  - This NAT can also be used to access AWS services
- The lambda function can use VPC endpoints to privately access AWS services without a NAT

## Lambda Function Configuration / Performance

- RAM: 128MB to 10GB
  - If application is CPU-bound, increase RAM
- Timeout: default 3 seconds, max 15 minutes

### Lambda Execution Context

- Temporary runtime environment that initialises any external dependencies
- Lambda reuses the execution context for subsequent invocations
- Initialise DB connections, SDK clients, etc. outside the handler function

### /tmp Directory

- Use /tmp directory for temporary storage
- Max size is 10GB
- Directory content remains when the execution context is frozen providing transient cache that can be used for multiple invocations
- For permanent persistence, use S3
- To encrypt content on /tmp, you must generate KMS Data keys

## Lambda Layers

- Custom Runtimes
  - Eg C++ and Rust
- Externalise dependencies
  - Eg Python libraries
  - Separate application code from dependencies

## File Systems Mounting

- Can access EFS file systems if the Lambda function is in a VPC
- Must leverage EFS Access Points
- Watch out for the EFS connection limits (one function instance == one connection)

## Lambda Concurrency and Throttling

- Up to 1000 concurrent executions
- Reserved concurrency
  - Guarantees a set number of concurrent executions for a function
  - Can be used to limit the maximum concurrency of a function
  - Each invocation over the concurrent limit is throttled
    - If synchronous, the caller receives a 429 error
    - If asynchronous, the event is retried and then sent to the DLQ
- Concurrency limit applies to functions across entire account
- Retry policy is in place for asynchronous invocations where it will retry for up to 6 hours
  - The retry interval increases exponentially from 1 second to 5 minutes
- Provisioned concurrency
  - Pre-warms a set number of function instances
  - Useful for latency-sensitive applications
  - Can be used to avoid cold starts
