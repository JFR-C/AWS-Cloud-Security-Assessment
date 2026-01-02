## AWS Cloud Security Assessment
Technical notes, audit & pentest methodology, list of tools, scripts and commands that are useful for assessing the security posture of an AWS Cloud environment.<br>
>The output files included here are the results of tools, scripts and commands that I ran against my testing AWS environment and some free CTF/Lab AWS environment. 

### Table of contents 


#### I. AWS Cloud Essentials (Definition, List of commands, ...)

- List of usefull AWS CLI commands
 
| Action | Command | 
| :-----: | :-----: | 
| Get basic account info | aws iam list-users | 
| List IAM Users | aws iam list-users | 
| List IAM Roles | aws iam list-roles | 
| List S3 buckets accessible to an account | aws s3 ls | 
| List EC2 instances (VM) | aws ec2 describe-instances | 


#### II. AWS Security Assessement

- Using an AWS account run tools and scripts that will extact and analyse the AWS environement looking for potential security misconfiguration, and other security issues in key services such as:
  - IAM
  - EC2
  - Lambda
  - ...  

- CloudSploit by Aqua - Cloud Security Scans - [CloudSploit](https://github.com/aquasecurity/cloudsploit) - It is an open-source project designed to allow detection of security risks in cloud infrastructure accounts, including: Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), Oracle Cloud Infrastructure (OCI), and GitHub. These scripts are designed to return a series of potential misconfigurations and security risks.

#### III. AWS Penetration Testing




List S3 buckets accessible to an account
