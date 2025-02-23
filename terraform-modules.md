# Chapter – Terraform Modules

## 1. Introduction

### 1.1 What Are Terraform Modules?
- **Definition:** A module is a container for multiple Terraform resources used together.
- **Purpose:**
  - Enables reuse of infrastructure code.
  - Simplifies complex configurations.
  - Improves maintainability and scalability.

### 1.2 Why Use Modules?
- **Reusability:** Write once, use multiple times.
- **Maintainability:** Easier to manage large infrastructure.
- **Consistency:** Standardize configurations across multiple projects.
- **Collaboration:** Teams can work on different modules independently.

### 1.3 When to Use Modules
- When managing a **repetitive** infrastructure component (e.g., multiple EC2 instances).
- When creating **standardized patterns** (e.g., VPC, Security Groups, IAM Roles).
- When structuring **large Terraform projects** into smaller, manageable pieces.

---

## 2. Structure of a Terraform Module

### 2.1 Basic Module Layout
A Terraform module consists of:
```
module_name/
├── main.tf       # Defines resources
├── variables.tf  # Input variables
├── outputs.tf    # Output values
```

### 2.2 Core Components of a Module
- **`main.tf`**: Defines the resources.
- **`variables.tf`**: Defines input parameters.
- **`outputs.tf`**: Exposes values for use in other configurations.

#### **Example: Simple EC2 Module**
```hcl
# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

# main.tf
resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890"
  instance_type = var.instance_type
}

# outputs.tf
output "instance_id" {
  value = aws_instance.example.id
}
```

---

## 3. Using a Module

### 3.1 Calling a Module
To use a module, reference it from the root Terraform configuration:
```hcl
module "ec2_instance" {
  source        = "./modules/ec2"
  instance_type = "t3.medium"
}
```
**Key Points:**
- `source` points to the module directory.
- Variables are passed to the module.

### 3.2 Using Modules from Terraform Registry
Modules can also be sourced from the Terraform Registry:
```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"
  name    = "my-vpc"
  cidr    = "10.0.0.0/16"
}
```
**Key Points:**
- Modules from the Terraform Registry simplify infrastructure creation.
- Specify the version to ensure stability.

---

## 4. Hands-On: Creating and Using a Module

### 4.1 Step 1: Create a Module
1. Create a new directory:
   ```bash
   mkdir -p modules/ec2 && cd modules/ec2
   ```
2. Create `main.tf`, `variables.tf`, and `outputs.tf`.

### 4.2 Step 2: Use the Module
1. In the root directory, create `main.tf`:
   ```hcl
   module "ec2_instance" {
     source        = "./modules/ec2"
     instance_type = "t3.micro"
   }
   ```
2. Initialize and apply:
   ```bash
   terraform init
   terraform apply
   ```

---

## 5. Best Practices

### 5.1 Keep Modules Simple
- Focus on a **single responsibility** per module.

### 5.2 Use Meaningful Outputs
- Expose useful attributes like IP addresses or resource IDs.

### 5.3 Version Control Modules
- Store modules in a Git repository for better collaboration.

### 5.4 Document Module Usage
- Use `README.md` to explain input variables and outputs.

---

## 6. Summary and Conclusion

### 6.1 Recap Key Points
- Modules simplify **reuse, maintainability, and collaboration** in Terraform.
- They contain **main.tf, variables.tf, and outputs.tf**.
- Modules can be **local or sourced from Terraform Registry**.
