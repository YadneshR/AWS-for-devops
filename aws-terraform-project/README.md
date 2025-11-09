# AWS Terraform Project

This repository contains a fully automated Terraform configuration that builds a scalable, load balanced, two-tier web architecture on AWS. The setup includes a custom VPC, public subnets, routing, an internet gateway, two EC2 web servers, an Application Load Balancer, a target group, health checks, and an S3 bucket.

---
![AWS Terraform Infra](https://github.com/user-attachments/assets/0abb17ae-3b89-4407-bd5c-9aa77077eade)

## Overview

This project provisions the following infrastructure components:

* **VPC** with a configurable CIDR block
* **Two public subnets** across different Availability Zones
* **Internet Gateway** plus routing for external access
* **Security group** allowing HTTP and SSH
* **Two EC2 instances** for hosting the web application
* **User-data scripts** for automated Apache installation and configuration
* **Application Load Balancer (ALB)** for traffic distribution
* **Target Group** with health checks
* **Target Group Attachments** linking EC2 instances
* **S3 bucket** for storage
* Output variable returning the **ALB DNS name**

This setup demonstrates a standard AWS architecture deployed via Terraform using Infrastructure as Code.

---

## Detailed Component Breakdown

### 1. VPC

Defines the project's isolated virtual network.

* **Resource**: `aws_vpc.myvpc`
* **Parameter**: `cidr_block = var.cidr`
* Provides foundational networking for all components.

### 2. Subnets

Two public subnets in different AZs for high availability.

* `sub1` (10.0.0.0/24) in **us-east-1a**
* `sub2` (10.0.1.0/24) in **us-east-1b**
* Public IP mapping on launch enabled

### 3. Internet Gateway

* Allows traffic from VPC to the internet
* Resource: `aws_internet_gateway.igw`

### 4. Route Table + Associations

Routes all outbound traffic through the internet gateway.

* Adds `0.0.0.0/0` to IGW
* Associates the route table with both subnets

### 5. Security Group

Resource: `aws_security_group.webSg`

Allows:

* **HTTP (80)** from anywhere
* **SSH (22)** from anywhere
* All outbound traffic

Used by EC2 instances and the ALB.

### 6. S3 Bucket

Creates an example S3 bucket for general use.

* Bucket name: `yadnesh-terraform-2025-project`

### 7. EC2 Instances (Web Servers)

Resources: `aws_instance.webserver1` and `aws_instance.webserver2`

Key features:

* AMI: `ami-0ecb62995f68bb549`
* Instance type: **t2.nano**
* Each instance is placed in a different subnet
* Security group applied
* Uses **Base64-encoded user-data scripts** for automatic Apache installation and serving content

### 8. Application Load Balancer (ALB)

Resource: `aws_lb.myalb`

* Internet-facing
* Type: **application**
* Subnets: both public subnets
* Security group applied

### 9. Target Group

Resource: `aws_lb_target_group.tg`

* Port: **80**
* Protocol: HTTP
* Health check:

  * Path `/`
  * `traffic-port`

### 10. Target Group Attachments

Links EC2 instances to the target group.

### 11. Load Balancer Listener

Resource: `aws_lb_listener.listener`

* Listens on **port 80**
* Forwards all traffic to the target group

### 12. Outputs

Returns the DNS of the ALB for direct access.

* Output: `loadbalancerdns`

---

## Prerequisites

Before running this Terraform project, ensure you have the following installed and configured:

**1. AWS Account**

* A valid AWS account with permissions to create VPCs, subnets, EC2 instances, ALBs, and S3 buckets.

**2. AWS CLI Installed**

* Install AWS CLI and configure it using:

```
aws configure
```

* Provide Access Key, Secret Key, region (us-east-1), and output format (json).

**3. Terraform Installed**

* Install Terraform (v1.0 or above recommended):

```
terraform -v
```

Confirm it prints a valid version.

**4. SSH Key Pair (Optional if you plan to SSH)**

* Create an AWS EC2 key pair if you want to manually SSH into instances.

**5. User Data Scripts Present in Project Directory**

* Ensure `userdata.sh` and `userdata1.sh` exist and are correctly configured to install Apache.
* Both files must be in the root of the same Terraform folder.

**6. Internet Access for Package Installation**

* Since EC2 installs Apache via user-data, instances must have outbound internet via IGW.

**7. Correct IAM Permissions for Terraform User**
Terraform user must have the following permissions:

* EC2 Full Access
* VPC Full Access
* ELB Full Access
* IAM PassRole (if roles are used)
* S3 Full Access

---

## How to Use

### Initialize Terraform

```
terraform init
```

### Validate the configuration

```
terraform validate
```

### Apply the infrastructure

```
terraform apply
```

Enter `yes` when prompted.

Once provisioning completes, the output will display:

```
loadbalancerdns = <ALB-DNS-NAME>
```

Open this DNS in a browser to access the deployed website.

### Destroy the infrastructure

```
terraform destroy
```

---

## File Structure

```
├── main.tf
├── variables.tf
├── userdata.sh
├── userdata1.sh
├── README.md
```

---

## Notes

* Ensure the user-data scripts install Apache and place an `index.html` under `/var/www/html/`.
* The ALB health check requires that each instance responds correctly to `HTTP GET /`.
* Use proper IAM credentials (`aws configure`) before running Terraform.

---

## Author
Yadnesh Raut
