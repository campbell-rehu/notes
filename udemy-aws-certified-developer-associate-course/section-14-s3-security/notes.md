# Amazon S3 Security

## S3 Encryption

- One of 4 methods:
  - Server-Side Encryption (SSE)
    - SSE with Amazon S3-Managed Keys (SSE-S3) - Enabled by Default
      - Encrypts S3 objects using keys handled, managed and owned by AWS
    - SSE with KMS Keys stored in AWS KMS (SSE-KMS)
      - Leverage AWS Key Management Service (KMS) to manage encryption keys
    - SSE with Customer-Provided Keys (SSE-C)
      - When you want to manage your own encryption keys
  - Client-Side Encryption

### SSE-S3

- Encryption using keys handled, managed and owned by AWS
- Encryption type is AES-256
- Must set header "x-amz-server-side-encryption":"AES256"
- Enabled by default for new buckets and new objects

### SSE-KMS

- Encryption using keys handled and managed by KMS
- User control + audit key usage using CloudTrail
- Must set header "x-amz-server-side-encryption":"aws:kms"
- API Calls to use KMS has limits

### SSE-C

- Amazon S3 does NOT store the encryption key you provide
- HTTPS must be used
- Encryption key must be provided in HTTP headers for every HTTP request made

### Client-Side Encryption

- Use client libraries such as Amazon S3 Client-Side Encryption Library
- Clients must encrypt and decrypt data themselves before sending or retrieving from Amazon S3
- Customer fully manages the keys and encryption cycle

### Force Encryption in Transit

- Apply a bucket policy to deny any traffic using HTTP (i.e. where "aws:SecureTransport": "false")

### Default encryption vs Bucket Policies

- Can be enforced via the bucket settings
- Can also be enforced using a Bucket Policy and refuse API calls without specific encryption headers
- Bucket policy are evaluated before Default settings

## CORS

- Cross-Origin Resource Sharing
- Origin = scheme (protocol e.g. https) + host (domain e.g. google.com) + port (e.g. 443 for HTTPS, 80 for HTTP)
- Web Browser based security mechanism to allow requests to other origins while visiting the main origin
  - E.g https://www.example.com & https://other.example.com
- Requests won't be fulfilled unless the other origin allows for the requests using CORS Headers (e.g. Access-Control-Allow-Origin)
- Can allow for a specific origin or for \* (all origins)

### S3 CORS

- Need to enable the correct CORS headers on S3 bucket if user makes cross-origin request

### MFA Delete

- Force users to generate a code on a device before doing any important S3 operations
- Versioning must be enabled on the bucket
- Only the bucket owner (not IAM users) can enable/disable MFA-Delete

### S3 Access Logs

- For audit purposes
- Any request made to S3 will be logged into another S3 bucket
- Data can be analysed using data analysis tools
- The target logging bucket must be in the same AWS region
- Do not log into the same bucket that is being monitored
