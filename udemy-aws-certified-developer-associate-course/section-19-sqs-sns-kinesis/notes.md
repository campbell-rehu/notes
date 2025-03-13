# SQS, SNS & Kinesis

## SQS

- Simple Queue Service
- Fully managed message queuing service
- Unlimited throughput, unlimited number of messages in the queue
- Default retention of messages: 4 days, maximum of 14 days
- Low latency
- < 256KB per message
- Can have duplicate messages (at least once delivery, occasionally more than one copy of the message is delivered)
- Can have out of order messages (best effort ordering)

### Producing Messages

- Producer sends messages to the queue using SendMessage API
- The message is persisted in SQS until a consumer deletes it

### Consuming Messages

- Consumers poll SQS for messages
- Receive up to 10 messages at a time
- Process the message
- Delete the message using the DeleteMessage API
- We can scale consumers horizontally (more instances) to improve throughput of processing

#### Auto-Scaling Groups

- We can use an Auto-Scaling Group to scale the number of consumers based on the number of messages in the queue (ApproximateNumberOfMessages metric)
- If ApproximateNumberOfMessages goes above a certain threshold, we can trigger a CloudWatch Alarm to scale out the number of consumers

### Security

- Encryption at rest using KMS keys
- In-flight encryption using HTTPS endpoints
- Access policies to the queues using IAM policies
- SQS access policies (similar to S3 bucket policies)

### SQS Queue Access Policy

- Cross Account access
- Publish S3 Event Notifications to SQS Queue

### Message Visibility Timeout

- When a message is polled by a consumer, it becomes invisible to other consumers
- Default visibility timeout is 30 seconds
  - That means the message has 30 seconds to be processed and deleted
- After the message visibility timeout is over, the message will be available to be received by another consumer
- If the processing of the message takes longer than expected, the consumer could call the ChangeMessageVisibility API to get more time

### Dead Letter Queue

- If a consumer fails to process a message, the message goes back to the queue
- We can set a threshold for how many times a message can go back to the queue (default is 5)
- After the threshold is exceeded, the message goes into a Dead Letter Queue (DLQ)
- Useful for debugging
- DLQ of a standard queue must be a standard queue
- DLQ of a FIFO queue must be a FIFO queue

#### Redrive to Source

- Redrive the messages from the DLQ back to the source queue

### Delay Queue

- Delay a message in the queue up to 15 minutes before it becomes available for consumers

### Long Polling

- Consumer requests messages from the queue, it can optionally wait for messages to arrive if there are non in the queue
- Long polling reduces the number of empty responses, and is more cost effective
- Wait time can be between 1 and 20 seconds
- Can be enabled using the ReceiveMessageWaitTimeSeconds parameter

### Extended Client

- For large messages (up to 2GB), we can use the SQS Extended Client Library (Java Library)
- The message is stored in S3, and the SQS message contains a reference to the message in S3

### API

- CreateQueue
- PurgeQueue
- DeleteQueue
- SendMessage
- ReceiveMessage
- DeleteMessage
- ChangeMessageVisibility
- MaxNumberOfMessages
- ReceiveMessageWaitTimeSeconds
- ChangeMessageVisibility

### FIFO Queue

- First In, First Out (ordering of messages in the queue)
- Limited throughput: 300 msg/s without batching, 3000 msg/s with batching
- Exactly-once send capability
- Messages are processed in order by the consumer
- Ordering by Message Group ID (all messages in the same group are ordered)
- Queue name must end in .fifo

#### FIFO Queue Advanced

##### Deduplication

- De-duplication interval is 5 minutes
- If you send the same message within a 5 minute interval, SQS will remove the second message
- Content-based deduplication: will do a SHA-256 hash of the message body and use it to deduplicate
- Message Deduplication ID: custom ID to override deduplication

##### Message Grouping

- Messages in the same message group are always processed one by one, always in order by one consumer

## SNS

