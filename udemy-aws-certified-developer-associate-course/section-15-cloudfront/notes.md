# AWS Cloudfront

## What is Cloudfront?

- Content Delivery Network (CDN) service
- Improves the read performance, content is cached at edge locations
- Improves user experience
- 216 Point of Presence globally (edge locations)
- DDoS protection

### Origins

- S3 bucket
  - For distributing files and caching them at the edge
  - Enhanced security with CloudFront Origin Access Control
  - CloudFront can be used as an ingress
- Custom Origin (HTTP)
  - Application Load Balancer
  - EC2 instance
  - S3 website
  - Any HTTP backend you want

### CloudFront vs S3 Cross Region Replication

- CloudFront:
  - Global Edge Network
  - Files are cached for a TTL
  - Great for static content that must be available everywhere
- S3 Cross Region Replication:
  - Must be setup for each region you want replication to happen
  - Files are updated in near real-time
  - Read-only
  - Great for dynamic content that needs to be available at low-latency in few regions

### Caching & Caching Policies

- Cache lives at each CloudFront edge location
- CloudFront identifies each object in the cache using the "Cache Key"
- You want to maximize the cache hit ratio to minimize the requests to the origin
- You can invalidate part of the cache using the CreateInvalidation API

#### CloudFront Cache Key

- A unique identifier for every object in the cache
- By default, the cache key consists of hostname + resource portion of the URL
- You can add other elements to the cache key, such as headers, cookies, query strings using CloudFront Cache Policies

#### Cache Policy

- Cache based on:
  - HTTP Headers
  - Cookies
  - Query Strings
- Control the TTL
  - Can be set between 0 seconds and 365 days
  - Can be set by the origin using the Cache-Control header, Expires header
- All HTTP Headers, cookies and query strings included in the cache key are forwarded to the origin

#### Origin Request Policy

- Specify HTTP Headers, Cookies and/or Query Strings without including them in the cache key
- Gives the ability to add CloudFront and Custom headers to the origin requests that were not included in the viewer request

#### Cache Invalidations

- CloudFront doesn't know about changes in the origin until after the TTL expires
- You can force an entire or partial cache refresh by performing a CloudFront invalidation
- You can invalidate all files or a special path

#### Cache Behaviours

- Different Cache behaviours for a given URL pattern
- Route different kind of origins/origin groups based on the content type or path pattern

#### CloudFront — EC2 as an Origin

- EC2 instances can be used as an origin in two ways:
  - Public EC2 instances
    - Must have a public IP
    - Security group must allow inbound traffic from
  - Private EC2 instances behind a public Application Load Balancer
    - The ALB must have a security group that allows inbound traffic from CloudFront
    - The EC2 instances must allow the security group of the ALB

### Geo Restriction

- You can restrict who can access your distribution via
  - allow list
  - block list

### CloudFront Signed URL / Cookies

- Protect Cloudfront content so only users with the URL or Cookies can access the files
- Signed URL = access to individual files (one URL)
- Signed Cookies = access to multiple files (one cookie)
- Process:
  - Create a trusted key group and generate a public / private key pair
  - The private key is used by the application to sign URLs
  - The public key is stored in CloudFront to verify the URLs

### CloudFront Advanced Topics

#### Price Classes

- Define the number of edge locations you want your content to be distributed to in order to balance costs and performance

#### Multiple Origins

- Route to different origins based on the content type

#### Origin Groups

- Have multiple origins to increase high-availability and provide failover

#### Field-Level Encryption

- Sensitive information can be encrypted at the edge using CloudFront public key
- The data can then be decrypted by the application server using the private key

### Real-time Logs

- Real-time requests received by CloudFront can be sent to Kinesis Data Streams
- Monitor, analyze and act based on performance
