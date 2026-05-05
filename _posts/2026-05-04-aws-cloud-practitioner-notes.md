---
title: "AWS Cloud Practitioner notes"
date: 2026-05-04
tags:
    - AWS
    - Cloud Practitioner
    - Exam
author: Irodotos Apostolou
---
## What is Cloud computing Section

### What is cloud computing

On demand delivery of resources , with pay-as-you-go pricing

provision exactly the right type and size and almost instantly

### Deployment models
    - private
    - public
    - hybrid

### Five characteristic of cloud computing

1. On-demand self service

2. Broad network access

3. Multi-tenancy and resource pooling

4. Rapid elasticity and scalability

5. Measured service

### Six advantages of Cloud computing

1. Trade capital expense (CAPEX) for operational expense (OPEX)

Pay on demand & reduce cost of ownership (TCO) and operational expense

2. Benefit from massive economies of scale

3. Stop guessing capacity

4. Increase speed and agility

5. Stop spending money running/maintaining data centers

6. Go global in minutes

### Problems solved by cloud

1. Flexibility on resources

2. Cost effectiveness pay-as-you-go

3. Scalability

4. Elasticity

5. High-availability and fault-tolerance

6. Agility

### Types of Cloud computing

1. Infrastructure as a service (IaaS)

networking, computers, data storages EC2,

2. Platform as a service (PaaS)

focus on the deployment and management of your apps Elastic Beanstalk

3. Software as a service (SaaS)

complete product that is running and managed by AWS Rekognition

### Managed by you

- On premise - networking, storage, servers, virtualization + all the rest

- IaaS : OS, middleware, runtime + data, application

- PaaS : Data, application

- SaaS : Nothing :)

### AWS Global Infrastructure

- Regions - a cluster of data centers

- Availability zones - each Region have min 3 and max 6 AZ - data center

- Edge locations

### Shared responsibility model

- Customer : data, encryption and auth of client data, OS, networking and firewall

networking traffic protection, application

- AWS : all the infrastructure, compute, storage databases, regions, edge locations

## IAM Section

### Users & Groups

- Root acc - created by default, should not used or shared

- Users - people in the org - can belong to multiple groups or none

- Groups - only contain users not other groups

### IAM Permission

least privilege principle

### IAM Security tools

- IAM Credentials Report (account level)

list all your accounts users and status

- IAM access advisor (user level)

shows service permission to a user

### IAM Guidelines & best practices

- Don’t use root acc except for AWS account setup

- One physical user = One AWS user

- Assign user to groups & assign permissions to groups

- Strong password policy

- Enforce MFA on users

- Create & use Roles for giving permissions to AWS services

- NEVER SHARE IAM USERS & ACCESS KEYS

### IAM Summary

- Users: mapped to physical user, has password for AWS Console

- Groups: contains users only

- Policies: JSON document that outline permissions

- Roles for AWS services

- Security: MFA & Password policy

- AWS CLI: managed AWS services from command line

- AWS SDK: managed AWS services using programming language

- Access Keys: access AWS using CLI & SDK

## EC2 Section

### EC2 = Elastic Computing

- Renting Virtual Machine (EC2)

- Storing data on virtual drives (EBS)

- Distribute load across machines (ELB)

- Scaling using auto-scaling (ASG)

### EC2 Instances Types

- General purpose

- Compute Optimized (HPC, Machine learning, Batch workloads)

- Memory Optimized (Databases, caches)

- Storage Optimized (OLTP, databases)

### EC2 Purchasing Options

- On demand - pay per second

- Reserved (1 or 3 years) - long workload with flexible instances

- Saving Plans (1 or 3 years) - long workloads commitment to amount of usage

- Spot Instances - cheap but can lose instances (not reliable)

- Dedicated Host - book entire server and control instance placement

- Dedicated Instances - no other customer share your hardware

- Capacity Reservations - reserve capacity in a specific AZ for any duration

Examples:

- On demand: Coming and stay in a hotel whenever we like and we

pay full price

- Reserved: Planning ahead and if we plan to stay for a long time we

get a discount

