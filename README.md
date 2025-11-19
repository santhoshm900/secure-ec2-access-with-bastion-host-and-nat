# secure-ec2-access-with-bastion-host-and-nat
Secure AWS VPC setup with a Bastion Host in the public subnet providing SSH access to a private EC2 instance. Includes public/private subnets, NAT Gateway for outbound internet, route tables, security groups, and complete network architecture following AWS best practices.”


![AWS Architecture](diagram/aws-bastion-architecture.png)

# Secure EC2 Access with Bastion Host and NAT Gateway

This project demonstrates a secure AWS network architecture using a Bastion Host for SSH access to private EC2 instances.  
The setup follows AWS best practices and includes public/private subnets, NAT Gateway for outbound internet access, Internet Gateway, custom route tables, and strict security groups.

---

## 📌 Architecture Overview

![AWS Architecture](diagram/aws-bastion-architecture.png)

---

## 🏗️ **Components Used**

### 🔹 VPC
- CIDR: **10.0.0.0/16**
- Custom VPC created for isolated networking

### 🔹 Subnets
| Subnet Type | Name | CIDR | Zone |
|-------------|------|-------|------|
| Public Subnet | pub-subnet | 10.0.0.0/24 | ap-south-1a |
| Private Subnet | private-subnet | 10.0.1.0/24 | ap-south-1b |

### 🔹 Internet Gateway (IGW)
- Attached to VPC for public subnet internet access

### 🔹 NAT Gateway
- Created in **public subnet**
- Provides internet access to **private EC2** only (outbound)

### 🔹 Route Tables
#### Public Route Table:
- Local route: 10.0.0.0/16 → local  
- IGW route: 0.0.0.0/0 → Internet Gateway

#### Private Route Table:
- Local route: 10.0.0.0/16 → local  
- NAT route: 0.0.0.0/0 → NAT Gateway

### 🔹 EC2 Instances
#### Public EC2 (Bastion Host)
- Subnet: 10.0.0.0/24  
- Private IP example: **10.0.0.x**  
- Used for SSH access to private EC2  
- Key pair copied here for jump access  

#### Private EC2
- Subnet: 10.0.1.0/24  
- Private IP: **10.0.1.192**  
- Accessible **only from public EC2** (bastion)

---

## 🔐 **Security Groups**

### Public EC2 SG:
- Inbound:  
  - SSH (22) → My IP
- Outbound: All allowed

### Private EC2 SG:
- Inbound:  
  - SSH (22) → Public EC2 SG only
- Outbound: All allowed (for NAT)

---

## 🧩 **How Access Works (Flow)**

1. **Laptop → Public EC2 (SSH allowed from your IP)**  
2. **Public EC2 → Private EC2 (SSH allowed only from Bastion Host SG)**  
3. Private EC2 gets internet using **NAT Gateway**

This isolates private instances from public exposure.

---
🖥️ Connect to Public EC2 Using PuTTY

Public EC2 Login:

ubuntu@<Public-EC2-Public-IP>


Key File Used:

linuxkeypair.pem

1️⃣ Convert .pem → .ppk using PuTTYgen

Open PuTTYgen

Click Load

Change file type to All Files (.)

Select your PEM file:

linuxkeypair.pem


Click Save private key

Save as:

linuxkeypair.ppk

2️⃣ Open PuTTY (Public EC2 Login)

Enter Hostname:

ubuntu@<Public-EC2-Public-IP>


Example:

ubuntu@15.xxx.xxx.xxx

3️⃣ Add Private Key in PuTTY

In PuTTY left panel:

Connection → SSH → Auth → Credentials


Then:

Click Browse

Select file:

linuxkeypair.ppk

4️⃣ Connect to Public EC2

Click Open

Click Yes for the popup

Successful login shows:

Welcome to Ubuntu...
ubuntu@ip-10-0-0-170:~$


✔️ Now you are inside the Public EC2 (Bastion Host)
