## AWS Cloud Security Assessment
Audit & pentest methodology, technical notes, list of tools, scripts and commands that are useful for assessing the security posture of an AWS Cloud environment.<br>
>The output files included here are the results of tools, scripts and commands that I ran against my testing AWS environment and some free CTF/Lab AWS environment. 

### Table of contents 
- I. AWS Cloud Essentials  
  - A. Introduction
  - B. Main AWS services across IaaS, PaaS, and SaaS
  - C. List of usefull AWS CLI commands
  - D. AWS Security Checklist (from AWS)
  - E. Cloud‑Native Application Protection Platforms (CNAPP)

- II. AWS Security Audit
  - A.
  - B.

- III. AWS Penetration Testing
  - A. Black-box penetration test (we start with no account)
  - B. Grey-box penetration test (we start with 1 low-privileged Windows account)

----------------

#### I. AWS Cloud Essentials 

> A. Introduction

- AWS provides cloud computing i.e. on-demand delivery of technology services through the Internet with pay-as-you-go pricing.
The AWS Cloud encompasses a broad set of global cloud-based products that includes compute, storage, databases, analytics, networking, mobile, developer tools, management tools, IoT, security, and enterprise applications: on-demand, available in seconds, with pay-as-you-go pricing.

- AWS delivers cloud capabilities through the three service models: Software as a Service (SaaS), Platform as a Service (PaaS), and Infrastructure as a Service (IaaS). 
These models differ in how much control the customer retains versus how much AWS manages. Understanding these layers helps teams choose the right level of abstraction for their applications, operations, and security responsibilities.

> B. Main AWS services across IaaS, PaaS, and SaaS

- AWS Cloud - Infrastructure as a Service (IaaS) - These services give raw compute, storage, and networking building blocks.
  - Amazon EC2 — Provides resizable virtual servers in the cloud with full OS‑level control.
  - Amazon S3 — Offers highly durable, scalable object storage for any type of data.
  - Amazon EBS — Delivers block‑level storage volumes for EC2 instances.
  - Amazon VPC — Lets you create isolated virtual networks with full control over routing and security.
  - Elastic Load Balancing (ELB) — Automatically distributes incoming traffic across multiple compute resources.
  - Amazon Route 53 — A scalable DNS and domain management service.
  - AWS IAM — Provides identity and access control for AWS resources.
  - Amazon EFS — A fully managed, scalable file storage system for Linux workloads.
  - AWS Direct Connect — Establishes dedicated network connections between your data center and AWS.
  - ...

- AWS Cloud - Platform as a Service (PaaS) - These services abstract away infrastructure so user can focus on code and applications.
  - AWS Lambda — Runs your code without provisioning or managing servers.
  - AWS Elastic Beanstalk — Automatically deploys and manages applications using preconfigured environments.
  - Amazon RDS — Provides managed relational databases with automated backups and patching.
  - Amazon Aurora — A high‑performance, fully managed relational database compatible with MySQL and PostgreSQL.
  - AWS Fargate — Runs containers without managing servers or clusters.
  - Amazon API Gateway — Creates, manages, and secures APIs at scale.
  - Amazon DynamoDB — A fully managed NoSQL database with single‑digit millisecond performance.
  - Amazon SQS — A managed message queuing service for decoupling distributed systems.
  - Amazon SNS — A pub/sub messaging service for event notifications.
  - AWS CloudTrail — Records API activity and governance events across your AWS environment.
  - AWS Control Tower (Cloud Foundation) — Provides automated landing zone setup and governance for multi‑account AWS environments.
  - AWS Secrets Manager - It securely stores and manages sensitive credentials, providing controlled access and automated rotation to keep your applications and cloud environment safer.
  - ...

