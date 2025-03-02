# ECS, ECR & Fargate - Docker in AWS

## Amazon Elastic Container Service (ECS)

- Launching Docker containers on AWS = Launching ECS Tasks on ECS Clusters
- EC2 Launch Type: you must provision and maintain the infrastructure (the EC2 instances)
- Each EC2 instance must run the ECS Agent to register in the ECS Cluster
- AWS takes care of starting / stopping containers

### Fargate Launch Type

- Launch Docker containers without provisioning or managing servers
- You just create task definitions, specify the CPU and memory requirements
- To scale, just increase the number of tasks

### IAM Roles for ECS

- EC2 Instance Profile

  - Used by the ECS agent to make API calls on your behalf

- ECS Task Role

  - Used by the ECS task to make API calls on your behalf
  - Use different roles for different ECS Services you run
  - Task role is defined in the task definition

### Load Balancer Integrations

- Application Load Balancer infron of ECS Services
- Network Load Balancer for high throughput or high performance needs

### Data Volumes

- Mount EFS (Elastic File System) onto ECS tasks
- Works for both EC2 and Fargate launch types
- Tasks running in any AZ will share the same data in the EFS system
- Amazon S3 cannot be mounted to ECS

### ECS Service Auto Scaling

- Automatically increase / decrease the desired number of ECS tasks
- Uses AWS Application Auto Scaling:
  - Average CPU utilization
  - Average memory utilization
  - Request Count per Target
- Target Tracking for a specific CloudWatch metric
- Step Scaling based on specific CloudWatch alarms
- Scheduled Scaling based on predictable changes
- ECS Service Auto Scaling (task level) != EC2 Auto Scaling (EC2 instance level)
- Fargate Auto Scaling us much easier to setup

#### Auto Scaling EC2 Instances

- Auto Scaling Group Scaling
  - Scale based on CPU utilization
  - Add EC2 instances over time
- ECS Cluster Capacity Provider (preferred)
  - Used to automatically provision and scale infrastructure for ECS Tasks
  - Capacity Provider paired with an Auto Scaling Group
  - Add EC2 instances when you're missing capacity (CPU, RAM etc)

### ECS Rolling Updates

- Minimum healthy percent: the minimum percentage of tasks that must be healthy during an update
- Maximum percent: the maximum percentage of tasks that are allowed to be in the RUNNING or PENDING state during an update

### Solutions Architectures

- ECS tasks invoked by Event Bridge
  - Some Event Bridge event triggers an ECS task
- ECS tasks invoked by Event Bridge Schedule
  - ECS tasks are scheduled to run at specific times
- ESC tasks with SQS Queue
  - ECS Service polls an SQS queue for messages
- Intercept Stopped Tasks using Event Bridge

### Task Definitions

- Metadata in JSON format to tell ECS how to run a Docker container
- Can contain definition up to 10 containers

#### Load Balancing (EC2 Launch Type)

- Dynamic Host Port Mapping
  - If you define only the container ports in the task definition
  - The ALB finds the right port on your EC2 instances
  - EC2 instance security group must allow traffic on all ports from the ALB's security group

#### Load Balancing (Fargate Launch Type)

- Each task has a unique private IP address through it's own ENI (Elastic Network Interface)
- The ALB can forward traffic to the ENI of the Fargate task

#### IAM Fole per Task Definition

- Each ECS Task can have a different IAM Role

#### Environment Variables

- Hardcoded
- SSM Parameter Store
- Secrets Manager
- Environment files (bulk) from S3

#### Data Volumes (Bind mounts)

- Share data between multiple containers in the same task definition
- EC2 Tasks: use EC2 instance storage
  - Data are tied to lifecycle of the EC2 instance
- Fargate Tasks: use ephemeral storage
  - Data are tied to lifecycle of the task
- Sidecar container pattern

### Task Placements

- When a task of type EC2 is launched, ECS must determine where to place it
- Similarly, when a service scales in, ECS must determine which task to terminate
- Can define task placement strategy and task placement constraints
- Only for ECS with EC2 launch type, not for Fargate

#### Task Placement Strategies

- Best effort
- Binpack:
  - Place tasks based on the least available amount of CPU or memory
  - Minimizes the number of instances in use
- Random:
  - Place the task randomly
- Spread:
  - Place the tasks evenly based on the specified value
- Can mix the strategies together

#### Task Placement Constraints

- distinctInstance:
  - place each task on a different container instance
- memberOf:
  - place the task on instances that satisfy an expression

## Amazon Elastic Container Registry (ECR)

- Store and manage Docker images on AWS
- Private and public repositories
- Fully integrated with ECS, backed by S3
- Access is controlled by IAM policies

### Using AWS CLI

- Login command
  - `aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com`
- Docker commands
  - push
    - `docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-web-app`
  - pull
    - `docker pull 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-web-app`
  - If can't pull a Docker image, check the IAM permissions

## AWS Copilot

- CLI tool to build, release, and operate production-ready containerized applications on AWS
- Run your apps on AppRunner, ECS, and Fargate
- Copilot will create all the infrastructure for you

## Amazon EKS (Elastic Kubernetes Service)

- To launch managed Kubernetes clusters on AWS
- Alternative to ECS, similar goal but different API
- EKS is more flexible and can run any Kubernetes application

### Node Types

- Managed Node Groups

  - Fully managed by AWS
  - Use EC2 instances

- Self-Managed Node Groups

  - You manage the EC2 instances

- AWS Fargate

### Data Volumes

- Specify StorageClass in the EKS cluster
- Leverages a Container Storage Interface (CSI) driver
- Supports EBS, EFS, FSx for Lustre, FSx for Windows
