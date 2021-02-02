<!-- markdownlint-disable -->
# terraform-aws-ec2-instance-group [![Latest Release](https://img.shields.io/github/release/cloudposse/terraform-aws-ec2-instance-group.svg)](https://github.com/cloudposse/terraform-aws-ec2-instance-group/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)
<!-- markdownlint-restore -->

[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

<!--




  ** DO NOT EDIT THIS FILE
  **
  ** This file was automatically generated by the `build-harness`.
  ** 1) Make all changes to `README.yaml`
  ** 2) Run `make init` (you only need to do this once)
  ** 3) Run`make readme` to rebuild this file.
  **
  ** (We maintain HUNDREDS of open source projects. This is how we maintain our sanity.)
  **





-->

Terraform Module for providing N general purpose EC2 hosts.

If you only need to provision a single EC2 instance, consider using the [terraform-aws-ec2-instance](https://github.com/cloudposse/terraform-aws-ec2-instance) module instead.

**IMPORTANT** This module by-design does not provision an AutoScaling group. It was designed to provision a discrete number of instances suitable for running stateful services such as databases (e.g. Kafka, Redis, etc).


Included features:
* Automatically create a Security Group
* Option to switch EIP attachment
* CloudWatch monitoring and automatic reboot if instance hangs
* Assume Role capability


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps.
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]


