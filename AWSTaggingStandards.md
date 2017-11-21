Tags enable us to c ategorize AWS resources in different ways, for example, by purpose, owner, or environment. Each tag consists of a key and an optional value so we can quickly identify a specific resource based on the tags assigned. It is important that we deploy a consistent set of tags across all resources to meet reporting and billing requirements. To meet this requirement we have the following defined standards.

#Common Tags

The following tags should be applied to all AWS resources that support tagging:

| Tag Key | Tag Example Values | Description |
| --- | --- | ---|
| Name | Refer to [AWS Naming Standards](AWSNamingStandards.md) | The Name tag should comply with the IPT AWS Naming Standards |
| Environment | `prp1`, `prd1`| The name of the environment that the resource belongs to. This should be the environment name in shorthand form, in lowercase and postfixed with a number. |
| EnvironmentZone | dazo | The name of the zone (within an environment) that the resource belongs to, in lowercase. |
| Owner | Terraform | The owner responsible for managing the resource. |
| Region | eu-west-2 | The region that the resource belongs to. |
| Product | (Value omitted as it is sensitive) | See architects for this value. |

#EC2 Tags

The following tags must be applied to EC2 resources (as well as the common tags)

| Tag Key | Tag Example Values | Description |
| --- | --- | ---|
| Name | `???` | The name of the resource e.g. Instance FQDN, Network name, NIC FQDN, VIP FQDN etc. |
| Role | `dbs`, `dbp` | Used to determine the role / service provided by the host. See - VM Role Identifiers|
| Service | `???`, `storm`, `kafka` | The name of the service |


#EBS Volume Tags

| Tag Key | Tag Example Values | Description |
| --- | --- | ---|
| Name | `???` | The EC2 instance name that the volume belongs to |
| Service | `??`, `storm`, `kafka` | The name of the service |
| Snapshot | `0 0 9 1/1 \* ? \*` | Optional value to indicate snapshot schedule |
| Mount | `root\`, `/mnt/data | The mount point name of the volume |

#ELB Tags

The following tags should be applied to ELB resources (as well as the common tags) where applicable - see Description for details.

| Tag Key | Tag Example Values | Description |
| --- | --- | ---|
| HAPattern | Only values from the following list should be used: `ActivePassive`| The HA pattern in use by the services that the ELB is fronting. Note - ActiveActive is the assumed default and the tag should be omitted altogether in this case.|

#Certificate Tags

| Tag Key | Tag Example Values | Description |
| --- | --- | ---|
| Name |???|The domain name of the certificate|
| expiry |2018-10-16 |The expiry date for the certificate|
| wildcard |true |If it is a wildcard certificate|


#Optional Tags

The following tags should be applied to all AWS resources where applicable:

| Tag Key | Tag Example Values | Description |
| --- | --- | ---|
| Schedule| `0 0 9 1/1 \* ? \*` | Resource schedule in CRON format, can be used to schedule resources using automation tools|
| Expiry |`01/09/2016 15:00`|Date and time of resource expiry, can be used to remove old resources using automation tools|
| Availability Zone |`eu-west-1a`| Use where resources are allocated to a specific availability zone|

#Tag Notes

The following basic restrictions apply to tags:
- Tags are key-value pairs
- Maximum number of tags per resource=50
- Maximum key length=127 Unicode characters in UTF-8
- Maximum value length=255 Unicode characters in UTF-8
- Tag keys and values are case sensitive.
- aws: prefix in your tag names or values because it is reserved for AWS use. You can\'t edit or delete tag names or values with this prefix. Tags with this prefix do not count against your tags per resource limit.
- If your tagging schema will be used across multiple services and resources, remember that other services may have restrictions on allowed characters. Generally allowed characters are: letters, spaces, and numbers representable in UTF-8, plus the following special characters: + - = . \_ : / @.

Additional tagging information can be found at https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html

#Implementation

Tags will be implemented within the respective AWS service modules. Where a module supports a map of tags being passed in, this will be preserved to allow additional tags to be added to a base set of standard tags (outlined on this page) which are either hard coded or passed in as variables.

For example, each module that supports tags would contain the following Terraform code to set its tags attribute:

```hcl-terraform
# Add a map of standards tags for this resource to a map of tags passed into the module

tags = "${merge(map(
 "Environment", "${var.environment}",
 "Name", "${var.name}",
 "EnvironmentZone", "${var.environment_zone}",
 "Owner", "${var.owner}",
 "Region", "${var.region}",
 ),"${var.tags}"
)}"
```

Where tags is a variable representing a map of any additional tags passed into the module that the caller wishes to set, merged with a hard coded map of IPT specific tags. A number of these tags will be defaulted within a variables file:

```hcl-terraform
variable "environment" {
 description = "(Required) The name of the environment that the resource belongs to - used for tagging."
}
variable "environment_zone" {
 description = "(Required) The name of the zone (within an environment) that the resource belongs to - used for tagging."
}
variable "owner" {
 description = "(Optional) The owner responsible for managing the resource - used for tagging, and defaulted to Terraform."
 default = "Terraform"
}
variable "region" {
 description = "(Required) The region that the resource belongs to - used for tagging."
}
variable "tags" {
 description = "(Optional) A map of additional tags to associate with the resource."
 type = "map"
 default = {}
}
```

**N.B The list of variables above is extended for each different type of resource in accordance with the tagging standards outlined for the respective resource on this page.**