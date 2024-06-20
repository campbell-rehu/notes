# Section 6: EC2 Instance Storage

## EBS Volume

- network drive you can attach to your instances while they run
- allows instance to persist data, even after termination
- only mounted to one instance at a time
- bound to a specific availability zone (AZ)
- uses the network to communicate with the instance so there might be a bit of latency
- can be detached and reattached quickly
- to move a volume across AZ, you first need to snapshot it
- has a provisioned capacity (size in GBs and IOPS)
- Delete on termination attribute
  - controls the EBS behaviour when an EC2 instance terminates
  - Root EBS volume is deleted by default
  - Any other attached EBS volume is not deleted by default
  - Use-case preserve root volume when the instance is terminated

### EBS Snapshots

- Make a backup (snapshot) of your EBS volumes
- Don't need to detach volume before doing a snapshot, but it is recommended
- Can copy snapshots across AZ or Region

#### EBS Snapshot Features

- EBS Snapshot Archive
  - move a snapshot to an "archive tier" that is 75% cheaper
- Recycle Bin for EBS Snapshots
  - Setup rules to retain deleted snapshots so you can recover from accidental deletion
  - Specify retention (from 1 day to 1 year)
- Fast Snapshot Restore (FSR)
  - Force full initialization of snapshot to have no latency on first use
  - expensive

## AMI (Amazon Machine Image)

- A customisation of an EC2 instance
  - add your own software, configuration, operating system etc.
  - faster boot / configuration time
- AMI are built for a specific region (and can be copied across regions)
- Launch EC2 instances from:
  - A public AMI: AWS provided
  - Your own AMI: you make and maintain them yourself
  - An AWS Marketplace AMI: an AMI someone else made (and potentially sells)

## EC2 Instance Store

- EBS volumes are network drives with good but limited performance
- EC2 Instance Store are high-performance hardward disk
  - Better I/O performance
  - EC2 Instance Store lose their storage if they're stopped (ephemeral)
  - Use-cases: buffer / cache / scratch data / temporary content
  - Risk of data loss if hardward fails
  - Backups and replication are your responsibility

## EBS Volume Types

- 6 types:
  - gp2 / gp3 (SSD) - general purpose SSD volume
  - io1 / io2 Block Express (SSD) - Highest-performance SSH volume, low-latency, high-throughput
  - st1 (HDD) - low-cost HDD volume frequently accessed, throughput-intensive workloads
  - sc1 (HDD) - lowest cost HDD volume for less frequently accessed workloads
- EBS volumes are characterised by Size / Throughput / IOPS (I/O Operations per second)
- Only gp2 / gp3 and io1 / io2 Block Express can be used as boot volumes

### General purpose SSD (gp2 / gp3)

- Cost effective, low-latency
- System boot volumes, virual desktops, development and test environments
- 1GB -> 16TB
- gp3:
  - 3000 IOPS
  - Throughput 125MB/s
  - Can increate up to 16000 IOPS and throughput of 1000MB/s independently
- gp2:
  - Small volumes burst IOPS to 3000
  - Size of volume and IOPS are linked, max IOPS is 16000
  - 3 IOPS per GB means at 5334 GB (15000 / 3) we are at max IOPS

## EBS Multi-Attach

- io1 / io2 family
- Attach the same EBS volume to multiple EC2 instances in the same AZ
- Each instance has full read / write permissions to the volume
- Up to 16 EC2 instances at a time
- Must use a file system that's cluster aware

## Amazon EFS - Elastic File System

- Managed NFS (network file system) that can be mounted on many EC2
- EFS works with EC2 instances in multi-AZ
- Highly available, scalable, expensive, pay per use

### Use-cases

- content-management, web serving, data sharing
- Uses NFSv4.1 protocol
- Uses security group to control access to EFS
- Only compatible with Linux-based AMI
- Encryption at rest using KMS
- Standard file API
- File system scales automatically, pay per use

### Performance & Storage Classes

- EFS Scale
- Performance Mode
- Throughput Mode
- Storage tiers:
  - Standard: for frequently accessed files
  - Infrequent access (EFS-IA): cost to retrieve, low-cost to store, enable with a lifecycle policy to move unaccessed files after a certain period
- Availability & Durability:
  - Standard: Multi-AZ
  - One zone: One AZ, backup enabled by default, compatible with IA, cheaper

## EBS vs EFS

- EBS volumes:
  - one instance (except multi-attach io1 / io2)
  - locked to AZ
  - gp2: IO increases if the disk size increases
  - gp3 & io1: can increase IO independently
  - migrating EBS volume across AZ:
    - take a snapshot, restore the snapshot in another AZ
  - Root EBS volumes get terminated by default if the EC2 instance gets terminated
- EFS:
  - Mounting 100s of instances across AZ
  - Only for Linux instances
  - Higher price-point than EBS
  - Can use EFS-IA for cost savings