- AWS Cloud - Software as a Service (SaaS) - These are fully managed applications delivered directly to end users.
  - Amazon Chime — A cloud‑based communications service for meetings, chat, and video conferencing.
  - Amazon QuickSight — A fully managed business intelligence service for dashboards and analytics.
  - Amazon WorkMail — A secure, managed business email and calendaring service.
  - Amazon WorkDocs — A cloud‑based document storage and collaboration platform.
  - AWS Managed Services (AMS) — Provides ongoing operations management for enterprise AWS environments.
  - AWS IoT Device Management — A fully managed service for onboarding and managing IoT devices at scale.
  - Amazon Connect — A cloud‑based contact center platform for customer service operations.
  - Amazon GuardDuty — A fully managed threat detection service that continuously monitors for malicious activity.
  - ...

> C. List of usefull AWS CLI commands
 
| Amazon Web Service | ACTION | COMMAND | 
| :-----: | :-----: | :-----: | 
| STS | Get basic account info | aws sts get-caller-identity | 
| IAM | List IAM users | aws iam list-users | 
| IAM | Display the (custom) account password policy | aws iam get-account-password-policy |
| IAM | Create or change the (custom) account password policy | aws iam update-account-password-policy |
| IAM | List all MFA devices | aws iam list-mfa-devices | 
| IAM | List all MFA devices for a IAM user | aws iam list-mfa-devices --user-name username | 
| IAM | Generate a credential report that lists all users in your account and the status of their various credentials, including passwords, access keys, and MFA devices. | aws iam generate-credential-report | 
| IAM | View the last credential report that was generated | aws iam get-credential-report | 
| IAM | List access keys for a user | aws iam list-access-keys --user-name username | 
| IAM | Backdoor account with second set of access keys | aws iam create-access-key --user-name username | 
| IAM | List IAM roles | aws iam list-roles | 
| IAM | Export and brute force all roles for assume role escalation | aws iam list-roles --query 'Roles[].Arn' PIPE-SYMBOL jq -r '.[]' >> rolearns.txt while read r; do echo $r; aws sts assume-role --role-arn $r --role-session-name awshax; done < rolearns.txt | 
| S3 | List S3 buckets accessible to an account | aws s3 ls | 
| S3 | List all S3 buckets | aws s3 ls PIPE-SYMBOL awk '{print $3}' >> s3-all-buckets.txt | 
| S3 | List the contents of an S3 bucket | aws s3 ls s3://bucketname | 
| S3 | Download contents of a S3 bucket | aws s3 sync s3://bucketname s3-files-dir | 
| EC2 (VM) | List EC2 instances | aws ec2 describe-instances | 
| EC2 (VM)| Export all EC2 Instance User Data | while read r; do for instance in $(aws ec2 describe-instances --query 'Reservations[].Instances[].InstanceId' --region $r PIPE-SYMBOL jq -r '.[]'); do aws ec2 describe-instance-attribute --region $r --instance-id $instance --attribute userData >> ec2-instance-userdata.txt; done; done < regions.txt | 
| EC2 (VM) | List EC2 subnets | aws ec2 describe-subnets | 
| EC2 (VM) | List EC2 network interfaces | aws ec2 describe-network-interfaces | 
| EC2 (VM) | Instance Metadata Service URL | http://169.254.169.254/latest/meta-data | 
| EC2 (VM) | Additional IAM creds possibly available here | http://169.254.169.254/latest/meta-data/iam/security-credentials/IAM-Role-Name | 
| DirectConnect (VPN) | List DirectConnect (VPN) connections | aws directconnect describe-connections | 
| RDS | List all RDS Snapshots | aws rds describe-db-snapshots --region us-east-1 --snapshot-type manual --query=DBSnapshots[*].DBSnapshotIdentifier | 
| Lambda (Serverless) | List Lambda functions | aws lambda list-functions --region region | 
| Lambda (Serverless) | Look at environment variables set for secrets and analyze code | aws lambda get-function --function-name lambda-function-name | 
| Lambda (Serverless) | List Lambda functions | aws lambda list-functions --region region | 
| Kubernetes (EKS) | List EKS clusters | aws eks list-clusters --region region | 
| Kubernetes (EKS) | Update kubeconfig | aws eks update-kubeconfig --name cluster-name --region region | 


> D. AWS Security Checklist (from AWS)

