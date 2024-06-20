# Section 5: EC2 Fundamentals

## EC2 Instance Types

- Different types optimised for different use-cases
- Naming convention: m5.2xlarge
  - m: instance class
  - 5: generation
  - 2xlarge: size within instance class

### General Purpose

- for diversity workloads such as web servers or code repositories
- Balanced between:
  - Compute
  - Memory
  - Networking

### Compute Optimised

- for compute-intensive tasks that require high performance processors

### Memory Optimised

- fast performance for workloads that process large data sets in memory

### Storage Optimised

- for storage-intensive tasks that require high, sequential read and write access to large data sets on local storage

## Seurity Groups

- Fundamental of network security in AWS
- Control how traffic is allowed into or out of EC2 instances
- Only contain allow rules
- Can reference by IP or by security group

## Deeper Dive

- security groups act as "firewall" on EC2 instances
- Regulate:
  - access to ports
  - authorised IP ranges (IPv4 and IPv6)
  - Control inbound network
  - Control outbound network
- Can be attached to multiple instances
- Locked down to region / VPC combination
- Lives outside EC2
- Good to maintain one separate group for SSH access
- If application is not accessible, then it's a security group issue
- If application gives "connection refused" error, then it's an application error or it's not launched
- All inbound traffic is blocked by default
- All outbound traffic is authorised by default

## Ports to know

- 22 = SSH (Secure Shell) - log into a Linux instance
- 21 = FTP (File Transfer Protocol) - upload files into a file share
- 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
- 80 = HTTP - access unsecured websites
- 443 = HTTPS - access secured websites
- 3389 = RDP (Remote Desktop Protocol) - log into a Windows instance

## IAM Roles for EC2

- It is best practice to assign an IAM role to EC2 instances
- One should not enter personal AWS credentials on an EC2 instance
