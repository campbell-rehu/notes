# Advanced Amazon S3

## Moving between Storage Classes

- Moving objects can be automated using Lifecycle Rules

### Lifecycle Rules

#### Transition Actions

- configure objects to transition to another storage class after some amount of time

#### Expiration Actions

- configure objects to expire (be deleted) after some time

- rules can be created for a certain prefix or for certain object tags

## Amazon S3 Analytics

- Help to decide when to transition objects to the right storage class
- Report is updated daily
- 24 to 48 hours to start seeing data analysis

## S3 Event Notifications

- React to actions performed on S3 objects
- Send events to SNS, SQS or Lambda Function
- Requires IAM Resource Policy on each notification target
- Can send all events to EventBridge which can forward them on to over 18 AWS services
  - Advanced filtering options
  - Multiple destinations
  - Archive, Replay Events, Reliable Delivery

## Baseline Performance

- 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD requests per second per prefix in a bucket
  - prefix = the value between the bucket name and the file name in the path
- No limit to the number of prefixes in a bucket

### Multi-Part Upload

- recommended for files > 100MB
- must use for files > 5GB
- Can help parallelise uploads

### S3 Transfer Acceleration

- Increase transfer speed by transferring file to AWS edge location and then forward to the S3 bucket in the target region
- Compatible with multi-part upload

### Byte-Range Fetches

- Parallelise GETs by requesting specific byte ranges
- Better resilience in case of failures
- Can be used to speed up downloads
- Can be used to retrieve only partial data

## Object Tags & Metadata

- User-defined metadata
  - Name-value pairs
  - Must begin with "x-amz-meta-"
  - S3 stores keys in lowercase
- Object Tags
  - Key-value pairs
  - Useful for fine-grained permissions
  - Useful for analytics purposes
- Cannot search the object metadata or tags
- Instead, you must use an external DB as a search index such as DynamoDB
