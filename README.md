Branch indexing
10:02:39 Connecting to https://api.github.com using 2493969/****** (test connections)
Obtained Jenkinsfile from 653499711d8634495490a8972f81659677691e34
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/BYOD3_dev
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
using credential jenkins-github-app
Cloning the remote Git repository
Cloning with configured refspecs honoured and without tags
Cloning repository https://github.com/deepakbatra5/BYOD.git
 > git init /var/lib/jenkins/workspace/BYOD3_dev # timeout=10
Fetching upstream changes from https://github.com/deepakbatra5/BYOD.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
using GIT_ASKPASS to set credentials test connections
 > git fetch --no-tags --force --progress -- https://github.com/deepakbatra5/BYOD.git +refs/heads/dev:refs/remotes/origin/dev # timeout=10
 > git config remote.origin.url https://github.com/deepakbatra5/BYOD.git # timeout=10
 > git config --add remote.origin.fetch +refs/heads/dev:refs/remotes/origin/dev # timeout=10
Avoid second fetch
Checking out Revision 653499711d8634495490a8972f81659677691e34 (dev)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 653499711d8634495490a8972f81659677691e34 # timeout=10
Commit message: "first"
First time build. Skipping changelog.

Could not update commit status. Message: {"message":"Resource not accessible by integration","documentation_url":"https://docs.github.com/rest/commits/statuses#create-a-commit-status","status":"403"}

[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Checkout)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
using credential jenkins-github-app
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/BYOD3_dev/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/deepakbatra5/BYOD.git # timeout=10
Fetching without tags
Fetching upstream changes from https://github.com/deepakbatra5/BYOD.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
using GIT_ASKPASS to set credentials test connections
 > git fetch --no-tags --force --progress -- https://github.com/deepakbatra5/BYOD.git +refs/heads/dev:refs/remotes/origin/dev # timeout=10
Checking out Revision 653499711d8634495490a8972f81659677691e34 (dev)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 653499711d8634495490a8972f81659677691e34 # timeout=10
Commit message: "first"

Could not update commit status. Message: {"message":"Resource not accessible by integration","documentation_url":"https://docs.github.com/rest/commits/statuses#create-a-commit-status","status":"403"}

[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Debug Info)
[Pipeline] echo
BRANCH_NAME from Jenkins: dev
[Pipeline] sh
+ echo Git branch detected:
Git branch detected:
+ git branch --show-current
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Terraform Init)
[Pipeline] dir
Running in /var/lib/jenkins/workspace/BYOD3_dev/infra/terraform
[Pipeline] {
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
+ terraform init
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/template versions matching "2.2.0"...
- Finding hashicorp/aws versions matching "~> 5.0"...
- Finding hashicorp/local versions matching "~> 2.3"...
- Installing hashicorp/template v2.2.0...
- Installed hashicorp/template v2.2.0 (signed by HashiCorp)
- Installing hashicorp/aws v5.100.0...
- Installed hashicorp/aws v5.100.0 (signed by HashiCorp)
- Installing hashicorp/local v2.6.1...
- Installed hashicorp/local v2.6.1 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!
+ echo Using terraform.tfvars
Using terraform.tfvars
+ ls -l
total 16
-rw-r--r-- 1 jenkins jenkins 1660 Dec 23 10:02 main.tf
-rw-r--r-- 1 jenkins jenkins   78 Dec 23 10:02 outputs.tf
-rw-r--r-- 1 jenkins jenkins  129 Dec 23 10:02 terraform.tfvars
-rw-r--r-- 1 jenkins jenkins  341 Dec 23 10:02 variables.tf
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Terraform Plan)
[Pipeline] dir
Running in /var/lib/jenkins/workspace/BYOD3_dev/infra/terraform
[Pipeline] {
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
+ terraform plan -var-file=terraform.tfvars -out=tfplan

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # data.template_file.ansible_inventory will be read during apply
  # (config refers to values not yet known)
 <= data "template_file" "ansible_inventory" {
      + id       = (known after apply)
      + rendered = (known after apply)
      + template = <<-EOT
            [appserver]
            ec2-app ansible_host=${public_ip} ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/fullstack-cicd.pem
        EOT
      + vars     = {
          + "public_ip" = (known after apply)
        }
    }

  # aws_instance.app_server will be created
  + resource "aws_instance" "app_server" {
      + ami                                  = "ami-03deb8c961063af8c"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + enable_primary_ipv6                  = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "fullstack-cicd"
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "cicd-app-server"
        }
      + tags_all                             = {
          + "Name" = "cicd-app-server"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification (known after apply)

      + cpu_options (known after apply)

      + ebs_block_device (known after apply)

      + enclave_options (known after apply)

      + ephemeral_block_device (known after apply)

      + instance_market_options (known after apply)

      + maintenance_options (known after apply)

      + metadata_options (known after apply)

      + network_interface (known after apply)

      + private_dns_name_options (known after apply)

      + root_block_device (known after apply)
    }

  # aws_security_group.app_sg will be created
  + resource "aws_security_group" "app_sg" {
      + arn                    = (known after apply)
      + description            = "Allow SSH and HTTP"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 22
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 22
                # (1 unchanged attribute hidden)
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 3000
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 3000
                # (1 unchanged attribute hidden)
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 4000
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 4000
                # (1 unchanged attribute hidden)
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 9090
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 9090
                # (1 unchanged attribute hidden)
            },
        ]
      + name                   = (known after apply)
      + name_prefix            = "app-sg"
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

  # local_file.ansible_inventory will be created
  + resource "local_file" "ansible_inventory" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./../ansible/inventory.ini"
      + id                   = (known after apply)
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + app_server_public_ip = (known after apply)
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Manual Approval)
[Pipeline] script
[Pipeline] {
[Pipeline] input
Input requested
Approved by Deepak Batra
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Terraform Apply)
[Pipeline] script
[Pipeline] {
[Pipeline] dir
Running in /var/lib/jenkins/workspace/BYOD3_dev/infra/terraform
[Pipeline] {
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
+ terraform apply -auto-approve tfplan
aws_security_group.app_sg: Creating...
aws_security_group.app_sg: Creation complete after 8s [id=sg-0a73f7860a37e2e7f]
aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [00m10s elapsed]
aws_instance.app_server: Creation complete after 18s [id=i-06cb8c4e844da38d5]
data.template_file.ansible_inventory: Reading...
data.template_file.ansible_inventory: Read complete after 0s [id=987fc9913c80a816bbfac4f30233182961fa2f6b4c76db03cf4e7c231fc62db9]
local_file.ansible_inventory: Creating...
local_file.ansible_inventory: Creation complete after 0s [id=9d7872afa5d6de8ee82604321e4166ed34dcb1b6]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

app_server_public_ip = "44.200.122.114"
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] echo
Terraform pipeline completed successfully.
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline

Could not update commit status. Message: {"message":"Resource not accessible by integration","documentation_url":"https://docs.github.com/rest/commits/statuses#create-a-commit-status","status":"403"}

Finished: SUCCESS