- Saving Plans: Pay a certain amount per hour for a certain period and

stay in any room type

- Spot Instance: The hotel allows bid for empty rooms for a very low

price but anytime someone bid higher you can get kicked

- Dedicated host: Book the entire hotel

- Capacity Reservation: Book a room for a period for full price even

if you don’t stay in it


## EC2 Instance Storage Section

### EBS (1st type of storage)

- like a network drive you can attach to your instance

- Is allows your instance to persist data

- Only mount to ONE instance at the time

- Bound to specific AZ

### EBS Snapshots

- Make a backup of your EBS

- Can copy across AZ

- Features: - Snapshot Archive: 75% cheaper but needs 1-3 days to restore

- Recycle Bin : Retain deleted snapshots

### AMI

- Customize of EC2 instance

- Build for specific region

- How it works:

- Start an EC2 instance and customize it

- Stop the instance

- Build the AMI - this will also create EBS snapshot

- Launch instance from other AMI

### EC2 Image builder

- Automate the creation of AMIs

### EC2 Instance storage (2nd type of storage)

- High performance hardware disk (Better performance than EBS)

- Better I/O performance

- Lose their storage if they are stopped

- Good for buffer / cache / temporary content

- Risk of data lose if HW fails

- Backups & Replication are your responsibility

### EFS - Elastic File System (3rd type of storage)

- Managed NFS (means the file system can be mount to 100s of EC2)

- Works with Linux EC2 in multi-AZ

- High availability & scalable expensive

### EFS Infrequent Access (EFS-IA)

- storage class that cost optimized for files not access every day

- 92% cheaper than EFS

### EC2 Instance Storage Summary

- EBS volumes - network drives attached to ONE EC2 at a time - mapped to
AZ - EBS Snapshots: for backup and move to different AZ

- AMI - ready to use EC2 with customizations

- EC2 Image builder: automatically build test and distribute AMIs

- EC2 instance storage - high performance HW disk attached to EC2
instance - lost if instance stop/terminated

- EFS: Network file system can be attached to 100s EC2

- EFS-IA: cost optimized storage for infrequent access files

### Load balancing & Auto scaling

- high availability & scalability

- vertical scaling: increase instance size

- horizontal scaling: increase number of instances

- multi AZ

### Scalability vs Elasticity vs Agility

- Scalability: accommodate a larger load by making the HW stronger
(scale up) or by adding nodes (scale out)

- Elasticity: the system can auto-scale based on the load

- cloud friendly: pay per use, match demand, handle cost

- Agility: new IT resources are a click away reduce time to make
resources

### ELB - Elastic Load Balancer

- managed load balancer

- AWS guarantees that it will be working

- AWS takes care of upgrades and high availability

- AWS provides only few configuration knobs

### 3 kinds of load balancer

- Application LB (HTTP/HTTPS) - layer 7 - HTTP / HTTPS based rules -
HTTP routing rules - Static DNS (URL)

- Network LB - ultra high performance - layer 4 - TCP / UDP - high
performance millions of req - static IP

- Gateway LB - layer 3 - generic protocol - route traffic to firewall
that you manage on EC2 - intrusion detection

### Auto Scaling Group

- scale out (load increase) and scale in (load decrease)

- ensure we have minimum & maximum number of machines running

- replace unhealthy instances

- cost savings run at optimal capacity

### Auto scaling strategies

- Manual scaling: update the size of ASG manually

- Dynamic scaling: respond to changes like CPU usage or request count

- Predictive scaling

### S3

- store objects (files) in buckets (directories)

- defined at region level

### Objects

- have a key

- object value is the content of the body

- metadata

- tags

- version ID

### Security

- user-based IAM policies

- resource-based - bucket policies - object access (ACL) - bucket access
(ACL)

- encryption

### Versioning

- enabled on bucket level

- best practice to version your bucket

### Replication (CRR & SRR)

- must enable versioning

- Cross region replication (CRR): lower latency, replication across acc

- Same region replication (SRR): live replication between prod and test
account

### S3 Storage classes

- Standard - general purpose

