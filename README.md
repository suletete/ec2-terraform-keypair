
# Mini Project: Terraform EC2 Instance with Key Pair and User Data

---

## 🎯 Purpose

In this mini project, you will use Terraform to automate the launch of an EC2 instance on AWS. The project includes:

- Generation of a **downloadable key pair**.
- Execution of a **user data script** to install and configure **Apache HTTP server**.

---

## 🧠 Objectives

### ✅ Terraform Configuration
- Learn how to write Terraform code to launch an EC2 instance with specified configurations.

### ✅ Key Pair Generation
- Generate a key pair and make it downloadable after EC2 instance creation.

### ✅ User Data Execution
- Use Terraform to execute a user data script on the EC2 instance during launch.

---

## 🛠 Project Tasks

### Task 1: Terraform Configuration for EC2 Instance

1. **Create a new project directory:**

   ```bash
   mkdir terraform-ec2-keypair
   cd terraform-ec2-keypair
   ```

2. **Create the Terraform configuration file:**

   ```bash
   nano main.tf
   ```

3. **Add the following Terraform configuration:**

   > ⚠️ Replace the values of AMI ID, SSH public key path, and security group ID as per your environment.

   ```hcl
   provider "aws" {
     region = "us-east-1"  # Change this to your desired AWS region
   }

   resource "aws_key_pair" "example_keypair" {
     key_name   = "example-keypair"
     public_key = file("~/.ssh/id_rsa.pub")  # Replace with the path to your public key file
   }

   resource "aws_instance" "example_instance" {
     ami           = "ami-0c55b159cbfafe1f0"  # Specify your desired AMI ID
     instance_type = "t2.micro"
     key_name      = aws_key_pair.example_keypair.key_name

     vpc_security_group_ids = ["sg-0123456789abcdef0"]  # Replace with your security group ID

     user_data = <<-EOF
                 #!/bin/bash
                 yum update -y
                 yum install -y httpd
                 systemctl start httpd
                 systemctl enable httpd
                 echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
                 EOF
   }

   output "public_ip" {
     value = aws_instance.example_instance.public_ip
   }
   ```

4. **Initialize Terraform:**

   ```bash
   terraform init
   ```

5. **Apply the configuration:**

   ```bash
   terraform apply
   ```

---

### Task 2: User Data Script Execution

- The `user_data` section in the `aws_instance` resource runs a script that:
  - Installs Apache HTTP Server (`httpd`)
  - Starts and enables the service
  - Creates a "Hello World" HTML file

---

### Task 3: Accessing the Web Server

1. After successful provisioning, Terraform will output the public IP of the EC2 instance.

2. Open a browser and enter:

   ```
   http://<public_ip>
   ```

3. You should see a message like:

   ```
   Hello World from ip-172-31-xx-xx.ec2.internal
   ```

---

## 📝 Documentation

### 🔍 Observations

- Apache server installed and started successfully.
- EC2 instance was accessible via browser using public IP.
- The key pair was created and used for SSH access.

### ⚠️ Challenges Faced

- Needed to replace the AMI ID with one available in the selected AWS region.
- Had to ensure the security group allowed inbound traffic on port 80 (HTTP).
- SSH key needed to exist locally or be generated using `ssh-keygen`.

---

## 📌 Side Notes

- Ensure you have AWS CLI installed and configured correctly:

  ```bash
  aws configure
  ```

- The AMI ID (`ami-0c55b159cbfafe1f0`) is just a sample. Use a suitable one for your AWS region (Amazon Linux 2 is recommended).

- Make sure the security group you specify allows **HTTP (port 80)** inbound traffic.

- This project is a hands-on **learning exercise** designed to teach infrastructure automation using Terraform.

---

**End of Project**
```

