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
