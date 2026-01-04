## AWS Cloud Security Assessment
Audit & pentest methodology, technical notes, list of tools, scripts and commands that are useful for assessing the security posture of an AWS Cloud environment.<br>

### Table of contents 
- I. AWS Cloud Essentials - Reminder
  - 1.1. Introduction
  - 1.2. Main AWS services across IaaS, PaaS, and SaaS
  - 1.3. AWS Security Best Practices
  - 1.4. IAM Users vs. IAM Roles — Key Differences
  - 1.5. Cloud‑Native Application Protection Platforms (CNAPP)
  - 1.6. How to use the AWS CLI
  - 1.7. List of usefull AWS CLI commands
  - 1.8. Basic tutorial to create a Kali Linux EC2 VM on AWS

- II. AWS Security Audit
  - 2.1. Run AWS security scans with audit tools to identify security misconfiguration
    - CloudSuite
    - Prowler
    - CloudSploit
  - 2.2. Check for known privesc attack vectors in AWS

- III. AWS Penetration Testing
  - 3.1. AWS Pentest scope and rules of engagement
  - 3.2. Black-box penetration test (we start with no account)
  - 3.3. Grey-box penetration test (we start with 1 low-privileged Windows account)

----------------

### I. AWS Cloud Essentials 

#### 1.1. Introduction

- AWS provides cloud computing i.e. on-demand delivery of technology services through the Internet with pay-as-you-go pricing.
The AWS Cloud encompasses a broad set of global cloud-based products that includes compute, storage, databases, analytics, networking, mobile, developer tools, management tools, IoT, security, and enterprise applications: on-demand, available in seconds, with pay-as-you-go pricing.

- AWS delivers cloud capabilities through the three service models: Software as a Service (SaaS), Platform as a Service (PaaS), and Infrastructure as a Service (IaaS). 
These models differ in how much control the customer retains versus how much AWS manages. Understanding these layers helps teams choose the right level of abstraction for their applications, operations, and security responsibilities.

#### 1.2. Main AWS services across IaaS, PaaS, and SaaS

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


#### 1.3. AWS Security Best Practices

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

#### 1.4. IAM Users vs. IAM Roles — Key Differences
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


#### 1.5. Cloud‑Native Application Protection Platforms (CNAPP)

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


#### 1.6. How to use the AWS CLI

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
  $ ls ~/.aws/credentials
  
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
    </i> Useful for: Temporary sessions, CI/CD pipelines, avoiding writing credentials to disk. </i>
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
  

```bash
# First of all, set your profile
aws configure --profile test 
set profile=test # Just for convenience

# ~/.aws/credentials
[default]
aws_access_key_id = XXX
aws_secret_access_key = XXXX

export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_DEFAULT_REGION=

# If we can steal AWS credentials, add to your configuration
aws configure --profile stolen
# Open ~/.aws/credentials
# Under the [stolen] section add aws_session_token and add the discovered token value here
aws sts get-caller-identity --profile stolen

# Check valid
aws sts get-caller-identity
aws sdb list-domains --region us-east-1

# SSH into created instance:
ssh -i ".ssh/key.pem" <user>@<instance-ip>
sudo mount /dev/xvdb1 /mnt
cat /mnt/home/ubuntu/setupNginx.sh
```

#### 1.7. List of usefull AWS CLI commands
 
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
| Kubernetes (EKS) | List EKS clusters | aws eks list-clusters --region region | 
| Kubernetes (EKS) | Update kubeconfig | aws eks update-kubeconfig --name cluster-name --region region | 


#### 1.8. Basic tutorial to create a Kali Linux EC2 VM on AWS

- Step 1 - Log in to the AWS Console and go to EC2 → Instances → Launch Instance.
- Step 3 - Name your instance (e.g., Kali-Linux-VM).
- Step 4 - Under Application and OS Images (AMI), click Browse more AMIs, search for “Kali Linux”, and select the official Kali AMI from AWS Marketplace.
- Step 5 - When prompted, accept the subscription (Kali AMIs are free, you only pay for compute).
- Step 6 - Under Instance type, choose the option/config you want. Below are the cheapest options:
  - t2.small (Specs: 1 vCPU, 2 GiB RAM, Burstable CPU)
  - t3.micro (Specs: 2 vCPUs, 1 GiB RAM, Better performance than t2.micro)
  - t2.micro (Specs: 1 vCPU, 1 GiB RAM, Burstable CPU, Free‑tier eligible)
- Step 7 - Under Key pair, create a new SSH key pair or select an existing one.
- Step 8 - Under Network settings, allow SSH (port 22) from your public IP only (recommended for security).
- Step 9 - Keep storage at the default (usually 8–10 GB gp2), which is the cheapest option.
- Step 10 - Click Launch Instance, wait for it to start, then connect via SSH:
  ```
  ssh -i yourkey.pem ec2-user@<public-ip>
  ```

----------------

### II. AWS SECURITY AUDIT

