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
