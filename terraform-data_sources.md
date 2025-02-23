# Chapter 2: Data Sources in Terraform

## 1. Introduction to Data Sources

**What Are Data Sources?**  
Data sources in Terraform allow you to fetch and use information from external systems or from resources already existing in your environment. Unlike resource blocks that create new infrastructure, data sources query and retrieve data that can be used to configure your resources.

**Why Use Data Sources?**  
- **Dynamic Configuration:** Retrieve current values such as the latest AMI ID or existing VPC details.
- **Consistency:** Ensure that your configuration is based on up-to-date information from your cloud provider.
- **Interoperability:** Integrate data from resources managed outside of your current Terraform configuration.

**When to Use Data Sources:**  
- Fetching the latest images (like AMIs for AWS).
- Retrieving configuration details of existing infrastructure.
- Integrating with third-party systems or APIs for dynamic configuration.

---

## 2. Understanding Filters in Data Sources

Filters are used within data source blocks to narrow down the results returned by the provider. They work by specifying:  
- **Name:** The attribute to filter on.  
- **Values:** A list of acceptable values for that attribute.

For example, when querying for an AMI, you might filter on the `name` attribute to match a pattern like `amzn2-ami-hvm-*-x86_64-ebs`. This tells Terraform to consider only AMIs whose names match that pattern. Filters help ensure that the data source returns exactly the data you need, even when multiple resources exist.

---

## 3. Hands-On Lab Examples

### **Example 1: Querying AMIs with Multiple Filters**

1. **Create a Project Directory**
   ```bash
   mkdir terraform-data-amis && cd terraform-data-amis
   ```

2. **Define the Terraform Configuration**

   Create a file named `main.tf`:
   
   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   # Data source for the latest Amazon Linux 2 AMI
   data "aws_ami" "amazon_linux" {
     most_recent = true
     owners      = ["amazon"]

     filter {
       name   = "name"
       values = ["amzn2-ami-hvm-*-x86_64-ebs"]
     }
   }

   # Data source for the latest Ubuntu 20.04 LTS AMI
   data "aws_ami" "ubuntu_20" {
     most_recent = true
     owners      = ["099720109477"]  # Official owner ID for Canonical

     filter {
       name   = "name"
       values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
     }

     filter {
       name   = "virtualization-type"
       values = ["hvm"]
     }
   }

   resource "aws_instance" "demo_amazon" {
     ami           = data.aws_ami.amazon_linux.id
     instance_type = "t2.micro"

     tags = {
       Name = "AmazonLinuxInstance"
     }
   }

   resource "aws_instance" "demo_ubuntu" {
     ami           = data.aws_ami.ubuntu_20.id
     instance_type = "t2.micro"

     tags = {
       Name = "Ubuntu20Instance"
     }
   }

   output "amazon_linux_ami_id" {
     value = data.aws_ami.amazon_linux.id
   }

   output "ubuntu_20_ami_id" {
     value = data.aws_ami.ubuntu_20.id
   }
   ```

3. **Initialize and Apply**
   ```bash
   terraform init
   terraform apply
   ```

---

### **Example 2: Using a Data Source to Retrieve an Existing VPC**

1. **Create a Project Directory**
   ```bash
   mkdir terraform-data-vpc && cd terraform-data-vpc
   ```

2. **Define the Terraform Configuration**
   
   Create a file named `main.tf`:
   
   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   data "aws_vpc" "default" {
     default = true
   }

   output "vpc_id" {
     value = data.aws_vpc.default.id
   }
   ```

3. **Initialize and Apply**
   ```bash
   terraform init
   terraform apply
   ```

---

### **Example 3: Retrieving Available Availability Zones**

1. **Create a Project Directory**
   ```bash
   mkdir terraform-data-azs && cd terraform-data-azs
   ```

2. **Define the Terraform Configuration**
   
   Create a file named `main.tf`:
   
   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   data "aws_availability_zones" "available" {
     state = "available"
   }

   output "azs" {
     value = data.aws_availability_zones.available.names
   }
   ```

3. **Initialize and Apply**
   ```bash
   terraform init
   terraform apply
   ```

---

## 4. Lab Summary

- **Understanding Data Sources:**  
  Data sources allow you to query external systems or existing resources, enabling dynamic and up-to-date configurations.

- **Understanding Filters:**  
  Filters help narrow down the results returned by data sources. They are defined by specifying a field name and one or more acceptable values, ensuring you retrieve only the resources that match your criteria.

- **Example Applications:**  
  - **AMI Query:** Retrieves the latest Amazon Linux 2 and Ubuntu 20.04 LTS AMI IDs using filters to match name patterns and virtualization type.
  - **VPC and Subnet Retrieval:** Fetches details of the default VPC and its subnets.
  - **Availability Zones:** Lists available zones to assist with resource placement.

- **Key Benefits:**  
  - Improved automation and configuration accuracy.
  - Reduction in manual updates as external data changes.
  - Enhanced integration with both internal and external systems.
