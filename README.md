## AWS Cloud Security Assessment
Technical notes, audit & pentest methodology, list of tools, scripts and commands that are useful for assessing the security posture of an AWS Cloud environment.<br>
>The output files included here are the results of tools, scripts and commands that I ran against my testing AWS environment and some free CTF/Lab AWS environment. 

### Table of contents 


#### I. AWS Cloud Essentials (Definition, List of commands, ...)

- List of usefull AWS CLI commands
 
| Amazon Web Service | ACTION | COMMAND | 
| :-----: | :-----: | :-----: | 
| IAM | Get basic account info | aws sts get-caller-identity | 
| IAM | List IAM users | aws iam list-users | 
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
  
  

#### II. AWS Security Assessement

- Using an AWS account run tools and scripts that will extact and analyse the AWS environement looking for potential security misconfiguration, and other security issues in key services such as:
  - IAM
  - EC2
  - Lambda
  - ...  

- CloudSploit by Aqua - Cloud Security Scans - [CloudSploit](https://github.com/aquasecurity/cloudsploit) - It is an open-source project designed to allow detection of security risks in cloud infrastructure accounts, including: Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), Oracle Cloud Infrastructure (OCI), and GitHub. These scripts are designed to return a series of potential misconfigurations and security risks.

#### III. AWS Penetration Testing




List S3 buckets accessible to an account