- Simple Notification Service
- Send one message to many receivers
- Fully managed, pub/sub messaging service
- Event producer only sends message to one SNS topic
- As many event receivers (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (fan-out)
- Up to 12.5 million subscriptions per topic
- 100,000 topics limit
- Integrates with a lot of AWS services

### How to publish

- Topic publish
  - Create a topic
  - Create a subscription (or many)
  - Publish to the topic
- Direct publish (for mobile apps SDK)
  - Create a platform application
  - Create a platform endpoint
  - Publish to the platform endpoint
  - Works with Google GCM, Apple APNS, Amazon ADM etc

### Security

- Encryption:
  - In-flight encryption using HTTPS
  - At-rest encryption using KMS keys
- Access policies using IAM policies
- SNS access policies (similar to S3 bucket policies)

### SNS + SQS: Fan Out

- Push once in SNS, receive in all SQS queues that are subscribers to the SNS topic
- Ability to add more SQS subscribers over time
- Make sure SQS queue access policy allows SNS to write to it
- Eg S3 events to multiple queues

### SNS to S3 via Kinesis Data Firehose (KDF)

- SNS cannot send messages to S3 directly
- Can use KDF to send messages from SNS to S3
  - SNS -> KDF -> S3

### FIFO Topic

- Same features as SQS FIFO
- Can provide fan out + ordering + deduplication

### SNS Message Filtering

- JSON policy to filter messages
- Without a filter policy, a subscriber receives every message

## Kinesis Data Streams

- Collect and store streaming data in real-time
- Retention between up to 365 days
- Ability to reprocess / replay data by consumers
- Data can't be deleted from Kinesis
- Data up to 1MB
- Data ordering guarantee for data with the same partition ID
- At-rest KMS encryption, in-flight encryption using HTTPS
- Kinesis Producer Library (KPL) to write an optimised producer application
- Kinesis Client Library (KCL) to write optimised consumer application

### Capacity Modes

- Provisioned Mode:
  - Choose number of shards
  - Each shard get 1MB/s in
  - Each shard gets 2MB/s out
  - Scale manually to increase or decrease the number of shards
  - You pay per shard provisioned per hour
- On-Demand Mode:
  - No need to specify the number of shards
  - Default capacity provisioned (4 MB/s in)
  - Scales automatically
  - Pay per consumed data (ingress, egress, storage)

### Data Firehose

- Fully-managed service to load data into S3, Redshift, Elasticsearch, Splunk, 3rd party services or even a custom HTTP endpoint
- Also
- Automatic scaling, serverless
- Near real-time
  - Has adjustable buffer to batch send data to destinations
- Supports CSV, JSON, Parquet, Raw Text, Binary data
- Options to do simple transformations (compression or conversion) or more complex transformations (using Lambda functions)

#### Kinesis Data Streams vs Kinesis Data Firehose

- Kinesis Data Streams:

  - Streaming data collection
  - Producer & Consumer code
  - Real-time
  - Provisioned or On-Demand mode
  - Storage up to 365 days
  - Replay Capability

- Amazon Data Firehose:
  - Load data into S3, Redshift, Elasticsearch, Splunk
  - Fully managed, serverless
  - Near real-time
    - Has adjustable buffer to batch send data to destinations
  - Automatic scaling
  - No storage
  - Doesn't support replay capability

### Amazon Managed Service for Apache Flink

- Flink is a framework for processing data streams (Kinesis Data Streams, Amazon MSK)
- Flink cannot read from Amazon Kinesis Data Firehose

## SQS vs SNS vs Kinesis

- SQS:
  - Consumers "pull data"
  - Data is deleted after being consumed
  - Can have as many workers as we want
  - No need to provision throughput
  - Ordering is only guaranteed on FIFO queues
  - Individual message delay capabilities
- SNS:
  - Push data to many subscribers
  - Up to 12.5 million subscribers
  - Data is not persisted (lost if not delivered)
  - Pub / Sub
  - Up to 100,000 topics
  - No need to provision throughput
  - Integrates with SQS for fan-out
  - FIFO capability for SQS FIFO
- Kinesis:
  - Standard: pull data
  - Enhanced fan-out: push data
  - Possible to replay data
  - Meant for real-time big data, analytics and ETL (Extract, Transform, Load) pipelines
  - Ordering at the shard level
  - Data expires after X days
  - Provisioned mode or on-demand capacity mode
