# Section 4: IAM & AWS CLI

- IAM = Identity and access management, Global service
- Root account
- Users; can be grouped
- Groups cannot contain groups
- Users can belong to multiple groups

## IAM: Permissions

- Users or groups can be assigned JSON documents called **policies**
- "Least privilege principle"

## IAM Users and Groups

- IAM is globally available

## IAM Policies

### Policies Structure

- Consists of:
  - Version - policy language version
  - Id - identifier for the policy (optional)
  - Statement - one or more individual statements (required)
    - Sid - identitfier for the statement
    - Effect - "Allow" or "Deny"
    - Principal - which account/user/role to which the policy will be applied to
    - Action - list of actions the policy allows or denies
    - Resource - list of resources to which the actions apply to
    - Condition - conditions for when this policy is in effect (optional)

## IAM Password Policy

- Can setup a password policy
- MFA = password you know + security device you own
  - if password is stolen or hacked, the account is not compromised
  - device options:
    - Virtual MFA device
      - multiple tokens on a single device
    - U2F security key
      - eg YubiKey
    - Hardware Key Fob MFA device
    - Hardware Key Fob for AWS GovCloud

## AWS Access

- Three options:
  - AWS Management Console - protected by password + MFA
  - AWS Command Line Interface (CLI) - protected by access keys
  - AWS Software Developer Kit (SDK) - for code: protected by access keys
- Access Keys are generated through AWS Console
- Users manage their own access keys

## IAM Roles for AWS Services

- Some AWS services will need to perform actions on your behalf
- Assign permissions to AWS services with IAM Roles
- Common roles:
  - EC2 Instance Roles
  - Lamda Function Roles
  - Roles for CloudFormation

## IAM Security Tools

- IAM Credentials Report (account-level)
  - lists all account's users and the status of their various credentials
- IAM Access Advisor
  - shows the service permissions granted to a user and when they were last accessed

## Shared Resposibility Model for IAM

- AWS is responsible for:
  - Infrastructure (global network security)
  - Configuration and vulnerability analysis
  - Compliance Validation
- You are responsible for:
  - Users, Groups, Roles, Policies management and monitoring
  - Enabling MFA on all accounts
  - Rotating keys often
  - Using IAM tools
  - Analyzing access patterns and review permissions

##
