# Setting up HPE Hybrid Cloud on AWS with Terraform

**Prerequisites:**
- AWS account with permissions.
- Basic AWS knowledge.
- Familiarity with Infrastructure as Code (IaC) and Terraform.

**Task 1.1: Manual EC2 Instance Setup with Terraform**

1. Launch EC2 Instance:
    - AWS Console: Region = `us-east-1`
    - Name: `"terraform-aws"`
    - OS: `Ubuntu 22.04`
    - Type: `t2.large`
    - KeyPair: `terraform-aws-KeyPair`
    - New Security Group Name: `terraform-aws-sg` and Allow ports `22 (SSH)` and `80 (HTTP)`.
    - Storage: `12 GiB`

2. SSH into Instance:
    - Use SSH client with KeyPair.
    - Username: `ubuntu`

3. Install Terraform:
    ```shell
    sudo hostnamectl set-hostname terraform-aws
    ```
    ```shell
    sudo apt update
    ```
    ```shell
    sudo apt install wget unzip -y
    ```
    ```shell
    wget https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip
    ```
    ```shell
    unzip terraform_1.5.7_linux_amd64.zip
    ```
    ```shell
    rm terraform_1.5.7_linux_amd64.zip
    ```
    ```shell
    sudo mv terraform /usr/local/bin
    ```
    ```shell
    terraform -v
    ```

**Task 1.2: Install Required Packages**

Install pip and AWS CLI:
```shell
sudo apt-get install python3-pip -y
```
```shell
sudo pip3 install awscli
```

**Task 1.3: Configure AWS CLI**

Configure AWS CLI:
```shell
aws configure
```
Provide Credentials like `Access Key` and `Secret Access Key`

**Step 2: Create a Working Directory**

```bash
mkdir terraform-aws
cd terraform-aws
```

**Step 3: Write Terraform Configuration**

Create `main.tf` and add Terraform configuration.

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
resource "aws_s3_bucket" "example_bucket" {
  bucket = "aws-modules-bucket"  # Replace with a unique bucket name
  acl    = "private"  # Adjust ACL settings as needed
}

```
**Step 4: Initialize Terraform Project**

Initialize the project:
```bash
terraform init
```

**Step 5: Review Execution Plan**

Review what Terraform will do:
```bash
terraform plan
```

**Step 6: Apply Configuration**

Apply the configuration:
```bash
terraform apply
```

**Step 7: Verify Resources**

Check AWS resources:
```bash
aws ec2 describe-instances
```

**Step 8: Optional - Destroy Resources** (If the resources are not needed.)

If you no longer need the AWS resources and want to clean up, you can use Terraform to destroy them:

```bash
terraform destroy
```

Be cautious when using this command, as it will delete the specified resources. Confirm by typing "yes" when prompted.

**Step 9: Cleanup**

After destroying the resources (if needed), it's important to remove your Terraform state files. Run the following commands:

```bash
terraform state rm aws_instance.kubernetes
terraform state rm aws_instance.terraform_instance
terraform state rm aws_vpn_gateway.vpn
terraform state rm aws_s3_bucket.example_bucket
```

Replace the resource names as needed. This removes the resources from the Terraform state. Afterward, you can safely delete your Terraform working directory.

---
