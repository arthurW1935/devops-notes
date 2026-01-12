# Terraform Notes — Part 1 

## 1. Overview of Terraform

Terraform is an **Infrastructure as Code (IaC)** tool developed by **HashiCorp**.
It allows developers and DevOps engineers to define infrastructure using configuration files instead of creating resources manually.

Terraform uses a **declarative approach**. You describe the final state you want, and Terraform figures out how to reach it.

Terraform works with many platforms, including:

* Amazon Web Services (AWS)
* Microsoft Azure
* Google Cloud Platform (GCP)
* Docker
* Kubernetes
* Many third-party providers

---

## 2. Infrastructure as Code (IaC)

Infrastructure as Code means managing infrastructure through code that can be stored, versioned, and reviewed like application code.

### Problems with Manual Setup

* Takes a lot of time
* Easy to make mistakes
* Environments become inconsistent
* No version history
* Hard to scale and repeat

### Benefits of IaC

* Same setup across environments
* Version controlled using Git
* Better collaboration
* Faster provisioning
* Lower operational risk

---

## 3. Why Use Terraform?

Terraform is popular because:

* Works across clouds
* Uses simple syntax (HCL)
* Tracks infrastructure state
* Fits well into CI/CD pipelines
* Manages full lifecycle of infrastructure

Terraform can:

* Create resources
* Update resources
* Delete resources

---

## 4. Terraform Core Components

Terraform uses these main pieces:

### Providers

Connect Terraform to external systems like cloud providers and SaaS platforms.

### Resources

Describe infrastructure items such as:

* Virtual machines
* Storage
* Containers
* Networks

### State File

Keeps track of real resources and their relation to Terraform code.

### Plan

Shows what Terraform will change before applying.

---

## 5. Installing Terraform (Linux)

Follow the official Terraform documentation for the latest Linux installation steps.

---

## 6. Terraform Configuration Language (HCL)

Terraform uses **HCL (HashiCorp Configuration Language)**.

Characteristics:

* Easy to read
* Structured
* Built for automation

Terraform files usually end with `.tf`.

---

## 7. Blocks and Arguments

Terraform configuration is written using **blocks** and **arguments**.

### Argument Example

```hcl
path = "/home/user/example.txt"
```

### Block Format

```hcl
block_type "label1" "label2" {
  key = value
}
```

---

## 8. Resource Blocks

Resources define actual infrastructure.

### Resource Syntax

```hcl
resource "provider_resource" "name" {
  property = value
}
```

### Example Resource

```hcl
resource "local_file" "demo" {
  filename = "/tmp/demo.txt"
  content  = "Terraform example"
}
```

---

## 9. Terraform Workflow

Typical workflow:

### Initialize

```bash
terraform init
```

Sets up the project and downloads providers.

### Plan

```bash
terraform plan
```

Shows what changes will be made.

### Apply

```bash
terraform apply
```

Creates or updates resources and saves state.

---

## 10. Terraform with Docker

### Terraform Block

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.21.0"
    }
  }
}
```

### Provider Block

```hcl
provider "docker" {}
```

### Docker Resources

```hcl
resource "docker_image" "nginx" {
  name = "nginx:latest"
}

resource "docker_container" "nginx_app" {
  image = docker_image.nginx.latest
  name  = "nginx-container"

  ports {
    internal = 80
    external = 8080
  }
}
```

---

## 11. Useful Terraform Commands

```bash
terraform fmt
terraform validate
terraform show
terraform state list
```

Meaning:

* `fmt` formats files
* `validate` checks syntax
* `show` displays state or plan
* `state list` shows tracked resources

---

## 12. Terraform Variables

Variables make configurations reusable.

### Declare Variables

```hcl
variable "file_name" {
  default = "/tmp/file.txt"
}

variable "file_content" {
  default = "Content from variable"
}
```

### Use Variables

```hcl
resource "local_file" "sample" {
  filename = var.file_name
  content  = var.file_content
}
```

---

## 13. Data Types

Terraform supports:

* string
* number
* bool
* list
* map
* object

Example:

```hcl
variable "config" {
  type = map(string)
}
```

---

## 14. Terraform Outputs

Outputs show values after resources are created.

### Example

```hcl
output "created_file" {
  value = local_file.sample.filename
}
```

Outputs are useful for:

* Debugging
* Passing values to other modules
* Displaying important information