- Used for frequently accessed data

- low latency & high throughput

- Infrequent Access Standard (S3 standard - IA) - less frequent access
but requires rapid access when needed - use cases: backups, disaster
recovery

- One zone - IA - high durability in one AZ - use cases: storing
secondary backups

- Glacier storage - low cost meant for archiving

1. Glacier Instant Retrieval

-   milliseconds retrieval
-   minimum storage 90 days

2. Glacier Flexible Retrieval

-   expedited (1-5 min), standard (3-5 hours), bulk (5-12 hours)
-   minimum storage 90 days

3. Glacier Deep Archive

-   standard (12 hours), bulk (48 hours)
-   minimum storage 180 days

### Intelligent Tiering

- monitor and auto-tiering fee

- moves objects automatically between access tiers based on usage

### S3 Express One Zone

- high performance, in one AZ

- 10x faster than standard S3 and 50% less cost

### S3 Encryption

- server side: servers encrypt files after upload

- client side: encrypt file before uploading

### AWS Snowball

- collect and process data at the edge and migrate data into and out AWS

### AWS Storage Gateway

- bridge between on-premise data and cloud data on S3

- hybrid storage service + allow on premise to seamlessly use AWS cloud

## Databases Section

### RDS

- Relational DB

- SQL

- advantages over using RDS versus deploying DB on EC2 - automated
provisioning, OS patching - continuous backups and restore to specific
timestamps - scaling

### Amazon Aurora

- Serverless

- PostgreSQL and MySQL supported

- Auto scaling

- Pay per second

### RDS Deployments

- Read replicas: scale the read workload

- Multi-AZ: failover in case of AZ outage

- Multi region (read replicas): disaster recovery and local performance

### Amazon ElastiCache

- in memory DB

- high performance low latency

- reduce load off DB for read intensive workloads

### Dynamo DB

- fully managed high availability with replication across 3 AZ

- NoSQL DB

- serverless

### Dynamo DB accelerator (DAX)

- in memory cache for Dynamo DB

- 10x improvement

- only used with Dynamo

### Dynamo DB - Global Tables

- make dynamo accessible low latency in multi region

- Active-Active application (read/write to any region)

### Redshift

- Postgre SQL for OLAP (online analytical processing)

- columnar storage data

- massive parallel query execution

- serverless

- pay for what you use

### Amazon EMR (Elastic Map Reduce)

- creating Hadoop clusters (big data)

- data processing, big data

### Amazon Athena

- serverless query service to analyze data stored in S3

### Amazon QuickSight

- serverless machine learning powered business to create interactive
dashboards

### Document DB

- same as Mongo DB

- store JSON

### Amazon Neptune

- Graph DB

- social network type DB

### Amazon Timestream

- time series DB

### Amazon Blockchain

- execute transactions without the need of a trusted central authority

### AWS Glue

- extract, transform and load (ETL) service

- serverless

### Database Migration Service (DMS)

- quickly and securely migrate databases to AWS

### Databases & Analytics Summary

- Relational DB: OLTP: RDS & Aurora (serverless)

- multi-AZ, read replicas, multi regions

- In-memory DB: Elastic Cache

- key/value DB: Dynamo DB (serverless) & DAX (cache for DynamoDB)

- warehouse OLAP: Redshift

- Hadoop cluster: EMR

- Athena: query data on S3 (serverless & SQL)

- QuickSight: dashboard on your data

- DocumentDB: JSON & NoSQL DB

- Amazon Managed Blockchain

- Glue: managed ETL

- Database Migration AWS

- Neptune: graph DB

- Timestream: time-series DB

## OTHER COMPUTE SERVICES Section

### ECS (Elastic Container Service)

- Launch Docker containers on AWS

- you must provision & maintain the infra (the EC2 instances)

### Fargate

- Launch Docker containers on AWS

- you MUST NOT provision the infra

- serverless

### ECR (Elastic Container Registry)

- store your docker images

### Amazon EKS (Elastic Kubernetes Service)

- launch and manage kubernetes cluster on AWS

### Lambda

- virtual functions

