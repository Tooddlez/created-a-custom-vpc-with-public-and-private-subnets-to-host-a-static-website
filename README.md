# AWS Multi-Tier VPC and Serverless Website Project

This repository documents the process of building a secure, multi-tier cloud infrastructure on AWS. The project demonstrates core networking, compute, and serverless principles by creating a custom Virtual Private Cloud (VPC) and hosting a static website on S3.

## 🚀 Overview

The primary goal of this project is to build a foundational, real-world cloud architecture. This involves:

1.  **Network Isolation:** Creating a custom VPC to ensure a secure and isolated environment for resources.
2.  **Tiered Security:** Implementing a public subnet for internet-facing resources and a private subnet for protected backend resources, following security best practices.
3.  **Secure Access:** Launching an EC2 instance in the public subnet (acting as a bastion host/web server) and another in the private subnet (acting as a backend server), ensuring the private instance is not directly accessible from the internet.
4.  **Serverless Architecture:** Demonstrating a modern, cost-effective approach by deploying a static website using Amazon S3, independent of the EC2 compute infrastructure.

## ✨ Key Architectural Components

-   **Custom VPC:** A logically isolated network (`10.0.0.0/16`) to house all project resources.
-   **Public Subnet:** A subnet (`10.0.1.0/24`) with a route to the Internet Gateway, allowing resources to be reached from the public internet.
-   **Private Subnet:** A subnet (`10.0.2.0/24`) with a route to a NAT Gateway, allowing outbound internet access for updates without allowing inbound connections.
![VPC with a public subnet that can communicate with the internet and a private subnet that is firewalled but can get updates](https://github.com/Tooddlez/created-a-custom-vpc-with-public-and-private-subnets-to-host-a-static-website/blob/main/VPC%20with%20a%20public%20subnet%20that%20can%20communicate%20with%20the%20internet%20and%20a%20private%20subnet%20that%20is%20firewalled%20but%20can%20get%20updates..PNG)
-   **Internet Gateway (IGW):** The "front door" connecting the VPC to the internet.
-   **NAT Gateway:** Enables instances in the private subnet to initiate outbound traffic while remaining firewalled.
  ![NAT Gateaway](https://github.com/Tooddlez/created-a-custom-vpc-with-public-and-private-subnets-to-host-a-static-website/blob/main/NAT%20Gateaway.PNG)
-   **Route Tables:** Control the flow of traffic within the VPC, directing public traffic to the IGW and private traffic to the NAT Gateway.
-  ![Route Tables](https://github.com/Tooddlez/created-a-custom-vpc-with-public-and-private-subnets-to-host-a-static-website/blob/main/Route%20Table.PNG)
-.  **EC2 Instances:**
    -   **Public Instance:** A `t2.micro` instance in the public subnet, accessible via SSH and HTTP.
    -   **Private Instance:** A `t2.micro` instance in the private subnet, accessible only from the public instance.
-   **Amazon S3:** A bucket configured for static website hosting, serving a simple HTML page.

## ⚙️ Technologies & Services Used

-   **AWS VPC (Virtual Private Cloud)**
-   **AWS EC2 (Elastic Compute Cloud)**
-   **AWS S3 (Simple Storage Service)**
-   **AWS IAM (Identity and Access Management)** (for security groups)
-   **Amazon Linux 2**
-   **Bash/Shell Scripting**

## 📋 Step-by-Step Implementation

The project was implemented in the following sequence:

### Phase 1: Network Foundation
1.  A custom VPC was created.
2.  Public and private subnets were provisioned, each in the same Availability Zone.
3.  An Internet Gateway was created and attached to the VPC.
4.  A public route table was created with a route `0.0.0.0/0` pointing to the IGW and associated with the public subnet.
5.  An Elastic IP and a NAT Gateway were created in the public subnet.
6.  The main (private) route table was configured with a route `0.0.0.0/0` pointing to the NAT Gateway.

### Phase 2: Compute Layer
1.  Two security groups were created:
    -   `public-sg`: Allowing inbound SSH (from My IP) and HTTP (from Anywhere).
    -   `private-sg`: Allowing inbound SSH only from the `public-sg`.
2.  The public EC2 instance was launched in the public subnet with the `public-sg` and a public IP address.
   ![Private Security Group](https://github.com/Tooddlez/created-a-custom-vpc-with-public-and-private-subnets-to-host-a-static-website/blob/main/Public%20Security%20Group.PNG)
3.  The private EC2 instance was launched in the private subnet with the `private-sg` and no public IP address.

### Phase 3: Serverless Website
1.  A globally unique S3 bucket was created.
2.  Public access was unblocked, and a bucket policy was applied to allow `s3:GetObject` for public read access.
3.  Static website hosting was enabled on the bucket, with `index.html` as the index document.
4.  A sample `index.html` file was uploaded to the bucket.

## 📸 Final Results

### S3 Static Website

The static website is successfully hosted on S3 and is publicly accessible via its endpoint URL.

![S3 Static Website Screenshot]([PASTE YOUR SCREENSHOT LINK HERE])
*Example Link: `https://raw.githubusercontent.com/your-username/your-repo/main/s3-website.png`*

### Network Connectivity

-   The **public instance** is accessible from the internet via its public IP address.
-   The **private instance** has no public IP and cannot be reached from the internet.
-   Successfully connected to the private instance by first SSH-ing into the public instance (bastion host).
-   The private instance can successfully ping external websites (e.g., `ping google.com`), confirming the NAT Gateway is working.

## 🤔 Challenges & Learnings

-   **NAT Gateway Provisioning:** The NAT Gateway takes a few minutes to provision, and routes depending on it will fail until it is fully available. Patience is key.
-   **Security Group Rules:** Correctly sourcing the security group for the private instance (`source: sg-xxxxxxxx`) instead of an IP range is a critical security practice that was implemented.
-   **S3 Permissions:** Configuring both the public access block settings and the bucket policy correctly is essential for S3 website hosting. Both must be set properly for the site to be visible.
-   **Architectural Choice:** This project highlighted the clear advantage of using S3 for static content over a traditional EC2 web server, showcasing the benefits of serverless for cost, scalability, and operational simplicity.
