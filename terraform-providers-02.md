# Terraform Hands-On Lab: Understanding Providers (Basic Level)

## 1. What is a Provider?

A **provider** in Terraform is a plugin that allows Terraform to interact with cloud platforms (AWS, Azure, GCP, etc.).

- Terraform needs a provider to know **which cloud** it should manage.
- Each provider has specific configurations (e.g., AWS requires credentials, region, etc.).

### Example Providers in Terraform
- `aws` → Manages AWS resources
- `azurerm` → Manages Azure resources
- `google` → Manages Google Cloud resources

---

## 2. Configuring the AWS Provider

### Step 1: Create a Terraform Project Directory
```bash
mkdir terraform-provider-demo && cd terraform-provider-demo
```

### Step 2: Create a Terraform Configuration File
```bash
touch main.tf
vim main.tf
```
Paste the following code into `main.tf`:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

✅ **Explanation:**  
- `provider "aws"` tells Terraform to use AWS.
- `region = "us-east-1"` sets the AWS region where resources will be created.

---

## 3. Authenticating with AWS

Terraform needs AWS credentials to access your AWS account. There are **two simple ways** to provide them:

### Option 1: Using Environment Variables (Recommended)
Set AWS credentials as environment variables:
```bash
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
```

### Option 2: Using AWS Credentials File
If you have AWS CLI installed, you can configure credentials with:
```bash
aws configure
```
It saves credentials in `~/.aws/credentials`.

---

## 4. Initializing Terraform & Installing the AWS Provider
```bash
terraform init
```

✅ **What happens in `terraform init`?**  
- Downloads the AWS provider plugin.
- Prepares Terraform to manage AWS resources.

Expected output:
```
Initializing provider plugins...
- Installing hashicorp/aws v5.x.x...
```

---

## 5. Applying and Testing the Configuration
Since this configuration doesn’t create any resources yet, Terraform will just verify the provider setup.

```bash
terraform plan
```
If everything is correct, it should show **"No changes. Your infrastructure matches the configuration."**

---

## 6. Destroying the Setup (If Needed)
```bash
terraform destroy
```
This is used when you have resources and want to clean them up.

---

## Lab Summary
✅ Configured AWS Provider  
✅ Set up AWS Authentication  
✅ Initialized Terraform  
✅ Verified the provider setup  

---

