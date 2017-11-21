# AWS Build Standards
| Code | Group | Standard |
| ---- | ---- | --- |
| GEN01 | General | All resources must be built within the EU Ireland region - eu-west-1. |
| GEN02 | General | Do not build any resources upon the default AWS network resources i.e. default VPCs, default Route Tables, default Subnets, default Network Access Control Lists, default Internet Gateways or default VPC Security Groups. As these resources are not managed by Terraform (which makes them hard to discover) and because the security groups by default provide open (insecure) access. |
| GEN03 | General | It is everyone\'s responsibility to help manage costs within the cloud. Where redundant resources are identified, flag their existence to the architects within the System Team who will take actions to remove them. |
| GEN04 | General | The Sandpit account is for proof of concepts / testing and trialing new software and build patterns. Users of this environment should remove resources once they are no longer required, and use it with the knowledge that resources may be remove at any time as part of automated housekeeping scripts. |
| GEN05 | General | All resources must comply with the [AWS Naming Standards](AWSNamingStandards.md). |
| GEN06 | General | NEVER copy a .terraform folder or a Terraform state file under this location between directory structures as this will risk cross contaminating environments. |
| GEN07 | General | Do not make manual changes via the AWS Console (Web UI), manual API changes or manual CLI changes - all of which could invalidate Terraform state files. |
| EIN01 | EC2 - Instances | All EC2 instances built within a single development, test or production environment must use the same EC2 key pair, these must match the [AWS Naming Standards](AWSNamingStandards.md) e,g. kp-prp1-np. |
| EIN02 | EC2 - Instances | All EC2 instances that are built as pet hosts (i.e. those that need to be individually named) must be given a Name tag [following this pattern](TBC.md) \<hostname\>-\<environment zone\>-\<environment\>. For example, document management would consist of two EC2 instances with the following Name tags docfm01-dazo-prp1 and docfm02-dazo-prp1. See [AWS DNS](TBC.md) page. |
| EIN03 | EC2 - Instances | All EC2 instances must have CloudWatch monitoring enabled. |
| EIN04 | EC2 - Instances | All EC2 instances must use current generation instances types i.e. C4, M4, R4, T2 depending upon the requirements of that service. |
| EIN05 | EC2 - Instances | All EC2 instances must use general purpose solid state drives (gp2), unless there are specific requirements of the service that change this. |
| EIN06 | EC2 - Instances | All EC2 instances must be given a hostname in user data and that host name must be registered within Consul DNS. This must match the \"node\" name on the [AWS DNS](TBC.md) page. |
| EIN07 | EC2 - Instances | All EC2 instances must have an IAM role and IAM instance profile attached, to enable the profile to be used to provide access from an EC2 instance to other AWS resources e.g. an S3 bucket, at a later date. These must match the IAM role and IAM instance profile naming standards on the [AWS Naming Standards](AWSNamingStandards.md) page. |
| EIN08 | EC2 - Instances | All EC2 instances must only be assigned a public IP address if they are used as an ingress service within an Internet Boundary Control. |
| EIN09 | EC2 - Instances | All EC2 instances that host database clusters should mount specific EBS volumes to ensure that the data can be persisted (unmounted / remounted) in parallel with terminating the instance. See [Build Pattern F](TBC.md) for more information. |
| EIN10 | EC2 - Instances | All EC2 instances user data should not contain secrets in the clear, all secrets should be obtained from Vault using a Consul template. |
| EIN11 | EC2 - Instances | All EC2 instances must have their root-volume encrypted. In addition, and mounted EBS volumes must also be encrypted. |
| ELB01 | EC2 - Elastic Load Balancer | All load balancers must have their idle timeout configured to be greater than the average duration of a service call through the load balancer to ensure that the call to the service does not get terminated by the load balancer. |
| ELB02 | EC2 - Elastic Load Balancer | All load balancers must have Access Logging enabled, and this should log to the respective S3 bucket within the storage account. (see ELB03) |
| ELB03 | EC2 - Elastic Load Balancer | The location of the ELB Access Logs must comply with standards: [AWS Common S3 Buckets\#ELBLogs](TBC.md) |
| ESG01 | EC2 - Security Groups | Never create any ingress rules that are open to the internet unless the rules are for an ingress service within an Internet Boundary Control. |
| ESG02 | EC2 - Security Groups | All security groups must have a single open egress rule i.e. protocol = -1 (All), from port = 0, to port = 0 and cidr block = 0.0.0.0/0. |
| ESG03 | EC2 - Security Groups | All security group ingress rules (except rules that manage external ingress into a boundary control from outside) must reference a source security group associated to the resource calling the service, as opposed to using a CIDR block e.g. an ingress rule on an EC2 instance would reference the security group of the ELB in front of the EC2 instance as a source security group. |
| ESG04 | EC2 - Security Groups | All EC2 instances must have at least 3 security groups associated to them: |
| | | - A VPC security group - e.g. sg-env1 - to allow SSH and key operations tooling e.g. Zabbix / Puppet to communicate with all instances within a VPC | |
| | | - An environment zone security group - e.g. sg-dazo-prp1 - to hold all rules that enable services to communicate with all instances within a zone | |
| | | - Service security group - e.g. sg-brplandingdb-prp1 - to hold all rules that enable communication with that service | |
| | | N.B. A service security group must not contain SSH on port 22, as it must be enabled by the VPC security group. | |
| ESG05 | EC2 - Security Groups | All security group \"Group Ids\" must be given humanly readable names - the same as their \"Name\" tags. This will enable debugging security groups much simpler, as you can then see which security group is related as opposed to which security group id is related. |
| ESG06 | EC2 - Security Groups | An ELB should only contain security group rules for the ports it listens to. Therefore, there it is incorrect to add VPC and Zone level security groups to an ELB. |
| S301 | S3 | No S3 buckets should have policies that open them to the internet unless explicitly agreed with the Infrastructure or Security lead | \ |
| S302 | S3 | All calls to S3 are encrypted in transit as the AWS API is TLS. All data stored in S3 buckets must be encrypted at rest, there are a couple of ways of achieving this, either encrypting the file on the client and sending an encrypted object to S3, or setting server side encryption on the put command to S3 (and a policy that enforces this on the bucket). | \ |
| RDS | Relational Database Service | All RDS instances that hold business data must adhere to the programmes backup and retention policies. Currently this is 14 days retention for non-production, 30 days retention for production. See BD policy on the following page - [General backup and logging policies](TBC.md) |
| TAG01 | Tagging | All resources must have a \"Name\" tag, for EC2 instances see standard EC02, and [AWS Naming Standards](AWSNamingStandards.md) for all other resource types. |
| TAG02 | Tagging | All resources must have a \"CostCentre\" tag, this must be set to the project team code of the team that is responsible for those resources, see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG03 | Tagging | All resources must have an \"Environment\" tag, this must be set to the name of the environment that the resource belongs to (in lowercase) e.g. prp1, see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG04 | Tagging | All resources must have an \"EnvironmentZone\" tag, this must be set to the name of the zone (within an environment) that the resource belongs to (in lowercase) e.g. dazo, see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG05 | Tagging | All resources must have an \"Owner\" tag, this must always be set to \"Terraform\" when infrastructure is built using Terraform, see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG06 | Tagging | All resources must have an \"Region\" tag, this must always be set to \"eu-west-1\", see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG07 | Tagging | All resources must have a \"Product\" tag, this must always be set to *\<a value that is sensitive\>*, see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG08 | Tagging | All resources must have a \"CreatedBy\" tag, set to the id of the person creating / updating the resource, this must be all lowercase and comprise of the persons surname then initial e.g. bloggsj, see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TAG09 | Tagging | All EC2 resources must have a \"Service\" tag, this must be set to the name of the service the host or ELB is providing (not the hostname) |
| TAG10 | Tagging | All EC2 resources must have a \"Role\" tag, this must always be set to one of the role within the [VM Role Identifiers](/display/EN/VM+Role+Identifiers) table. |
| TAG11 | Tagging | All ELB resources must have a \"HAPattern\" tag applied see [AWS Tagging Standards](AWSTaggingStandards.md). |
| TER01 | Terraform | AMIs should not be created or copied from within a Terraform script. They should be created at the end of the AMI workflow within each of the IPT resource accounts (NP, PR, OPS, SAN and NPA). |
| TER02 | Terraform | All IPT services should be built in accordance to the [AWS Build Patterns](/display/EN/AWS+Build+Patterns). Every effort should be made to reuse / extend with provider scripts these patterns |
| TER03 | Terraform | All Terraform code must be stored in Bitbucket under the \"DTP Terraform\" project and then within either the respective locations for holding Terraform modules or provider scripts - see [Module and Provider Structure](/display/EN/Module+and+Provider+Structure). |
| TER04 | Terraform | All Terraform state must be held remotely to facilitate sharing across the team - see [Terraform - State File Management](TBC.md). The location of the state file in S3 must mirror the directory location of the Terraform code it corresponds to. |
| TER05 | Terraform | Use lists instead of comma separated strings, actively refactor code to align to this standard. |
| TER06 | Terraform | Actively use the AWS native service modules within IPT service modules unless they do not fully meet your need, in which case Terraform resources should be declared inline, as long as naming and tagging standards are still honored. |
| TST01 | Test - Test Target Security Groups | EC2 instances and RDS databases that are a test target, i.e. will have tests executed against them on on them must be in a Test Target Security Group (`sgtt`). See [Test Tooling - TTZO](TBC.md) and [AWS Migration](TBC.md) or the Instance Spreadsheet for more detail. |
| VPC01 | Virtual Private Cloud | All VPCs will use class \'A\' IPv4 IP addresses i.e. 10.x.y.z. |
| VPC02 | Virtual Private Cloud | All VPCs that contain developments, test and production environments will use the full /16 subnet range. Boundary control VPCs will use smaller subnet ranges. |
| VPC03 | Virtual Private Cloud - Subnets | A Subnet must be created in each availability zones within a region. A Subnet group represents a collection of subnets across availability zones that are to be used within the same network tier classification (e.g. public, private, data). |
| VPC04 | Virtual Private Cloud - Route Table | A single Route Table must be created to route traffic for each subnet group. |
| VPC05 | Virtual Private Cloud - Network Access Control List | A single Network Access Control List must be created to restrict traffic for each subnet group. |
| VPC06 | Virtual Private Cloud - Internet Gateway | An Internet Gateway must only be created within a public subnet group within an Internet Boundary Control. |
| VPC07 | Virtual Private Cloud - NAT Gateway | A NAT Gateway must only be created within a public subnet group within an Internet Boundary Control. |
| VPC08 | Virtual Private Cloud - Security Group | A single security group for all nodes in the VPC must be created. See [AWS Naming Standards](AWSNamingStandards.md) for name. |
| ENV01 | Environment - Security Group | **No** security group should exist for the environment; only VPC, environment zone and service. See ESG04. |
 