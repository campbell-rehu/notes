# AWS Monitoring & Audit: Cloudwatch, X-Ray and CloudTrail

## Why monitoring is important?

- Users only care that the application is working
  - Application latency
  - Application outages
- Internal monitoring:
  - Performance
  - Availability
  - Security
  - Cost

### AWS CloudWatch

- Metrics: Collect and track key metrics
- Logs: Collect, monitor, and store log files
- Events: Send notifications when certain events happen in your AWS
- Alarms: React in real-time to metrics or events

### AWS X-Ray

- Troubleshooting application performance and errors
- Distributed tracing of requests

### AWS CloudTrail

- Internal monitoring of API calls being made
- Audit changes to AWS resources by your users

## CloudWatch Metrics

- Cloudwatch provides metrics for every service in AWS
- Metric is a variable to monitor (CPUUtilization, NetworkIn...)
- Metrics belong to namespaces
- Dimension is an attribute of a metric (instance id, environment...)
- Up to 30 dimensions per metric
- Metrics have timestamps
- Can create CloudWatch dashboards of metrics

### EC2 Detailed Monitoring

- EC2 instance metrics are recorded every 5 minutes by default
- Can increase this to 1 minute with detailed monitoring for additional cost
  - Use detailed monitoring if you want to more promptly scale your ASG
- AWS free tier allows 10 detailed monitoring metrics
- EC2 memory usage is not pushed to CloudWatch by default
  - You need to use a custom metric to push memory usage

### Custom Metrics

- Define and send your own custom metrics to CloudWatch
- Use API call `PutMetricData`
- Ability to use dimensions (attributes) to segment metrics
- Metric resolution:
  - Standard: 1 minute
  - High resolution: as low as 1 second (Higher cost)
- Metric data points two weeks in the past and two hours in the future are accepted

## CloudWatch Logs

- Log groups: arbitrary name, usually representing an application
- Log streams: instances within the application / log files / containers
- Can send logs to:
  - S3
  - Kinesis Data Streams
  - Kinesis Data Firehose
  - AWS Lambda
  - Opensearch
- Logs are encrypted by default

### Sources

- SDK, CloudWatch Logs Agent
- Elastic Beanstalk: collection of logs from application
- ECS: collection of logs from containers
- AWS Lambda: collection of logs from function logs
- VPC Flow Logs: VPC specific logs
- API Gateway: log API calls
- CloudTrail based on filter
- Route53: Log DNS queries

### Querying Logs

- Logs can be queried using CloudWatch Insights
- Search and analyze logs
- Can query multiple log groups in different AWS accounts
- Query engine, not a real-time engine

### CloudWatch Logs Subscriptions

- Get real-time streams of log data from CloudWatch Logs to:
  - Lambda
  - Kinesis Data Streams
  - Kinesis Data Firehose
- Log aggregation

#### Cross-Account Log Subscription

- You can use cross-account log data subscription to send log data to a destination in a different account:
  - Subscription filter in the sending account
  - Subscription destination in the receiving account
  - Attach a Destination access policy to allow the sending account to send log data to the receiving account
  - Create an IAM role in the receiving account to allow sending records to the Kinesis data stream
  - Allow the sending account to assume the IAM role in the receiving account

### CloudWatch Logs for EC2

- By default, no logs are sent to CloudWatch from EC2
- You can use the CloudWatch agent to push logs to CloudWatch
- The CloudWatch agent can be installed on on-premises servers

#### CloudWatch Logs Agent & Unified Agent

- CloudWatch Logs Agent

  - Old version of the agent
  - Can only send to CloudWatch Logs

- CloudWatch Unified Agent
  - Newer version of the agent
  - Collect additional system-level metrics
  - Collect logs to send to CloudWatch Logs
  - Centralized configuration using SSM Parameter Store
  - More granular metrics around CPU, memory, disk, and network utilization

### Logs Metric Filter

- Metric filters can be used to:
  - Count occurrences of a term
  - Extract values from JSON
  - Create filters for CloudWatch alarms
- Filters do not retroactively filter data
  - Filters only publish new data points to CloudWatch for events after the filter was created
- Ability to specify up to 3 Dimensions for the filter

## CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Alarm states:
  - OK
  - INSUFFICIENT_DATA
  - ALARM
- Period: Length of time in seconds to evaluate the metric

### Alarm Targets

- EC2 actions:
  - Stop, terminate, reboot, recover
- Trigger Auto Scaling actions
- Send notifications to SNS

### Composite Alarms

- Combine multiple alarms together
- AND / OR logic

### EC2 Instance Recovery

- Status Check:
  - Instance status check
  - System status check
  - Attached EBS status

## CloudWatch Synthetics Canary

- Configurable script that monitors your APIs, URLs, and website endpoints
- Reproduce what your customers do programmatically to find issues before customers are impacted
- Script written in Node.js or Python
- Integration with CloudWatch Alarms
- Can run once or on a regular schedule

### Canary Blueprints

