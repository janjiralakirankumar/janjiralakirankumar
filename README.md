# AWS Resources Setup:

### Prerequisites:
- An AWS account with the necessary permissions.
- AWS CLI installed and configured on your local machine (to manage AWS credentials).
- Basic knowledge of AWS concepts like instances, security groups, and key pairs.
- Familiarity with the concept of Infrastructure as Code (IaC) and Terraform.

### Step-by-Step Guide:

### Task 1.1: Manually Launch One EC2 Instance in AWS and Install Terraform on It

1. **Follow the Specific Configurations.:**

    - Log in to AWS Console:
    - **Region:** Choose `North Virginia (us-east-1)` as your AWS region.
    - **Instance Name and Tags:** Give your instance a name like `"YourName-DevOps-Server."`
    - **Application and OS Images (Amazon Machine Image):** Select `Ubuntu-22.04` as the operating system.
    - **Instance Type:** Pick `"t2.micro"` as it's part of the free tier.
    - Click on `"Create a New KeyPair"` and give the Key pair name as `YourName-DevOps-KeyPair`
    - **Network Settings:** `Edit` >> In "Security group name" Enter `"YourName-DevOps-SecurityGroup` and allow ports `22 (SSH),` `80 (HTTP),` and `8080 (Custom)` for web traffic.
    - **Configure storage:** Set storage to `10 GiB.`
    - Click on `Launch Instance.`

2. **SSH into Your EC2 Instance:**

    - Use an SSH client `(like PuTTY or MobaXterm)` and the `KeyPair` you created.
    - Username as `ubuntu` 

3. **Install Terraform:**
  
    ```shell
    sudo hostnamectl set-hostname DevOps-Server
    bash
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
    terraform
    ```
    ```shell
    terraform -v
    ```

### Task 1.2: Install Required Packages.

- Install Python package manager (pip) and AWS CLI:

```shell
sudo apt-get install python3-pip -y
```

```shell
sudo pip3 install awscli
```

### Task 1.3: Configure AWS CLI for AWS Account Access

**To configure AWS CLI, please follow these steps::**

1.  Type `aws configure` and press enter.
2.  Provide `AWS Access Key ID`, `AWS Secret Access Key`.
3.  Press Enter for `default region name (us-east-1)`, and
4.  Press Enter for `default output format (json)`.

```shell
aws configure
```

### Note: If Credentials are not available, generate them from AWS IAM Service.

Once logged in, check your account access in the terminal with the following command:

```shell
aws s3 ls
```

or

```shell
aws iam list-users
```

Now you are authenticated to create resources in your AWS account.

---
**Step 2: Create a Working Directory**

Create a dedicated directory on your computer where you'll organize your Terraform configuration files. For example, create a folder called "terraform-aws-setup."

```bash
mkdir terraform-aws-setup
cd terraform-aws-setup
```

**Step 3: Write Terraform Configuration**

Copy and paste the Terraform configuration you provided earlier into a file named `main.tf` within your working directory.

```bash
vi aws-main.tf
```

Paste the Terraform configuration inside it.
```
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

# Create an S3 bucket
resource "aws_s3_bucket" "example_bucket" {
  bucket = "your-unique-bucket-name"  # Replace with a unique bucket name
  acl    = "private"  # Adjust ACL settings as needed
}
```
**Step 4: Initialize the Terraform Project**

Now, initialize your Terraform project. This step downloads the necessary provider plugins and sets up your working directory for Terraform.

```bash
terraform init
```

**Step 5: Review the Execution Plan**

Before making any changes to your AWS resources, it's a good practice to review the execution plan to understand what Terraform will do.

```bash
terraform plan
```

This command will provide a summary of the actions Terraform plans to take based on your configuration. Carefully review this plan to ensure it aligns with your intentions.

**Step 6: Apply the Configuration**

Once you're satisfied with the plan, you can apply the Terraform configuration to create AWS resources.

```bash
terraform apply
```

Terraform will display a list of the changes it intends to make. If everything looks correct, type "yes" when prompted to confirm. Terraform will then proceed to create the specified AWS resources.

**Step 7: Verify Resources**

After Terraform has successfully created the AWS resources, you can verify their existence by checking the AWS Management Console or by using AWS CLI commands.

For example, to list your EC2 instances, you can run:

```bash
aws ec2 describe-instances
```

**Step 8: Optional - Destroy Resources**

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
