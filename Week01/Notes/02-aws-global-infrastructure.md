AWS Identity and Access Management (IAM)

Overview

AWS Identity and Access Management (IAM) is the AWS service responsible for authentication and authorization. It controls who can access AWS resources and what actions they are allowed to perform.

IAM follows the Principle of Least Privilege, ensuring that users, applications, and AWS services receive only the permissions necessary to perform their tasks.

⸻

Authentication vs Authorization

Although these terms are often used together, they serve different purposes.

Authentication

Authentication answers the question:

Who are you?

Examples:

* Username and password
* Multi-Factor Authentication (MFA)
* Fingerprint
* Face ID
* Single Sign-On (SSO)

Authentication verifies identity before access is granted.

⸻

Authorization

Authorization answers the question:

What are you allowed to do?

Examples:

* Read objects from an S3 bucket
* Launch an EC2 instance
* Delete an RDS database
* View CloudWatch logs

Authorization determines which AWS resources and actions an authenticated identity can access.

⸻

AWS Root User

When an AWS account is created, AWS automatically creates a Root User.

The Root User has unrestricted access to every AWS service and account setting.

The Root User should only be used for:

* Initial AWS account setup
* Billing management
* Account recovery
* Tasks that require root-level permissions

Best Practices

* Enable Multi-Factor Authentication (MFA)
* Do not use the Root User for daily development
* Create IAM users or use IAM Identity Center for administrators and developers

⸻

IAM User

An IAM User represents a single person or application within an AWS account.

Examples:

* Developer
* DevOps Engineer
* Database Administrator
* CI/CD Service Account

Each IAM User has unique credentials and permissions.

⸻

IAM Groups

An IAM Group is a collection of IAM Users that share the same permissions.

Example:

Developers
├── John
├── Sarah
├── Emma
└── David

Instead of assigning permissions to every developer individually, permissions are assigned once to the group.

Benefits

* Easier permission management
* Consistent access control
* Simplified onboarding and offboarding

⸻

IAM Policies

IAM Policies are JSON documents that define permissions.

A policy specifies:

* Effect (Allow or Deny)
* Action (What can be performed)
* Resource (Which AWS resource)

Example:

{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::tessbuilds-assets/*"
}

This policy allows reading objects only from the specified S3 bucket.

⸻

IAM Roles

An IAM Role is a set of temporary permissions that can be assumed by trusted AWS services, users, or external identities.

Unlike IAM Users, Roles do not have permanent credentials.

Common AWS services that assume Roles include:

* Lambda
* EC2
* ECS
* EKS
* AWS Step Functions
* GitHub Actions (OIDC)
* Other AWS Accounts

⸻

Why IAM Roles Are Better Than Access Keys

Older applications often stored:

AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY

inside:

* Source code
* Environment variables
* Configuration files

Problems:

* Credentials can leak
* Manual rotation is required
* Long-lived credentials increase security risk

IAM Roles eliminate these issues by providing temporary credentials.

⸻

AWS Security Token Service (STS)

AWS Security Token Service (STS) generates temporary security credentials for IAM Roles.

Workflow:

AWS Service (EC2/Lambda)
        │
        ▼
 Assume IAM Role
        │
        ▼
 AWS STS
        │
 Issues Temporary Credentials
        │
        ▼
 AWS SDK
        │
        ▼
 AWS Services (S3, DynamoDB, SNS, etc.)

These credentials:

* Expire automatically
* Rotate automatically
* Are managed entirely by AWS

Applications never need to store long-lived AWS credentials.

⸻

Lambda Execution Role

Each Lambda function has an Execution Role.

The Execution Role defines what AWS resources the function can access.

Example:

Lambda Function
        │
        ▼
Execution Role
        │
        ▼
AWS STS
        │
        ▼
Temporary Credentials
        │
        ▼
Amazon S3

In my production applications, AWS SAM automatically creates and attaches the execution role during deployment.

The AWS SDK retrieves temporary credentials automatically through the default credential provider chain, so no Access Keys are stored in my application code.

⸻

Principle of Least Privilege

The Principle of Least Privilege states:

Grant only the permissions required to perform a specific task.

Example:

A Lambda function that uploads images should receive:

s3:PutObject

on a specific bucket.

It should not receive:

* AdministratorAccess
* Full S3 access
* EC2 permissions
* IAM permissions

Restricting permissions reduces the impact if an application or credential is compromised.

⸻

Production Example

My production backend architecture:

iOS App
    │
    ▼
API Gateway
    │
    ▼
AWS Lambda
    │
    ├── Amazon S3
    ├── CloudWatch
    └── Other AWS Services

Authentication between Lambda and AWS services is handled using the Lambda Execution Role.

No Access Keys are stored in the application.

AWS automatically provides temporary credentials through STS, and the AWS SDK authenticates transparently.

⸻

Interview Questions

What is the difference between Authentication and Authorization?

Authentication verifies identity (“Who are you?”), while Authorization determines what actions an authenticated identity is allowed to perform.

⸻

Why should EC2 or Lambda use IAM Roles instead of Access Keys?

IAM Roles provide temporary credentials that are automatically generated, rotated, and expired by AWS STS. They eliminate the need to store long-lived credentials and improve overall security.

⸻

What is AWS STS?

AWS Security Token Service (STS) is the AWS service that issues temporary security credentials for IAM Roles.

⸻

What is the Principle of Least Privilege?

Grant only the permissions required for a workload to perform its intended function—nothing more.

⸻

Why are IAM Groups useful?

Groups simplify permission management by assigning permissions once to multiple users, improving consistency and reducing administrative overhead.

⸻

Key Takeaways

* IAM controls authentication and authorization.
* Never use the Root User for daily work.
* Use IAM Groups to manage users efficiently.
* IAM Policies define permissions using JSON.
* IAM Roles provide temporary credentials through AWS STS.
* Lambda and EC2 should authenticate using IAM Roles, not Access Keys.
* Follow the Principle of Least Privilege to minimize security risks.
* AWS SDKs automatically retrieve temporary credentials from the execution environment, eliminating the need to manage AWS secrets in application code.
  
