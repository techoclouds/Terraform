# Chapter 7: For Expressions and Conditional Logic in Terraform

## 1. Introduction

### 1.1 What You Will Learn
- **Dynamically Generate and Filter Lists and Maps:**  
  Learn how to use for expressions to iterate over collections and transform data into new lists or maps.
- **Simplify Resource Configuration with Conditionals:**  
  Use conditional expressions to select values based on logic, reducing the need for hardcoding.
- **Combine Techniques for Cleaner Code:**  
  Integrate for expressions and conditionals to write concise, dynamic, and maintainable Terraform configurations.

### 1.2 Why For Expressions and Conditionals Matter
- **Avoid Repetition:**  
  Eliminate the need to manually duplicate configuration blocks.
- **Data-Driven Infrastructure:**  
  Automatically derive configuration values from input data.
- **Improved Maintainability and Scalability:**  
  Dynamic code adapts more easily to changes in requirements or data inputs.

---

## 2. Fundamentals of For Expressions

For expressions in Terraform let you iterate over lists and maps to create new collections. They help transform your input data without writing verbose loops manually.

### 2.1 Basic Syntax for Lists

The basic syntax for a for expression over a list is:

```hcl
[ for item in list : <transformed_value> ]
```

- **`for item in list`**: Iterates over each element in the list.
- **`<transformed_value>`**: The expression to compute for each element.

#### **Example 1: Multiply All Values in a List by 2**

```hcl
variable "numbers" {
  description = "A list of numbers"
  type        = list(number)
  default     = [1, 2, 3, 4]
}

locals {
  # For each number in the list, multiply it by 2.
  doubled = [for n in var.numbers : n * 2]
}

output "doubled_numbers" {
  value = local.doubled  # Expected Output: [2, 4, 6, 8]
}
```

#### **Example 2: Convert a List of Names into Uppercase**

```hcl
variable "names" {
  description = "A list of names"
  type        = list(string)
  default     = ["alice", "bob", "carol"]
}

locals {
  upper_names = [for name in var.names : upper(name)]
}

output "upper_names" {
  value = local.upper_names  # Expected Output: ["ALICE", "BOB", "CAROL"]
}
```

#### **Example 3: Create Multiple Resources Based on a List**

```hcl
variable "availability_zones" {
  description = "List of Availability Zones"
  type        = list(string)
  default     = ["us-east-1a", "us-east-1b", "us-east-1c"]
}

resource "aws_subnet" "example" {
  # Using 'for_each' to create one subnet per availability zone.
  # 'for_each' accepts a set or map, so we convert the list to a set using 'toset()'.
  for_each = toset(var.availability_zones)
  
  vpc_id = "vpc-12345678"  # Example VPC ID
  
  # 'cidr_block' is generated dynamically.
  # The 'index()' function returns the position of the current element in the original list.
  # 'each.key' is the current availability zone value from the set.
  # By using 'index(var.availability_zones, each.key)', we get the index corresponding to that AZ.
  # This index is then interpolated into the CIDR block to generate a unique subnet range.
  cidr_block = "10.0.${index(var.availability_zones, each.key)}.0/24"
  
  # 'each.key' represents the current element of the set (the AZ name), and is used here directly.
  availability_zone = each.key
  
  /*
  Detailed Explanation:
  - The 'for_each' meta-argument allows us to iterate over each element in a collection (in this case, a set of availability zones).
  - Converting the list to a set with 'toset()' is required because 'for_each' expects a set or map.
  - 'each.key' is a built-in object available inside resource blocks that use 'for_each'. It holds the current key (or element) being processed.
  - The 'index()' function is used to determine the position of 'each.key' within the original list 'var.availability_zones'. This numeric index is then used to create a unique CIDR block for each subnet.
  - For instance, if "us-east-1a" is at index 0, the generated CIDR block will be "10.0.0.0/24". For "us-east-1b" at index 1, it will be "10.0.1.0/24", and so on.
  */
}


---

### 2.2 For Expressions with Maps

The syntax for iterating over maps is:

```hcl
{ for key, value in map : <new_key> => <new_value> }
```

#### **Example 1: Transforming a Map of Region Names into Standardized Tags**

```hcl
variable "regions" {
  description = "Map of region codes to region names"
  type        = map(string)
  default = {
    us_east_1 = "US East (N. Virginia)"
    us_west_2 = "US West (Oregon)"
  }
}

locals {
  region_tags = { for code, name in var.regions : code => format("Region: %s", name) }
}

output "region_tags" {
  # Expected Output: { "us_east_1" = "Region: US East (N. Virginia)", "us_west_2" = "Region: US West (Oregon)" }
  value = local.region_tags
}
```

#### **Example 2: Filtering Out Certain Keys Based on Conditions**

```hcl
variable "servers" {
  description = "Map of server roles to instance IDs"
  type        = map(string)
  default = {
    web   = "i-abc12345"
    db    = "i-def67890"
    cache = "unused"
  }
}

locals {
  active_servers = {
    for role, id in var.servers : role => id
    if id != "unused"
  }
}

output "active_servers" {
  # Expected Output: { "web" = "i-abc12345", "db" = "i-def67890" }
  value = local.active_servers
}
```

---

## 3. Fundamentals of Conditional Expressions

The syntax of a conditional expression is:

```hcl
condition ? true_value : false_value
```

#### **Example: Choosing an Instance Type Based on the Environment**

```hcl
variable "environment" {
  description = "Deployment environment"
  type        = string
  default     = "production"
}

locals {
  instance_type = var.environment == "production" ? "t3.medium" : "t2.micro"
}

output "selected_instance_type" {
  # Expected Output: "t3.medium" if production, otherwise "t2.micro"
  value = local.instance_type
}
```

---

## 4. Combining For Expressions and Conditional Logic

### **Filtering Lists with "if"**

```hcl
variable "numbers" {
  description = "A list of numbers"
  type        = list(number)
  default     = [1, 2, 3, 4, 5, 6]
}

locals {
  even_numbers = [for n in var.numbers : n if n % 2 == 0]
}

output "even_numbers" {
  # Expected Output: [2, 4, 6]
  value = local.even_numbers
}
```

---

## 5. Summary and Conclusion

- **For Expressions:** Iterate over and transform lists/maps dynamically.
- **Conditional Expressions:** Select values dynamically based on conditions.
- **Combined Power:** Write concise, flexible, and maintainable Terraform configurations.

By mastering these techniques, you can write scalable and dynamic Terraform configurations that adapt to changing inputs and requirements.
