# Terraform Notes — Part 2

## 1. Terraform State Overview

Terraform keeps a **state file** that records all infrastructure it manages.
This file connects Terraform configuration with real resources.

Default file:

```
terraform.tfstate
```

The state file stores:

* Resource IDs
* Current resource attributes
* Metadata
* Dependency relationships

---

## 2. Why Terraform State Is Important

Terraform relies on state for almost every operation.

State is used to:

* Know which resources are managed
* Decide what must change
* Run operations faster
* Understand dependencies

State acts as Terraform’s **single source of truth**.

---

## 3. Terraform State Workflow

* `terraform init`
  Prepares backend and downloads providers

* `terraform plan`
  Compares desired configuration with state

* `terraform apply`
  Makes changes and updates state

Even existing infrastructure needs state to be tracked.

---

## 4. Terraform State Locking

State locking prevents two users from modifying the same state at once.

* Locking happens automatically
* Terraform stops if lock cannot be obtained
* Prevents corruption

### Force Unlock

```
terraform force-unlock LOCK_ID
```

Disabling locking is dangerous, especially in production.

---

## 5. Sensitive Data in State

State files may contain secrets such as:

* Passwords
* Tokens
* API keys

### Sensitive Output Example

```
output "db_password" {
  value     = aws_db_instance.db.password
  sensitive = true
}
```

This hides the value in CLI output, but it still exists in the state file.

---

## 6. Terraform Backends

A backend defines:

* Where state is stored
* How state is read and written
* How Terraform runs operations

Backends must be initialized.

---

## 7. Local Backend

Default backend.

### Characteristics

* State stored on local machine
* Uses file locking
* Good for single user

### Example

```
terraform {
  backend "local" {
    path = "terraform.tfstate"
  }
}
```

Not suitable for teams.

---

## 8. Remote Backend

State stored in shared storage.

### Benefits

* Team collaboration
* Central state
* Locking support
* Better security

Common backends:

* Amazon S3
* Terraform Cloud
* Consul

Backend settings can be partially defined to avoid storing secrets in code.

---

## 9. Using Terraform with AWS

### Requirements

* AWS CLI installed
* IAM user configured
* Access key and secret key

### Set Environment Variables

```
export AWS_ACCESS_KEY_ID=<access_key>
export AWS_SECRET_ACCESS_KEY=<secret_key>
```

Terraform uses these credentials to talk to AWS.

---

## 10. AWS Provider Configuration

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }
  required_version = ">= 1.2.0"
}

provider "aws" {
  region = "us-east-1"
}
```

---

## 11. Creating an EC2 Instance

```
resource "aws_instance" "example" {
  ami           = "ami-08c40ec9ead489470"
  instance_type = "t2.micro"

  tags = {
    Name = "TerraformEC2"
  }
}
```

### Output Public IP

```
output "public_ip" {
  value = aws_instance.example.public_ip
}
```

---

## 12. Terraform State Commands

```
terraform state list
terraform state show <resource>
terraform state mv
terraform state rm
terraform state pull
terraform state push
```

These change only state, not real infrastructure.

---

## 13. Terraform Meta-Arguments

Meta-arguments control resource behavior.

### count

```
resource "aws_instance" "node" {
  count         = 3
  ami           = var.ami
  instance_type = "t2.micro"
}
```

### for_each

```
resource "aws_instance" "node" {
  for_each      = var.ami_map
  ami           = each.value
  instance_type = "t2.micro"
}
```

### depends_on

```
depends_on = [aws_s3_bucket.bucket]
```

---

## 14. Terraform Modules

Modules are **reusable blocks of Terraform code**.

### Structure

```
module/
 ├── main.tf
 ├── variables.tf
 └── outputs.tf
```

### Use Module

```
module "ec2" {
  source   = "./ec2-module"
  image_id = "ami-xxxx"
}
```

Modules improve reuse and organization.

---

## 15. Module Inputs and Outputs

### Input Variable

```
variable "image_id" {
  type = string
}
```

### Output

```
output "private_ip" {
  value = aws_instance.node.private_ip
}
```

---

## 16. Terraform Built-in Functions

Terraform provides built-in functions.

Examples:

```
max(1, 5, 10)
element(["x", "y", "z"], 0)
lookup({env="prod"}, "env", "dev")
```

User-defined functions are not supported.

---

## 17. Terraform Provisioners

Provisioners run scripts or commands on resources.

They should be avoided when possible.

---

## 18. Types of Provisioners

### local-exec

```
provisioner "local-exec" {
  command = "echo Terraform running"
}
```

### file

```
provisioner "file" {
  source      = "config.txt"
  destination = "/tmp/config.txt"
}
```

### remote-exec

```
provisioner "remote-exec" {
  inline = [
    "sudo apt update",
    "sudo apt install -y nginx"
  ]
}
```

---

## 19. Provisioner Behavior

* Run during resource creation
* Failure marks resource as tainted
* Tainted resources are recreated

### Destroy-Time Provisioner

```
provisioner "local-exec" {
  when    = destroy
  command = "echo Resource removed"
}
```

---

## 20. Terraform Debugging

Terraform supports logging.

### Enable Logging

```
export TF_LOG=TRACE
export TF_LOG_PATH=terraform.log
```

Log levels:

* TRACE
* DEBUG
* INFO
* WARN
* ERROR
