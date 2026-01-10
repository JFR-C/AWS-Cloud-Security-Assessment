## AWS Cloud Security Assessment
Technical notes, list of tools, scripts and commands that are useful for assessing the security posture of an AWS Cloud environment (audit & pentest).

### Table of contents 
- I. AWS Cloud Essentials - Reminder
  - [1.1. Introduction](#11-INTRODUCTION)
  - [1.2. Main AWS services across IaaS, PaaS, and SaaS](#12-main-aws-services-across-iaas-paas-and-saas)
  - [1.3. AWS security best practices](#13-AWS-Security-Best-Practices)
  - [1.4. Key Differences Between the AWS Root User, IAM Users, Roles, and Groups]([#14-key-differences-between-root-user-iam-user-role-and-group)
  - [1.5. Cloud‑Native Application Protection Platforms (CNAPP)](#15-cloudnative-application-protection-platforms-cnapp)
  - [1.6. How to use the AWS CLI](#16-How-to-use-the-AWS-CLI)
  - [1.7. List of usefull AWS CLI commands](#17-List-of-usefull-AWS-CLI-commands)
  - [1.8. List of AWS URL services](#18-LIST-OF-AWS-URL-Services)
  - [1.9. Basic tutorial to create a Kali Linux EC2 VM on AWS](#19-Basic-tutorial-to-create-a-Kali-Linux-EC2-VM-on-AWS)

- II. AWS Security Audit
  - [2.1. Run AWS security scans using audit tools to identify potential misconfigurations](#21-run-aws-security-scans-using-audit-tools-to-identify-potential-security-misconfigurations)
    - CloudSuite
    - Prowler
    - CloudSploit
    - Cloudsplaining
  - [2.2. Check for known privesc attack vectors in AWS (IAM, Lambda, Glue, CodeStar)](#22-check-for-known-privesc-attack-vectors-in-aws)

- III. AWS Penetration Testing
  - [3.1. AWS customer support and service policy for penetration testing](#31-aws-customer-support-and-service-policy-for-penetration-testing)
  - [3.2. AWS pentest methodology - Usefull ressources](#32-aws-pentest-methodology---usefull-ressources)
  - [3.3. List of AWS penetration testing tools](#33-list-of-aws-pentest-tools)
  - [3.4. List of AWS pentesting and security CTFs](#34-list-of-aws-cloud-pentesting-and-securty-ctfs)

----------------

### I. AWS Cloud Essentials - Reminder

#### 1.1. INTRODUCTION

- AWS provides cloud computing i.e. on-demand delivery of technology services through the Internet with pay-as-you-go pricing.
The AWS Cloud encompasses a broad set of global cloud-based products that includes compute, storage, databases, analytics, networking, mobile, developer tools, management tools, IoT, security, and enterprise applications: on-demand, available in seconds, with pay-as-you-go pricing.

- AWS delivers cloud capabilities through the three service models: Software as a Service (SaaS), Platform as a Service (PaaS), and Infrastructure as a Service (IaaS). 
These models differ in how much control the customer retains versus how much AWS manages. Understanding these layers helps teams choose the right level of abstraction for their applications, operations, and security responsibilities.


#### 1.2. MAIN AWS SERVICES ACROSS IAAS, PaaS, AND SaaS

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


#### 1.3. AWS SECURITY BEST PRACTICES

- Identity & Access Management
  - 1. Secure your AWS account.
    - Use AWS Organizations to manage your accounts, use the root user by exception with multi-factor authentication (MFA) enabled, and configure account contacts.
  - 2. Rely on centralized identity provider.
    - Centralize identities using either AWS Single Sign-On or a third-party provider to avoid routinely creating IAM users or using long-term access keys—this approach makes it easier to manage multiple AWS accounts and federated applications.
  - 3. Use multiple AWS accounts to separate workloads and workload stages such as production and non-production.
    - Multiple AWS accounts allow you to separate data and resources, and enable the use of Service Control Policies to implement guardrails. AWS Control Tower can help you easily set up and govern a multi-account AWS environment.
  - 4. Store and use secrets securely.
    - Where you cannot use temporary credentials, like tokens from AWS Security Token Service, store your secrets like database passwords using AWS Secrets Manager which handles encryption, rotation, and access control.

- Role Management
  - 1. Apply least‑privilege permissions and use IAM roles instead of long‑lived IAM users  
    - Grant only the minimum permissions required for each role and rely on temporary credentials through IAM roles to eliminate static access keys.
  - 2. Enforce strong separation of duties with role‑based access control (RBAC)  
    - Create distinct roles for administration, deployment, monitoring, and automation so no single role has excessive or overlapping privileges.
  - 3. Use IAM Access Analyzer and IAM policy validation to detect overly permissive roles  
    - Continuously analyze trust policies and permissions to identify roles that allow unintended external access or wildcard permissions.
  - 4. Enable role session tagging and logging for traceability and accountability  
    - Use CloudTrail and session tags to track who assumed which role, when, and for what purpose, strengthening auditability and incident response.
  - 5. Manage the usage and risks of assumed roles by restricting who can assume them and monitoring cross‑account trust relationships
    - Overly broad trust policies or permissive 'sts:AssumeRole' permissions can allow unintended access paths, privilege escalation, or unauthorized lateral movement across accounts. Regularly review trust policies, enforce MFA‑protected role assumption, and monitor CloudTrail for unusual or high‑risk role‑assumption patterns.

- Infrastructure Protection
  - 1. Patch your operating system, applications, and code.
    - Use AWS Systems Manager Patch Manager to automate the patching process of all systems and code for which you are responsible, including your OS, applications, and code dependencies.
  - 2. Implement distributed denial-of-service (DDoS) protection for your internet facing resources.
    - Use Amazon Cloudfront, AWS WAF and AWS Shield to provide layer 7 and layer 3/layer 4 DDoS protection.
  - 3. Control access using VPC Security Groups and subnet layers.
    - Use security groups for controlling inbound and outbound traffic, and automatically apply rules for both security groups and WAFs using AWS Firewall Manager. Group different resources into different subnets to create routing layers, for example database resources do not need a route to the internet.

- Network Security
  - 1. Enforce least‑privilege network access using Security Groups and NACLs  
    - Design inbound and outbound rules so that only required ports, protocols, and sources are allowed, and segment workloads into separate security groups to minimize lateral movement.
    - Security Groups are stateful virtual firewalls that control inbound and outbound traffic at the instance or ENI level, enforcing least‑privilege access to workloads.
    - Network ACLs (NACLs) are stateless subnet‑level firewalls that provide an additional layer of network filtering, allowing or denying traffic before it reaches resources.
  - 2. Eliminate public exposure by using private subnets, VPC Endpoints, and AWS PrivateLink  
    - Keep databases, internal services, and sensitive workloads off the public internet, and route traffic to AWS services privately to reduce attack surface.
    - VPC (Virtual Private Cloud) is a logically isolated section of the AWS Cloud where you define your own IP ranges, subnets, routing, and network boundaries, forming the foundation of AWS network security.
    - PrivateLink & VPC Endpoints allow to securely connect to AWS services or third‑party SaaS without exposing traffic to the public internet, reducing attack surface.
  - 3. Implement multi‑layered perimeter protection with AWS WAF, Shield, and Network Firewall  
    - Use WAF to filter malicious HTTP/S traffic, Shield to mitigate DDoS attacks, and Network Firewall for deep packet inspection and centralized rule enforcement across VPCs.
  - 4. Continuously monitor and analyze network activity with VPC Flow Logs and GuardDuty  
    - Capture flow logs for visibility into traffic patterns and anomalies, and rely on GuardDuty to detect suspicious behavior such as port scanning, unusual API calls, or compromised instances.
    - Amazon GuardDuty is a threat detection service that analyzes VPC Flow Logs, DNS logs, and CloudTrail events to identify suspicious or malicious activity in your network.

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

#### 1.4. KEY DIFFERENCES BETWEEN ROOT USER, IAM USER, ROLE, AND GROUP

  - Root User
    - Root user = account owner with unlimited power (i.e., it has full, irreversible access to every AWS resource and billing setting).
    - Created automatically when the AWS account is created.
    - Uses the email and password of the AWS account owner.
    - Cannot be restricted by IAM policies.
    - Should be used only for specific tasks such as: changing the AWS account settings, closing the AWS account, restoring MFA, managing certain billing features.
    - Security Best Practices: enable MFA on the root account immediately, create an admin IAM user for administrative tasks and DO NOT use the root account (lock away the root credentials and use them only when required).

  - IAM User
    - User = A long‑term identity with permanent credentials (password, access keys).
    - Users authenticate directly, while roles require AssumeRole to obtain temporary credentials.
    - Users are for humans or applications, roles are for delegation and cross‑account access, and groups are for permission management.
    - Can be part of groups, roles, and policies.
    - Used for everyday operations such as: Managing EC2, S3, RDS, Lambda, ..., deploying applications, admin tasks (if granted)

  - IAM Role
    - Role = An identity that provides temporary credentials and must be assumed via STS; it has no password or access keys of its own.
    - Roles are commonly used by AWS services (EC2, Lambda, etc.) to access other AWS resources securely.

  - IAM Group
    - A container for users that lets you assign permissions to many users at once; it is not an identity and cannot be assumed.
    - Groups cannot contain other groups (no nesting), and they cannot be used by AWS services.

  - IAM Users vs. IAM Roles — Key Differences
    - Credential Type
      - IAM Users: Have long‑lived credentials — passwords and access keys that persist until manually rotated.
      - IAM Roles: Provide temporary credentials issued by STS that automatically expire, reducing risk if compromised.
    - Intended Use
      - IAM Users: Designed for human access (admins, developers) who need console login or programmatic access.
      - IAM Roles: Designed for AWS services, applications, and cross‑account access — not for direct login.
    - How Access Is Granted
      - IAM Users: Are directly assigned permissions via policies attached to the user or their groups.
      - IAM Roles: Have no inherent permissions — they gain permissions only when someone or something assumes the role.
    - Security Model
      - IAM Users: Higher risk because static keys can leak, be stolen, or be forgotten in code repositories.
      - IAM Roles: More secure by default thanks to short‑lived credentials, MFA‑protected assumption, and better auditability.
    - Trust Relationships
      - IAM Users: Do not use trust policies — they authenticate with their own credentials.
      - IAM Roles: Use trust policies to define who is allowed to assume the role (users, services, accounts).
    - Audit & Traceability
      - IAM Users: Actions are logged under the user’s identity.
      - IAM Roles: Actions are logged under the role, but CloudTrail shows who assumed the role, improving accountability.


#### 1.5. CLOUD‑NATIVE APPLICATION PROTECTION PLATFORMS (CNAPP)

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


#### 1.6. HOW TO USE THE AWS CLI

- Step 1 - Install the AWS CLI  
  <i/>The AWS Command Line Interface (CLI) is a tool that lets you interact with AWS services from your terminal.</i>
  ```
  Linux:
  $ sudo apt install awscli
  $ aws --version
  or
  $ sudo yum install awscli
  $ aws --version
  
  MacOS:
  $ brew install awscli
  $ aws --version

  Windows:
  - Download the installer from AWS
  - Run it and follow the steps
  Ps C:\> aws --version
  ```

- Step 2 - Create an IAM User (if not already done)  
  <i/>You need an IAM user with 'Programmatic accesss' so in the AWS Web console:</i>
  ```
  - Go to IAM
  - Select Users
  - Click Create user
  - Enable Access key – Programmatic access
  - Attach permissions (e.g., AdministratorAccess or a custom policy)
  - Download the Access Key ID and Secret Access Key (Note: this is the only time AWS shows you the secret key)
  ```
  
- Step 3 - Configure the AWS CLI
  - Method 1 — Using aws configure (recommended for beginners)
    ```
    $ aws configure
    -> You will be prompted for:
     AWS Access Key ID: <paste your key>
     AWS Secret Access Key: <paste your secret>
     Default region name: eu-west-1 (or your region)
     Default output format: json
    
    -> This creates a config file in:
    For Linux/macOS: ~/.aws/credentials and ~/.aws/config
    For Windows: C:\Users\<you>\.aws\credentials
  
    Where Your Credentials Are Stored
    ---------------------------------
    $ cat ~/.aws/credentials
    
    [default]
    aws_access_key_id = AKIA...
    aws_secret_access_key = abc123...
    
    [myprofile]
    aws_access_key_id = AKIA...
    aws_secret_access_key = xyz789...
    ```

  - Method 2 — Using named profiles (Great when you manage multiple AWS accounts)
    ```
    $ aws configure --profile myprofile
      -> You will be prompted for:
       AWS Access Key ID: <paste your key>
       AWS Secret Access Key: <paste your secret>
       Default region name: eu-west-1 (or your region)
       Default output format: json
    ```

  - Method 3 — Using environment variables (export)  
    <i/> Useful for: Temporary sessions, CI/CD pipelines, avoiding writing credentials to disk. </i>
    ```
    $ export AWS_ACCESS_KEY_ID="<your key>"
    $ export AWS_SECRET_ACCESS_KEY="<your secret>"
    $ export AWS_DEFAULT_REGION="eu-west-1"
    $ export AWS_SESSION_TOKEN="<token>"   # only if using temporary credentials
    
    Note: You can aslo use profiles if you want. In that case run first the command: export AWS_PROFILE="myprofile"
    ```

- Step 4 - Test Your Connection
  - Method 1 (without profile)
    ```
    $ aws sts get-caller-identity
    {
        "UserId": "EXAMPLE",
        "Account": "123456789012",
        "Arn": "arn:aws:iam::123456789012:user/your-user"
    }
    ```
  - Method 2 (with profile)
    ```
    $ aws --profile myprofile sts get-caller-identity
    {
        "UserId": "EXAMPLE",
        "Account": "123456789012",
        "Arn": "arn:aws:iam::123456789012:user/your-user"
    }
    ```
  
  
#### 1.7. LIST OF USEFULL AWS CLI COMMANDS
   
| Amazon Web Service | ACTION | COMMAND | 
| :-----: | :-----: | :-----: | 
| STS | Get basic account info | aws sts get-caller-identity | 
| STS | Show the AWS account ID, IAM user/role, and ARN associated with the provided profile. It returns the identity whose credentials are being used, which includes IAM user, IAM role, assumed role (including EC2 instance profiles) | aws --profile test sts get-caller-identity | 
| STS | Request temporary STS session credentials | aws sts get-session-token | 
| STS | Check which AWS account an access key belongs to | aws sts get-access-key-info --access-key-id=XXXXXXXXX | 
| STS | How to assume a role (basic example) | aws sts assume-role --role-arn arn:aws:iam::123456789012:role/MyTargetRole --role-session-name MySession | 
| STS | How to assume a role (example using a profile) | aws --profile mysourceprofile sts assume-role --role-arn arn:aws:iam::123456789012:role/MyTargetRole --role-session-name MySession | 
| STS | How to assume a role (example with MFA) | aws sts assume-role --role-arn arn:aws:iam::123456789012:role/MyTargetRole --role-session-name MySession --serial-number arn:aws:iam::111122223333:mfa/myuser --token-code 123456 | 
| IAM | List IAM users | aws iam list-users | 
| IAM | Create a new IAM user | aws iam create-user --user-name username | 
| IAM | Show the IAM account password policy | aws iam get-account-password-policy |
| IAM | Create or change the (custom) account password policy | aws iam update-account-password-policy |
| IAM | List all MFA devices | aws iam list-mfa-devices | 
| IAM | List all MFA devices for a IAM user | aws iam list-mfa-devices --user-name username | 
| IAM | Generate a credential report that lists all users in your account and the status of their various credentials, including passwords, access keys, and MFA devices. | aws iam generate-credential-report | 
| IAM | View the last credential report that was generated | aws iam get-credential-report | 
| IAM | List access keys for a user | aws iam list-access-keys --user-name username | 
| IAM | Creates a brand‑new access key for that IAM user. Note: It does not modify, rotate, or overwrite an existing key (backdoor) | aws iam create-access-key --user-name	username | 
| IAM | Deactivate existing access keys for a IAM user | aws iam update-access-key --user-name <username> --access-key-id XXXXXXXX --status Inactive | 
| IAM | Delete existing access keys for a IAM user | aws iam delete-access-key --user-name <username> --access-key-id XXXXXXXX | 
| IAM | List IAM roles | aws iam list-roles | 
| IAM | List IAM groups | aws iam list-groups | 
| IAM | Retrieve details and metadata about a specific IAM role | aws --profile "test" iam get-role --role-name "test-role" | 
| IAM | Export and brute force all roles for assume role escalation | aws iam list-roles --query 'Roles[].Arn' \| jq -r '.[]' >> rolearns.txt while read r; do echo $r; aws sts assume-role --role-arn $r --role-session-name awshax; done < rolearns.txt | 
| IAM | Get policies available | aws --profile "$profile" iam list-policies \| jq -r ".Policies[].Arn" | 
| IAM | Retrieve the default version ID of a specific IAM managed policy | aws --profile "$profile" iam get-policy --policy-arn "$i" --query "Policy.DefaultVersionId" --output text | 
| IAM | Retrieve the full JSON document of the default version of a specific IAM managed policy | aws iam get-policy-version --policy-arn provide_policy_arn --version-id $(aws iam get-policy --policy-arn provide_policy_arn --query 'Policy.DefaultVersionId' --output text) | 
| IAM | Get all juicy info oneliner (search for Action/Resource */*) | profile="test"; for i in $(aws --profile "$profile" iam list-policies \| jq -r '.Policies[].Arn'); do echo "Describing policy $i" && aws --profile "$profile" iam get-policy-version --policy-arn "$i" --version-id $(aws --profile "$profile" iam get-policy --policy-arn "$i" --query 'Policy.DefaultVersionId' --output text); done \| tee /tmp/policies.log | 
| IAM | List managed policies attached to a user | aws --profile "test" iam list-attached-user-policies --user-name "test-user" | 
| IAM | List managed policies attached to a group | aws --profile "test" iam list-attached-group-policies --group-name "test-group" | 
| IAM | List managed policies attached to a role | aws --profile "test" iam list-attached-role-policies --role-name "test-role" | 
| IAM | List inline IAM policies directly embedded in a specific user | aws --profile "test" iam list-user-policies --user-name "test-user" | 
| IAM | List inline IAM policies directly embedded in a specific group | aws --profile "test" iam list-group-policies --group-name "test-group" | 
| IAM | List inline IAM policies directly embedded in a specific role | aws --profile "test" iam list-role-policies --role-name "test-role" | 
| IAM | Describe inline IAM policies directly embedded in a specific user| aws --profile "test" iam get-user-policy --user-name "test-user" --policy-name "test-policy" | 
| IAM | Describe inline IAM policies directly embedded in a specific group | aws --profile "test" iam get-group-policy --group-name "test-group" --policy-name "test-policy" | 
| IAM | Describe inline IAM policies directly embedded in a specific role | aws --profile "test" iam get-role-policy --role-name "test-role" --policy-name "test-policy" | 
| S3 | List S3 buckets accessible to an account | aws s3 ls | 
| S3 | List S3 buckets | aws s3api list-buckets | 
| S3 | List all S3 buckets | aws s3 ls \| awk '{print $3}' >> s3-all-buckets.txt | 
| S3 | List the contents of an S3 bucket | aws s3 ls s3://bucketname | 
| S3 | List the contents of an S3 bucket | aws s3 ls --recursive s3://bucket.com | 
| S3 | Download contents of a S3 bucket | aws s3 sync s3://bucketname s3-files-dir | 
| S3 | Copy contents of a S3 bucket | aws s3 cp s3://bucket-name/<file> <destination> | 
| S3 | Upload contents to a S3 bucket | aws s3 cp/mv test-file.txt s3://bucket-name | 
| S3 | Delete contents of a S3 bucket | aws s3 rm s3://bucket-name/test-file.txt | 
| EC2 (VM) | List all EC2 instances and their details in the current region | aws ec2 describe-instances | 
| EC2 (VM) | List all EC2 (EBS) snapshots visible to your account (owned or shared) | aws ec2 describe-snapshots | 
| EC2 (VM) | List all EC2 (EBS) snapshots owned by a specific AWS account | aws ec2 describe-snapshots --owner-ids {user-id} | 
| EC2 (VM)| Export all EC2 Instance User Data | while read r; do for instance in $(aws ec2 describe-instances --query 'Reservations[].Instances[].InstanceId' --region $r \| jq -r '.[]'); do aws ec2 describe-instance-attribute --region $r --instance-id $instance --attribute userData >> ec2-instance-userdata.txt; done; done < regions.txt | 
| EC2 (VM) | List all EC2 (VPC) subnets and their details in the current region | aws ec2 describe-subnets | 
| EC2 (VM) | List all EC2 Elastic Network Interfaces (ENIs) and their details in the current region | aws ec2 describe-network-interfaces | 
| EC2 (VM) | List all EC2 (VPC) security groups and their configuration details in the current region | aws ec2 describe-security-groups | 
| EC2 (VM) | List security groups that allow inbound access from anywhere (0.0.0.0/0) | aws ec2 describe-security-groups --filters Name=ip-permission.cidr,Values='0.0.0.0/0' --query "SecurityGroups[*].[GroupName]" --output text | 
| EC2 (VM) | Retrieve details for the specific security group identified by its Group ID in the specified region | aws ec2 describe-security-groups --group-ids VPC-Security-Group-ID --region region | 
| EC2 (VM) | Instance Metadata Service URL | http://169.254.169.254/latest/meta-data | 
| EC2 (VM) | Additional IAM creds possibly available here | http://169.254.169.254/latest/meta-data/iam/security-credentials/IAM-Role-Name | 
| DirectConnect (VPN) | List DirectConnect (VPN) connections | aws directconnect describe-connections | 
| RDS | List all RDS Snapshots | aws rds describe-db-snapshots --region us-east-1 --snapshot-type manual --query=DBSnapshots[*].DBSnapshotIdentifier | 
| Lambda (Serverless) | List all Lambda functions in the specified region, including their configuration details (up to 50 per call) | aws lambda list-functions --region region | 
| Lambda (Serverless) | Look at environment variables set for secrets and analyze code | aws lambda get-function --function-name lambda-function-name | 
| Lambda (Serverless) | Extract Lambda function's code | aws lambda list-functions --profile uploadcreds \ aws lambda get-function --function-name "LAMBDA-NAME-HERE-FROM-PREVIOUS-QUERY" --query 'Code.Location' --profile uploadcreds \ wget -O lambda-function.zip url-from-previous-query --profile uploadcreds | 
| Kubernetes (EKS) | List EKS clusters | aws eks list-clusters --region region | 
| Kubernetes (EKS) | Update kubeconfig | aws eks update-kubeconfig --name cluster-name --region region | 


#### 1.8. LIST OF AWS URL Services

| Service      | URL                   |
|--------------|-----------------------|
| s3           | `https://{user_provided}.s3.amazonaws.com` |
| cloudfront   | `https://{random_id}.cloudfront.net` |
| ec2          | `https://ec2-{ip-seperated}.compute-1.amazonaws.com` |
| es           | `https://{user_provided}-{random_id}.{region}.es.amazonaws.com` |
| elb          | `http://{user_provided}-{random_id}.{region}.elb.amazonaws.com:80/443` |
| elbv2        | `https://{user_provided}-{random_id}.{region}.elb.amazonaws.com` |
| rds          | `mysql://{user_provided}.{random_id}.{region}.rds.amazonaws.com:3306` |
| rds          | `postgres://{user_provided}.{random_id}.{region}.rds.amazonaws.com:5432` |
| route 53     | `{user_provided}` |
| execute-api  | `https://{random_id}.execute-api.{region}.amazonaws.com/{user_provided}` |
| cloudsearch  | `https://doc-{user_provided}-{random_id}.{region}.cloudsearch.amazonaws.com` |
| transfer     | `sftp://s-{random_id}.server.transfer.{region}.amazonaws.com` |
| iot          | `mqtt://{random_id}.iot.{region}.amazonaws.com:8883` |
| iot          | `https://{random_id}.iot.{region}.amazonaws.com:8443` |
| iot          | `https://{random_id}.iot.{region}.amazonaws.com:443` |
| mq           | `https://b-{random_id}-{1,2}.mq.{region}.amazonaws.com:8162` |
| mq           | `ssl://b-{random_id}-{1,2}.mq.{region}.amazonaws.com:61617` |
| kafka        | `b-{1,2,3,4}.{user_provided}.{random_id}.c{1,2}.kafka.{region}.amazonaws.com` |
| kafka        | `{user_provided}.{random_id}.c{1,2}.kafka.useast-1.amazonaws.com` |
| cloud9       | `https://{random_id}.vfs.cloud9.{region}.amazonaws.com` |
| mediastore   | `https://{random_id}.data.mediastore.{region}.amazonaws.com` |
| kinesisvideo | `https://{random_id}.kinesisvideo.{region}.amazonaws.com` |
| mediaconvert | `https://{random_id}.mediaconvert.{region}.amazonaws.com` |
| mediapackage | `https://{random_id}.mediapackage.{region}.amazonaws.com/in/v1/{random_id}/channel` |

#### 1.9. BASIC TUTORIAL TO CREATE A KALI LINUX EC2 VM ON AWS

- Step 1 - Log in to the AWS Console and go to EC2 → Instances → Launch Instance.
- Step 3 - Name your instance (e.g., Kali-Linux-VM).
- Step 4 - Under Application and OS Images (AMI), click Browse more AMIs, search for “Kali Linux”, and select the official Kali AMI from AWS Marketplace.
- Step 5 - When prompted, accept the subscription (Kali AMIs are free, you only pay for compute).
- Step 6 - Under Instance type, choose the option/config you want. Below are example of the cheapest options:
  - t2.small (Specs: 1 vCPU, 2 GiB RAM, Burstable CPU)
  - t3.micro (Specs: 2 vCPUs, 1 GiB RAM, Better performance than t2.micro)
- Step 7 - Under Key pair, create a new SSH key pair or select an existing one.
- Step 8 - Under Network settings, allow SSH (port 22) from your public IP only (recommended for security).
- Step 9 - Keep storage at the default (usually 8–10 GB gp2), which is the cheapest option.
- Step 10 - Click Launch Instance, wait for it to start, then connect via SSH:
  ```
  ssh -i yourkey.pem ec2-user@<public-ip>
  ```

----------------

### II. AWS SECURITY AUDIT

#### 2.1. RUN AWS SECURITY SCANS USING AUDIT TOOLS TO IDENTIFY POTENTIAL SECURITY MISCONFIGURATIONS

- Use audit tools and scripts to evaluate the security posture of an AWS cloud environment and identify potential misconfigurations across key services such as IAM, EC2, S3, Lambda, VPC, CloudTrail, etc.
- Minimum Roles & Privileges for AWS Security Audit Tools (General Guidance)
  ```
  - All major audit tools recommend using a read‑only IAM role that the tool assumes. This role typically includes:
    + 'SecurityAudit' AWS managed policy
    + 'ReadOnlyAccess' AWS managed policy (optional but common)
  - Some tools check services that the 'SecurityAudit' policy doesn’t fully cover and may require extra read‑only permissions such as:
    + iam:Get*, iam:List*
    + ec2:Describe*
    + s3:GetBucketPolicy, s3:GetEncryptionConfiguration
    + cloudtrail:DescribeTrails, cloudtrail:GetEventSelectors
    + config:Describe*
    + kms:ListKeys, kms:DescribeKey
  ```

- AUDIT TOOL N°1 - [SCOUTSUITE](https://github.com/nccgroup/ScoutSuite)  
  - Scout Suite is an open source multi-cloud security-auditing tool, which enables security posture assessment of cloud environments. Using the APIs exposed by cloud providers, Scout Suite gathers configuration data for manual inspection and highlights risk areas. Rather than going through dozens of pages on the web consoles, Scout Suite presents a clear view of the attack surface automatically.
  - Scout Suite was designed by security consultants/auditors. It is meant to provide a point-in-time security-oriented view of the cloud account it was run in. Once the data has been gathered, all usage may be performed offline.  
    
  ```
  ----------------
  Install via Git
  ----------------
  $ git clone https://github.com/nccgroup/ScoutSuite
  $ cd ScoutSuite
  $ virtualenv -p python3 venv
  $ source venv/bin/activate
  $ pip install -r requirements.txt
  ```
  ```
  ----------------
  Install via PIP
  ----------------
  $ virtualenv -p python3 venv
  $ source venv/bin/activate
  $ pip install scoutsuite
  $ scout --help
  ```
  ```
  --------------
  Help (for AWS)
  --------------
  ┌──(venv)─(auditor㉿kali)-[~/Documents/Tools/Cloud-AWS/ScoutSuite]
  └─$ python scout.py aws -help
  usage: scout.py aws [-h] [-f] [-l] [--max-rate MAX_RATE] [--debug] [--quiet] [--logfile [LOG_FILE]] [--update] [--ruleset [RULESET]] [--no-browser] [--max-workers MAX_WORKERS] [--report-dir REPORT_DIR] [--report-name REPORT_NAME] [--timestamp [TIMESTAMP]] [--services SERVICES [SERVICES ...]] [--list-services] [--skip SKIPPED_SERVICES [SKIPPED_SERVICES ...]] [--exceptions [EXCEPTIONS]] [--result-format {json,sqlite}] [--serve [DATABASE_NAME]] [--host HOST_IP] [--port HOST_PORT] [-p PROFILE | --access-keys] [--access-key-id AWS_ACCESS_KEY_ID] [--secret-access-key AWS_SECRET_ACCESS_KEY] [--session-token AWS_SESSION_TOKEN] [-r REGIONS [REGIONS ...]] [-xr EXCLUDED_REGIONS [EXCLUDED_REGIONS ...]] [--ip-ranges IP_RANGES [IP_RANGES ...]] [--ip-ranges-name-key IP_RANGES_NAME_KEY]
  
  options:
    -h, --help            show this help message and exit
  
  Scout Arguments:
    -f, --force           Overwrite existing files
    -l, --local           Use local data previously fetched and re-run the analysis.
    --max-rate MAX_RATE   Maximum number of API requests per second
    --debug               Print the stack trace when exception occurs
    --quiet               Disables CLI output
    --logfile [LOG_FILE]  Additional output to the specified file
    --update              Reload all the existing data and only overwrite data in scope for this run
    --ruleset [RULESET]   Set of rules to be used during the analysis.
    --no-browser          Do not automatically open the report in the browser.
    --max-workers MAX_WORKERS
                          Maximum number of threads (workers) used by Scout Suite (default is 10)
    --report-dir REPORT_DIR
                          Path of the Scout report.
    --report-name REPORT_NAME
                          Name of the Scout report.
    --timestamp [TIMESTAMP]
                          Timestamp added to the name of the report (default is current time in UTC).
    --services SERVICES [SERVICES ...]
                          Name of in-scope services, defaults to all.
    --list-services       List available services.
    --skip SKIPPED_SERVICES [SKIPPED_SERVICES ...]
                          Name of out-of-scope services.
    --exceptions [EXCEPTIONS]
                          Exception file to use during analysis.
    --result-format {json,sqlite}
                          [EXPERIMENTAL FEATURE] The database file format to use. JSON doesn't require a server to view the report, but cannot be viewed if the result file is over 400mb.
    --serve [DATABASE_NAME]
                          [EXPERIMENTAL FEATURE] Serve the specified result database on the server to show the report. This must be used when the results are exported as an sqlite database.
    --host HOST_IP        [EXPERIMENTAL FEATURE] Address on which you want the server to listen. Defaults to localhost.
    --port HOST_PORT      [EXPERIMENTAL FEATURE] Port on which you want the server to listen. Defaults to 8000.
  
  Authentication modes:
    -p, --profile PROFILE
                          Run with a named profile
    --access-keys         Run with access keys
  
  Authentication parameters:
    --access-key-id AWS_ACCESS_KEY_ID
                          AWS Access Key ID
    --secret-access-key AWS_SECRET_ACCESS_KEY
                          AWS Secret Access Key
    --session-token AWS_SESSION_TOKEN
                          AWS Session Token
  
  Additional arguments:
    -r, --regions REGIONS [REGIONS ...]
                          Name of regions to run the tool in, defaults to all
    -xr, --exclude-regions EXCLUDED_REGIONS [EXCLUDED_REGIONS ...]
                          Name of regions to excluded from execution
    --ip-ranges IP_RANGES [IP_RANGES ...]
                          Config file(s) that contain your known IP ranges
    --ip-ranges-name-key IP_RANGES_NAME_KEY

  ```
  ```
  ---------------------------------
   PoC in my AWS test environment
  ---------------------------------
  
  ┌──(venv)─(auditor㉿kali)-[~/Documents/Tools/Cloud-AWS/ScoutSuite]
  └─$ python scout.py aws --access-keys --access-key-id AKIAXXXXXXXXXX --secret-access-key YYYYYYYYYYY --no-browser --report-dir . --report-name cloudsuite-scan-test 
  2026-01-03 20:10:30 kali scout[2717] INFO Launching Scout
  2026-01-03 20:10:30 kali scout[2717] INFO Authenticating to cloud provider
  /home/kali/Documents/Tools/Cloud-AWS/ScoutSuite/ScoutSuite/providers/utils.py:137: SyntaxWarning: invalid escape sequence '\-'
  <SNIP>
  2026-01-03 20:10:33 kali scout[2717] INFO Gathering data from APIs
  2026-01-03 20:10:33 kali scout[2717] INFO Fetching resources for the ACM service
  2026-01-03 20:10:33 kali scout[2717] INFO Fetching resources for the Lambda service
  2026-01-03 20:10:34 kali scout[2717] INFO Fetching resources for the CloudFormation service
  2026-01-03 20:10:34 kali scout[2717] INFO Fetching resources for the CloudTrail service
  2026-01-03 20:10:34 kali scout[2717] INFO Fetching resources for the CloudWatch service
  2026-01-03 20:10:35 kali scout[2717] INFO Fetching resources for the CloudFront service
  2026-01-03 20:10:35 kali scout[2717] INFO Fetching resources for the CodeBuild service
  2026-01-03 20:10:36 kali scout[2717] INFO Fetching resources for the Config service
  2026-01-03 20:10:36 kali scout[2717] INFO Fetching resources for the Direct Connect service
  2026-01-03 20:10:37 kali scout[2717] INFO Fetching resources for the DynamoDB service
  2026-01-03 20:10:37 kali scout[2717] INFO Fetching resources for the EC2 service
  2026-01-03 20:10:38 kali scout[2717] INFO Fetching resources for the EFS service
  2026-01-03 20:10:38 kali scout[2717] INFO Fetching resources for the ElastiCache service
  2026-01-03 20:10:38 kali scout[2717] INFO Fetching resources for the ELB service
  2026-01-03 20:10:39 kali scout[2717] INFO Fetching resources for the ELBv2 service
  2026-01-03 20:10:39 kali scout[2717] INFO Fetching resources for the EMR service
  2026-01-03 20:10:40 kali scout[2717] INFO Fetching resources for the IAM service
  2026-01-03 20:10:40 kali scout[2717] INFO Fetching resources for the KMS service
  2026-01-03 20:10:40 kali scout[2717] INFO Fetching resources for the RDS service
  2026-01-03 20:10:41 kali scout[2717] INFO Fetching resources for the RedShift service
  2026-01-03 20:10:41 kali scout[2717] INFO Fetching resources for the Route53 service
  2026-01-03 20:10:41 kali scout[2717] INFO Fetching resources for the S3 service
  2026-01-03 20:10:42 kali scout[2717] INFO Fetching resources for the SES service
  2026-01-03 20:10:43 kali scout[2717] INFO Fetching resources for the SNS service
  2026-01-03 20:10:43 kali scout[2717] INFO Fetching resources for the SQS service
  2026-01-03 20:10:43 kali scout[2717] INFO Fetching resources for the VPC service
  2026-01-03 20:10:44 kali scout[2717] INFO Fetching resources for the Secrets Manager service
  2026-01-03 20:11:48 kali scout[2717] INFO Running pre-processing engine
  2026-01-03 20:11:48 kali scout[2717] INFO Running rule engine
  2026-01-03 20:11:51 kali scout[2717] INFO Applying display filters
  2026-01-03 20:11:51 kali scout[2717] INFO Running post-processing engine
  2026-01-03 20:11:51 kali scout[2717] INFO Saving data to ./scoutsuite-results/scoutsuite_results_cloudsuite-scan-test.js
  2026-01-03 20:11:52 kali scout[2717] INFO Saving data to ./scoutsuite-results/scoutsuite_exceptions_cloudsuite-scan-test.js
  2026-01-03 20:11:52 kali scout[2717] INFO Creating ./cloudsuite-scan-test.html
  ```
  ```
  ---------------------------------------------------------------------------------------------------
  Examples of security issues identified in my vulnerable AWS test environment for IAM, EC2, and VPC
  ---------------------------------------------------------------------------------------------------
  
  ----- IAM service -----
  
  Danger (Red) 
  + AssumeRole Policy Allows All Principals
  + Credentials Unused for 90 Days or Greater Are Not Disabled
  + Cross-Account AssumeRole Policy Lacks External ID and MFA
  + Lack of Key Rotation for 90 Days (Key Status: Active)
  + Managed Policy Allows All Actions
  + Minimum Password Length Too Short
  + Password Expiration Disabled
  + Password Policy Allows the Reuse of Passwords
  + Passwords Expire after 90 Days
  + Root Account Used Recently
  + Root Account without Hardware MFA 
  
  Good (Green)
  + Group with Inline Policies
  + Group with No Users
  + Lack of Key Rotation for 90 Days (Key Status: Inactive)
  + Managed Policy Allows "iam:PassRole" For All Resources
  + Managed Policy Allows "NotActions"
  + Managed Policy Allows "sts:AssumeRole" For All Resources
  + Managed Policy Not Attached to Any Entity
  + Policy with Denied User Actions for Group Objects
  + Role with Inline Policies
  + Root Account Has Active Keys
  + Root Account Has Active X.509 Certs
  + Root Account without MFA
  + User with inline Policies
  + User with Multiple API Keys
  + User with Password and Keys Enabled
  + User without MFA 
  
  ----- EC2 service -----

  Danger (Red) 
  + EBS Volume Not Encrypted
  + Security Group Opens SSH Port to All
  
  Warning (Amber)
  + EBS Encryption By Default Is Disabled
  + Non-empty Rulesets for Default Security Groups
  + Security Group Opens All Ports
  + Unrestricted Network Traffic within Security Group
  + Unused Security Group 
  
  Good (Green)
  + Default Security Groups in Use
  + Potential Secret in Instance User Data
  + Security Group Allows ICMP Traffic to All
  + Security Group Opens All Ports to All
  + Security Group Opens DNS Port to All
  + Security Group Opens FTP Port
  + Security Group Opens MongoDB Port to All
  + Security Group Opens MsSQL Port to All
  + Security Group Opens MySQL Port to All
  + Security Group Opens NFS Port to All
  + Security Group Opens Oracle DB Port to All
  + Security Group Opens PostgreSQL Port to All
  + Security Group Opens RDP Port to All
  + Security Group Opens SMTP Port to All
  + Security Group Opens TCP Port to All
  + Security Group Opens Telnet Port
  + Security Group Opens UDP Port to All
  + Security Group Uses Port Range
  + Security Group Whitelists AWS CIDRs

  ----- VPC Dashboard -----
  
  Warning (Amber)
  + Network ACLs Allow All egress Traffic (default)
  + Network ACLs Allow All ingress Traffic (default)
  + Subnet with "Allow All" egress NACLs
  + Subnet with "Allow All" ingress NACLs
  + Subnet without a Flow Log
  
  Good (Green)
  + Network ACLs Allow All egress Traffic (custom)
  + Network ACLs Allow All ingress Traffic (custom)
  + Unused Network ACLs
  ```

  
- AUDIT TOOL N°2 - [PROWLER](https://github.com/prowler-cloud/prowler)  
  - Prowler is an Open Cloud Security platform trusted by thousands to automate security and compliance in any cloud environment. With hundreds of ready-to-use checks and compliance frameworks, Prowler delivers real-time, customizable monitoring and seamless integrations, making cloud security simple, scalable, and cost-effective for organizations of any size.
    
  ```
  --------------------------------------------------------------
  Install Prowler CLI - Pip package (with Python >3.9.1, <3.13)
  --------------------------------------------------------------
  $ virtualenv -p python3 venv2
  $ source venv2/bin/activate
  $ pip install prowler
  ```
  ```
  ---------------------------------------------------------------------------------------
  Install Prowler CLI from GitHub (Python >3.9.1, <3.13 is required with pip and Poetry)
  ---------------------------------------------------------------------------------------
  $ git clone https://github.com/prowler-cloud/prowler
  $ cd prowler
  $ eval $(poetry env activate)
  $ poetry install
  ```
  ```
  --------------
  Help (for AWS)
  --------------

  $ prowler aws -h
  usage: prowler aws [-h] [-q] [-M {csv,json,json-asff,html,json-ocsf} [{csv,json,json-asff,html,json-ocsf} ...]] [-F [OUTPUT_FILENAME]] [-o [OUTPUT_DIRECTORY]] [--verbose] [-z] [-b] [--slack]
                     [--unix-timestamp] [--log-level {DEBUG,INFO,WARNING,ERROR,CRITICAL}] [--log-file [LOG_FILE]] [--only-logs] [-c CHECKS [CHECKS ...]] [-C [CHECKS_FILE]] [-s SERVICES [SERVICES ...]]
                     [--severity {informational,low,medium,high,critical} [{informational,low,medium,high,critical} ...]]
                     [--compliance {aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} [{aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} ...]]
                     [--categories CATEGORIES [CATEGORIES ...]] [-x [CHECKS_FOLDER]] [-e EXCLUDED_CHECKS [EXCLUDED_CHECKS ...]] [--excluded-services EXCLUDED_SERVICES [EXCLUDED_SERVICES ...]] [-l |
                     --list-checks-json | --list-services | --list-compliance |
                     --list-compliance-requirements {aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} [{aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} ...] |
                     --list-categories] [--config-file [CONFIG_FILE]] [-p [PROFILE]] [-R [ROLE]] [--sts-endpoint-region [STS_ENDPOINT_REGION]] [--mfa] [-T [SESSION_DURATION]] [-I [EXTERNAL_ID]]
                     [-f {il-central-1,ap-east-1,eu-west-2,ca-central-1,us-west-1,us-east-2,eu-south-1,ap-southeast-3,us-gov-west-1,ap-southeast-1,ap-south-2,eu-central-1,eu-south-2,eu-west-3,eu-central-2,me-central-1,me-south-1,ap-southeast-2,af-south-1,eu-north-1,sa-east-1,cn-northwest-1,ap-southeast-4,ap-northeast-1,ap-northeast-3,us-west-2,ap-northeast-2,ap-south-1,us-east-1,cn-north-1,us-gov-east-1,eu-west-1} [{il-central-1,ap-east-1,eu-west-2,ca-central-1,us-west-1,us-east-2,eu-south-1,ap-southeast-3,us-gov-west-1,ap-southeast-1,ap-south-2,eu-central-1,eu-south-2,eu-west-3,eu-central-2,me-central-1,me-south-1,ap-southeast-2,af-south-1,eu-north-1,sa-east-1,cn-northwest-1,ap-southeast-4,ap-northeast-1,ap-northeast-3,us-west-2,ap-northeast-2,ap-south-1,us-east-1,cn-north-1,us-gov-east-1,eu-west-1} ...]]
                     [-O [ORGANIZATIONS_ROLE]] [-S] [--skip-sh-update] [-i] [-B [OUTPUT_BUCKET] | -D [OUTPUT_BUCKET_NO_ASSUME]] [-N [SHODAN]] [-w [ALLOWLIST_FILE]]
                     [--resource-tags RESOURCE_TAGS [RESOURCE_TAGS ...] | --resource-arn RESOURCE_ARN [RESOURCE_ARN ...]] [--aws-retries-max-attempts [AWS_RETRIES_MAX_ATTEMPTS]] [--ignore-unused-services]
  
  options:
    -h, --help            show this help message and exit
  
  Outputs:
    -q, --quiet           Store or send only Prowler failed findings
    -M, --output-modes {csv,json,json-asff,html,json-ocsf} [{csv,json,json-asff,html,json-ocsf} ...]
                          Output modes, by default csv, html and json
    -F, --output-filename [OUTPUT_FILENAME]
                          Custom output report name without the file extension, if not specified will use default output/prowler-output-ACCOUNT_NUM-OUTPUT_DATE.format
    -o, --output-directory [OUTPUT_DIRECTORY]
                          Custom output directory, by default the folder where Prowler is stored
    --verbose             Display detailed information about findings
    -z, --ignore-exit-code-3
                          Failed checks do not trigger exit code 3
    -b, --no-banner       Hide Prowler banner
    --slack               Send a summary of the execution with a Slack APP in your channel. Environment variables SLACK_API_TOKEN and SLACK_CHANNEL_ID are required (see more in
                          https://docs.prowler.cloud/en/latest/tutorials/integrations/#slack).
    --unix-timestamp      Set the output timestamp format as unix timestamps instead of iso format timestamps (default mode).
  
  Logging:
    --log-level {DEBUG,INFO,WARNING,ERROR,CRITICAL}
                          Select Log Level
    --log-file [LOG_FILE]
                          Set log file name
    --only-logs           Print only Prowler logs by the stdout. This option sets --no-banner.
  
  Specify checks/services to run:
    -c, --checks CHECKS [CHECKS ...]
                          List of checks to be executed.
    -C, --checks-file [CHECKS_FILE]
                          JSON file containing the checks to be executed. See config/checklist_example.json
    -s, --services SERVICES [SERVICES ...]
                          List of services to be executed.
    --severity {informational,low,medium,high,critical} [{informational,low,medium,high,critical} ...]
                          List of severities to be executed [informational, low, medium, high, critical]
    --compliance {aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} [{aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} ...]
                          Compliance Framework to check against for. The format should be the following: framework_version_provider (e.g.: ens_rd2022_aws)
    --categories CATEGORIES [CATEGORIES ...]
                          List of categories to be executed.
    -x, --checks-folder [CHECKS_FOLDER]
                          Specify external directory with custom checks (each check must have a folder with the required files, see more in https://docs.prowler.cloud/en/latest/tutorials/misc/#custom-checks).
  
  Exclude checks/services to run:
    -e, --excluded-checks EXCLUDED_CHECKS [EXCLUDED_CHECKS ...]
                          Checks to exclude
    --excluded-services EXCLUDED_SERVICES [EXCLUDED_SERVICES ...]
                          Services to exclude
  
  List checks/services/categories/compliance-framework checks:
    -l, --list-checks     List checks
    --list-checks-json    Output a list of checks in json for use with --checks-file
    --list-services       List services
    --list-compliance     List compliance frameworks
    --list-compliance-requirements {aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} [{aws_well_architected_framework_reliability_pillar_aws,cisa_aws,fedramp_low_revision_4_aws,pci_3.2.1_aws,gdpr_aws,aws_audit_manager_control_tower_guardrails_aws,nist_800_171_revision_2_aws,ffiec_aws,iso27001_2013_aws,gxp_21_cfr_part_11_aws,cis_1.5_aws,ens_rd2022_aws,aws_well_architected_framework_security_pillar_aws,nist_800_53_revision_4_aws,mitre_attack_aws,aws_foundational_security_best_practices_aws,gxp_eu_annex_11_aws,nist_csf_1.1_aws,rbi_cyber_security_framework_aws,nist_800_53_revision_5_aws,soc2_aws,hipaa_aws,cis_2.0_aws,cis_1.4_aws,fedramp_moderate_revision_4_aws,cis_2.0_gcp} ...]
                          List compliance requirements for a given compliance framework
    --list-categories     List the available check's categories
  
  Configuration:
    --config-file [CONFIG_FILE]
                          Set configuration file path
  
  Authentication Modes:
    -p, --profile [PROFILE]
                          AWS profile to launch prowler with
    -R, --role [ROLE]     ARN of the role to be assumed
    --sts-endpoint-region [STS_ENDPOINT_REGION]
                          Specify the AWS STS endpoint region to use. Read more at https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html
    --mfa                 IAM entity enforces MFA so you need to input the MFA ARN and the TOTP
    -T, --session-duration [SESSION_DURATION]
                          Assumed role session duration in seconds, must be between 900 and 43200. Default: 3600
    -I, --external-id [EXTERNAL_ID]
                          External ID to be passed when assuming role
  
  AWS Regions:
    -f, --region, --filter-region {il-central-1,ap-east-1,eu-west-2,ca-central-1,us-west-1,us-east-2,eu-south-1,ap-southeast-3,us-gov-west-1,ap-southeast-1,ap-south-2,eu-central-1,eu-south-2,eu-west-3,eu-central-2,me-central-1,me-south-1,ap-southeast-2,af-south-1,eu-north-1,sa-east-1,cn-northwest-1,ap-southeast-4,ap-northeast-1,ap-northeast-3,us-west-2,ap-northeast-2,ap-south-1,us-east-1,cn-north-1,us-gov-east-1,eu-west-1} [{il-central-1,ap-east-1,eu-west-2,ca-central-1,us-west-1,us-east-2,eu-south-1,ap-southeast-3,us-gov-west-1,ap-southeast-1,ap-south-2,eu-central-1,eu-south-2,eu-west-3,eu-central-2,me-central-1,me-south-1,ap-southeast-2,af-south-1,eu-north-1,sa-east-1,cn-northwest-1,ap-southeast-4,ap-northeast-1,ap-northeast-3,us-west-2,ap-northeast-2,ap-south-1,us-east-1,cn-north-1,us-gov-east-1,eu-west-1} ...]
                          AWS region names to run Prowler against
  
  AWS Organizations:
    -O, --organizations-role [ORGANIZATIONS_ROLE]
                          Specify AWS Organizations management role ARN to be assumed, to get Organization metadata
  
  AWS Security Hub:
    -S, --security-hub    Send check output to AWS Security Hub
    --skip-sh-update      Skip updating previous findings of Prowler in Security Hub
  
  Quick Inventory:
    -i, --quick-inventory
                          Run Prowler Quick Inventory. The inventory will be stored in an output csv by default
  
  AWS Outputs to S3:
    -B, --output-bucket [OUTPUT_BUCKET]
                          Custom output bucket, requires -M <mode> and it can work also with -o flag.
    -D, --output-bucket-no-assume [OUTPUT_BUCKET_NO_ASSUME]
                          Same as -B but do not use the assumed role credentials to put objects to the bucket, instead uses the initial credentials.
  
  3rd Party Integrations:
    -N, --shodan [SHODAN]
                          Shodan API key used by check ec2_elastic_ip_shodan.
  
  Allowlist:
    -w, --allowlist-file [ALLOWLIST_FILE]
                          Path for allowlist yaml file. See example prowler/config/aws_allowlist.yaml for reference and format. It also accepts AWS DynamoDB Table or Lambda ARNs or S3 URIs, see more in
                          https://docs.prowler.cloud/en/latest/tutorials/allowlist/
  
  AWS Based Scans:
    --resource-tags RESOURCE_TAGS [RESOURCE_TAGS ...]
                          Scan only resources with specific AWS Tags (Key=Value), e.g., Environment=dev Project=prowler
    --resource-arn RESOURCE_ARN [RESOURCE_ARN ...]
                          Scan only resources with specific AWS Resource ARNs, e.g., arn:aws:iam::012345678910:user/test arn:aws:ec2:us-east-1:123456789012:vpc/vpc-12345678
  
  Boto3 Config:
    --aws-retries-max-attempts [AWS_RETRIES_MAX_ATTEMPTS]
                          Set the maximum attemps for the Boto3 standard retrier config (Default: 3)
  
  Ignore Unused Services:
    --ignore-unused-services
                          Ignore findings in unused services
  ```
  ```
  ---------------------------------
   PoC in my AWS test environment
  ---------------------------------
  
  ┌──(venv2)─(kali㉿kali)-[~/Documents/Tools/Prowler]
  └─$ AWS_ACCESS_KEY_ID=AKIAXXXXXXXXXX \
  AWS_SECRET_ACCESS_KEY=YYYYYYYYYYYYYY \
  prowler aws -o reports/ -M json html csv                
                           _
   _ __  _ __ _____      _| | ___ _ __
  | '_ \| '__/ _ \ \ /\ / / |/ _ \ '__|
  | |_) | | | (_) \ V  V /| |  __/ |
  | .__/|_|  \___/ \_/\_/ |_|\___|_|v3.11.3
  |_| the handy cloud security tool
  
  Date: 2026-01-03 22:56:19
  
  
  This report is being generated using credentials below:
  
  AWS-CLI Profile: [default] AWS Filter Region: [all]
  AWS Account: [489XXXXXXXX UserId: [AKIAXXXXXXXXXX]
  Caller Identity ARN: [arn:aws:iam::48XXXXXXXXXX:user/Auditor]
  
  Executing 301 checks, please wait...
  
  Something went wrong in iam_avoid_root_usage, please use --log-level ERROR
  Something went wrong in iam_rotate_access_key_90_days, please use --log-level ERROR
  Something went wrong in iam_user_accesskey_unused, please use --log-level ERROR
  -> Scan completed! |▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉| 301/301 [100%] in 2:50.4 
  
  Overview Results:
  ╭─────────────────────┬─────────────────────╮
  │ 48.78% (421) Failed │ 50.75% (438) Passed │
  ╰─────────────────────┴─────────────────────╯
  
  Account 4899XXXXXX Scan Results (severity columns are for fails only):
  ╭────────────┬───────────────────┬───────────┬────────────┬────────┬──────────┬───────╮
  │ Provider   │ Service           │ Status    │   Critical │   High │   Medium │   Low │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ accessanalyzer    │ FAIL (17) │          0 │      0 │        0 │    17 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ account           │ FAIL (1)  │          0 │      0 │        1 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ athena            │ FAIL (34) │          0 │      0 │       34 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ backup            │ FAIL (1)  │          0 │      0 │        0 │     1 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ cloudtrail        │ FAIL (20) │          0 │     17 │        0 │     3 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ cloudwatch        │ FAIL (15) │          0 │      0 │       15 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ config            │ FAIL (17) │          0 │      0 │       17 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ ec2               │ FAIL (95) │          0 │     19 │       74 │     2 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ emr               │ PASS (17) │          0 │      0 │        0 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ glue              │ FAIL (34) │          0 │      0 │       34 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ guardduty         │ FAIL (17) │          0 │      0 │       17 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ iam               │ FAIL (22) │          1 │      3 │        9 │     9 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ inspector2        │ FAIL (17) │          0 │      0 │       17 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ network-firewall  │ FAIL (17) │          0 │      0 │       17 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ organizations     │ FAIL (3)  │          0 │      0 │        2 │     1 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ resourceexplorer2 │ FAIL (1)  │          0 │      0 │        0 │     1 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ s3                │ FAIL (1)  │          0 │      1 │        0 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ securityhub       │ FAIL (17) │          0 │      0 │       17 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ ssm               │ FAIL (1)  │          0 │      0 │        0 │     1 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ trustedadvisor    │ PASS (1)  │          0 │      0 │        0 │     0 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ support           │ FAIL (1)  │          0 │      0 │        0 │     1 │
  ├────────────┼───────────────────┼───────────┼────────────┼────────┼──────────┼───────┤
  │ aws        │ vpc               │ FAIL (90) │          0 │      0 │       90 │     0 │
  ╰────────────┴───────────────────┴───────────┴────────────┴────────┴──────────┴───────╯
  * You only see here those services that contains resources.
  
  Detailed results are in:
   - HTML: reports//prowler-output-489XXXXXXXX-20260103225619.html
   - CSV: reports//prowler-output-489XXXXXXXX-20260103225619.csv
   - JSON: reports//prowler-output-489XXXXXXXX-20260103225619.json
                                                                  
  ```

- AUDIT TOOL N°3 - [CLOUDSPLOIT](https://github.com/aquasecurity/cloudsploit) 
  - It is an open-source project designed to allow detection of security risks in cloud infrastructure accounts, including: Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), Oracle Cloud Infrastructure (OCI), and GitHub. These scripts are designed to return a series of potential misconfigurations and security risks.
  ```
  -------------
  Using Docker
  -------------
  $ git clone https://github.com/aquasecurity/cloudsploit.git
  $ cd cloudsploit
  $ docker build . -t cloudsploit:0.0.1 

  ┌──(auditor㉿kali)-[~/…/Tools/Cloud-AWS/CloudSploit/cloudsploit]
  └─$ docker run cloudsploit:0.0.1                          
  
     _____ _                 _  _____       _       _ _   
    / ____| |               | |/ ____|     | |     (_) |  
   | |    | | ___  _   _  __| | (___  _ __ | | ___  _| |_ 
   | |    | |/ _ \| | | |/ _` |\___ \| '_ \| |/ _ \| | __|
   | |____| | (_) | |_| | (_| |____) | |_) | | (_) | | |_ 
    \_____|_|\___/ \__,_|\__,_|_____/| .__/|_|\___/|_|\__|
                                     | |                  
                                     |_|                  
  
    CloudSploit by Aqua Security, Ltd.
    Cloud security auditing for AWS, Azure, GCP, Oracle, and GitHub
  
  usage: cloudsploit-scan [-h] [--config CONFIG]
                          [--compliance {hipaa,cis,cis1,cis2,pci}]
                          [--plugin PLUGIN] [--govcloud] [--china] [--csv CSV]
                          [--json JSON] [--junit JUNIT]
                          [--console {none,text,table}]
                          [--collection COLLECTION] [--ignore-ok] [--exit-code]
                          [--skip-paginate] [--suppress SUPPRESS]
                          [--remediate REMEDIATE]
                          [--cloud {aws,azure,github,google,oracle,alibaba}]
                          [--run-asl]
  
  optional arguments:
    -h, --help            show this help message and exit
    --config CONFIG       The path to a CloudSploit config file containing cloud
                          credentials. See config_example.js. If not provided,
                          logic will use default AWS credential chain and will
                          also override provided cloud
    --compliance {hipaa,cis,cis1,cis2,pci}
                          Compliance mode. Only return results applicable to the
                          selected program.
    --plugin PLUGIN       A specific plugin to run. If none provided, all
                          plugins will be run. Obtain from the exports.js file.
                          E.g. acmValidation
    --govcloud            AWS only. Enables GovCloud mode.
    --china               AWS only. Enables AWS China mode.
    --csv CSV             Output: CSV file
    --json JSON           Output: JSON file
    --junit JUNIT         Output: Junit file
    --console {none,text,table}
                          Console output format. Default: table
    --collection COLLECTION
                          Output: full collection JSON as file
    --ignore-ok           Ignore passing (OK) results
    --exit-code           Exits with a non-zero status code if non-passing
                          results are found
    --skip-paginate       AWS only. Skips pagination (for debugging).
    --suppress SUPPRESS   Suppress results matching the provided Regex. Format:
                          pluginId:region:resourceId
    --remediate REMEDIATE
                          Run remediation the provided plugin
    --cloud {aws,azure,github,google,oracle,alibaba}
                          The name of cloud to run plugins for. If not provided,
                          logic will assume cloud from config.js file based on
                          provided credentials
    --run-asl             When set, it will execute custom plugins.
  ```
  ```
  ------------------
  Example of command
  ------------------
  $ docker run --rm -e AWS_ACCESS_KEY_ID=XXXXX -e AWS_SECRET_ACCESS_KEY=YYYYYYY cloudsploit:0.0.1 --cloud aws 
  ```

- AUDIT TOOL N°4 - [Cloudsplaining](https://github.com/salesforce/cloudsplaining)
  - Cloudsplaining is an AWS IAM Security Assessment tool that identifies violations of least privilege and generates a risk-prioritized report.

  ```
  --------------------
  Install using PIP3
  --------------------
  $ virtualenv -p python3 venv2
  $ source venv2/bin/activate
  $ pip3 install --user cloudsplaining
  ```
  ```
  ------------------------------------------------------
  Example of command -  Scanning an entire AWS Account
  ------------------------------------------------------
  - You must have AWS credentials configured that can be used by the CLI.
  - You must have the privileges to run iam:GetAccountAuthorizationDetails.
    The arn:aws:iam::aws:policy/SecurityAudit policy includes this, as do many others that allow Read access to the IAM Service.

  Step 1. Downloading Account Authorization Details
  $ cloudsplaining download                      #If you use environment variables 
  $ cloudsplaining download --profile myprofile  #With a profile (~/.aws/credentials)

  Step 2. Create Exclusions file
  cloudsplaining create-exclusions-file          #Follow instructions from the Github project 
  
  Step 3. Scanning the Authorization Details file
  $ cloudsplaining scan --exclusions-file exclusions.yml --input-file examples/files/example.json --output examples/files/
  -> It will create an HTML report and a JSON report.
  ```

  
#### 2.2. CHECK FOR KNOWN PRIVESC ATTACK VECTORS IN AWS
  
- Usefull ressources:
  - https://github.com/RhinoSecurityLabs/AWS-IAM-Privilege-Escalation
  - https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/
  - https://bishopfox.com/blog/5-privesc-attack-vectors-in-aws
  - https://bishopfox.com/blog/privilege-escalation-in-aws

##### 2.2.1 Privilege escalation - Abusing IAM permissions

- IAM Permissions on Other Users  
  - The following user account and group permissions can all be used to escalate privileges:
      + <i/> iam:CreateAccessKey </i>
      + <i/> iam:CreateLoginProfile </i>
      + <i/> iam:UpdateLoginProfile </i>
      + <i/> iam:AddUserToGroup </i>  
  - Using a blacklist instead of a whitelist approach could leave the door open for users to escalate their privileges. This is especially true if a wildcard is used under the Resource section, because that means the user can take these actions for any group, policy, role, or other user account. Some ways that these permissions could allow privilege escalation include:
      + A user adding their own account to an Admin group
      + A user creating a new API key for a more privileged user account
      + A user updating the account password for a more privileged user account
     
- IAM Permissions on Policies
  - The following policy permissions can lead to privilege escalation:
      + <i/> iam:CreatePolicyVersion </i>
      + <i/> iam:SetDefaultPolicyVersion </i>
      + <i/> iam:AttachUserPolicy </i>
      + <i/> iam:AttachGroupPolicy </i>
      + <i/> iam:AttachRolePolicy </i>
      + <i/> iam:PutUserPolicy </i>
      + <i/> iam:PutGroupPolicy </i>
      + <i/> iam:PutRolePolicy </i>
  - The permission to create a new policy version allows a user to completely replace the permissions in a policy. If this policy applies to them, this opens the door for the user to just assign themselves full AWS administrative privileges. Attaching a policy or creating a new policy for a user, group, or role can similarly increase the permissions applied to the user.

- IAM policies or trust relationships misconfiguration
  - Overly permissive trust policies <i/>(The trust policy specifies which principals are allowed to use the role - Synonym for AssumeRolePolicy)</i>
      + If a role’s trust policy allows any principal (user, role, or account) to assume it, someone with lower privileges could gain access to a more powerful role.
      + Example:  A role intended for administrators trusts “any AWS principal” instead of a specific role or account. 
  - Wildcard permissions in IAM policies
      + If a user or role has permissions like 'sts:AssumeRole' with wildcards, they may be able to assume roles that grant more privileges than intended.

- IAM - Updating an AssumeRolePolicy
  - The 'iam:UpdateAssumeRolePolicy' permission provides an entity with the ability to change a role’s AssumeRolePolicy. So, if a user is not included in this policy but they have the ability to update it, they can just add their user account to the AssumeRolePolicy and consequently assume the role. Depending on the permissions associated with the role, this could provide a path for privilege escalation.
   
- IAM - 'iam:PassRole:*'
  - The 'iam:PassRole' permission allows a user to pass a role to an AWS entity. Passing roles is a crucial element in AWS permissions and resource management. For instance, when deploying an application to AWS, the application may need to perform certain actions on the back end, such as accessing databases or running Lambda functions. In order to allow the application to access these AWS services, you pass a role to it that contains the necessary permissions.
  - Problems with the 'iam:PassRole' permission occur when there is no defined role or set of roles that the principal is allowed to pass. For instance, policies that allow the use of iam:PassRole on wildcard resources (iam:PassRole:*). In effect, this allows the user to pass any role that exists in the environment, including any existing privileged roles. This may open up the possibility for a user to pass a privileged role to an AWS resource or service, and then use that resource or service to perform privileged actions.

##### 2.2.2 Privilege escalation - Abusing LAMBDA permissions

- LAMBDA - 'lambda:UpdateFunctionCode' and 'lambda:UpdateFunctionConfiguration'
  - With access to the 'lambda:UpdateFunctionCode' permission, an adversary can modify an existing Lambda function's code. This would allow them to gain access to the privileges of the associated IAM role the next time the function is executed. The risk here depends on the permissions with which the Lambda function operates.

- LAMBDA - 'lambda:UpdateFunctionConfiguration'
  - With access to the lambda:UpdateFunctionConfiguration permission, an adversary can modify an existing Lambda function's configuration to add a new Lambda Layer. This Layer would then override an existing library and allow an adversary to execute malicious code under the privilege of the role associated with the Lambda function.

##### 2.2.3 Privilege escalation - Abusing GLUE permissions

- GLUE - 'glue:UpdateDevEndpoint' and 'glue:GetDevEndpoint'
  - The permission to modify a Glue development endpoint (glue:UpdateDevEndpoint) can lead to privilege escalation because a user might, for example, change the SSH key associated with an endpoint so they can then log into the system and use it to perform privileged actions (i.e, gain access to IAM credentials associated with the role attached to the glue endpoint). Again, the risk depends on the permissions assigned to the endpoint.
    - Though not required, it may be helpful to have the glue:GetDevEndpoint permission as well, if the existing endpoint cannot be identified via other means.

##### 2.2.4 Privilege escalation - Abusing CODESTAR permissions

- CODESTAR - 'codestar:CreateProject' and 'codestar:AssociateTeamMember'
  - With access to the 'codestar:CreateProject' and 'codestar:AssociateTeamMember' permissions, an adversary can create a new CodeStar project and associate themselves as an Owner of the project. This will attach a new policy to the user that provides access to a number of permissions for AWS services. This is most useful for further enumeration as it gives access to 'lambda:List*', 'iam:ListRoles', 'iam:ListUsers', and more.

##### 2.2.5 Privilege escalation - Abusing CloudFormation permissions

- CloudFormation and Data Pipeline may be used for privesc if the user has the 'iam:PassRole' permission.

----------------

### III. AWS PENETRATION TESTING

#### 3.1. AWS CUSTOMER SUPPORT AND SERVICE POLICY FOR PENETRATION TESTING

Link - https://aws.amazon.com/pt/security/penetration-testing/

- AWS customers are welcome to carry out security assessments or penetration tests of their AWS infrastructure without prior approval for the services listed in the next section under “Permitted Services.” Additionally, AWS permits customers to host their security assessment tooling within the AWS IP space or other cloud provider for on-prem, in AWS, or third party contracted testing. All security testing that includes Command and Control (C2) requires prior approval.
- Note: Customers are not permitted to conduct any security assessments of AWS infrastructure or the AWS services themselves. If you discover a security issue within any of the AWS services observed in your security assessment, please contact AWS Security immediately.

- Permitted Services
  - Amazon EC2 instances, WAF, NAT Gateways, and Elastic Load Balancers
  - Amazon RDS
  - Amazon CloudFront
  - Amazon Aurora
  - Amazon API Gateways
  - AWS AppSync
  - AWS Lambda and Lambda Edge functions
  - Amazon Lightsail resources
  - Amazon Elastic Beanstalk environments
  - Amazon Elastic Container Service
  - AWS Fargate
  - Amazon OpenSearch Service
  - Amazon FSx
  - Amazon Transit Gateway

- Prohibited Activities  
  <i/> Customers seeking to test non approved services will need to work directly with AWS Support or your account representative.</i> 
  - DNS zone walking via Amazon Route 53 Hosted Zones
  - DNS hijacking via Route 53
  - DNS Pharming via Route 53
  - Denial of Service (DoS), Distributed Denial of Service (DDoS),
  - Simulated DoS, Simulated DDoS (These are subject to the DDoS Simulation Testing policy Port flooding
  - Protocol flooding
  - Request flooding (login request flooding, API request flooding)
  - S3 bucket takeover
  - Subdomain Takeover

- Prohibited Services for Outbound Penetration Testing
  - Amazon API Gateway

#### 3.2. AWS PENTEST METHODOLOGY - USEFULL RESSOURCES

  - https://github.com/swisskyrepo/InternalAllTheThings/tree/main/docs/cloud/aws
  - https://pentestbook.six2dez.com/enumeration/cloud/aws
  - https://github.com/CyberSecurityUP/Awesome-Cloud-PenTest
  - https://github.com/CyberSecurityUP/Cloud-Security-Attacks
  - https://www.hackerone.com/knowledge-center/penetration-testing-aws-practical-guide
  - https://github.com/dafthack/CloudPentestCheatsheets/blob/master/cheatsheets/AWS.md
 

#### 3.3. LIST OF AWS PENTEST TOOLS


| Tools | Description |
| --- | --- |
| [PACU](https://github.com/RhinoSecurityLabs/pacu) | The AWS exploitation framework, designed for testing the security of Amazon Web Services environments. |
| [Cloud_enum](https://github.com/initstring/cloud_enum) | Multi-cloud OSINT tool. Enumerate public resources in AWS, Azure, and Google Cloud. |
| [CloudFox](https://github.com/BishopFox/CloudFox/) | Automating situational awareness for cloud penetration tests. |
| [S3Scanner](https://github.com/sa7mon/S3Scanner) | Scan for misconfigured S3 buckets across S3-compatible APIs. |
| [S3_objects_check](https://github.com/nccgroup/s3_objects_check) | Whitebox evaluation of effective S3 object permissions, to identify publicly accessible files.|
| [CloudPEASS](https://github.com/peass-ng/CloudPEASS) | Multi-Cloud Privilege Escalation Awesome Script Suite - AWSPEAS is your ultimate tool for enumerating AWS permissions and uncovering potential privilege escalation paths and other attack vectors—all while leaving your target environment unchanged. It leverages multiple techniques to gather, simulate, and even infer permissions, giving you deep insights into the security posture of your AWS setup.Whitebox evaluation of effective S3 object permissions, to identify publicly accessible files.|
| [Enumerate-IAM](https://github.com/andresriancho/enumerate-iam) | Enumerate the permissions associated with AWS credential set. |
| [IAMActionHunter](https://github.com/RhinoSecurityLabs/IAMActionHunter) | An AWS IAM policy statement parser and query tool. |
| [AWS IAM Privilege Escalation](https://github.com/RhinoSecurityLabs/AWS-IAM-Privilege-Escalation) | AWS IAM Privilege Escalation Methods |
| [Orca-Security - Iam-ape](https://github.com/orcasecurity/orca-toolbox/tree/main/iam-ape) | APE takes all of your AWS IAM policies attached to a User, Group, or Role object, and presents you with a single policy, summarizing all of their actual permissions. Taking into account permissions, denials, inherited permissions and permission boundaries! |
| [Stratus Red Team](https://github.com/DataDog/stratus-red-team) | It is "Atomic Red Team™" for the cloud, allowing to emulate offensive attack techniques in a granular and self-contained manner. |


#### 3.4. LIST OF AWS CLOUD PENTESTING AND SECURTY CTFS

 | LAB or CTF | Description |
| --- | --- |
| [FLAWS CTF](http://flaws.cloud) | This challenge comprises a series of 6 levels designed to teach some common security mistakes made when using Amazon Web Services (AWS) |
| [FLAWS 2 CTF](http://flaws2.cloud) | This challenge has two paths : Attacker and Defender! In the Attacker path, you’ll exploit your way through misconfigurations in serverless (Lambda) and containers (ECS Fargate). In the Defender path, that target is now viewed as the victim and you’ll work as an incident responder for that same app, understanding how an attack happened. You’ll get access to logs of a previous successful attack. As a Defender you’ll learn the power of jq in analyzing logs, and instructions on how to set up Athena in your own environment. |
| [AWSGoat](https://github.com/ine-labs/AWSGoat) | AWSGoat : A Damn Vulnerable AWS Infrastructure |
| [Big IAM Challenge by Wiz](https://bigiamchallenge.com/challenge/1)  | Test Your Cloud Security Skills |
| [iam-vulnerable](https://github.com/BishopFox/iam-vulnerable) | Use Terraform to create your own vulnerable by design AWS IAM privilege escalation playground |
| [cloudhuntinggames](https://www.cloudhuntinggames.com) | The Cloud Hunting Games CTF: Test Your Cloud IR Skills |
| [cloudbreachsim](https://cloudbreachsim.senayakut.com/) | Your interactive cloud security simulation environment. Select a scenario to visualize attack paths, understand vulnerabilities, and learn how to defend your infrastructure. |

   