- Identity & Access Management
  - 1. Secure your AWS account.
    - Use AWS Organizations to manage your accounts, use the root user by exception with multi-factor authentication (MFA) enabled, and configure account contacts.
  - 2. Rely on centralized identity provider.
    - Centralize identities using either AWS Single Sign-On or a third-party provider to avoid routinely creating IAM users or using long-term access keys—this approach makes it easier to manage multiple AWS accounts and federated applications.
  - 3. Use multiple AWS accounts to separate workloads and workload stages such as production and non-production.
    - Multiple AWS accounts allow you to separate data and resources, and enable the use of Service Control Policies to implement guardrails. AWS Control Tower can help you easily set up and govern a multi-account AWS environment.
  - 4. Store and use secrets securely.
    - Where you cannot use temporary credentials, like tokens from AWS Security Token Service, store your secrets like database passwords using AWS Secrets Manager which handles encryption, rotation, and access control.

- Infrastructure Protection
  - 1. Patch your operating system, applications, and code.
    - Use AWS Systems Manager Patch Manager to automate the patching process of all systems and code for which you are responsible, including your OS, applications, and code dependencies.
  - 2. Implement distributed denial-of-service (DDoS) protection for your internet facing resources.
    - Use Amazon Cloudfront, AWS WAF and AWS Shield to provide layer 7 and layer 3/layer 4 DDoS protection.
  - 3. Control access using VPC Security Groups and subnet layers.
    - Use security groups for controlling inbound and outbound traffic, and automatically apply rules for both security groups and WAFs using AWS Firewall Manager. Group different resources into different subnets to create routing layers, for example database resources do not need a route to the internet.

- Data Protection
  - 1. Protect data at rest.
    - Use AWS Key Management Service (KMS) to protect data at rest across a wide range of AWS services and your applications. Enable default encryption for Amazon EBS volumes, and Amazon S3 buckets.
  - 2. Encrypt data in transit.
    - Enable encryption for all network traffic, including Transport Layer Security (TLS) for web based network infrastructure you control using AWS Certificate Manager to manage and provision certificates.
  - 3. Use mechanisms to keep people away from data.
    - Keep all users away from directly accessing sensitive data and systems. For example, provide an Amazon QuickSight dashboard to business users instead of direct access to a database, and perform actions at a distance using AWS Systems Manager automation documents and Run Command.

- Incident Detection
  - 1. Enable foundational services: AWS CloudTrail, Amazon GuardDuty, and AWS Security Hub.
    - For all your AWS accounts configure CloudTrail to log API activity, use GuardDuty for continuous monitoring, and use AWS Security Hub for a comprehensive view of your security posture.
  - 2. Configure service and application level logging.
    - In addition to your application logs, enable logging at the service level, such as Amazon VPC Flow Logs and Amazon S3, CloudTrail, and Elastic Load
Balancer access logging, to gain visibility into events. Configure logs to flow to a central account, and protect them from manipulation or deletion.
  - 3. Configure monitoring and alerts, and investigate events.
    - Enable AWS Config to track the history of resources, and Config Managed Rules to automatically alert or remediate on undesired changes. For all your sources of logs and events, from AWS CloudTrail, to Amazon GuardDuty and your application logs, configure alerts for high priority events and investigate.
   
- Incident Response
  - 1. Ensure you have an incident response (IR) plan.
    - Begin your IR plan by building runbooks to respond to unexpected events in your workload. For details, see the AWS Security Incident Response Guide.
  - 2. Make sure that someone is notified to take action on critical findings.
    - Begin with GuardDuty findings. Turn on GuardDuty and ensure that someone with the ability to take action receives the notifications. Automatically creating trouble tickets is the best way to ensure that GuardDuty findings are integrated with your operational processes.
  - 3. Practice responding to events.
    - Simulate and practice incident response by running regular game days, incorporating the lessons learned into your incident management plans, and continuously improving them.
  

> E. Cloud‑Native Application Protection Platforms (CNAPP)

