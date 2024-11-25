# Amazon S3

- one of the main building blocks of AWS
- advertised as "infinitely scaling" storage

## Use cases

- Backup and storage
- Disaster Recovery
- Archive
- Hybrid Cloud storage
- Application hosting
- Media hosting
- Data lakes and big data analytics
- Software delivery
- Static websites

## Buckets

- store objects (files) in "buckets" (directories)
- must have a globally unique name (across all regions, all accounts)
- defined at the region level
- naming convention
  - no uppercase, no underscore
  - 3-63 characters long
  - not an IP address
  - Must start with lowercase letter or number
  - Must not start with prefix xn--
  - Must not end with the suffix -s3alias

## Objects

- Objects (files) have a Key
- The key is the FULL path
  - s3://my-bucket/my_file.txt
- The key is composed of prefix + object name
- There is no concept of "directories" within buckets
- Object values are the content of the body:
  - Max object size is 5TB
  - If uploading > 5GB, must use "multi-part upload"
- Metadata (list of key / value pairs - system or user metadata)
- Tags
- Version Id

## Security

- User-based
  - IAM policies
- Resource-based
  - Bucket policies
  - Object Access Control List (ACL)
  - Bucket Access Control List (ACL)
- IAM principal can access an S3 object if:
  - user IAM permissions ALLOW it OR resource policy ALLOWs it AND there's no explicit DENY
- Encrypt objects using encryption keys

### Bucket Policies

- JSON-based policies
- Resources: buckets and/or objects
- Effect: Allow / Deny
- Actions: Set of API to Allow or Deny
- Principal: the account or user to apply the policy to

## Static Website Hosting

- S3 can host static websites and have them accessible on the Internet
- Ensure the bucket policy allows public reads

## Versioning

- Can version files
- Enabled at the bucket level
- Same key overwrite will change the "version"
- Protect against unintended deletes
- Easily rollback to previous version
- Files that are not versioned prior to enabling versioning have version "null"
- Suspending versioning does not delete previous versions

## Replication

- CRR: Cross-Region Replication
- SRR: Same-Region Replication
- Enable versioning in source and destination buckets
- Buckets can be in different accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
- Use cases:
  - CRR - compliance, lower latency access, replication across accounts
  - SRR - log aggregation, live replication between production and test accounts
- After enabling replication, only new objects are replicated
  - Must use S3 batch replication to replicate existing objects
- Can replicate delete markers from source to target (optional setting)
- No chaining of replication

## Storage Classes

- Standard - General Purpose
- Standard-Infrequent Access (IA)
- One Zone-Infrequent Access
- Glacier Instant Retrieval
- Glacier Flexible Retrieval
- Glacier Deep Archive
- Intelligent Tiering
- Can move between classes manually or using S3 Lifecycle configurations

### Durability and Availability

- Durability:
  - High durability (99.99999999999%, 11 9s) of objects across multiple AZs
  - Same for all storage classes
- Availability:
  - How readily available a service is
  - Varies depending on storage class

### Standard - General Purpose

- 99.99% availability
- Used for frequently accessed data
- Low latency and high throughput
- Sustain 2 concurrent facility failures
- Use cases: big data analytics, mobile / gaming applications, content distribution

### Infrequent Access

- Less-frequently accessed data but rapid access when needed
- Lower cost than S3 standard

#### Standard-Infrequent Access (Standard-IA)

- 99.9% availability
- Use cases: disaster recovery, backups

#### One Zone-Infrequent Access (One Zone-IA)

- High durability in a single AZ
- Data lost when AZ is destroyed
- 99.5% availability
- Use cases: storing secondary backup copies of on-prem data, data you can recreate

### Glacier Storage Classes

- Low-cost object storage meant for archiving / backup
- Price for storage + object retrieval cost

#### Glacier Instant Retrieval

- Millisecond retrieval, great for data accessed once a quarter
- Minimum storage duration of 90 days

#### Glacier Flexible Retrieval

- Expedited (1 to 5 mins), Standard (3 to 5 hours), Bulk (5 to 12 hours)
- Minimum storage duration of 90 days

#### Glacier Deep Archive

- Standard (12 hours), Bulk (48 hours)
- Minimum storage duration of 180 days

### Intelligent Tiering

- Small monthly monitoring and auto-tiering fee
- Moves objects automatically between access tiers based on usage
- No retrieval charges
- Frequent Access tier (automatic): default tier
- Infrequent Access tier (automatic): objects not accessed for 30 days
- Archive Instant Access tier (automatic): objects not accessed for 90 days
- Archive Access tier (optional): configurable from 90 days to 700+ days
- Deep Archive Access tier (optional): configurable from 180 days to 700+ days
