# AWS DynamoDB

## Overview

- Fully managed, highly available with replication across multiple AZs
- NoSQL database, key-value and document data models
- Scales to massive workloads
- Millions of requests per second
- Low latency
- Integrated with IAM
- Enables event-driven programming with DynamoDB Streams
- Low cost and auto-scaling capabilities
- Standard & Infrequent Access (IA) Table classes

## Basics

- Made up of tables, items, and attributes
- Each table has a primary key (must be defined at table creation)
- Each table can have an infinite number of items (rows)
- Each item has attributes (columns)
  - can be added over time - can be null

## Primary Keys

- Option 1: Partition key (simple primary key)
  - Single attribute
  - Must be unique for each item
- Option 2: Partition key + Sort key (composite primary key)
  - The combination of partition key and sort key must be unique for each item
  - Data is grouped by partition key and sorted by sort key

## Read / Write Capacity Modes

- Provisioned Mode
  - Specify read and write capacity per second
  - Plan capacity at runtime
  - Pay for provisioned
- On-Demand Mode
  - Read/writes automatically scale up/down with your workloads
  - No capacity planning required
  - Pay for what you use, more expensive
- Can switch between modes once every 24 hours

### Provisioned Mode

- Table must have provisioned read and write capacity units
- Read Capacity Units (RCU) - throughput for reads
- Write Capacity Units (WCU) - throughput for writes
- Option to setup auto-scaling of throughput to meet demand
- Throughput can be exceeded temporarily using "Burst capacity"
- If Burst Capacity is exceeded, it will return a "ProvisionedThroughputExceededException"

### Write Capacity Units (WCU)

- One Write Capacity Unit = 1 write per second for an item of size 1KB
- If the items are large, more WCU are consumed

### Read Capacity Units (RCU)

- Eventually consistent reads (default)
- Strongly consistent reads
  - Consumes twice the RCU
- One Read Capacity Unit = 1 Strongly consistent read per second, or 2 eventually consistent reads per second for an item up to 4KB
  - Strongly consistent reads: rate * (Item size [rounded to nearest 4KB] / 4)
  - Eventually consisten reads: (rate * (Item size [rounded to nearest 4KB] / 4)) / 2

### Partitions

- Data is stored in partitions
- Partition keys go through a hashing algorithm to know to which partition the item should go

### Throttling

- If the provisioned throughput is exceeded, DynamoDB will throw a ProvisionedThroughputExceededException
- Reasons:
  - Hot keys
  - Hot partitions
  - Very large items
- Solutions:
  - Exponential backoff
  - Distribute partition keys
  - DynamoDB Accelerator (DAX)

### On-Demand Mode

- Read/writes automatically scale up/down with your workloads
- No capacity planning required
- Pay for what you use, more expensive
- 2.5x more expensive
- Unknown workload or unpedictable workload
- Can switch between modes once every 24 hours

### Conditional Writes

- Specify a condition expression to determine which items should be modified
  - attribute_exists
  - attribute_not_exists
    - does not overwrite existing items
  - attribute_type
  - contains
  - begins_with
  - IN
  - BETWEEN
  - size

### Local Secondary Indexes

- Alternative Sort Key for your table
- Sort key consists of one scalar attribute
- Up to 5 LSI per table
- Must be defined at table creation time

### Global Secondary Indexes

- Alternative Primary Key (Hash or Hash+Range) from the base table
- Speed-up queries on non-key attributes
- Must provision RCUs and WCUs for the index
- Can be added/modified after table creation
- If the writes are throttled on the GSI, then the main table will also be throttled
- Choose your GSI partition key carefully
- Assign your WCU capacity carefully

### PartiQL

- SQL-like syntaz to manipulate DynamoDB tables
- Supports some (but not all) statements

### Optimistic Locking

- Each item has an attribute that acts as a version number
- To ensure that the item has not changed since it was last read

### DynamoDB Accelerator (DAX)

