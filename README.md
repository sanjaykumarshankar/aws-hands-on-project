# aws-hands-on-project
My first AWS project


# VPC with Public-Private Subnet in Production

## 🧾 About the Project

This project demonstrates how to create a **Virtual Private Cloud (VPC)** for a production environment using AWS services.

Key Features:
- Multi-AZ deployment for high availability.
- Auto Scaling group and Application Load Balancer for scalability and fault tolerance.
- EC2 instances in **private subnets** for security.
- NAT Gateways in each AZ for outbound internet access from private instances.

---

## 🗺️ Architecture Overview

- VPC has both **public** and **private** subnets in two Availability Zones.
- Each **public subnet** contains:
  - A NAT Gateway
  - A Load Balancer node
- **EC2 instances** (application servers) are launched in **private subnets** using:
  - Launch Templates
  - Auto Scaling Group
- Application servers receive traffic via Load Balancer.
- Outbound internet access is enabled via NAT Gateway.

---

## 🛠️ Step-by-Step Procedure

### 1. Create a VPC
- Basic configuration with two public and two private subnets (across two AZs).

### 2. Create an Auto Scaling Group
- **Create a Launch Template** with:
  - A Security Group allowing:
    - Custom TCP (Port 8000, Anywhere)
    - SSH (Port 22, Anywhere)
- **Create Auto Scaling Group**:
  - Use the Launch Template
  - Choose only **Private Subnets** under network settings
  - ❌ No Load Balancer at this stage

> ✅ You now have two EC2 instances **without public IPs**.

---

### 3. Create a Public EC2 Instance (Jump Box / Bastion Host)
- Used to SSH into private EC2s
- Configure with SSH (Port 22, Anywhere)

### 4. Copy `.pem` Key File to Public EC2

```bash
scp -i "C:\Users\bhawa\Sanjay\sanjay-first.pem" "C:\Users\bhawa\Sanjay\sanjay-first.pem" ec2-user@<PUBLIC-IP>:/home/ec2-user

```
### 5. Connect to Public EC2

```bash
ssh -i sanjay-first.pem ec2-user@<PUBLIC-IP>
```

### 6. SSH into Private EC2 from the Public EC2

```bash
ssh -i sanjay-first.pem ec2-user@<PRIVATE-IP>
```

App Setup on Private EC2
Create a Simple Web Page

```bash
vim index.html
```
Inside the file paste the content

<!DOCTYPE html>
<html>
<body>
<h1> My First AWS Project to Demonstrate apps to Private Subnet</h1>
</body>
</html>

Run a Simple Python Web Server

```bash
python3 -m http.server 8000
```
Create Application Load Balancer
Network Mapping: Select both AZs (only public subnets)

Listener: HTTP on port 8000

Security Group: Use the one created earlier

Routing:
Create Target Group

Register the two private instances

Choose instance as the target type

After provisioning, copy the DNS name of the Load Balancer and paste it in your browser. Test it in multiple browsers.