- Effective CNAPP solutions must consolidate capabilities rather than rebrand existing traditional security tools:
  - Cloud Security Posture Management (CSPM) - Continuous misconfiguration detection and security posture monitoring
  - Cloud Workload Protection Platform (CWPP) - Runtime protection for VMs, containers, and serverless functions
  - Cloud Infrastructure Entitlement Management (CIEM) - Identity governance and cloud infrastructure access controls
  - Data Security Posture Management (DSPM) - Sensitive data discovery and data security classification
  - Infrastructure as Code (IaC) scanning - Early development process security validation
  - API security
  - Container/Kubernetes runtime protection
  - Attack path analysis
  - Unified risk scoring
  - ...
 
- AWS doesn’t have a native CNAPP but it provides modular security services that customers can combine like:
  - GuardDuty (threat detection)
  - Security Hub (security posture aggregation)
  - Inspector (vulnerability scanning)
  - IAM Access Analyzer (identity risk analysis)
  - CloudTrail (activity logging)
 
- AWS Security Hub is not a CNAPP, but it does provide some CNAPP‑like capabilities. It is a unified cloud security service that gives you a comprehensive view of your AWS security posture and helps you identify, prioritize, and respond to security issues at scale.  
Key Capabilities (from AWS documentation):
  - Centralized visibility across all AWS accounts and regions, unifying security operations in one place.
  - Automated correlation and enrichment of security signals to detect critical issues quickly.
  - CSPM (Cloud Security Posture Management) features that assess your environment against AWS best practices and industry standards like CIS, PCI DSS, and NIST.
  - Integration with AWS services such as GuardDuty (threat detection), Inspector (vulnerability scanning), and Macie (sensitive data discovery) to provide a holistic risk picture.
  - Automated workflows that help teams respond to findings efficiently at scale

- TOP 5 Cloud‑Native Application Protection Platforms (CNAPP)
  - PRISMA Cloud (Palo Alto Networks)
    - It is a full CNAPP platform that secures cloud environments across the entire application lifecycle, including CSPM (security posture management), CWPP (workload protection), CIEM, IaC scanning, and runtime protection.
    - It provides unified visibility and threat detection across multi‑cloud deployments, helping organizations secure workloads, identities, and configurations from code to production.
  - WIZ
    - It is an agentless CNAPP platform known for deep cloud visibility, vulnerability detection, and risk prioritization across multi‑cloud environments. It correlates misconfigurations, vulnerabilities, identities, and network exposure to highlight the most critical attack paths.
    - Security Graph technology analyzes relationships between technologies in cloud environments to uncover critical pathways to breaches with contextual data. Attack path analysis provides prioritized issues showing toxic combinations of risk with high probability of exploitation and significant business impact.
  - Microsoft Defender for Cloud
    - It offers CSPM and CWPP with deep Azure integration and strong support for AWS and GCP. It provides automated hardening recommendations, threat detection, and compliance monitoring.
  - ORCA Security
    - It is an agentless CNAPP platform that scans cloud environments using side‑scanning technology to detect vulnerabilities, misconfigurations, and identity risks. It provides broad visibility across workloads, containers, and cloud services without requiring agents.
  - CrowdStrike Falcon Cloud Security
    - It is a cloud security suite that focuses heavily on workload protection, runtime threat detection, and behavioral analytics. It integrates with the broader Falcon platform, providing unified endpoint and cloud threat intelligence.


----------------

#### II. AWS SECURITY AUDIT

- Using an AWS account run tools and scripts that will extract and analyse the AWS environment looking for potential security misconfiguration, and other security issues in key services such as:
  - IAM
  - EC2
  - Lambda
  - ...  

- CloudSploit by Aqua - Cloud Security Scans - [CloudSploit](https://github.com/aquasecurity/cloudsploit) - It is an open-source project designed to allow detection of security risks in cloud infrastructure accounts, including: Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), Oracle Cloud Infrastructure (OCI), and GitHub. These scripts are designed to return a series of potential misconfigurations and security risks.

----------------

#### III. AWS PENETRATION TESTING

1. Black-box penetration test (we start with no account)
2. Grey-box penetration test (we start with 1 low-privileged Windows account)


