# ELB + ASG

## High Availability & Scalability

- Scalability => an application / system can handle greater loads by adapting
- Two types of scalability:
  - Vertical scalability
  - Horizontal scalability (elasticity)
- Scalability is liked but different to High Availability

### Vertical Scalability

- Vertically scalability means **increasing the size of the instance**
- going from a t2.micro -> t2.large
- common for non-distributed systems
- RDS, ElastiCache are services that can scale vertically

### Horizontal Scalability

- Horizontal scalability means **increasing the number of instances**
- Horizontal scaling implies distributed systems
- Common for web apps / modern apps

### High Availability

- Means running application in at least 2 data centers (AKA AZs)
- Goal is to survive data center loss

#### High availability & scalability for EC2

- Vertical scaling: Increase instance size (scale up / down)
- Horizontal scaling: Increase number of instances (scale out / in)
- High availability: run instances for the same app in multi AZ
  - Auto scaling group multi AZ
  - Load balancer multi AZ

## Load balancer

- Servers that forward traffic to multiple services (e.g. EC2 instances) downstream
- Spread the load across multiple downstream instances
- Expose a single point of access to the app
- Handle failures of downstream instances
- Do healh checks on instances
- Provide SSL termination for websites
- Enforce stickiness with cookies
- High availability across AZ
- Separate public traffic from private traffic

### Elastic Load Balancer

- Managed load balancer from AWS
- Integrated with many AWS services
  - EC2, EC2 Auto scaling Groups, ECS
  - AWS Certificate Manager (ACM), CloudWatch
  - Route 53, AWS WAF, AWS Global Accelerator

### Health Checks

- Enable load balancer to know if instances it forwards traffic to are available to handle requests
- Health check is done on a port and a route (`/health` is common)
- If the response in not 200, traffic will not be routed to that instance

### Types of load balancer on AWS

- 4 types of managed Load Balancers:
  - Classic Load Balancer (v1 - old generation) - CLB
    - HTTP, HTTPS, TCP, SSL (Secure TCP)
  - Application Load Balancer - ALB
    - HTTP, HTTPS, Websocket
  - Network Load Balancer - NLB
    - TCP, TLS (Secure TCP), UDP
  - Gateway Load Balancer - GWLB
    - Operates at Layer 3 - IP protocol
- Some load balancers can be setup as internal (private) or external (public) ELBs

### Load Balancer Security Groups

- Load balancer security group
  - Specific type, protocol, port etc with source = `0.0.0.0/0` (anywhere)
- Then have a security group on the application which only accepts requests from the load balancer
  - Enhanced security mechanism

#### Application Load Balancer

- Layer 7 only (HTTP)
- Route to multiple HTTP applications across machines (target groups)
- Load balance to multiple applications on the same machine (ex containers)
- Support for HTTP/2 and Websockets
- Support redirects
- Support routing tables
  - Routing based on path in URL e.g. /user and /search can sit behind the same ALB
  - Routing based on hostname in URL
  - Routing based on Query String, Headers
- ALB great for microservices and container-based application
- Port mapping feature to redirect to a dynamic port in ECS

#### ALB Target Groups

- Can be EC2 instances
- Can be ECS tasks
- Can be Lambda functions
- Can be IP addresses (must be private IPs)
- ALB can route to multiple target groups
- Health checks are at the target group level

#### Network Load Balancer

- Layer 4 load balancer
- Forward TCP & UDP traffic
- Less latency (~100ms vs 400ms for ALB)
- one static IP per AZ
- used for extreme performance, TCP or UDP traffic
- not included in free tier

#### NLB Target Groups

- Can be EC2 instances
- Can be IP addresses (must be private IPs)
- Can be ALB
- Health checks support TCP, HTTP and HTTPS

#### Gateway Load Balancer

- Operates at Layer 3 (Network layer) - IP Packets
- Transparent network gateway
  - single entry/exit for all traffic
- Load Balancer
  - distributes traffic to your virtual applicances
- Uses the GENEVE protocol on port 6081
- Example use-cases:
  - Firewalls
  - Intrusion detection and prevention systems
  - Deep packet inspection systems
  - Payload manipulation

#### GLB Target Groups

- EC2 instances
- IP Addresses

### Sticket Sessions (Session affinity)
