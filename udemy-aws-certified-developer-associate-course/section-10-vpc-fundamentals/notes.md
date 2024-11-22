# VPC

## VPC and Subnets

- VPC: private network to deploy your resources
- Subnets: allow you to partition your network inside your VPC
  - Public subnet: accessible from the internet
  - Private subnet: not accessible from the internet
  - Tied to an Availability Zone
- Use Route Tables

## Internet gateways & NAT Gateways

- Internet gateways (IGW) help VPC instances connect with the internet
- Public subnets have a route to the internet gateway
- NAT gateways (AWS-managed) and NAT instances (self-managed) allow private subnet instances to access the internet while remaining private
- NAT gateway in the public subnet with a route to the IGW, private subnet instance has a route to the NAT gateway so can access the internet

## Network ACL & Security Groups

- Network ACL (NACL)
  - firewall which controls traffic to and from a subnet
  - can have ALLOW and DENY rules
  - attached at the subnet level
  - rules only include IP addresses
- Security groups
  - firewall which controls trffic to and from an Elastic Network Interface / EC2 instance
  - Can only have ALLOW rules
  - rules include IP addresses and other security groups

## VPC Flow Logs

- Capture info about IP traffic going into your interfaces:
  - VPC flow logs
  - Subnet flow logs
  - Elastic Network Interface flow logs
- Helps to monitor and troubleshoot connectivity issues

## VPC Peering

- Connect two VPC, privately using AWS' network
- Make them behave as if they were on the same network
- Must not have overlapping CIDR (IP address ranges)
- Not transitive (must establish connection between each VPC that needs to communicate)

## VPC Endpoint

- Allow you to connect to AWS services using a private network instead of the public www network
- VPC Endpoint Gateway to S3 and DymamoDB
- VPC Endpoint Interface to any other AWS services

## Site-to-Site VPN

- Connect to on-premises VPN to AWS
- Connection is encrypted and goes over the public internet

## Direct Connect (DX)

- Physical connection between on-premises and AWS
- Private, secure and fast
- Over a private network
- Takes at least a month to establish
