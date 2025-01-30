# Terraform Hands-On Lab: Understanding Variables

## 1. Why Use Variables in Terraform?

Variables in Terraform help in making the infrastructure configuration **dynamic, reusable, and maintainable**.

### Problems with Hardcoded Values
If we write a Terraform script like this:
```hcl
resource "aws_instance" "my_ec2" {
  instance_type = "t2.micro"
  ami           = "ami-0abcdef1234567890"
}
```
❌ **Issues:**  
- Not reusable – every time we need a different instance type, we have to manually change the `.tf` file.  
- Hardcoded values make maintenance difficult.  

✅ **Solution:** Use Variables to **parametrize** values like instance type and AMI.

---

## 2. Defining Variables in Terraform

### Step 1: Create a New Terraform Project
```bash
mkdir terraform-variables-demo && cd terraform-variables-demo
```

### Step 2: Create a `main.tf` File
```bash
touch main.tf
vim main.tf
```
Paste the following code:
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2" {
  instance_type = var.instance_type
  ami           = var.ami
}
```
✅ **Explanation:**  
- `var.instance_type` → Refers to a variable named `instance_type`.  
- `var.ami` → Refers to a variable named `ami`.

---

## 3. Declaring Variables in a Separate File

Create a file `variables.tf`:
```bash
touch variables.tf
vim variables.tf
```
Paste the following code:
```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "ami" {
  description = "Amazon Machine Image ID"
  type        = string
}
```
✅ **Explanation:**  
- **`description`** → Helps understand the purpose of the variable.  
- **`type`** → Ensures only correct data type is used (string, number, etc.).  
- **`default`** → Provides a default value if none is supplied.

---

## 4. Passing Variables in Terraform
Terraform allows passing variable values in multiple ways:

### Option 1: Using Command Line Arguments
```bash
terraform apply -var "ami=ami-0abcdef1234567890"
```
✅ This overrides the value in `variables.tf`.

### Option 2: Using a `.tfvars` File
Create a file `terraform.tfvars`:
```bash
touch terraform.tfvars
vim terraform.tfvars
```
Paste:
```hcl
ami = "ami-0abcdef1234567890"
instance_type = "t3.micro"
```
Now apply Terraform without specifying variables manually:
```bash
terraform apply
```
✅ Terraform will automatically read `terraform.tfvars`.

### Option 3: Using Environment Variables
Set environment variables:
```bash
export TF_VAR_ami="ami-0abcdef1234567890"
export TF_VAR_instance_type="t3.micro"
terraform apply
```
✅ Terraform automatically picks up environment variables with the **`TF_VAR_`** prefix.

---

## 5. Data Types in Variables

Terraform supports multiple **data types**:

### 1️⃣ String Variables
```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

Usage:
```hcl
instance_type = var.instance_type
```
```bash
terraform apply -var "instance_type=t3.micro"
```

### 2️⃣ Number Variables
```hcl
variable "instance_count" {
  type    = number
  default = 2
}
```
Usage:
```hcl
count = var.instance_count
```
```bash
terraform apply -var "instance_count=3"
```

### 3️⃣ Boolean Variables
```hcl
variable "enable_monitoring" {
  type    = bool
  default = false
}
```
Usage:
```hcl
monitoring = var.enable_monitoring
```
```bash
terraform apply -var "enable_monitoring=true"
```

### 4️⃣ List Variables
```hcl
variable "availability_zones" {
  type    = list(string)
  default = ["us-east-1a", "us-east-1b"]
}
```
Usage:
```hcl
availability_zone = var.availability_zones[0]
```
```bash
terraform apply -var 'availability_zones=["us-east-1c", "us-east-1d"]'
```

### 5️⃣ Map Variables
```hcl
variable "instance_settings" {
  type = map(string)
  default = {
    instance_type = "t2.micro"
    ami           = "ami-0abcdef1234567890"
    region        = "us-east-1"
  }
}
```
Usage:
```hcl
instance_type = var.instance_settings["instance_type"]
```
```bash
terraform apply -var 'instance_settings={instance_type="t3.micro", ami="ami-0987654321", region="us-west-2"}'
```

---

## 6. Sensitive Variables & Security
To hide sensitive values:
```hcl
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```
✅ **Sensitive variables will not be displayed in CLI output.**

---

## 7. Initializing and Applying Terraform
Run:
```bash
terraform init
terraform plan
terraform apply
```
Terraform will ask for confirmation before applying changes.

---

## 8. Destroying Resources
```bash
terraform destroy
```

---

## Lab Summary
✅ Used Variables for Reusability  
✅ Passed Variables via CLI, `.tfvars`, and Environment Variables  
✅ Understood Data Types (String, Number, List, Map)  
✅ Secured Sensitive Variables  

---

### Next Step: Understanding Outputs in Terraform
Proceed to **Terraform Outputs** to learn how to display and use resource details dynamically.
