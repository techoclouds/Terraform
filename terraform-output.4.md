# Terraform Hands-On Lab: Understanding Outputs

## 1. What are Outputs in Terraform?

Terraform **outputs** are used to **display important resource information** after applying Terraform.

### Why Do We Need Outputs?
🔹 **Outputs help retrieve values** of deployed resources without manually checking the cloud provider’s console.  
🔹 **Outputs make automation easier** (values can be used in scripts or referenced in another Terraform module).  
🔹 **Outputs improve visibility** by showing essential information like:  
   - EC2 Instance **Public IP**  
   - S3 Bucket **Name**  
   - Database **Endpoint**  

### Example Without Outputs (Hard to Find Values)
If we deploy an EC2 instance **without outputs**, we would have to:  
1️⃣ Log into AWS Console  
2️⃣ Go to EC2 Dashboard  
3️⃣ Find the instance manually  
4️⃣ Copy its Public IP  

❌ **Time-consuming and not scalable**  

✅ **With Terraform Outputs:**  
- Terraform will display the **Public IP** directly after deployment.  
- No need to log into AWS Console.  

---

## 2. Defining Outputs in Terraform

### Step 1: Create a Terraform Project Directory
```bash
mkdir terraform-outputs-demo && cd terraform-outputs-demo
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
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
}
```

### Step 3: Define an Output for EC2 Public IP
Create an `outputs.tf` file:
```bash
touch outputs.tf
vim outputs.tf
```
Paste:
```hcl
output "instance_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.my_ec2.public_ip
}

output "instance_id" {
  description = "The ID of the EC2 instance"
  value       = aws_instance.my_ec2.id
}
```
✅ **Explanation:**  
- `instance_public_ip` → Displays the **Public IP** of the EC2 instance.  
- `instance_id` → Displays the **EC2 instance ID**.  

---

## 3. Applying Terraform and Viewing Outputs

### Step 1: Initialize Terraform
```bash
terraform init
```
✅ This installs the necessary provider plugins.

### Step 2: Apply Terraform Configuration
```bash
terraform apply
```
Terraform will ask for confirmation. Type **`yes`** and press Enter.

Example output:
```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:
instance_public_ip = "54.210.123.45"
instance_id = "i-0a1b2c3d4e5f67890"
```

✅ **Now, you can use this public IP to connect to your EC2 instance directly!**  

---

## 4. Where Do Output Values Come From?

### How Do We Know `aws_instance.my_ec2.id` Will Return the Instance ID?

Terraform provides **resource output attributes** for every resource type. These attributes define **what values a resource can return** after being created.

### Where to Find Output Attributes?
👉 **Check the official Terraform documentation:**  
🔗 [AWS Instance Resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance)

### Example: AWS Instance Attributes
| Attribute Name | Description |
|--------------|-------------|
| `id` | The ID of the instance |
| `public_ip` | The public IPv4 address assigned to the instance |
| `private_ip` | The private IPv4 address of the instance |
| `public_dns` | The public DNS name assigned to the instance |
| `availability_zone` | The Availability Zone where the instance is launched |

📌 **From the documentation, we can see that `id` is an available output attribute of `aws_instance`.**  

---

## 5. Using `terraform state show` to See Available Attributes

If you're unsure about what attributes are available, use the following command:

```bash
terraform state show aws_instance.my_ec2
```

👀 **Example Output:**
```
id                  = "i-0a1b2c3d4e5f67890"
public_ip           = "54.210.123.45"
private_ip          = "10.0.1.5"
availability_zone   = "us-east-1a"
```

✅ **Now, you can confirm the values Terraform stores and retrieves.**

---

## 6. Defining Multiple Outputs for Different Resources

### Example: Display EC2 Instance Public DNS and Availability Zone
```hcl
output "instance_public_dns" {
  description = "Public DNS of the EC2 instance"
  value       = aws_instance.my_ec2.public_dns
}

output "availability_zone" {
  description = "Availability Zone of the EC2 instance"
  value       = aws_instance.my_ec2.availability_zone
}
```

✅ **Terraform will display all these values automatically after deployment.**  

---

## 7. Hiding Sensitive Output Data

Some outputs (e.g., passwords, API keys) should **not** be displayed in logs.

### Example: Marking an Output as Sensitive
```hcl
output "db_password" {
  description = "Database password"
  value       = "SuperSecretPassword123!"
  sensitive   = true
}
```
✅ **Terraform will hide this value in the CLI output.**  

Example output:
```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:
db_password = <sensitive>
```

🔒 **To retrieve the value later:**
```bash
terraform output db_password
```

---

## 8. Using Outputs in Scripts

Fetch Terraform outputs dynamically in a shell script:

```bash
terraform output instance_public_ip
```
or store it in a variable:
```bash
EC2_IP=$(terraform output -raw instance_public_ip)
echo "The EC2 instance IP is: $EC2_IP"
```

✅ **This is useful for automation scripts.**  

---

## 9. Destroying Resources

To clean up:
```bash
terraform destroy
```

---

## Lab Summary
✅ Defined Outputs in Terraform  
✅ Displayed EC2 Instance Public IP, ID, and Availability Zone  
✅ Used Outputs to Pass Values Between Modules  
✅ Marked Sensitive Outputs for Security  
✅ Used Outputs in Shell Scripts for Automation  
✅ Verified Terraform State and Attributes  

---

