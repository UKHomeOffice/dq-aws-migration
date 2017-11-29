To ensure consistency and visibility within AWS all resources should be tagged (where possible) using the following naming standards.

It should be noted that a number of AWS resources cannot be renamed after creation so it is important to ensure the resource adheres to the standard on creation.

For the correct naming values, refer to the **AWS-DQ-Network-Routing** spreadsheet in Dropbox.

The following variables are referenced in the naming standards:

| Naming Variable | Description | Example |
| ---- | ------ | ------ |
| `{env}` | environment name | tlg1, env1 
| `{env-group}` | environment group | dc1 |
| `{subnet-type}` | private or public subnet type | private, public |
| `{service}` | name of the service | brplandingdb, storm, kafka |
| `{env-zone}` | name of the zone | przo, dazo |
| `{#}` | unique number where multiple resources exist | 1,2,3 |
| `{az}` | availability zone number | 1a, 1b, 1c |


Core AWS Services Naming
------------------------

| Component | Naming Standard | Examples | Comments 
| ---- | ------ | ------ | ----|
| S3 Buckets | **ho.ipt**.{account name}.{service name} | ??? | It is highly recommended that all bucket names comply with DNS naming conventions. Please see https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html for naming restrictions. See [AWS Common S3 Buckets](/display/EN/AWS+Common+S3+Buckets) |
| VPC | **vpc-**{env group} |  ???  |  
 | | | ??? | |
