# AWS Elastic Beanstalk

## Overview

- Developer-centric view of deploying an application on AWS
- Managed service
  - Automatically handles capacity provisioning, load balancing, scaling, and application health monitoring, instance configuration, and maintenance
  - Only the application code is the responsibility of the developer
- Full control over the configuration
- Beanstalk is free, but you pay for the underlying resources
- Relies on CloudFormation to provision other AWS services

### Components

- Application
  - collection of Elastic Beanstalk components
- Application version
  - an iteration of your application code
- Environment
  - collection ow AWS resources running an application version
  - Tiers
    - Web server environment tier
    - Worker environment tier
  - Can create multiple environments for an application
- Single instance and High availability environment
  - Single instance: one EC2 instance
  - High availability: multiple EC2 instances

## Deployment Options

- All at once
  - Deploys the new version to all instances simultaneously
  - Downtime
- Rolling
  - Deploys the new version in batches
  - No downtime
- Rolling with additional batch
  - Deploys the new version in batches
  - Launches new instances to move the batch
  - No downtime
- Immutable
  - Deploys the new version to a fresh group of instances and then swaps instances when everything is ready
  - No downtime
- Blue/Green
  - Deploys the new version to a separate environment and then switch over when everything is ready
  - Manually done via Route 53 weighted routing
  - No downtime
- Traffic splitting
  - Deploys the new version to a separate environment and then gradually shifts traffic to the new version (canary deployment)
  - No downtime

## Elastic Beanstalk CLI

- EB CLI can be used to automate the deployment process

## Lifecycle Policy

- Can store at most 1000 application versions
- If limit is reached, no new versions can be deployed
- Lifecycle policy can be used to delete old application versions
  - Based on time or space
- Versions that are currently deployed cannot be deleted

## Elastic Beanstalk Extensions

- Zip file containing application code must be deployed to Elastic Beanstalk
- Parameters set in the UI can be configured with code using files
- .ebextensions directory in the root of the application source bundle
  - YAML or JSON files
  - .config file extension
  - Can modify some default settings
  - Can configure resources, environment variables, etc.

## Elastic Beanstalk Migration: Load Balancer

- After creating an Elastic Beanstalk environment, the load balancer cannot be changed
- To change the load balancer:
  - create a new environment with the same configuration except the load balancer,
  - deploy the application to the new environment
  - perform a CNAME swap or Route 53 update

## Elastic Beanstalk Migration: RDS

- RDS can be provisioned with Elastic Beanstalk, great for development and testing
- For production, it is recommended to create a separate RDS instance and configure the application to use it
- To migrate from RDS provisioned with Elastic Beanstalk to a standalone RDS instance:
  - Create a snapshot of the RDS instance
  - Protect the database from being deleted via RDS console
  - Create a new Elastic Beanstalk environment with the same configuration as the existing environment without RDS
  - Create a new RDS instance from the snapshot
  - Update the application configuration to use the new RDS instance
  - Deploy the application
  - Perform a CNAME swap or Route 53 update
  - Terminate the old environment
  - Delete CloudFormation stack
