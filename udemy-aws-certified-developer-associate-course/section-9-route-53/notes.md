# Route 53

## What is DNS?

- Domain Name System
- Translates human friendly hostnames into machine IP addresses
- Terminology:
  - Domain registrar: Amazon Route 53, GoDaddy etc.
  - DNS Recordst: A, AAAA, CNAME, NS etc.
  - Zone File: contains DNS records
  - Name Server: resolves DNS queries
  - Top-Level Domain (TLD): .com, .nz, etc.
  - Second-Level Domain (SLD): amazon.com, website.nz

## Amazon Route 53

- Highly-available, scalable managed and Authoritative DNS
  - Authoritative means the customer can update the DNS records
- Also a Domain Registrar
- Health checks
- Only AWS service which provides 100% availability SLA
- 53 is the traditional DNS port

### Records

- How you want to route trffic for a domain
- Each record contains:
  - Domain / Subdomain Name
  - Record Type
  - Value
  - Routing Poilcy
  - TTL
- Supports the following DNS record types:
  - A, AAAA, CNAME, NS and others

#### Record Types

- A - maps a hostname to IPv4 address
- AAAA - maps a hostname to IPv6 address
- CNAME - maps a hostname to another hostname
  - The target is a domain name which must have an A or AAAA record
  - Can't create a CNAME record for the top node of a DNS namespace
- NS - Name Servers for the Hosted Zone

### Hosted Zones

- A container for records that define how to route traffic to a domain and its subdomains
- Public Hosted Zones & Private Hosted Zones

### Records TTL

- High TTL e.g. 24 hours
  - Less traffic on Route 53
  - Possibly outdated records
- Low TTL e.g. 60 seconds
  - More traffic on Route 53 (increased AWS cost)
  - Records are outdated for less time
  - Easy to change records
- TTL is mandatory for all records except Alias records

### CNAME vs Alias

- CNAME points a hostname to any other hostname. Only works for Non-Root Domain (something.mydomain.com)
- Alias points a hostname to an AWS Resource. Works for Root and Non-Root Domain (mydomain.com). Free

### Alias Records Targets

- Elastic Load Balancers
- CloudFront Distributions
- API Gateway
- Elastic Beanstalk environments
- S3 Websites
- VPC Interface Endpoints
- Global Accelerator
- Route 53 record in the same hosted zone
- Cannot set an Alias record for an EC2 DNS name

### Routing Policies

#### Simple Policy

- Route traffic to a single resource
- Can specify multiple values
- When Alias enabled, specify only one AWS resource
- Can't be associated with Health Checks

#### Weighted Policy

- Control the % of the requests that go to each specific resource
- Weights don't need to sum up to 100
- DNS records must have the same name and type
- Use cases: load balancing between regions, testing new application versions
- Weight of 0 will stop sending traffic to a resource
- If all records have weight of 0, then all records will be returned equally

#### Latency-based

- Redirect to the resource that has the least latency close to us
- Latency is based on traffic between users and AWS Regions
- Can be associated with Health Checks
