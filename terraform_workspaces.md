# Chapter – Terraform Workspaces

## 1. Introduction

### 1.1 What Are Terraform Workspaces?
- **Definition:** Terraform workspaces allow managing multiple environments (e.g., dev, staging, production) within the same Terraform configuration.
- **Purpose:**
  - Enables environment-specific configurations without duplicating code.
  - Facilitates infrastructure changes across different stages of deployment.
  - Helps manage state files separately for each workspace.

### 1.2 Why Use Workspaces?
- **Multi-Environment Management:** Easily switch between development, staging, and production.
- **State Isolation:** Keeps infrastructure changes independent.
- **Improved Team Collaboration:** Different teams can manage separate workspaces without conflicts.

### 1.3 When to Use Workspaces
- Managing **multiple deployments** of the same infrastructure.
- Handling **feature-based environments** (e.g., creating test environments for a new feature branch).
- Working with **shared Terraform configurations** where teams need independent state management.

---

## 2. Understanding Terraform Workspaces

### 2.1 Default Workspace
- Terraform starts with a default workspace named `default`.
- Running `terraform apply` applies changes to the `default` workspace unless another workspace is selected.

### 2.2 Creating and Switching Workspaces
#### **List Existing Workspaces**
```bash
terraform workspace list
```
This command displays all available workspaces in the current Terraform configuration.

#### **Create a New Workspace**
```bash
terraform workspace new dev
```
Creates a new workspace named `dev`, allowing separate infrastructure state management.

#### **Switch to an Existing Workspace**
```bash
terraform workspace select dev
```
Switches the Terraform context to the `dev` workspace, ensuring that infrastructure changes are applied only within that environment.

#### **Delete a Workspace**
```bash
terraform workspace delete dev
```
Deletes the `dev` workspace. This will remove its state, but the infrastructure remains unchanged unless explicitly destroyed.

**Key Points:**
- Workspaces allow you to **logically separate** environments.
- Each workspace has its **own Terraform state file**.

---

## 3. Using Workspaces in Terraform Configurations

### 3.1 Referencing the Active Workspace
Terraform provides the built-in variable `terraform.workspace` to reference the current workspace dynamically.
```hcl
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket-${terraform.workspace}"
  acl    = "private"
}
```
**Expected Outcome:**
- If the workspace is `dev`, the bucket will be named `my-bucket-dev`.
- If the workspace is `prod`, the bucket will be named `my-bucket-prod`.

### 3.2 Using Workspaces with Conditional Logic
Define environment-specific variables based on the active workspace:
```hcl
variable "instance_type" {
  default = {
    default = "t2.micro"
    dev     = "t3.micro"
    prod    = "t3.large"
  }
}

resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890"
  instance_type = lookup(var.instance_type, terraform.workspace, "t2.micro")
}
```
**Explanation:**
- The `lookup()` function retrieves the instance type corresponding to the active workspace.
- If the workspace is `dev`, it selects `t3.micro`.
- If the workspace is `prod`, it selects `t3.large`.
- If an unknown workspace is used, it defaults to `t2.micro`.

### 3.3 Using Workspaces to Manage Multiple Environments
#### **Example: Different Subnets for Different Workspaces**
```hcl
variable "subnet_ids" {
  default = {
    default = "subnet-12345"
    dev     = "subnet-67890"
    prod    = "subnet-54321"
  }
}

resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t3.micro"
  subnet_id     = lookup(var.subnet_ids, terraform.workspace, "subnet-12345")
}
```
**Explanation:**
- Ensures that instances are created in different subnets based on the active workspace.
- Dev instances will be created in `subnet-67890`.
- Prod instances will be created in `subnet-54321`.

---

## 4. Managing Workspace State in Remote Backends

### 4.1 Configuring Workspaces with Remote State (S3 Backend)
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "env/${terraform.workspace}/terraform.tfstate"
    region = "us-east-1"
  }
}
```
**Explanation:**
- Stores state files in an S3 bucket with separate paths for each workspace.
- Workspace `dev` will store state in `s3://my-terraform-state/env/dev/terraform.tfstate`.
- Workspace `prod` will store state in `s3://my-terraform-state/env/prod/terraform.tfstate`.

### 4.2 Enabling State Locking (S3 with DynamoDB)
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "env/${terraform.workspace}/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-lock"
  }
}
```
**Benefits:**
- Prevents **simultaneous modifications** of the state file.
- Ensures that only one user modifies a workspace at a time.

### 4.3 Using Workspaces with Multiple AWS Profiles and Printing Account ID
#### **Example: Managing Different AWS Accounts**
```hcl
variable "aws_profiles" {
  default = {
    default = "default"
    dev     = "dev-profile"
    prod    = "prod-profile"
  }
}

provider "aws" {
  region  = "us-east-1"
  profile = lookup(var.aws_profiles, terraform.workspace, "default")
}

data "aws_caller_identity" "current" {}

output "aws_account_id" {
  value = data.aws_caller_identity.current.account_id
}
```
**Explanation:**
- Uses different AWS profiles based on the active workspace.
- Prints the AWS **account ID** associated with the current workspace.

---

## 5. Best Practices for Workspaces

### 5.1 Use Workspaces for Logical Separation
- Ideal for managing **multiple deployments** of similar infrastructure.

### 5.2 Define Environment-Specific Variables
- Use `terraform.workspace` dynamically for resource names, instance types, and security policies.

### 5.3 Secure State Files
- **Enable state locking** with DynamoDB.
- **Restrict access** using IAM policies.
- **Encrypt state files** in remote backends.

---

## 6. Summary and Conclusion

### 6.1 Recap Key Points
- Workspaces enable **multi-environment management** in Terraform.
- Workspaces provide **state isolation** for different environments.
- Use `terraform.workspace` to reference workspace-specific configurations dynamically.