- serverless, scaling is automated

- short executions

- event driven

- price -\> pay per call

### API Gateway

- create, publish, maintain, monitor and secure APIs

- serverless and scalable

### AWS Batch

- fully managed batch processing at any scale

- dynamically launch EC2 instances or Spot instances

- batch jobs are defined as docker images and run on ECS


### Lambda Vs Batch

- Lambda - time limit - limited runtimes - limited temporary disk
space - serverless

- Batch - no time limit - any runtime as long as provided as Docker
image - rely on EBS - rely on EC2 (can be managed by AWS)

### Amazon Lightsail

- virtual servers, storage, databases and networking

- low and predictable pricing

- great for people with low cloud experience

### Deploying and Managing infra at scale

- Cloud Formation (AWS only) IaC

- Beanstalk (AWS only) PaaS to deploy code and run

- Code Deploy (hybrid) deploy code to services

- System Manager (hybrid SSM) run commands at scale

### Developer Services

- Code Commit: git repo

- Code Build: build and test code

- Code Deploy

- Code Pipeline: from code to build and deploy

- Code Artifact: dependencies on AWS

- AWS Code: deploy IaC and runtime together


## Global Infra Section

### Why global application

- application deployed in multiple geographies locations

- multi region and/or edge locations

- Benefits - decrease latency - disaster recovery - attack protection

### Route 53: Global DNS

- route users to closest deployment

- policies - simple routing - no health checks - weighted routing -
latency routing - failover routing - health checks

### CloudFront: Global CDN

- cache static content for UI

- improves read performance

- DDoS protection

- edge cache content static

### S3 acceleration

- transfer files to edge location and forward them to S3

### Global accelerator

- improves availability & performance using AWS global network

### CloudFront Origins

- S3 bucket

- VPC

- HTTP

### AWS Outposts

- Deploy your own racks on your data centers to extend AWS cloud

### AWS Wavelength

- 5G network , telecommunications

### AWS Local Zones

- Extension of regions

- eg eu-west-1 (London)

### Global Application Architectures

- Single region, single AZ - High Availability X - Global latency X -
easy

- Single region, multi AZ - High Availability ✓ - Global latency X -
more difficult

- Multi region, Active-Passive - High availability ✓ - Global read
latency ✓ - Global write latency X - more difficult

- Multi region, Active-Active - Availability ✓ - Read ✓ - Write ✓ -
difficult

## Cloud Integration Section

### SQS

- Simple queue

- Decouple services

- Messages are deleted after read by consumers

### Kinesis

- Real time big data streaming

### Amazon Firehose

- Load kinesis data streams to S3 , Redshift

### Amazon SNS

- Pub/Sub model

- Each subscriber to the topic will get all the messages

### Amazon MQ

- MQTT , AMQP protocol

### Cloud Monitoring Section

### Cloud Watch

- Metrics

- Alarms

- Logs

### Event Bridge

- React to events of type like a cron job

### Cloud Trail

- Provides compliance and audit for your AWS account

### X-Ray

- Visualise analysis and trace req of your distributed application

### AWS Health Dashboard

- status of all AWS services

### AWS Account Health Dashboard

- AWS events that impact your infra

### Code Guru

- Automated code reviews

### VPC

- VPC Virtual Private Cloud

- Subnets divide AZ network partition of your VPC

- Internet Gateway at the VPC level provides internet access to the VPC

- NAT Gateway subnet level provides internet access to your subnet

- NACL stateless subnet rules for inbound and outbound

- VPC Peering connect 2 VPC without overlapping IP addresses

- Elastic IP fixed public IP

- VPC Endpoints provide private access to AWS services within VPC

- PrivateLink private connection to a 3rd party VPC

- VPN for hybrid cloud

- Direct Connect dedicated private connection to AWS

- Transit Gateway connect thousands of VPC and on-premises networks
together

## Security & Compliance Section

- Shield DDoS protection + 24/7 support

- WAF firewall to filter incoming request

- KMS managed encryption keys

- Cloud HSM AWS encryption but we manage the keys

