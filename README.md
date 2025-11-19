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

## 🖥️ Connect to Your EC2 Using PuTTY

**Instance Name:** public-ec2  
**Key File:** linuxkeypair.pem  

### 1️⃣ Convert your `.pem` file to `.ppk` using PuTTYgen

Open **PuTTYgen**

Click **Load**

Change file type → **All Files (*.*)**

Select your key file:


Click **Save private key**

Save the new file as:

1️⃣ Open FileZilla

Go to:
File → Site Manager → New Site

Set the following:

🔹 Protocol
SFTP – SSH File Transfer Protocol

🔹 Host
<PUBLIC-EC2-PUBLIC-IP>


Example:

15.xx.xx.xx

🔹 Port
22

🔹 Logon Type
Key file

🔹 User
ubuntu

🔹 Key File

Select your PuTTY converted key:

linuxkeypair.ppk

2️⃣ Connect to Public EC2

Click Connect

If it asks "Trust host key?" → Click Yes

Now your Ubuntu user directory will open:

/home/ubuntu/

3️⃣ Upload Private EC2 Key File

Drag & drop this file from your laptop → public EC2:

private-keypair.pem


Upload location must be:

/home/ubuntu/


✔️ Key uploaded successfully for jump access.

4️⃣ Set Correct Permission (IMPORTANT)

Now go to your PuTTY public-ec2 terminal and run:

chmod 400 private-keypair.pem


✔️ This is required
❗ Otherwise SSH will fail with:
"Permission denied (publickey)"