| VPC Subnet | **sn-**{subnet-type}{\#}**-**{env}**-**{az} |  ???  | A unique number will be appended to the subnet-type starting from 1 in case of multiple public or private subnets |
 | | |  ???  | See [AWS Infrastructure Design\#Subnets](/display/EN/AWS+Infrastructure+Design#AWSInfrastructureDesign-Subnets). |
| Route Table | **rt-**{subnet-type}{\#}**-**{env} |  ???  | A route table will be defined for each subnet and span availability zones |
| Internet Gateway | **igw-**{env group} |  ???  | One Internet Gateway will be defined in each VPC |
| DHCP Option Set | **dhcp-**{env group}**-**{\#} |  ???  | A unique number will be appended in case of multiple DHCP option sets |
| VPC Peering Connection | **peer-**{env group}**-**{account}**-**{env group}**-**{account} |  ???  | A peering connection will be created between a source and a target VPC that need to communicate with each other. |
 | | where | | N.B. AWS will only support tagging of the source peer |
 | | *peer-\[source env\]-\[source acc\]-\[dest env\]-\[dest acc\]* | | |
| Security Group - Name Tag | sg-{service}**-**{env} | **Service SG**  Three types of security groups will be created, service based, zone based and VPC wide based. |
| | | ??? | |
| | ELB SG**  | Service SG\'s are used for role specific rules that are specific to the function of the instance, Zone SG\'s are the zone specific security groups and the VPC SG security group is VPC wide. |
| | sgelb-**{service}**-**{env} | **ELB SG**  |
| | | sgelb-soacdp-prp1 | See [AWS Security Groups and Network Access Control Lists](/display/EN/AWS+Security+Groups+and+Network+Access+Control+Lists). |
| | EFS SG  | |
| | **sgefs**-{service}-{env} | **EFS SG  To ensure that environments remain segrated within a VPC, separate service security groups will be created for each environment. |
| | | ??? | |
| | RDS SG  | |
| | **sgrds**-{service}-{env} | **RDS SG  |
| | | ??? | |
| | Lambda SG  | |
| | **sglam**-{service}-{env} | **Zone Grouping SG  |
| | | ??? |
| | Zone Grouping SG   ???   |
| | **sg-**{env zone}**-**{env} | ??? | |
 | | VPC SG  **VPC SG  |
| | **sg-**{env group} | ???  |
| | | ??? | |
| | Test target SG  | |
| | **sgtt-**{target group}-{env} | **Test target SG  |
| | | **???  |
| Security Group - Group Name | **sgrp-**{service/env zone/target group}-{env} | sgrp-soacdp-prp1 |  
 | | ELB | | |
 | | **sgrpelb**-{service}-{env} |  ???  | |
 | | **sgrpefs**-{service}-{env} |  | |
 | | **sgrprds**-{service}-{env} |  ???  | |
 | | **sgrplam**-{service}-{env} |  | |
 | | VPC |  | |
 | | **sgrp**-{env group}   ???  | |
 | | | ??? | |
| Network Access Control Lists | **acl**-{subnet-type}{\#}-{env group}-{az} |  ???  | A Network Access Control List (NACL) will be defined for each subnet. |
 | | | ??? | See AWS Security Groups and Network Access Control Lists. |
| VPN Connection | **vpn-**{env}**-**{account}**-**{env}**-**{account}   ???  | A VPN connection will be created between a source and a target AWS account (or external account) that need to communicate with each other. |
 | | where | | |
 | | *vpn-\[source env\]-\[source acc\]-\[dest env\]-\[dest acc\]*  | |
| RDS - DB Instance Name | **rds-**{db name}**-**{env} | ??? | RDS instance names will refer to the service within an environment zone, within an environment. Region and account are implied, as RDS instance names are built within a region, within an account. |
| RDS - Subnet Group | **rds-sng-{service}-**{env} | ??? | RDS uses subnet groups for multi-az deployments. Each subnet group for RDS would correspod to the relevant service |
| RDS - Parameter Group | **rds-pgrp**{\#}**-**{service}**-**{env} |  ???  | You manage your DB engine configuration through the use of parameters in a DB parameter group. DB parameter groups act as a *container* for engine configuration values that are applied to one or more DB instances |
| RDS - Final Snapshot | **rds**-{service}-{env}-final |    
| DNS | \<service\>**-**\<env zone\>**-**\<env\>**.np.iptho.co.uk** |  ???  | DNS names will refer to the service within an environment zone, within an environment. |
| EFS Name | **efs-**{service}**-**{env zone}**-**{env} |  ???  | Unique name based on the service, prefixed by efs- with the zone and environment identified. |
 | | | | See \"AWS EFS MountPoints\" section below for the mountpoint foldername standards. |
| ELB Name | **elb-**{service}**-**{env zone}**-**{env}   ???  | Unique name based on the service, prefixed by elb- with the zone and environment identified |
| Target group | **tg-**\[{role}-\]{service}-{env zone}-{env} |  ???  | Unique name based on the service, prefixed by tg- with the zone and environment identified |
 | | | | A role should be included if more than one target group is defined for a single load balancer |
| ASG Name | **asg-**{service}**-**{env zone}**-**{env} | asg-jump-pibc-tlg1 | Unique name based on the service, prefixed by asg- with the zone and environment identified |
| Placement Group | **placegrp**{\#}**-**{service}**-**{env} | placegrp1-soacdp-tlg1 | Placement groups are logical grouping of instances within a single Availability Zone (AZ) that enables applications to participate in a low-latency, 10 Gbps network. You create a placement group first, and then you can launch instances in the placement group |
| AMI Name | **ami-**{region}-{OS}-{OS version}-{operating system}-{bake level}-{hardening level}-{service}-{version} | ami-eu-west-one-centos-7.0-fb-hd-puppet-1.1 | Base operating system & version, FB (Fully Baked) or PB (Partial Baked), HD (Hardened) or NH (Not Hardened), service, service version |
 | | | ami-eu-west-one-centos-7.0-pb-hd-base-0.17 | AMIs that are used as a base for other AMIs should have the service name \"base\". |
| AMI Snapshot Name | **snap**ami-****{region}-{OS}-{OS version}-{operating system}-{bake level}-{hardening level}-{service}-{version} | snapami-eu-west-one-centos-7.0-fb-hd-puppet-1.1 | Base operating system & version, FB (Fully Baked) or PB (Partial Baked), HD (Hardened) or NH (Not Hardened), service, service version |
 | | | snapami-eu-west-one-centos-7.0-pb-hd-base-0.17 | AMIs that are used as a base for other AMIs should have the service name \"base\". |
| EC2 Key Pairs | **kp**-{env}-{account} | kp-prp1-np | ::: {userkey="2c99e4cb47836f100148a225924f005c"} |
| | | | Martin Jordan - can you confirm you are ok with one ec2 key-pair per environment - can you define a policy for rotation and how these should be stored for us please |
 | | | | One EC2 key pair per environment, so individual environments can be revoked, rotated regularly in accordance with security policies |
| IAM KMS Key Alias | **kms**- |    
| SQS Queue Name | **sqs**-{business / technical service}-{env} | sqs-containerhosta-prp1 | Queue names must be 1-80 characters in length and be composed of alphanumeric characters, hyphens (-), and underscores (\_). |
 | | | | Queues will be used for business or technical services within environments, e.g. a rancher cluster notifying the rancher server it is scaling up or down to update the number of hosts within that cluster within the rancher server. |
| SNS Topic | **sns**-{topic}\[-{env zone}\[-{env}\]\] | sns-state-monitor | SNS topic names should reflect the purpose or function of the topic. Topics are generally account scoped (or global), however if they are only to be used at VPC or environment scope, these should be added to the name |
 | | | ??? | |
| Lambda function | **lambda**-{name}\[-{env zone}\[-{env}\]\] | lambda-sg-monitor | Lambda function names should reflect their purpose or function. Lambda functions are generally account scoped (or global), however if they are only to be used at VPC or environment scope, these should be added to the name |
| Launch Configuration Name | **lc-**{service}**-**{env zone}**-**{env} | lc-jenkins-dazo-prp1 | Launch configurations are used for autoscaling groups and cannot be updated after creation. |
| Redshift Name | redshift-{service}-{env zone} | redshift-mi-dazo | Unique name for \${var.name} when using Redshift Module |
| Elasticache | **ec**-{service}-{env} | ec-logaggregation-tlg1 | One per account within the shared tooling network |
| Elasticache - Subnet Group | **ec-sng**-{service}-{env} | ec-sng-logaggregation-tlg1 |  
| Elasticache - Parameter Group | **ec-pgrp**{\#}**-**{service}**-**{env} | ec-pgrp1-logaggregation-tlg1 |  
| Elasticache - snapshot name | **ec**-{service}-{env} | ec-logaggregation-tlg1-\<timestamp\> |  
| SES configuration set | **sesconf-**{service}-{env/account} | sesconf-notify-prp1 |  

# AWS IAM Naming

| Component | Naming Standard | Examples | Comments|
| --- | --- | ---| ---|
| IAM Instance Profile | **iampolicy-**{service}**-**{env} |N/A | Instance Profiles that can be used with IAM Roles for EC2 Instances|
| IAM Managed Policy | N/A | N/A | Managed policies that can be used to apply permissions to IAM users, groups, and roles cannot be named, the Ref return value should be used to address the resource after creation |
| IAM Policy | TBA | TBA | Associates an IAM policy with IAM users, roles, or groups |
| IAM Role |**iamrole-**{service}**-**{env} |??? | Creates an IAM role. Use an IAM role to enable applications running on an EC2 instance to securely access your AWS resources|
| IAM User |TBA |TBA |Resource creates an IAM user |
| IAM service User |**iamuser-**{service)-{environment/account} |iamuser-payments-np |Some services (e.g. SES) requires an IAM user. Ensure these users only have the minimum roles required to function associated with them|
| IAM Group |TBA |TBA |Resource creates an IAM group|
| IAM Server Certificate |? |? |?|

#AWS EFS MountPoints (Folder Name)
| Component |Naming Standard |Examples |Comments |
| --- | --- | ---| ---|
| EFS Mountpoint |/efs/{service}\# |/efs/goldengate |The mount folder for the EFS targets is standardized as /efs/{service}\#. When standardized, it\'s easier to identify the folder /ebs/ and identify the mounted efs targets on the EC2 instance.|