- AWS Certificate Manager manage SSL/TLS certificates

- Artifact access to compliance reports such as PCI, ISO

### Guard Duty

- find malicious behavior with VPC , DNS

### Inspector

- find software vulnerabilities in EC2 , ECR images and lambda

### Network Firewall

- protect VPC against network attacks

### Config

- track config changes and compliance against rules

### Macie

- find sensitive data on S3

### Cloud Trail

- track API calls made by user within account

### AWS Security Hub

- central security findings from multiple AWS services

### Amazon Detective

- find root cause of security issues or suspicious activities

### AWS Abuse

- report AWS resources for abusive or illegal purposes

### Root user privileges

- change account settings

- close your AWS account

- change or cancel your AWS support plan

- register as a seller in the Reserved Instance Marketplace

### IAM Access Analyzer

- identify which resources are shared externally

### Firewall Manager

- manage security rules across organization (WAF, Shield)

## Machine Learning Section

- Rekognition face detection

- Transcribe audio to text

- Polly text to audio

- Translate

- Lex build conversational bots

- Connect contact center / calls

- Comprehend NLP

- Sage Maker build ML models

- Kendra document search service search engine

- Personalize real time recommendations

- Textract extract text and data from documents

## Account Management Section

### AWS Organization

- operate multiple accounts

- one billing across all accounts

- aggregated usage

- reserve EC2 instances for optimal savings

### SCP (Service Control Policies)

- restrict accounts powers

- applied to all users & roles

### AWS Control Tower

- easily set up multiple accounts with best practices

### AWS RAM (Resource Access Manager)

- share AWS resources you own with other AWS accounts

### AWS Service Catalog

- self service portal to launch a set of authorized products pre defined
by admins

### AWS Pricing Models

- pay as you go

- save when you reserve

- pay less by using more

- pay less as AWS grows

### AWS Best Practices

- Organizations: operate in multiple accounts

- SCP: restrict account powers

- Control Tower: easily setup multiple accounts with best practices


## Billing and costing tools

- Compute Optimizer: recommends resources configuration to reduce cost

- Pricing calculator: cost of services on AWS

- Billing dashboard: high level overview

- Cost allocation tags: tag resources to create detailed report

- Cost & Usage report: most comprehensive billing dataset

- Cost explorer: view and analyze usage

- Billing alarms

- Budgets: send alarms when budget exceeds

- Savings plans

- Cost anomaly detection

- Service quotas

## Advance Identity Section

- STS (Security Token Service): temporary access to resources

- Cognito: create DB of users for mobile & web apps

- Directory Services: integrate Microsoft Active Directory in AWS

- IAM Identity Center: SSO

## Other AWS Services Section

- Amazon Workspaces: provision virtual desktops

- AppStream 2.0: application streaming

- IoT Core: connect IoT devices

- AppSync: sync data with GraphQL

- Amplify: develop and deploy apps

- Step Functions: build workflows

- Device Farm: test apps on real devices

- AWS Backup: backup service

- KMS: encryption

- Cloud9: cloud IDE

- Ground Station: satellite communication

- Pintpoint: 2 way video meeting communication


### Disaster Recovery Strategies

- Backup & Restore: backup in S3 and restore when needed

- Pilot light: core functions are ready to scale

- Warm standby: full version on AWS but minimum size

- Multi site / hot site: full version on AWS and full size

### Cloud Migration Strategies (The 7Rs)

1. Retire: turn off things you don't need

2. Retain: do nothing for now

3. Rehost: move apps from on premise to the cloud

4. Replatform: lift and shift but optimize

5. Repurchase: switch to different product

6. Refactor: re-architect to cloud native

## AWS Architecture Section

### Well Architected Framework

1. Operational Excellence: run and monitor systems

Design principles:
- perform operations as code (IaC)
- make frequent small reversible changes
- refine operations procedures
- anticipate failure
- learn from failures
- use managed services
- implement observability

2. Security: protect information systems

Design principles:
- implement strong identity foundation
- enable traceability
- apply security at all levels
- automate security best practices
- protect data in transit and at rest
- keep people away from data
- prepare for security events
- shared responsibility model

