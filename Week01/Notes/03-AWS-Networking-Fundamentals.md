Week 1 • Day 3 — AWS Networking Fundamentals

Learning Objectives

By the end of this lesson, I understand:

* Amazon VPC
* CIDR Blocks
* Public and Private Subnets
* Route Tables
* Internet Gateway (IGW)
* NAT Gateway
* Security Groups
* Network ACLs (NACLs)
* Bastion Hosts
* VPC Endpoints
* Production network architecture in AWS

⸻

Amazon Virtual Private Cloud (VPC)

Amazon Virtual Private Cloud (VPC) is a logically isolated virtual network inside AWS where cloud resources such as EC2 instances, RDS databases, and Lambda functions can run securely.

Think of a VPC as a private data center hosted inside AWS.

Every AWS workload runs inside a VPC.

⸻

Default VPC vs Custom VPC

Default VPC

AWS automatically creates a Default VPC in every Region.

Characteristics:

* Public subnets already exist
* Internet Gateway attached
* Suitable for development and learning
* Simple configuration

⸻

Custom VPC

Production environments typically use Custom VPCs.

Benefits:

* Better network isolation
* Custom IP ranges
* Separate public and private subnets
* Improved security
* Greater flexibility

⸻

CIDR Block

A CIDR (Classless Inter-Domain Routing) block defines the IP address range available inside a VPC.

Example:

10.0.0.0/16

This provides approximately 65,536 private IP addresses.

Common private IPv4 ranges:

10.0.0.0/8
172.16.0.0/12
192.168.0.0/16

⸻

Subnets

A subnet divides a VPC into smaller networks.

Each subnet belongs to a single Availability Zone.

Example:

VPC (10.0.0.0/16)
├── Public Subnet A
├── Public Subnet B
├── Private Subnet A
└── Private Subnet B

⸻

Public Subnet

A Public Subnet has a route to an Internet Gateway.

Typical resources:

* Application Load Balancer
* Bastion Host
* Public EC2 instances
* NAT Gateway

Resources in a public subnet may have public IP addresses.

⸻

Private Subnet

A Private Subnet has no direct route to the Internet.

Typical resources:

* Amazon RDS
* Redis
* Backend APIs
* Internal EC2 instances
* ECS Tasks

Private resources should not be directly accessible from the Internet.

⸻

Route Tables

A Route Table determines where network traffic is sent.

Example:

Destination        Target
10.0.0.0/16       Local
0.0.0.0/0         Internet Gateway

Private subnets typically route internet-bound traffic through a NAT Gateway instead of an Internet Gateway.

⸻

Internet Gateway (IGW)

An Internet Gateway connects a VPC to the public Internet.

Functions:

* Allows inbound internet traffic
* Allows outbound internet traffic
* Required for public subnets

Without an Internet Gateway, public resources cannot communicate with the Internet.

⸻

NAT Gateway

A NAT (Network Address Translation) Gateway allows resources in private subnets to access the Internet without exposing them to inbound public traffic.

Common use cases:

* Download software updates
* Pull Docker images
* Access AWS APIs
* Install application dependencies

A NAT Gateway supports outbound-only internet communication for private resources.

⸻

Security Groups

Security Groups act as virtual firewalls for AWS resources.

Characteristics:

* Instance-level firewall
* Stateful
* Allow rules only
* Attached to EC2 instances, Lambda ENIs, RDS, etc.

Example:

Inbound
HTTPS (443) → Allow
SSH (22) → Allow only from administrator IP
Outbound
Allow All

Because Security Groups are stateful, return traffic is automatically allowed.

⸻

Network ACL (NACL)

A Network ACL is a subnet-level firewall.

Characteristics:

* Stateless
* Supports Allow and Deny rules
* Applies to the entire subnet

Unlike Security Groups, return traffic must be explicitly allowed.

⸻

Security Groups vs Network ACLs

Security Groups	Network ACLs
Stateful	Stateless
Resource-level	Subnet-level
Allow rules only	Allow and Deny rules
Return traffic automatically allowed	Return traffic must be explicitly allowed

⸻

Bastion Host

A Bastion Host is a secure EC2 instance placed in a public subnet.

Administrators connect to the Bastion Host using SSH or RDP and then securely access private resources inside the VPC.

Modern AWS environments increasingly replace Bastion Hosts with AWS Systems Manager Session Manager.

⸻

VPC Endpoints

VPC Endpoints allow private resources to communicate with AWS services without traversing the public Internet.

Examples:

* Amazon S3
* DynamoDB
* Secrets Manager
* Systems Manager

Benefits:

* Improved security
* Lower latency
* Traffic remains on the AWS network

⸻

Typical Production Architecture

                 Internet
                     │
                     ▼
                Route 53
                     │
                     ▼
        Application Load Balancer
             │                 │
             ▼                 ▼
       EC2 (AZ-A)        EC2 (AZ-B)
             │                 │
             └────────┬────────┘
                      ▼
             Private Subnet
                      │
        ┌─────────────┴─────────────┐
        ▼                           ▼
   Amazon RDS                  Amazon ElastiCache
EC2 / Lambda
      │
      ▼
Amazon S3

Characteristics:

* Load Balancer deployed in public subnets
* Application servers deployed across multiple Availability Zones
* Database deployed in private subnets
* Storage handled by Amazon S3
* High availability through Multi-AZ deployment

⸻

Production Best Practices

* Never expose databases directly to the Internet.
* Deploy applications across multiple Availability Zones.
* Keep backend services in private subnets.
* Use Security Groups to enforce least privilege.
* Use NAT Gateways for outbound internet access from private resources.
* Prefer VPC Endpoints when accessing AWS services from private workloads.
* Eliminate Single Points of Failure.

⸻

Interview Questions

What is a VPC?

A VPC is a logically isolated virtual network inside AWS where cloud resources securely communicate.

⸻

What is the difference between a Public and Private Subnet?

A Public Subnet has a route to an Internet Gateway and can host internet-facing resources. A Private Subnet has no direct internet access and hosts internal services such as databases and backend applications.

⸻

Why are databases deployed in Private Subnets?

To prevent direct internet access and reduce the attack surface.

⸻

What is an Internet Gateway?

An Internet Gateway enables communication between a VPC and the public Internet.

⸻

Why is a NAT Gateway required?

A NAT Gateway allows private resources to access the Internet for outbound communication while preventing unsolicited inbound connections.

⸻

What is the difference between Security Groups and Network ACLs?

Security Groups are stateful, resource-level firewalls. Network ACLs are stateless, subnet-level firewalls that support both allow and deny rules.

⸻

Key Takeaways

* Every AWS workload runs inside a VPC.
* Subnets divide a VPC into smaller networks.
* Public subnets host internet-facing resources.
* Private subnets host internal services.
* Route Tables determine where network traffic flows.
* Internet Gateways provide internet access to public subnets.
* NAT Gateways enable secure outbound internet access for private subnets.
* Security Groups protect individual resources.
* Network ACLs protect entire subnets.
* Production architectures prioritize security, high availability, and fault isolation through proper network design.
