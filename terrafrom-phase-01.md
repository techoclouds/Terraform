# Terraform Hands-On Lab: Phase 1 - Fundamentals

## 1. Introduction to Infrastructure as Code (IaC)

### Why Infrastructure as Code (IaC)?
Before Terraform, infrastructure was often set up manually using cloud provider consoles (AWS, Azure, etc.), leading to:
- **Inconsistency**: Difficult to replicate environments
- **Manual Errors**: Misconfigurations due to human mistakes
- **Scalability Issues**: Managing 100+ servers manually is nearly impossible

**IaC solves these problems** by allowing infrastructure to be defined in code, making it:
✅ **Consistent** (same configuration every time)  
✅ **Version-controlled** (track changes using Git)  
✅ **Scalable** (automate creation of multiple instances)  

### Why Terraform?
Terraform is a **declarative** tool that describes **what** the infrastructure should look like, and it automatically figures out **how** to create or update resources.  
✅ **Multi-Cloud Support** (AWS, Azure, GCP, Kubernetes)  
✅ **State Management** (Keeps track of resources)  
✅ **Reusable Modules** (Can break infrastructure into reusable components)  

---

## 2. Installing Terraform

Terraform installation on **Linux**:

```bash
sudo apt update && sudo apt install -y wget unzip
wget https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip
unzip terraform_1.5.7_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform -version
```

✅ **Explanation**:
- `wget` downloads Terraform.
- `unzip` extracts the binary.
- `mv` moves Terraform to `/usr/local/bin/`, making it accessible system-wide.
- `terraform -version` verifies the installation.

---

## 3. Understanding Terraform Workflow

Terraform follows a standard workflow:

1️⃣ **Initialize (`terraform init`)**  
2️⃣ **Plan (`terraform plan`)**  
3️⃣ **Apply (`terraform apply`)**  
4️⃣ **Destroy (`terraform destroy`)**  

---

## 4. Writing the First Terraform Script

### Goal: Create an S3 Bucket on AWS

### Step 1: Create a new directory for the project
```bash
mkdir terraform-s3-demo && cd terraform-s3-demo
```

### Step 2: Create a Terraform configuration file
```bash
touch main.tf
vim main.tf
```

Paste the following code into `main.tf`:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-first-terraform-bucket-12345"
}
```

✅ **Explanation**:
- `provider "aws"` tells Terraform to use AWS as the cloud provider.
- `resource "aws_s3_bucket"` defines an S3 bucket.
- The **name of the bucket must be globally unique** (change it if needed).

### Step 3: Initialize Terraform
```bash
terraform init
```

✅ **Explanation**:
- Downloads the AWS provider plugin
- Prepares the project for Terraform execution

### Step 4: Preview the Changes
```bash
terraform plan
```

✅ **Why `terraform plan`?**
- Shows what Terraform **will do** before making changes.
- Helps avoid mistakes before applying real changes.

### Step 5: Apply the Changes (Create S3 Bucket)
```bash
terraform apply
```

Terraform will prompt for confirmation:
```
Do you want to perform these actions? (yes/no)
```
**Type `yes` and press Enter.**

✅ **Why does Terraform ask for confirmation?**
- To prevent accidental infrastructure changes.
- Helps verify planned changes before execution.

**If you want to skip confirmation, use `-auto-approve`:**
```bash
terraform apply -auto-approve
```

⚠️ **Warning: Use `-auto-approve` carefully.**
- Best for automation (CI/CD pipelines).
- In production, always review changes before applying.

---

## 5. Terraform State

After applying the changes, Terraform creates a **state file** (`terraform.tfstate`).

**Why does Terraform use a state file?**
- Keeps track of deployed resources.
- Helps Terraform understand what exists vs. what needs changes.

Check the state file:
```bash
cat terraform.tfstate
```

---

## 6. Destroying Resources

To delete the S3 bucket:
```bash
terraform destroy
```

Terraform will ask for confirmation. If you want to skip confirmation:
```bash
terraform destroy -auto-approve
```

✅ **Why destroy resources?**
- Avoid unnecessary costs.
- Keep environments clean after testing.

---

## Lab Summary

In this lab, you:
✅ Installed Terraform  
✅ Created an AWS S3 bucket using Terraform  
✅ Understood Terraform’s workflow (init, plan, apply, destroy)  
✅ Learned about Terraform state management  

---