3. Reliability: recover from disruptions

Design principles:
- test recovery procedures
- automatically recover from failure
- scale horizontally
- stop guessing capacity
- manage change in automation

4. Performance Efficiency

Design principles:
- democratize advanced technologies
- go global in minutes
- use serverless architecture
- experiment more often
- mechanical sympathy

5. Cost Optimization

Design principles:
- adopt consumption model
- measure overall efficiency
- stop spending money on data center operations
- analyze and attribute expenditure
- use managed and application level services

6. Sustainability

## Tips & Tricks

- Multi-AZ = High availability

- Shared Responsibility: AWS is responsible for security and compliance of the cloud, including edge location management

- Compute Optimizer: EC2, Auto scaling, EBS, Lambda

- 3 Storage Gateway types:
    - Tape Gateway
    - File Gateway
    - Volume Gateway

- AWS Shield Advance running on:
    - Route 53
    - AWS Global Accelerator
    - CloudFront

- Advantage of cloud computing: trade capital expense for variable expense

- Amazon Inspector: automate security checks and vulnerabilities on EC2, ECR and Lambda

- NACL has both Allow and Deny rules

- Security Group has only Allow rules

- NAT Gateway is managed by AWS

- These services have automatic encryption:
    - Cloud Trail Logs
    - S3
    - Dynamo

- How to remove an account from AWS Organizations:
    - The account needs to be able to operate as a standalone account

- You can reserve:
    - EC2
    - RDS
    - Dynamo
    - Redshift
    - Elastic Cache

- Use AWS Organizations to share reserved instances among all units

- EC2 minimum pay is 60 seconds

- EBS is attached to a single AZ vs EFS attached to multiple AZs

- Trust Advisor helps with cost optimization, performance and architecture

- Components of Site-to-site VPN:
    - Virtual Private Gateway
    - NAT Gateway

- Services with default high availability:
    - Dynamo
    - EFS

- AWS Knowledge Center: forum with most frequent questions

- Cost Allocation Tags:
    - You must activate both AWS and user tags
    - For each resource, each key must be unique and each tag key can have only one value

- AWS Budgets types:
    - Cost budget
    - Usage budget
    - Reservation budget

- Read Replicas = Database Scalability

- Regional in scope:
    - Lambda
    - Rekognition

- Security group is stateful, it automatically allows return traffic

- NACL contains a numbered list of rules and evaluates these rules in increasing order while deciding whether to allow traffic

- Page 27

- WAF benefits:
    - Check for SQL injection
    - Block all traffic except what you allow

- S3 classes with zero data retrieval fee:
    - S3 Standard
    - S3 Intelligent-Tiering

- S3 data transfer out is free when sent to EC2 in the same Region

- Trusted Advisor: provides alerts for when you have off CloudTrail or when S3 bucket is public

- AWS Global Accelerator:
    - Good for non-http cases such as UDP, MQTT
    - Provides static IP as fixed entry point

- EFS can be directly used with on-premises systems

- You can give IAM roles to EC2 to access resources like Dynamo

- VPC Interface Endpoint: connect VPC to AWS service

- AWS Budget: alerts for Reserved Instances utilization

- AWS Organization:
    - Share RI instances among accounts
    - Volume discount for EC2 and S3

- CloudTrail: provides centralized view of security alerts and compliance status

- System Manager: centrally view, manage and operate nodes to quickly identify issues

- EBS snapshots are stored in S3

- VPC spans all AZs within an AWS Region

- CloudWatch monitoring types:
    - Resource utilization
    - Application performance

- EBS charges:
    - Volume type
    - Provisioned IOPS

- Saving plans:
    - Compute plan
    - EC2 plan

- Transcoder: convert media files into other formats

- API Gateway:
    - Supports Rest, http, websockets

- Different sign-in methods:
    - Management console: username and password
    - SDK and CLI: access key and key ID

- Beanstalk health monitor:
    - Does NOT publish any metrics to CloudWatch
    - Determines if ASG is available with at least one instance