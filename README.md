# Setting up HPE Hybrid Cloud on AWS with Terraform

**Prerequisites:**
- AWS account with permissions.
- Basic AWS knowledge.
- Familiarity with Infrastructure as Code (IaC) and Terraform.

**Task 1: Manual EC2 Instance Setup with Terraform**

1. Launch EC2 Instance:
    - AWS Console: Region = `us-east-1`
    - Name: `"terraform-aws"`
    - OS: `Ubuntu 22.04`
    - Type: `t2.large`
    - KeyPair: `terraform-aws-KeyPair`
    - New Security Group Name: `terraform-aws-sg` and Allow ports `22 (SSH)` and `80 (HTTP)`.
    - Storage: `12 GiB`
    - To Directly Install Terraform on to Server using Userdata, Copy and Paste it in Userdata Section.

```
#!/bin/bash

# Change the HostName
sudo hostnamectl set-hostname terraform-aws

# Update and upgrade packages, install AWS CLI, and Terraform
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install python3-pip -y
sudo apt-get install -y awscli

# Install the latest version of Terraform
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update -y
sudo apt-get install -y terraform

# Verify installations
aws --version
terraform version
```
**Task 2: Configure AWS CLI**

Configure AWS CLI:
```shell
aws configure
```
Provide Credentials like `Access Key` and `Secret Access Key`

```shell
aws s3 ls
```

**Task 2: Create a Working Directory**

```bash
mkdir terraform-aws
cd terraform-aws
```

**Task 3: Write Terraform Configuration**

Create `main.tf` and add Terraform configuration.

```shell
vi main.tf
```

```bash
# Set the AWS provider configuration
provider "aws" {
  region = "us-east-1"  # Change this to your desired AWS region
}

# Create 3 EC2 instances for Kubernetes (Change instance details as needed)
resource "aws_instance" "kubernetes" {
  count         = 3
  ami           = "ami-053b0d53c279acc90"  # Replace with your desired AMI
  instance_type = "t2.large"  # Adjust instance type as needed
  key_name      = "terraform-aws-KeyPair"  # Replace with your SSH key name
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

# Create an S3 bucket
resource "aws_s3_bucket" "example" {
  bucket = "my-tf-example-bucket"
}

resource "aws_s3_bucket_ownership_controls" "example" {
  bucket = aws_s3_bucket.example.id
  rule {
    object_ownership = "BucketOwnerPreferred"
  }
}

resource "aws_s3_bucket_public_access_block" "example" {
  bucket = aws_s3_bucket.example.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

resource "aws_s3_bucket_acl" "example" {
  depends_on = [
    aws_s3_bucket_ownership_controls.example,
    aws_s3_bucket_public_access_block.example,
  ]

  bucket = aws_s3_bucket.example.id
  acl    = "public-read"
}

```
**Initialize Terraform Project**

```bash
terraform init
```

```bash
terraform plan
```

```bash
terraform apply
```

```bash
aws ec2 describe-instances
```
---

**Task 7: Optional - Destroy Resources** (If the resources are not needed.)

If you no longer need the AWS resources and want to clean up, you can use Terraform to destroy them:

```bash
terraform destroy
```

Be cautious when using this command, as it will delete the specified resources. Confirm by typing "yes" when prompted.

**Task 8: Cleanup**

After destroying the resources (if needed), it's important to remove your Terraform state files. Run the following commands:

```bash
terraform state rm aws_instance.kubernetes
terraform state rm aws_instance.terraform_instance
terraform state rm aws_vpn_gateway.vpn
terraform state rm aws_s3_bucket.example_bucket
```

Replace the resource names as needed. This removes the resources from the Terraform state. Afterward, you can safely delete your Terraform working directory.

---
