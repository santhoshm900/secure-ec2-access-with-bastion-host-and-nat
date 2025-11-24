


![AWS Architecture](diagram/aws-bastion-architecture.png)

# Secure EC2 Access with Bastion Host and NAT Gateway
This project demonstrates a secure AWS network architecture using a Bastion Host for SSH access to private EC2 instances.  
The setup follows AWS best practices and includes public/private subnets, NAT Gateway for outbound internet access, Internet Gateway, custom route tables, and strict security groups.

---

## 📌 Architecture Overview

![AWS Architecture](diagram/aws-bastion-architecture.png)

---

## Components Used 🔶

### VPC 🔷

CIDR: 10.0.0.0/16

Custom VPC created for isolated networking 
![VPC](diagram/vpc.png.PNG)

### Subnets 🔷
Subnet Type	Name	CIDR	Zone
Public Subnet	pub-subnet	10.0.0.0/24	ap-south-1a
Private Subnet	private-subnet	10.0.1.0/24	ap-south-1b
### Internet Gateway 🔷
Attached to VPC for public subnet internet access

### NAT Gateway 🔷

Placed inside the public subnet

Allows private EC2 outbound internet (updates, packages, etc.)

###  Route Tables 🔷
Public Route Table

10.0.0.0/16 → local

0.0.0.0/0 → Internet Gateway

Private Route Table

10.0.0.0/16 → local

0.0.0.0/0 → NAT Gateway

### EC2 Instances 🔹
Public EC2 (Bastion Host)

Subnet: 10.0.0.0/24

Used to SSH into private EC2

Key pair stored here for jump access

Private EC2

Subnet: 10.0.1.0/24

IP Example: 10.0.1.192

Not publicly accessible

SSH allowed only from Bastion Host

### Security Groups 🔐
Public EC2 SG

Inbound:

SSH (22) → Your IP only

Outbound:

All allowed

Private EC2 SG

Inbound:

SSH → Public EC2 SG only

Outbound:

All allowed (uses NAT)

### Access Flow 🔄

Laptop → Public EC2 (via SSH)

Public EC2 → Private EC2 (via SSH using private key)

Private EC2 → Internet (via NAT Gateway)

This ensures complete isolation and secure SSH access.

### Connecting to EC2 Using PuTTY 🖥️
Key Pair: linuxkeypair.pem

(Will be converted to .ppk for PuTTY)

1️⃣ Convert .pem → .ppk using PuTTYgen

Open PuTTYgen

Click Load

Change file type → All Files

Select your
linuxkeypair.pem

Click Save Private Key

Save as:
linuxkeypair.ppk

### Upload Private Key to Public EC2 (Using FileZilla) 📤
2️⃣ Open FileZilla → Site Manager
Field	Value
Protocol	SFTP
Host	Public EC2 Public IP
Port	22
Logon Type	Key file
User	ubuntu
Key File	linuxkeypair.ppk

Click Connect

3️⃣ Upload Key File

Upload your private key:

private-keypair.pem


to your Public EC2 home directory.

✔ File upload successful.

### 4️⃣ Fix Key Permissions in Public EC2 🔒

Run:

chmod 400 private-keypair.pem

### 5️⃣ SSH from Public EC2 → Private EC2 🖥️
Step 1: Confirm key exists
ls


You should see:

private-keypair.pem

Step 2: Ensure permissions
chmod 400 private-keypair.pem

Step 3: SSH into Private EC2

Replace the IP with your private EC2’s IP:

ssh -i private-keypair.pem ubuntu@10.0.1.192

Step 4: If login succeeds, you’ll see:
Welcome to Ubuntu...
ubuntu@ip-10-0-1-192:~$
