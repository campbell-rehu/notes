# RDS + Aurora + Elasticache

## Amazon RDS

- RDS = Relational Database Service
- AWS managed DB service for SQL DBs
- Supported engines:
  - Postgres
  - MySQL
  - MariaDB
  - Oracle
  - Microsoft SQL Server
  - IBM DB2
  - Aurora (AWS Proprietary DB)

### Whey use RDS vs DB on EC2

- RDS is a managed service
  - Automated provisioning, OS Patching
  - Continuous backup and restore
  - Monitoring Dashboards
  - Read replicas for improved read performance
  - Multe AZ setup for disaster recovery
  - Maintenance windows for upgrades
  - Scaling capability (vertical and horizontal)
  - Storage backed by EBS
- Can't SSH into instances

### Storage Auto Scaling

- Increase storage on RDS DB instance dynamically and automatically

### RDS Read Replicas

- Scale data reads
- Up to 15 read replicas
- Within AZ, Cross AZ or Cross Region
- Replication is asynchronous so reads are eventually consistent
- Replicas can be promoted to their own DB
- Applications must update the connection string to leverage read replicas

#### Read Replicas – Use Cases

- Production database that is taking on normal load
- You want to run reporting application to run some analytics
- You create a Read Replica to run the new workload there
- The production application is unaffected
- Read replicas are only for SELECT statements

#### Network Cost

- Read replicas within the same region, you don't pay a fee
- Cost is only incurred when data goes from one AZ to another

### RDS Multi AZ (Disaster Recovery)

- Sync replication
- One DNS name - automatic app failover to standby
- Increase availability
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling
- Read replicas can be setup as Multi AZ for Disaster Recovery

### From Single-AZ to Multi-AZ

- Just click modify on the db
- Zero downtime operation
- Process:
  - A snapshot is taken
  - A new DB is restored from the snapshot
  - Synchronisation is established between the two DBs

## Amazon Aurora

- Proprietary technology from AWS (closed source)
- Postgres and MySQL are both supported
- "Cloud-optimised", "5x performance MySQL on RDS", "3x performance than Postgres on RDS"
- Storage automatically grows in increments of 10GB up to 128 TB
- Can have up to 15 replicas
- Failover in Aurora is instantaneous. It's high-availability native
- Costs more than RDS (~20% more) – but it is more efficient

### High Availability and Read Scaling

- One Aurora instance master for writes, up to 15 replicas for reads
- Any of the reads can become the master if the master fails
- Data has replication, self-healing and auto-expanding

### Aurora DB Cluster

- Writer endpoint - pointing to the master instance for writes
- Reader endpoint which connects to all read replicas and load balances across them
- Auto-scaling can be applied to the read replicas

## RDS & Aurora Security

- At-rest encryption:
  - Database master & replicas encryption using AWS KMS
  - Must be defined at launch time
  - To encrypt an unencrypted db, restore from a snapshot as encrypted
- In-flight encryption is ready by default
- IAM Auth
- Security Groups
- No SSH except on RDS Custom
- Audit Logs can be enabled and sent to CloudWatch

## Amazon RDS Proxy

- Fully managed database proxy for RDS
- Allows apps to pool and share DB connections which minimises stress on db resources and open connections
- Autoscaling and highly available
- Reduced failover time
- Supports RDS and Aurora
- Enforce IAM Authentication for DB
- RDS Proxy is never publicly accessible (must be accessed from VPC)

## Elasticache

- Managed Redis or Memcached
- High-performance, low-latency in-memory dbs
- Helps reduce load on dbs for read intensive workloads

### Example Architecture – DB Cache

- Applications query Elasticache, if the data is not there, then get from RDS and store in Elasticache
- Cache must have an invalidation strategy to make sure only the most current data is in cache

### Example Architecture – User Session Store

- User logs into application and application writes session data into Elasticache
- User hits another instance of the application and that instance retrieves the data and the user is already logged in

### Redis vs Memcached

#### Redis

- Multi-AZ with Auto-Failover
- Read replicas
- Data durability using AOF (Append-Only File) persistence
  - Every write operation is logged and can be replayed again on startup to recreate the original dataset
- Backup and Restore
- Supports Sets and Sorted Sets

#### Memcached

- Multi-node for partioning of data
- No high-availability
- Non-persistent
- No backup and restore

### Elasticache Strategies

- Lazy-loading
  - Check for data in cache, if not there, query db and store in cache
  - Pros:
    - Only requested data is in cache
    - Node failures are not fatal (impact will be increased latency)
  - Cons:
    - 3 round trips for not finding data in cache (impact will be increased latency)
    - Stale data: data can be updated in the db and outdated in the cache
- Write Through
  - When an add or update action comes in, write it to the db and to the cache
  - Pros:
    - Data in cache is never stale
    - Reads are quick
  - Cons:
    - Missing data until it has been saved to RDS. Mitigation is combining with Lazy-loading strategy
    - Cache churn: lots of data will never be read

#### Cache Evictions and Time-to-live (TTL)

- Cache evictions can occuin three ways:
  - delete the item explicitly from the cache
  - item is evicted because the memory is full and the data hasn't been accessed recently
  - you set an item time-to-live (TTL)
- TTL can range from few seconds to hours or days
- If too many evictions happen due to memory, scale up or out

### MemoryDB for Redis

- Redis-compatible, durable, in-memory db service
- Fast performance with over 160 million requests / second
- Multi-AZ transactional log
- Scale from 10s GBs to 100s TBs of storage
- Use cases: web and mobile apps, online gaming, media streaming