- In-memory cache for DynamoDB
- Doesn't require application logic modification
- Solves the "Hot Key" problem (too many reads)
- 5 minutes TTL for cache (default)
- Up to 10 nodes in the cluster
- Multi-AZ

#### vs Elasticache

- Can be used in combination with DAX

### DynamoDB Streams

- Ordered stream of item-level modifications in a table
- Records can be:
  - Sent to Kinesis
  - Read by AWS Lambda
  - Read by Kinesis Client Library applications
- Data Retention for up to 24 hours
- Use cases:
  - react to changes in real-time
  - analytics
  - insert into derivative tables
  - Insert into OpenSearch service
  - Implement cross-region replication
- Choose what information will be written to the stream:
  - KEYS
  - NEW_IMAGE
  - OLD_IMAGE
  - NEW_AND_OLD_IMAGES
- Records are not retroactively populated in the stream, only new records are captured

#### & AWS Lambda

- Define Event Source Mapping to read from DynamoDB Streams
- Ensure the Lambda function has the appropriate permissions
- function is invoked synchronously

### Time to Live (TTL)

- Automatically delete items after an expiry timestamp
- Doesn't consume any WCUs
- Unix Epoch timestamp
- Expired items deleted within a few days of expiration
- Delete operation for each expired item enters the DynamoDB Streams

### DynamoDB CLI

- --projection-expression: specify which attributes to return
- --filter-expression: filter items before returned to you
- General AWS CLI Pagination options:
  - --page-size: specify that AWS CLI retrieves the full list of items but with a larger number of API calls instead of one (default: 1000)
  - --max-items: max number of items to show in the CLI (returns NextToken)

### DynamoDB Transactions

- All-or-nothing operations to multiple items across one or more tables
- Provides ACID (atomicity, consistency, isolation, durability) properties
- Read modes: Eventual, Consistency, Strong Consistency, Transactional
- Write modes: Standard, Transactional
- Consumes 2x WCUs & RCUs
  - DynamoDB performs 2 operations for every item
- Two operations:
  - TransactGetItems: one or more GetItem operations
  - TransactWriteItems: one or more PutItem, UpdateItem, and DeleteItem operations

### DynamoDB as Session State Cache

- Store session state in DynamoDB
- vs Elasticache:
  - Elasticache is in-memory, DynamoDB is serverless
  - Both are key/value stores
- vs EFS:
  - EFS must be attached to EC2 instances as a network drive
- vs EBS & Instance Store:
  - can only be used for local caching, not shared caching
- vs S3:
  - higher latency, not meant for small objects

### DynamoDB Write Sharding

- Distribute items evenly across partitions
- Add a suffix to Partition Key value
- Two methods:
  - Sharding using random suffix
  - Sharding using calculated suffix

### DynamoDB Write Types

- Concurrent writes: multiple writes to the same item at the same time
- Conditional writes: writes that only succeed if a condition is met
- Atomic writes: when updates are accumulative to the same item, all updates succeed
- Batch writes: user updates multiple items in a single request

### DynamoDB Patterns with S3

#### Large objects pattern

- Store large objects in S3 and reference metadata in DynamoDB

#### Indexing S3 Objects Metadata

- Store metadata about S3 objects in DynamoDB for query and search capabilities

### DynamoDB Operations

#### Table Cleanup

- Option 1: Scan + DeleteItem one by one
  - Very slow, expensive
- Option 2: Drop Table + Recreate Table

#### Copying a DynamoDB Table

- Option 1: Using AWS Backup
- Option 2: Using AWS Glue
- Option 3: Scan + PutItem or Batch WriteItem

### DynamoDB Security + Other Features

#### Security

- VPC Endpoints available to access DynamoDB privately without going through the internet
- Access fully controlled by IAM
- Encryption at rest using AWS KMS and in-transit using TLS

#### Backup & Restore

- Point-in-time recovery
- No performance impact

#### Global Tables

- Multi-region, multi-active, fully replicated, high performance

#### DynamoDB Local

- Develop and test apps locally without accessing the DynamoDB web service
- AWS Database migration service can be used to migrate to DynamoDB from other databases
