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
  - Syncronisation is established between the two DBs

## Amazon Aurora

