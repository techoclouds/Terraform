# Chapter – Module Composition & Best Practices in Terraform

## 1. Introduction

### 1.1 What is Module Composition?
- **Definition:** Module composition is the practice of combining multiple Terraform modules to build complex infrastructure.
- **Purpose:**
  - Enables **scalability** by breaking large configurations into smaller, manageable modules.
  - Enhances **reusability** by grouping related resources together.
  - Improves **collaboration** by allowing teams to work on separate modules independently.

### 1.2 Why Use Module Composition?
- **Organized Codebase:** Reduces duplication and complexity.
- **Easier Debugging:** Isolate issues to specific modules.
- **Faster Deployment:** Parallelize infrastructure provisioning using modular design.

### 1.3 When to Use Module Composition
- Deploying **multi-tier applications** (e.g., separate modules for networking, compute, storage).
- Managing **multi-cloud environments**.
- Implementing **shared modules** across teams.

---

## 2. Structuring Terraform Projects with Modules

### 2.1 Recommended Directory Structure
```
project-root/
├── main.tf            # Root configuration file
├── variables.tf       # Root-level variables
├── outputs.tf         # Root-level outputs
├── modules/
│   ├── vpc/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   ├── ec2/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   ├── rds/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
```
**Key Points:**
- Keep modules **isolated and reusable**.
- Define **inputs and outputs** for better interaction.
- Store modules in **a separate directory** for maintainability.

---

## 3. Composing Modules

### 3.1 Using Multiple Modules in Root Configuration
```hcl
module "network" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
}

module "compute" {
  source        = "./modules/ec2"
  vpc_id        = module.network.vpc_id
  instance_type = "t3.medium"
}

module "database" {
  source        = "./modules/rds"
  vpc_id        = module.network.vpc_id
  db_name       = "mydb"
}
```
**Key Points:**
- **Pass module outputs** as inputs to dependent modules.
- **Modules remain independent** but interact through shared variables.

### 3.2 Nested Modules (Modules Within Modules)
```hcl
module "application" {
  source    = "./modules/application"
  vpc_id    = module.network.vpc_id
  instances = module.compute.instance_ids
}
```
**Key Points:**
- Nested modules **encapsulate** complex configurations.
- Useful for **grouping related infrastructure components**.

### 3.3 Example: Complete Infrastructure with Networking, Compute, and Database Modules
#### **Networking Module (`modules/vpc`)**
```hcl
variable "cidr" {}
resource "aws_vpc" "main" {
  cidr_block = var.cidr
}
output "vpc_id" {
  value = aws_vpc.main.id
}
```

#### **Compute Module (`modules/ec2`)**
```hcl
variable "vpc_id" {}
resource "aws_instance" "web" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t3.medium"
  vpc_security_group_ids = [var.vpc_id]
}
output "instance_id" {
  value = aws_instance.web.id
}
```

#### **Database Module (`modules/rds`)**
```hcl
variable "vpc_id" {}
variable "db_name" {}
resource "aws_db_instance" "main" {
  allocated_storage = 20
  engine            = "mysql"
  instance_class    = "db.t3.micro"
  name              = var.db_name
  vpc_security_group_ids = [var.vpc_id]
}
output "db_instance" {
  value = aws_db_instance.main.address
}
```

---

## 4. Hands-On: Implementing Module Composition

### 4.1 Step 1: Create Required Modules
- **Networking Module (`modules/vpc`)**
- **Compute Module (`modules/ec2`)**
- **Database Module (`modules/rds`)**

### 4.2 Step 2: Connect Modules
1. Reference **network module** from the compute and database modules.
2. Pass required **output values** between modules.
3. Ensure **dependencies are clear** in the root configuration.

### 4.3 Step 3: Deploy Infrastructure
```bash
terraform init
terraform apply
```
**Expected Outcome:**
- A **VPC is created** first.
- EC2 and RDS instances are provisioned **using the VPC ID from the network module**.

---

## 5. Best Practices for Module Composition

### 5.1 Keep Modules Independent
- Each module should serve **one purpose**.
- Minimize **interdependencies** where possible.

### 5.2 Version Control Modules
- Store modules in **Git repositories**.
- Use **versioning** for module updates:
  ```hcl
  module "vpc" {
    source  = "git::https://github.com/myorg/terraform-vpc.git?ref=v1.2.0"
  }
  ```

### 5.3 Use Meaningful Variables and Outputs
- Clearly define **input variables** to customize module behavior.
- Use **descriptive outputs** to expose relevant data.

### 5.4 Document Module Usage
- Provide a `README.md` for each module explaining input variables, outputs, and example usage.
- Example documentation format:
  ```md
  # VPC Module
  ## Inputs
  - `cidr` (string): The CIDR block for the VPC.
  ## Outputs
  - `vpc_id`: The ID of the created VPC.
  ```

---

## 6. Summary and Conclusion

### 6.1 Recap Key Points
- **Module composition** simplifies Terraform project structure.
- **Inter-module communication** allows dependencies to be efficiently managed.
- **Using nested modules** helps **organize and scale** infrastructure deployments.

### 6.2 Next Steps
- Learn about **Terraform Workspaces** to manage multiple environments.
- Implement **CI/CD pipelines** for Terraform module automation.
- Explore **module testing frameworks** for infrastructure validation.
