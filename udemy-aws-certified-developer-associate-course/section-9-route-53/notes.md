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

#### Health Checks

- Only for public resources
- Health Checks allow for Automated DNS failover in one of three way:
  - Monitor an endpoint
  - Monitor other health checks (Calculated Health Checks)
  - Monitor CloudWatch Alarms (Helpful for private resources)

##### Monitor an Endpoint

- ~15 global health checkers
- HTTP, HTTPS and TCP
- Healthy / Unhealthy threshold
- Interval of healthchecks
- If > 18% of health checkers report the endpoint is healthy, Route 53 considers it healthy. Otherwise, unhealthy.
- Only pass for 2xx or 3xx status code responses
- Can be setup to pass / fail based on the text in the first 5120 bytes of the response
- Configure to allow incoming requests from Route 53 health checker IP address ranges

##### Calculated Health Checks

- Combine the results of multiple health checks
- Use OR, AND or NOT
- Monitor up to 256 Health Checks

##### Private Hosted Zones

- Route 53 health checkers are outside the VPC
- Can't access private endpoints
- Create a CloudWatch Metric and associated Alarm and then have the health checker monitor the alarm

#### Failover (Active-Passive)

- Primary and Secondary instances
- Associate Route 53 health check with primary instance and failover to secondary instance

#### Geolocation

- Routing is based on user location
- Specify location by Continent, Country, or by US State
- Use cases: website localisation, restrict content distribution, load balancing
- Can be associated with health checks

#### Geoproximity

- Routh traffic to resources based on user location and resource location
- Resources have a defined "bias" where the higher the number, the wider the geographic area for users to be routed to that resource

#### Traffic Flow

- Visual editor to manage complex routing decision trees
- Simplify the process of creating and maintaining records in large and complex configurations
- Supports versioning
- Can be applied to different Route 53 Hosted Zones

#### IP-Based Routing

- Routing based on client's IP addresses
- Provide a list CIDRs for the clients
- Use cases: optimise performance, reduce network costs
- Example: route users from a particular ISP to a specific endpoint

#### Multi-value

- Routing traffic to multiple resources
- Can be associated with health checks so only healthy records are returned
- Multi-value is not a substitute for having an ELB
- Up to 8 healthy records returned

## 3rd-party Domains & Route 53

- Create a hosted zone in Route 53
- Update the NS records on the 3rd party website to use Route 53 Name Servers
