# CloudFormation

## What is CloudFormation?

- Infrastructure as code (IaC) service
- Declarative way of outlining your AWS Infrastructure for any resources
- Automates provisioning and updating of your AWS resources

## Benefits of CloudFormation

### Infrastructure as Code

- No resources are manually created, which is excellent for control
- The code can be version controlled for example using git
- Changes to the infrastructure are reviewed through code

### Cost

- Each resources within the stack is tagged with an identifier so you can easily see how much a stack costs you
- You can estimate the costs of your resources using the CloudFormation template

### Productivity

- Ability to destroy and re-create an infrastructure on the cloud on the fly
- Automated generation of Diagram for your templates
- Declarative programming (no need to figure out ordering and orchestration)

### Separation of concern

- You can create many stacks for many apps and many layers
  - VPC stacks
  - Network stacks
  - App stacks

## How does CloudFormation work?

- Templates uploaded to S3 and then referenced in CloudFormation
- To update a template, we can't edit previous ones. We have to re-upload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation

## Deploying CloudFormation templates

- Manual way:
  - Editing templates in the CloudFormation Designer
  - Using the console to input parameters, etc.
- Automated way:
  - Editing templates in a YAML file
  - Using the AWS CLI to deploy the templates
  - Recommended way when you fully want to automate your flow

## Building Blocks

- Templates components:

  - AWSTemplateFormatVersion - template version
  - Description - comments about the template
  - Resources (required) - your AWS resources declared in the template
  - Parameters - dynamic inputs for your template
  - Mappings - static variables for your template
  - Outputs - references to what has been created
  - Conditionals - list of conditions to perform resource creation

- Template's helpers:
  - References
  - Functions

## Resources

- Core of the CloudFormation template
- Represent the different AWS components that will be created and configured
- Resources are declared and can reference each other
- AWS figures out creation, updates, and deletes of resources for us
- Resource types identifiers are of the form:
  - `service-provider::service-name::data-type-name`
- Use the CloudFormation documentation to refer to the resources you need
- Use CloudFormation Custom Resources for resources not supported by default

## Parameters

- Parameters are a way to provide inputs to your AWS CloudFormation template
- Useful if you want to reuse your templates
- Also if some inputs can't be determined ahead of time
- Can prevent errors due to types
- Reference to a parameter using `!Ref`
- Pseudo parameters:
  - Parameters available by default in any CloudFormation template
  - `AWS::AccountId`
  - `AWS::Region`
  - `AWS::StackId`
  - `AWS::StackName`

## Mappings

- Fixed variables within your CloudFormation template
- Access values using `!FindInMap`
  - `!FindInMap [MapName, TopLevelKey, SecondLevelKey]`
  - e.g. `!FindInMap [RegionMap, !Ref "AWS::Region", 32]`

## Outputs

- Optional outputs values that we can import into other stacks
- View the outputs in the AWS Console or in using the AWS CLI
- `Export`
- `!ImportValue MyExportedValue`

## Conditions

- Control the creation of resources or outputs based on a condition

## Intrinsic Functions

- Ref
  - can be leverated to reference resources or parameters
- Fn::GetAtt
  - can be used to get the attributes of a resource
- Fn::FindInMap
- Fn::ImportValue
- Condition Functions
- Fn::Base64

## Rollbacks

- Stack Creation Fails:
  - Default: everything rolls back (gets deleted). You can look at the log to see what happened
  - Option to disable rollback and troubleshoot what happened
- Stack Update Fails:
  - The stack automatically rolls back to the previous known working state
  - Ability to see in the log what happened and error messages
- Rollback Failure?
  - Fix resources manually then issue ContinueUpdateRollback API call

## Service Roles

- IAM role that allows CloudFormation to create or update AWS resources on your behalf
  - IAM: PassRole
- If a resource has an IAM role, CloudFormation needs permission to pass the role to that resource
- The user who creates or updates the stack must have the `iam:PassRole` permission in order for CloudFormation to pass the role to that resource
- If the user doesn't have `iam:PassRole`, the stack creation or update fails

## Capabilities

- Some resources require explicit opt-in to use
- Capabilities are the way to acknowledge that the CloudFormation template might create IAM resources
- These can be accepted via the UI

## Deletion Policy

- Control what happens when the CloudFormation template is deleted or when a resource is removed from a CloudFormation template
- `Delete` - (default) which resource to delete in case of CloudFormation deletion
- `Retain` - which resource to preserve in case of CloudFormation deletion
- `Snapshot` - which resource to snapshot before CloudFormation deletion

## Stack Policy

- JSON document that defines the update actions that allowed on specific resources during Stack updates
- Protect resources from unintential updates or deletions

## Termination Protection

- Protects against accidental deletion of a stack

## Custom Resources

- For any resources that CloudFormation doesn't support
- Define custom provisioning logic for resources outside of AWS
- Backed by a Lambda function or an SNS topic
- `Custom::MyCustomResourceTypeName`
- ServiceToken property is required and must be in the same region (Lambda function ARN or SNS topic ARN)

## StackSets

- Allows you to create, update, or delete stacks across multiple accounts and regions with a single operation
- Administrator account is the one that creates the StackSet and the target accounts are the ones that have the stacks created in them
- Update a stack set, all instances are updated