- Heartbeat monitor: load URL, store screenshot and an HTTP archive file
- API Canary: test basic read and write functions of REST APIs
- Broken Link Checker: check for broken links on a website
- Visual Monitoring: compare a screenshot taken during a canary run with a baseline screenshot
- Canary recorder: used with CloudWatch Synthetics Recorder (record actions on a website and automatically generate a script)
- GUI Workflow Builder: create a canary using a visual workflow builder

## Amazon EventBridge

- Schedule: Cron jobs
- Event Pattern: Event rules to react to a service doing something
- Trigger Lambda Functions etc
- Default event bus, partner event bus, custom event bus

### EventBridge Schema Registry

- EventBridge can analyse the events and infer the schema
- Schema Registry can generate code for your application that will know how the data is structured
- Schema can be versioned

### Resource-Based Policy

- Manage permissions for a specific Event Bus
- Use-case: can have a central Event Bus for all events that all accounts can send events to

### Multi-account Aggregation

- Aggregate events from multiple accounts into a central account
- Define an Event rule in the sending accounts to send events to the central account
- Define a Resource policy on the central account to allow the sending accounts to send events
- Define an Event rule in the central account to react to the events

## AWS X-Ray

- Visual analysis of your applications
- Helps understand how your application is performing
- Troubleshooting performance (bottlenecks)
- Understand dependencies in a microservices architecture
- Pinpoint service issues
- Review request behavior
- Where am I throttled?
- Are we meeting time SLAs?
- Identify users that are impacted

### Tracing

- End to end way to follow a request
- Each component dealing with the request adds its own "trace"
- Tracing is made of segments (+ sub-segments)
- Annotations can be added to traces to provide extra-information

### How to enable it?

- 1. Must import the AWS X-Ray SDK into your application
- 2. Install the X-Ray daemon on on-premises servers or enable X-Ray AWS Integration for services within AWS
  - Each application must have IAM rights to write data to X-Ray

### Instrumentation

- The measure of product's performance, diagnose errors, and write trace data
- Use the X-Ray SDK to instrument your application
- Modify application code to customise and annotate the data that the SDK sends to X-Ray, using interceptors, filters, handlers, middleware

### Concepts

- Segments: each application / service will send them
- Subsegments: if you need more details in your segment
- Trace: segments collected together to form an end-to-end trace
- Sampling: decrease the amount of requests sent to X-Ray, reduce cost
- Annotations: key-value pairs used to index traces and use with filters
- Metadata: key-value pairs, not indexed, not used for searching

### Sampling Rules

- With sampling rules, you control the amount of data that you record
- You can modify sampling rules without changing code
- By default, the X-Ray SDK records the first request each second (reservoir), and five percent of any additional requests (rate)
- These can be adjusted on the fly

### X-Ray APIs

- X-Ray daemon needs to have the correct IAM policy authorising these API calls

#### Write APIs

- PutTraceSegments: upload segments to X-Ray
- PutTelemetryRecords: upload aggregated telemetry data to X-Ray
- GetSamplingRules: retrieve all sampling rules
- GetSamplingTargets, GetSamplingStatisticSummaries

#### Read APIs

- GetServiceGraph: retrieve a document that describes services in your application
- BatchGetTraces: retrieve a list of traces specified by ID
- GetTraceSummaries: retrieve a list of trace summaries
- GetTraceGraph: retrieve a service graph for one or more specific trace summaries

### X-Ray with Elastic Beanstalk

- X-Ray can be enabled at the environment level

### X-Ray with ECS

- ECS cluster
  - X-Ray daemon container running on each EC2 instance
  - X-Ray container as a sidecar to your application container
- Fargate Cluster
  - X-Ray container as a sidecar to your application container

### AWS Distro for OpenTelemetry

- AWS supported distribution of OpenTelemetry
- Collects distributed traces and metrics from your applications
- Similar to X-ray but open-source
- Can send traces to AWS services or third-party services

### CloudTrail

- Provides governance, compliance, and audit for AWS account
- Enabled by default
- Get a history of events/API calls made within your AWS account by:
  - Console
  - SDK
  - CLI
  - AWS Services
- Can put CloudTrail logs from multiple AWS accounts into a single S3 bucket
- A trail can be applied to all regions or a single region

#### CloudTrail Events

- Management Events: operations that are performed on resources in your AWS account
  - Read separate Read Events and Write Events
- Data Events: data events are not logged by default because they are high-volume operations
  - S3 object-level API activity
  - Lambda function execution activity
- CloudTrail Insights: identify unusual activity in your account
  - Continuously analyze write management events to detect unusual patterns
  - Anomalies appear in the CloudTrail console
  - Event is sent to S3
  - EventBridge event is generated (for automation needs)
- Events are stored for 90 days in CloudTrail
- Can be archived to S3 for longer retention and use Athena to query the events

## CloudTrail vs CloudWatch vs X-Ray

- CloudTrail
  - audit API calls made within your AWS account
  - useful to detect unauthorized calls or root cause of changes
- CloudWatch
  - CloudWatch metrics over time for monitoring
  - CloudWatch logs for storing application log
  - CloudWatch alarms to send notifications in case of unexpected metrics
- X-Ray
  - Automated tracing of requests & Central Service Map Visualisation
  - Latency, Errors and Fault analysis
  - Request tracking across distributed systems