#### A. Run AWS security scans with tools like CloudSuite, Prowler and CloudSploit.
- Using an AWS account run tools and scripts that will extract and analyse the AWS environment looking for potential security misconfiguration, and other security issues in key services such as IAM, EC2, Lambda and S3.
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
- Security Audit Tool n°1 - [CLOUDSPLOIT](https://github.com/aquasecurity/cloudsploit) 
  - It is an open-source project designed to allow detection of security risks in cloud infrastructure accounts, including: Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), Oracle Cloud Infrastructure (OCI), and GitHub. These scripts are designed to return a series of potential misconfigurations and security risks.
  ```
  Docker
  ------
  $ git clone https://github.com/aquasecurity/cloudsploit.git
  $ cd cloudsploit
  $ docker build . -t cloudsploit:0.0.1
  $ docker run cloudsploit:0.0.1 -h
  $ docker run -e AWS_ACCESS_KEY_ID=XX -e AWS_SECRET_ACCESS_KEY=YY cloudsploit:0.0.1 --compliance=pci
  ```

- Security Audit Tool n°2 - [SCOUTSUITE](https://github.com/nccgroup/ScoutSuite)  
  - Scout Suite is an open source multi-cloud security-auditing tool, which enables security posture assessment of cloud environments. Using the APIs exposed by cloud providers, Scout Suite gathers configuration data for manual inspection and highlights risk areas. Rather than going through dozens of pages on the web consoles, Scout Suite presents a clear view of the attack surface automatically.
  - Scout Suite was designed by security consultants/auditors. It is meant to provide a point-in-time security-oriented view of the cloud account it was run in. Once the data has been gathered, all usage may be performed offline.
  ```
  Install via Git
  ----------------
  $ git clone https://github.com/nccgroup/ScoutSuite
  $ cd ScoutSuite
  $ virtualenv -p python3 venv
  $ source venv/bin/activate
  $ pip install -r requirements.txt
  
  Run
  ---
  $ python scout.py --help
  ```
  ```
  Install via PIP
  ----------------
  $ virtualenv -p python3 venv
  $ source venv/bin/activate
  $ pip install scoutsuite
  $ scout --help
  
  Run
  ---
  $  python scout.py aws --profile basc -f
  ```

- Security Audit Tool n°3 - [PROWLER](https://github.com/prowler-cloud/prowler)  
  - Prowler is an Open Cloud Security platform trusted by thousands to automate security and compliance in any cloud environment. With hundreds of ready-to-use checks and compliance frameworks, Prowler delivers real-time, customizable monitoring and seamless integrations, making cloud security simple, scalable, and cost-effective for organizations of any size.
  ```
  Install Prowler CLI - Pip package (with Python >3.9.1, <3.13)
  --------------------------------------------------------------
  $ pip install prowler
  
  Run
  ---
  $ prowler -v
  ```
  ```
  Install Prowler CLI from GitHub (Python >3.9.1, <3.13 is required with pip and Poetry)
  ---------------------------------------------------------------------------------------
  $ git clone https://github.com/prowler-cloud/prowler
  $ cd prowler
  $ eval $(poetry env activate)
  $ poetry install
  
  Run
  ---
  $ python prowler-cli.py -v
  ```

#### B. Check for known privesc attack vectors in AWS
  
- Usefull ressources:
  - https://github.com/RhinoSecurityLabs/AWS-IAM-Privilege-Escalation
  - https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/
  - https://bishopfox.com/blog/5-privesc-attack-vectors-in-aws
  - https://bishopfox.com/blog/privilege-escalation-in-aws
  
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
     
- Permissions on Policies
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

- Updating an AssumeRolePolicy
  - The 'iam:UpdateAssumeRolePolicy' permission provides an entity with the ability to change a role’s AssumeRolePolicy. So, if a user is not included in this policy but they have the ability to update it, they can just add their user account to the AssumeRolePolicy and consequently assume the role. Depending on the permissions associated with the role, this could provide a path for privilege escalation.
   
- 'iam:PassRole:*'
  - The 'iam:PassRole' permission allows a user to pass a role to an AWS entity. Passing roles is a crucial element in AWS permissions and resource management. For instance, when deploying an application to AWS, the application may need to perform certain actions on the back end, such as accessing databases or running Lambda functions. In order to allow the application to access these AWS services, you pass a role to it that contains the necessary permissions.
  - Problems with the 'iam:PassRole' permission occur when there is no defined role or set of roles that the principal is allowed to pass. For instance, policies that allow the use of iam:PassRole on wildcard resources (iam:PassRole:*). In effect, this allows the user to pass any role that exists in the environment, including any existing privileged roles. This may open up the possibility for a user to pass a privileged role to an AWS resource or service, and then use that resource or service to perform privileged actions.

- Privilege Escalation Using AWS Services
  - It’s possible in some cases to escalate privileges using an AWS service. Lambda and Glue are two services that may allow users to execute commands that they should not be able to execute, and CloudFormation and Data Pipeline may be used for privesc as well if the user also has the iam:PassRole permission.
  - For Lambda, just having the permission to modify functions can lead to privilege escalation because a user can update a Lambda function to perform privileged actions on their behalf. The risk here depends on the permissions with which the Lambda function operates.
  - Similarly, the permission to modify a Glue development endpoint can lead to privilege escalation because a user might, for example, change the SSH key associated with an endpoint so they can then log into the system and use it to perform privileged actions. Again, the risk depends on the permissions assigned to the endpoint.
   

----------------

### III. AWS PENETRATION TESTING

https://pentestbook.six2dez.com/enumeration/cloud/aws
https://www.hackerone.com/knowledge-center/penetration-testing-aws-practical-guide

#### A. Black-box penetration test (we start with no account)
#### B. Grey-box penetration test (we start with 1 low-privileged Windows account)


