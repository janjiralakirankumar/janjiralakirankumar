# Set the AWS provider configuration
provider "aws" {
  region = "us-east-1"  # Change this to your desired AWS region
}

# Create 3 EC2 instances for Kubernetes (Change instance details as needed)
resource "aws_instance" "kubernetes" {
  count         = 3
  ami           = "ami-0c55b159cbfafe1f0"  # Replace with your desired AMI
  instance_type = "t2.micro"  # Adjust instance type as needed
  key_name      = "your-key-name"  # Replace with your SSH key name
  tags = {
    Name = "kubernetes-instance-${count.index + 1}"
  }
}

# Create an AWS VPN
resource "aws_vpn_gateway" "vpn" {
  # Configure VPN options as needed
  tags = {
    Name = "vpn-gateway"
  }
}

# Create an EC2 instance for Terraform (Change instance details as needed)
resource "aws_instance" "terraform_instance" {
  ami           = "ami-0c55b159cbfafe1f0"  # Replace with your desired AMI
  instance_type = "t2.large"  # Adjust instance type as needed
  key_name      = "your-key-name"  # Replace with your SSH key name
  tags = {
    Name = "terraform-instance"
  }
}

# Create an S3 bucket
resource "aws_s3_bucket" "example_bucket" {
  bucket = "your-unique-bucket-name"  # Replace with a unique bucket name
  acl    = "private"  # Adjust ACL settings as needed
}

---
# Explanation of Each Task


### Step 1: Set Up the AWS Provider

In Terraform, the provider specifies the cloud platform you're using. In this case, we're using AWS.

```hcl
provider "aws" {
  region = "us-east-1"  # Replace with your desired AWS region
}
```

- `provider "aws"`: This section specifies that we're using AWS as our cloud provider.
- `region = "us-east-1"`: Replace `"us-east-1"` with your preferred AWS region.

### Step 2: Create EC2 Instances for Kubernetes

We're going to create 3 EC2 instances for running Kubernetes. These are the virtual machines that will be part of your Kubernetes cluster.

```hcl
resource "aws_instance" "kubernetes" {
  count         = 3
  ami           = "ami-0c55b159cbfafe1f0"  # Replace with your desired AMI
  instance_type = "t2.micro"  # Adjust instance type as needed
  key_name      = "your-key-name"  # Replace with your SSH key name
  tags = {
    Name = "kubernetes-instance-${count.index + 1}"
  }
}
```

- `resource "aws_instance" "kubernetes"`: This declares an AWS resource block to create EC2 instances.
- `count = 3`: We're creating 3 instances using the `count` parameter.
- `ami`: Replace `"ami-0c55b159cbfafe1f0"` with the Amazon Machine Image (AMI) ID you want to use.
- `instance_type`: Set the instance type according to your requirements.
- `key_name`: Replace `"your-key-name"` with the name of your SSH key pair.

### Step 3: Create an AWS VPN

Now, let's create an AWS VPN Gateway.

```hcl
resource "aws_vpn_gateway" "vpn" {
  # Configure VPN options as needed
  tags = {
    Name = "vpn-gateway"
  }
}
```

- `resource "aws_vpn_gateway" "vpn"`: This declares an AWS VPN Gateway resource.
- `tags`: You can add tags to the VPN gateway for better organization.

### Step 4: Launch an EC2 Instance for Terraform

We'll create another EC2 instance, this time for running Terraform.

```hcl
resource "aws_instance" "terraform_instance" {
  ami           = "ami-0c55b159cbfafe1f0"  # Replace with your desired AMI
  instance_type = "t2.large"  # Adjust instance type as needed
  key_name      = "your-key-name"  # Replace with your SSH key name
  tags = {
    Name = "terraform-instance"
  }
}
```

- `resource "aws_instance" "terraform_instance"`: This declares an AWS resource block for the Terraform instance.
- `ami`, `instance_type`, and `key_name`: Similar to the Kubernetes instances, configure these based on your preferences.

### Step 5: Create an S3 Bucket

Finally, let's create an S3 bucket.

```hcl
resource "aws_s3_bucket" "example_bucket" {
  bucket = "your-unique-bucket-name"  # Replace with a unique bucket name
  acl    = "private"  # Adjust ACL settings as needed
}
```

- `resource "aws_s3_bucket" "example_bucket"`: This declares an AWS S3 bucket resource.
- `bucket`: Replace `"your-unique-bucket-name"` with a globally unique name for your S3 bucket.
- `acl`: Set the access control level as needed (e.g., "private" for maximum security).

Remember to replace placeholder values with your specific configurations, such as AMI IDs, key names, and bucket names. Also, ensure that the AWS region specified in the provider block matches your desired AWS region.

Once you've written your Terraform script, you can use the `terraform init`, `terraform plan`, and `terraform apply` commands to initialize the project, preview changes, and create the resources in your AWS account, respectively.