[![Terraform Open Source Modules](https://docs.cloudposse.com/images/terraform-open-source-modules.svg)][terraform_modules]



It's 100% Open Source and licensed under the [APACHE2](LICENSE).







We literally have [*hundreds of terraform modules*][terraform_modules] that are Open Source and well-maintained. Check them out!







## Usage


**IMPORTANT:** We do not pin modules to versions in our examples because of the
difficulty of keeping the versions in the documentation in sync with the latest released versions.
We highly recommend that in your code you pin the version to the exact version you are
using so that your infrastructure remains stable, and update versions in a
systematic way so that they do not catch you by surprise.

Also, because of a bug in the Terraform registry ([hashicorp/terraform#21417](https://github.com/hashicorp/terraform/issues/21417)),
the registry shows many of our inputs as required when in fact they are optional.
The table below correctly indicates which inputs are required.


Note: add `${var.ssh_key_pair}` private key to the `ssh agent`.

Include this repository as a module in your existing terraform code.


### Simple example:

```hcl
module "instance" {
  source = "cloudposse/ec2-instance-group/aws"
  # Cloud Posse recommends pinning every module to a specific version
  # version = "x.x.x"
  namespace                   = "eg"
  stage                       = "prod"
  name                        = "app"
  ami                         = "ami-a4dc46db"
  ami_owner                   = "099720109477"
  ssh_key_pair                = var.ssh_key_pair
  instance_type               = var.instance_type
  vpc_id                      = var.vpc_id
  security_groups             = var.security_groups
  subnet                      = var.subnet
  instance_count              = 3
}
```

### Example with additional volumes and EIP

```hcl
module "kafka_instance" {
  source = "cloudposse/ec2-instance-group/aws"
  # Cloud Posse recommends pinning every module to a specific version
  # version = "x.x.x"
  namespace                   = "eg"
  stage                       = "prod"
  name                        = "app"
  ami                         = "ami-a4dc46db"
  ami_owner                   = "099720109477"
  ssh_key_pair                = var.ssh_key_pair
  vpc_id                      = var.vpc_id
  security_groups             = var.security_groups
  subnet                      = var.subnet
  associate_public_ip_address = true
  additional_ips_count        = 1
  ebs_volume_count            = 2
  allowed_ports               = [22, 80, 443]
  instance_count              = 3
}
```

### Additional complete working example with variations of how to use the module
In /examples directory

This module depends on these modules:

* [terraform-terraform-label](https://github.com/cloudposse/terraform-terraform-label)

It is necessary to run `terraform get` or `terraform init` to download this module.

Now reference the label when creating an instance (for example):

```hcl
resource "aws_ami_from_instance" "example" {
  count              = length(module.instance.*.id)
  name               = "app"
  source_instance_id = element(module.instance.*.id, count.index)
}
```






<!-- markdownlint-disable -->
## Makefile Targets
```text
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen
  lint                                Lint terraform code

```
<!-- markdownlint-restore -->
<!-- markdownlint-disable -->
## Requirements

| Name | Version |
|------|---------|
| terraform | >= 0.13 |
| aws | >= 2.0 |

## Providers

| Name | Version |
|------|---------|
| aws | >= 2.0 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| additional\_ips\_count | Count of additional EIPs | `number` | `0` | no |
| additional\_tag\_map | Additional tags for appending to tags\_as\_list\_of\_maps. Not added to `tags`. | `map(string)` | `{}` | no |
| allowed\_ports | List of allowed ingress ports | `list(number)` | `[]` | no |
| ami | The AMI to use for the instance | `string` | n/a | yes |
| ami\_owner | Owner of the given AMI | `string` | n/a | yes |
| applying\_period | The period in seconds over which the specified statistic is applied | `number` | `60` | no |
| assign\_eip\_address | Assign an Elastic IP address to the instance | `bool` | `true` | no |
| associate\_public\_ip\_address | Associate a public IP address with the instance | `bool` | `false` | no |
| attributes | Additional attributes (e.g. `1`) | `list(string)` | `[]` | no |
| availability\_zone | Availability Zone the instance is launched in. If not set, will be launched in the first AZ of the region | `string` | `""` | no |
| comparison\_operator | The arithmetic operation to use when comparing the specified Statistic and Threshold. Possible values are: GreaterThanOrEqualToThreshold, GreaterThanThreshold, LessThanThreshold, LessThanOrEqualToThreshold | `string` | `"GreaterThanOrEqualToThreshold"` | no |
| context | Single object for setting entire context at once.<br>See description of individual variables for details.<br>Leave string and numeric variables as `null` to use default value.<br>Individual variable settings (non-null) override settings in context object,<br>except for attributes, tags, and additional\_tag\_map, which are merged. | <pre>object({<br>    enabled             = bool<br>    namespace           = string<br>    environment         = string<br>    stage               = string<br>    name                = string<br>    delimiter           = string<br>    attributes          = list(string)<br>    tags                = map(string)<br>    additional_tag_map  = map(string)<br>    regex_replace_chars = string<br>    label_order         = list(string)<br>    id_length_limit     = number<br>    label_key_case      = string<br>    label_value_case    = string<br>  })</pre> | <pre>{<br>  "additional_tag_map": {},<br>  "attributes": [],<br>  "delimiter": null,<br>  "enabled": true,<br>  "environment": null,<br>  "id_length_limit": null,<br>  "label_key_case": null,<br>  "label_order": [],<br>  "label_value_case": null,<br>  "name": null,<br>  "namespace": null,<br>  "regex_replace_chars": null,<br>  "stage": null,<br>  "tags": {}<br>}</pre> | no |
| create\_default\_security\_group | Create default Security Group with only Egress traffic allowed | `bool` | `true` | no |
| default\_alarm\_action | Default alarm action | `string` | `"action/actions/AWS_EC2.InstanceId.Reboot/1.0"` | no |
| delete\_on\_termination | Whether the volume should be destroyed on instance termination | `bool` | `true` | no |
| delimiter | Delimiter to be used between `namespace`, `environment`, `stage`, `name` and `attributes`.<br>Defaults to `-` (hyphen). Set to `""` to use no delimiter at all. | `string` | `null` | no |
| disable\_api\_termination | Enable EC2 Instance Termination Protection | `bool` | `false` | no |
| ebs\_device\_names | Name of the EBS device to mount | `list(string)` | <pre>[<br>  "/dev/xvdb",<br>  "/dev/xvdc",<br>  "/dev/xvdd",<br>  "/dev/xvde",<br>  "/dev/xvdf",<br>  "/dev/xvdg",<br>  "/dev/xvdh",<br>  "/dev/xvdi",<br>  "/dev/xvdj",<br>  "/dev/xvdk",<br>  "/dev/xvdl",<br>  "/dev/xvdm",<br>  "/dev/xvdn",<br>  "/dev/xvdo",<br>  "/dev/xvdp",<br>  "/dev/xvdq",<br>  "/dev/xvdr",<br>  "/dev/xvds",<br>  "/dev/xvdt",<br>  "/dev/xvdu",<br>  "/dev/xvdv",<br>  "/dev/xvdw",<br>  "/dev/xvdx",<br>  "/dev/xvdy",<br>  "/dev/xvdz"<br>]</pre> | no |
| ebs\_iops | Amount of provisioned IOPS. This must be set with a volume\_type of io1 | `number` | `0` | no |
| ebs\_optimized | Launched EC2 instance will be EBS-optimized | `bool` | `false` | no |
| ebs\_volume\_count | Count of EBS volumes that will be attached to the instance | `number` | `0` | no |
| ebs\_volume\_encrypted | Size of the EBS volume in gigabytes | `bool` | `true` | no |
| ebs\_volume\_size | Size of the EBS volume in gigabytes | `number` | `10` | no |
| ebs\_volume\_type | The type of EBS volume. Can be standard, gp2 or io1 | `string` | `"gp2"` | no |
| enabled | Set to false to prevent the module from creating any resources | `bool` | `null` | no |
| environment | Environment, e.g. 'uw2', 'us-west-2', OR 'prod', 'staging', 'dev', 'UAT' | `string` | `null` | no |
| evaluation\_periods | The number of periods over which data is compared to the specified threshold | `number` | `5` | no |
| generate\_ssh\_key\_pair | If true, create a new key pair and save the pem for it to the current working directory | `bool` | `false` | no |
| id\_length\_limit | Limit `id` to this many characters.<br>Set to `0` for unlimited length.<br>Set to `null` for default, which is `0`.<br>Does not affect `id_full`. | `number` | `null` | no |
| instance\_count | Count of ec2 instances to create | `number` | `1` | no |
| instance\_enabled | Flag to control the instance creation. Set to false if it is necessary to skip instance creation | `bool` | `true` | no |
| instance\_type | The type of the instance | `string` | `"t2.micro"` | no |
| ipv6\_address\_count | Number of IPv6 addresses to associate with the primary network interface. Amazon EC2 chooses the IPv6 addresses from the range of your subnet | `number` | `0` | no |
| ipv6\_addresses | List of IPv6 addresses from the range of the subnet to associate with the primary network interface | `list(string)` | `[]` | no |
| kms\_key\_id | KMS key ID used to encrypt EBS volume. When specifying kms\_key\_id, ebs\_volume\_encrypted needs to be set to true | `string` | `null` | no |
| label\_key\_case | The letter case of label keys (`tag` names) (i.e. `name`, `namespace`, `environment`, `stage`, `attributes`) to use in `tags`.<br>Possible values: `lower`, `title`, `upper`. <br>Default value: `title`. | `string` | `null` | no |
| label\_order | The naming order of the id output and Name tag.<br>Defaults to ["namespace", "environment", "stage", "name", "attributes"].<br>You can omit any of the 5 elements, but at least one must be present. | `list(string)` | `null` | no |
| label\_value\_case | The letter case of output label values (also used in `tags` and `id`).<br>Possible values: `lower`, `title`, `upper` and `none` (no transformation). <br>Default value: `lower`. | `string` | `null` | no |
| metadata\_http\_endpoint\_enabled | Whether the metadata service is available | `bool` | `true` | no |
| metadata\_http\_tokens\_required | Whether or not the metadata service requires session tokens, also referred to as Instance Metadata Service Version 2. | `bool` | `true` | no |
| metric\_name | The name for the alarm's associated metric. Allowed values can be found in https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ec2-metricscollected.html | `string` | `"StatusCheckFailed_Instance"` | no |
| metric\_namespace | The namespace for the alarm's associated metric. Allowed values can be found in https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-namespaces.html | `string` | `"AWS/EC2"` | no |
| metric\_threshold | The value against which the specified statistic is compared | `number` | `1` | no |
| monitoring | Launched EC2 instance will have detailed monitoring enabled | `bool` | `true` | no |
| name | Solution name, e.g. 'app' or 'jenkins' | `string` | `null` | no |
| namespace | Namespace, which could be your organization name or abbreviation, e.g. 'eg' or 'cp' | `string` | `null` | no |
| permissions\_boundary\_arn | Policy ARN to attach to instance role as a permissions boundary | `string` | `""` | no |
| private\_ips | Private IP address to associate with the instances in the VPC | `list(string)` | `[]` | no |
| regex\_replace\_chars | Regex to replace chars with empty string in `namespace`, `environment`, `stage` and `name`.<br>If not set, `"/[^a-zA-Z0-9-]/"` is used to remove all characters other than hyphens, letters and digits. | `string` | `null` | no |
| region | AWS Region the instance is launched in | `string` | n/a | yes |
| root\_block\_device\_encrypted | Whether to encrypt the root block device | `bool` | `true` | no |
| root\_iops | Amount of provisioned IOPS. This must be set if root\_volume\_type is set to `io1` | `number` | `0` | no |
| root\_volume\_size | Size of the root volume in gigabytes | `number` | `10` | no |
| root\_volume\_type | Type of root volume. Can be standard, gp2 or io1 | `string` | `"gp2"` | no |
| security\_groups | List of Security Group IDs allowed to connect to the instance | `list(string)` | `[]` | no |
| source\_dest\_check | Controls if traffic is routed to the instance when the destination address does not match the instance. Used for NAT or VPNs | `bool` | `true` | no |
| ssh\_key\_pair | SSH key pair to be provisioned on the instance | `string` | `""` | no |
| ssh\_key\_pair\_path | Path to where the generated key pairs will be created. Defaults to $${path.cwd} | `string` | `""` | no |
| stage | Stage, e.g. 'prod', 'staging', 'dev', OR 'source', 'build', 'test', 'deploy', 'release' | `string` | `null` | no |
| statistic\_level | The statistic to apply to the alarm's associated metric. Allowed values are: SampleCount, Average, Sum, Minimum, Maximum | `string` | `"Maximum"` | no |
| subnet | VPC Subnet ID the instance is launched in | `string` | n/a | yes |
| tags | Additional tags (e.g. `map('BusinessUnit','XYZ')` | `map(string)` | `{}` | no |
| user\_data | Instance user data. Do not pass gzip-compressed data via this argument | `string` | `""` | no |
| vpc\_id | The ID of the VPC that the instance security group belongs to | `string` | n/a | yes |
| welcome\_message | Welcome message | `string` | `""` | no |

## Outputs

| Name | Description |
|------|-------------|
| alarm\_ids | CloudWatch Alarm IDs |
| aws\_key\_pair\_name | Name of AWS key pair |
| ebs\_ids | IDs of EBSs |
| eip\_per\_instance\_count | Number of EIPs per instance. |
| eni\_to\_eip\_map | Map of ENI with EIP |
| ids | Disambiguated IDs list |
| instance\_count | Total number of instances created |
| name | Instance(s) name |
| new\_ssh\_keypair\_generated | Was a new ssh\_key\_pair generated |
| primary\_network\_interface\_ids | IDs of the instance's primary network interface |
| private\_dns | Private DNS records of instances |
| private\_ips | Private IPs of instances |
| public\_dns | All public DNS records for the public interfaces and ENIs |
| public\_ips | List of Public IPs of instances (or EIP) |
| role\_names | Names of AWS IAM Roles associated with creating instance |
| security\_group\_ids | ID on the new AWS Security Group associated with creating instance |
| ssh\_key\_pem\_path | Path where SSH key pair was created (if applicable) |

<!-- markdownlint-restore -->



## Share the Love

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/terraform-aws-ec2-instance-group)! (it helps us **a lot**)

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [terraform-aws-ec2-instance](https://github.com/cloudposse/terraform-aws-ec2-instance) - Terraform Module for providing a general EC2 instance provisioned by Ansible
- [terraform-aws-ec2-bastion-server](https://github.com/cloudposse/terraform-aws-ec2-bastion-server) - Terraform Module to define a generic Bastion host with parameterized user_data
- [https://github.com/cloudposse/terraform-aws-rds-cluster](https://github.com/cloudposse/terraform-aws-rds-cluster) - Terraform module to provision an RDS Aurora cluster for MySQL or Postgres




## References

For additional context, refer to some of these links.

- [terraform-aws-ec2-bastion-server](https://github.com/cloudposse/terraform-aws-ec2-bastion-server) - Terraform module to define a generic Bastion host with parameterized user_data


## Help

**Got a question?** We got answers.

File a GitHub [issue](https://github.com/cloudposse/terraform-aws-ec2-instance-group/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## DevOps Accelerator for Startups


We are a [**DevOps Accelerator**][commercial_support]. We'll help you build your cloud infrastructure from the ground up so you can own it. Then we'll show you how to operate it and stick around for as long as you need us.

[![Learn More](https://img.shields.io/badge/learn%20more-success.svg?style=for-the-badge)][commercial_support]

Work directly with our team of DevOps experts via email, slack, and video conferencing.

We deliver 10x the value for a fraction of the cost of a full-time engineer. Our track record is not even funny. If you want things done right and you need it done FAST, then we're your best bet.

- **Reference Architecture.** You'll get everything you need from the ground up built using 100% infrastructure as code.
- **Release Engineering.** You'll have end-to-end CI/CD with unlimited staging environments.
- **Site Reliability Engineering.** You'll have total visibility into your apps and microservices.
- **Security Baseline.** You'll have built-in governance with accountability and audit logs for all changes.
- **GitOps.** You'll be able to operate your infrastructure via Pull Requests.
- **Training.** You'll receive hands-on training so your team can operate what we build.
- **Questions.** You'll have a direct line of communication between our teams via a Shared Slack channel.
- **Troubleshooting.** You'll get help to triage when things aren't working.
- **Code Reviews.** You'll receive constructive feedback on Pull Requests.
- **Bug Fixes.** We'll rapidly work with you to fix any bugs in our projects.

## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Discourse Forums

Participate in our [Discourse Forums][discourse]. Here you'll find answers to commonly asked questions. Most questions will be related to the enormous number of projects we support on our GitHub. Come here to collaborate on answers, find solutions, and get ideas about the products and services we value. It only takes a minute to get started! Just sign in with SSO using your GitHub account.

## Newsletter

Sign up for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover.

## Office Hours

[Join us every Wednesday via Zoom][office_hours] for our weekly "Lunch & Learn" sessions. It's **FREE** for everyone!

[![zoom](https://img.cloudposse.com/fit-in/200x200/https://cloudposse.com/wp-content/uploads/2019/08/Powered-by-Zoom.png")][office_hours]

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/terraform-aws-ec2-instance-group/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or [help out](https://cpco.io/help-out) with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2021 [Cloud Posse, LLC](https://cpco.io/copyright)



## License

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

See [LICENSE](LICENSE) for full details.

```text
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
```









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

<!-- markdownlint-disable -->
|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Jamie Nelson][Jamie-BitFlight_avatar]][Jamie-BitFlight_homepage]<br/>[Jamie Nelson][Jamie-BitFlight_homepage] | [![Vladimir][SweetOps_avatar]][SweetOps_homepage]<br/>[Vladimir][SweetOps_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] | [![Chris Weyl][rsrchboy_avatar]][rsrchboy_homepage]<br/>[Chris Weyl][rsrchboy_homepage] |
|---|---|---|---|---|
<!-- markdownlint-restore -->

  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: https://img.cloudposse.com/150x150/https://github.com/osterman.png
  [Jamie-BitFlight_homepage]: https://github.com/Jamie-BitFlight
  [Jamie-BitFlight_avatar]: https://img.cloudposse.com/150x150/https://github.com/Jamie-BitFlight.png
  [SweetOps_homepage]: https://github.com/SweetOps
  [SweetOps_avatar]: https://img.cloudposse.com/150x150/https://github.com/SweetOps.png
  [aknysh_homepage]: https://github.com/aknysh
  [aknysh_avatar]: https://img.cloudposse.com/150x150/https://github.com/aknysh.png
  [rsrchboy_homepage]: https://github.com/rsrchboy
  [rsrchboy_avatar]: https://img.cloudposse.com/150x150/https://github.com/rsrchboy.png

[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=docs
  [website]: https://cpco.io/homepage?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=website
  [github]: https://cpco.io/github?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=github
  [jobs]: https://cpco.io/jobs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=jobs
  [hire]: https://cpco.io/hire?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=hire
  [slack]: https://cpco.io/slack?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=slack
  [linkedin]: https://cpco.io/linkedin?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=linkedin
  [twitter]: https://cpco.io/twitter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=twitter
  [testimonial]: https://cpco.io/leave-testimonial?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=testimonial
  [office_hours]: https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=office_hours
  [newsletter]: https://cpco.io/newsletter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=newsletter
  [discourse]: https://ask.sweetops.com/?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=discourse
  [email]: https://cpco.io/email?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=email
  [commercial_support]: https://cpco.io/commercial-support?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=commercial_support
  [we_love_open_source]: https://cpco.io/we-love-open-source?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=we_love_open_source
  [terraform_modules]: https://cpco.io/terraform-modules?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=terraform_modules
  [readme_header_img]: https://cloudposse.com/readme/header/img
  [readme_header_link]: https://cloudposse.com/readme/header/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=readme_header_link
  [readme_footer_img]: https://cloudposse.com/readme/footer/img
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=readme_footer_link
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ec2-instance-group&utm_content=readme_commercial_support_link
  [share_twitter]: https://twitter.com/intent/tweet/?text=terraform-aws-ec2-instance-group&url=https://github.com/cloudposse/terraform-aws-ec2-instance-group
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=terraform-aws-ec2-instance-group&url=https://github.com/cloudposse/terraform-aws-ec2-instance-group
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/terraform-aws-ec2-instance-group
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/terraform-aws-ec2-instance-group
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/terraform-aws-ec2-instance-group
  [share_email]: mailto:?subject=terraform-aws-ec2-instance-group&body=https://github.com/cloudposse/terraform-aws-ec2-instance-group
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/terraform-aws-ec2-instance-group?pixel&cs=github&cm=readme&an=terraform-aws-ec2-instance-group
