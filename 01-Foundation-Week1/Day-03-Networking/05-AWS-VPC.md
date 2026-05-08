# 📄 05 — AWS VPC — Understand & Create Practically

> Previous: [← How Google.com Works](./04-How-Google-Works-OSI.md)
> Next: [Networking Interview Q&A →](./06-Networking-Interview-QA.md)

---

## 📌 Topics Covered

* What is VPC?
* VPC Components — one by one
* Public vs Private Subnet
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups
* NACLs (Network Access Control Lists)
* VPC Peering
* Create VPC from scratch — step by step practically

---

## 🏗️ Topic 1: What is VPC?

VPC = Virtual Private Cloud

Your own **isolated, private network** inside AWS.
Like renting your own floor in a shared building — others are in the same building but cannot access your floor.

---

### 🧠 Without VPC vs With VPC

```
Without VPC (old way):
→ All AWS customers share same flat network
→ Anyone could potentially reach your servers
→ No isolation, no security

With VPC:
→ Your resources are in your own private network
→ You control all traffic rules
→ Nobody outside can access unless you allow it
→ Complete isolation and security
```

---

### 🌍 VPC Scope

```
AWS Region (e.g., ap-south-1 — Mumbai)
    └── Your VPC (10.0.0.0/16)
            ├── Availability Zone A (ap-south-1a)
            │       ├── Public Subnet  (10.0.1.0/24)
            │       └── Private Subnet (10.0.2.0/24)
            └── Availability Zone B (ap-south-1b)
                    ├── Public Subnet  (10.0.3.0/24)
                    └── Private Subnet (10.0.4.0/24)
```

---

### 🔥 DevOps Insight

> Every company that uses AWS has at least one VPC.
> As a DevOps engineer — you will create, manage, and debug VPCs daily.
> Understanding VPC = Understanding AWS.

---

## 🧩 Topic 2: VPC Components — One by One

---

### 🔢 CIDR Block

The IP address range for your entire VPC.

```
Default AWS VPC CIDR: 172.31.0.0/16
Custom VPC CIDR:      10.0.0.0/16   ← recommended for production

/16 gives you 65,536 IP addresses to distribute across subnets
```

---

### 📦 Subnets

A subnet is a smaller network inside your VPC.

```
VPC: 10.0.0.0/16

Public Subnets (accessible from internet):
    10.0.1.0/24  → AZ-A public (256 IPs)
    10.0.2.0/24  → AZ-B public (256 IPs)

Private Subnets (NOT accessible from internet):
    10.0.10.0/24 → AZ-A private (256 IPs)
    10.0.11.0/24 → AZ-B private (256 IPs)

Database Subnets (most secure):
    10.0.20.0/24 → AZ-A database (256 IPs)
    10.0.21.0/24 → AZ-B database (256 IPs)
```

👉 Note: AWS reserves 5 IPs in every subnet (first 4 + last 1)
So /24 gives 256 - 5 = **251 usable IPs**

---

### 🌐 Internet Gateway (IGW)

Allows resources in **public subnet** to communicate with internet.

```
Internet ←──────→ Internet Gateway ←──────→ Public Subnet EC2
                        ↕
              Route Table must have:
              0.0.0.0/0 → igw-xxx
```

👉 One IGW per VPC
👉 IGW is highly available — no need for redundancy
👉 Free — no extra cost

---

### 🔄 NAT Gateway

Allows resources in **private subnet** to reach internet (outbound only).
Internet cannot initiate connection to private subnet.

```
Private EC2 (10.0.10.5)
    ↓ wants to run: apt update
NAT Gateway (in public subnet, has Elastic IP)
    ↓
Internet
    ↓ response comes back to NAT Gateway
    ↓
Private EC2 receives response
```

👉 NAT Gateway must be in a **public subnet**
👉 Costs money — ~$0.045 per hour + data transfer charges
👉 Need one per AZ for high availability

---

### 🗺️ Route Tables

Route Tables control where network traffic is directed.

```
Public Route Table:
    Destination     Target
    10.0.0.0/16  →  local    (traffic within VPC stays local)
    0.0.0.0/0    →  igw-xxx  (all other traffic → internet)

Private Route Table:
    Destination     Target
    10.0.0.0/16  →  local    (traffic within VPC stays local)
    0.0.0.0/0    →  nat-xxx  (outbound internet → NAT Gateway)
```

👉 Every subnet must be associated with a Route Table
👉 Public subnet → Route Table with IGW
👉 Private subnet → Route Table with NAT Gateway

---

### 🛡️ Security Groups

Instance-level firewall. Controls traffic to/from individual EC2 instances.

```
Web Server Security Group:
    Inbound:
    Port 80  (HTTP)  → Source: 0.0.0.0/0       (everyone)
    Port 443 (HTTPS) → Source: 0.0.0.0/0       (everyone)
    Port 22  (SSH)   → Source: your IP /32      (only you)

    Outbound:
    All traffic      → Destination: 0.0.0.0/0   (allow all outbound)

Database Security Group:
    Inbound:
    Port 3306 (MySQL) → Source: Web-SG          (only web servers)

    Outbound:
    All traffic       → Destination: 0.0.0.0/0
```

---

### 🔄 Security Groups Key Points

| Feature | Security Group |
|---------|---------------|
| Level | Instance (EC2) |
| State | Stateful — return traffic automatically allowed |
| Default | Deny all inbound, Allow all outbound |
| Rules | Allow only — no explicit deny |
| Can reference | Other security groups |

---

### 🧱 NACLs — Network Access Control Lists

Subnet-level firewall. Controls traffic for the entire subnet.

```
Public Subnet NACL:
    Inbound:
    Rule 100: Allow Port 80   from 0.0.0.0/0
    Rule 110: Allow Port 443  from 0.0.0.0/0
    Rule 120: Allow Port 22   from your IP
    Rule *:   Deny ALL

    Outbound:
    Rule 100: Allow ALL to 0.0.0.0/0
    Rule *:   Deny ALL
```

---

### 🔄 Security Group vs NACL

| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow and Deny |
| Evaluation | All rules evaluated | Rules in number order |
| Default | Deny all inbound | Allow all |
| Use case | Primary firewall | Additional defense layer |

---

### 🔗 VPC Peering

Connect two VPCs so resources can communicate using private IPs.

```
VPC-A (10.0.0.0/16) ←──── Peering ────► VPC-B (172.16.0.0/16)

EC2 in VPC-A can reach EC2 in VPC-B using private IPs
No traffic goes over internet — stays on AWS backbone
```

👉 VPC Peering is NOT transitive:
```
VPC-A ←→ VPC-B ←→ VPC-C
VPC-A CANNOT reach VPC-C through VPC-B
```

---

## 🛠️ Topic 3: Create VPC from Scratch — Step by Step

---

### 📋 What We Are Building

```
VPC: devops-vpc (10.0.0.0/16)
    ├── Public Subnet: 10.0.1.0/24 (AZ: ap-south-1a)
    ├── Private Subnet: 10.0.2.0/24 (AZ: ap-south-1a)
    ├── Internet Gateway
    ├── NAT Gateway (in public subnet)
    ├── Public Route Table (with IGW)
    ├── Private Route Table (with NAT)
    └── Security Groups (web + database)
```

---

### 🔧 Step 1 — Create VPC

```
AWS Console → VPC → Create VPC
    Name: devops-vpc
    CIDR: 10.0.0.0/16
    Tenancy: Default
→ Create VPC
```

---

### 🔧 Step 2 — Create Subnets

```
VPC → Subnets → Create Subnet
    VPC: devops-vpc

Subnet 1 — Public:
    Name: public-subnet-1a
    AZ: ap-south-1a
    CIDR: 10.0.1.0/24

Subnet 2 — Private:
    Name: private-subnet-1a
    AZ: ap-south-1a
    CIDR: 10.0.2.0/24
```

---

### 🔧 Step 3 — Enable Auto-Assign Public IP for Public Subnet

```
Select public-subnet-1a
→ Actions → Edit subnet settings
→ Enable auto-assign public IPv4 address ✅
→ Save
```

---

### 🔧 Step 4 — Create Internet Gateway

```
VPC → Internet Gateways → Create Internet Gateway
    Name: devops-igw
→ Create

Attach to VPC:
    Actions → Attach to VPC → devops-vpc
→ Attach
```

---

### 🔧 Step 5 — Create NAT Gateway

```
VPC → NAT Gateways → Create NAT Gateway
    Name: devops-nat
    Subnet: public-subnet-1a    ← MUST be public subnet
    Connectivity: Public
    Elastic IP: Allocate Elastic IP
→ Create NAT Gateway

Wait 2-3 minutes for status: Available
```

---

### 🔧 Step 6 — Create Route Tables

```
Public Route Table:
    VPC → Route Tables → Create Route Table
    Name: public-rt
    VPC: devops-vpc
    → Create

    Add route to internet:
    Select public-rt → Routes → Edit routes → Add route
    Destination: 0.0.0.0/0
    Target: igw → devops-igw
    → Save changes

    Associate with public subnet:
    Select public-rt → Subnet associations → Edit
    Check: public-subnet-1a
    → Save

Private Route Table:
    Create Route Table
    Name: private-rt
    VPC: devops-vpc
    → Create

    Add route through NAT:
    Destination: 0.0.0.0/0
    Target: NAT Gateway → devops-nat
    → Save changes

    Associate with private subnet:
    Select private-rt → Subnet associations → Edit
    Check: private-subnet-1a
    → Save
```

---

### 🔧 Step 7 — Create Security Groups

```
Web Server Security Group:
    VPC → Security Groups → Create security group
    Name: web-sg
    VPC: devops-vpc

    Inbound rules:
    Port 22  (SSH)   → My IP
    Port 80  (HTTP)  → Anywhere (0.0.0.0/0)
    Port 443 (HTTPS) → Anywhere (0.0.0.0/0)

    Outbound rules:
    All traffic → Anywhere
    → Create security group

Database Security Group:
    Name: db-sg
    VPC: devops-vpc

    Inbound rules:
    Port 3306 (MySQL) → Source: web-sg  ← reference security group!

    Outbound: All traffic
    → Create
```

---

### 🔧 Step 8 — Launch EC2 and Test

```
Public EC2 (web server):
    Launch Instance
    Subnet: public-subnet-1a
    Security Group: web-sg
    → Launch

Private EC2 (app server):
    Launch Instance
    Subnet: private-subnet-1a
    Security Group: web-sg (for testing)
    → Launch

Test public EC2:
    ssh -i key.pem ubuntu@public-ip   → should work ✅

Test private EC2 (from public EC2):
    ssh -i key.pem ubuntu@10.0.2.x    → should work ✅

Test internet from private EC2:
    ping google.com                    → should work via NAT ✅
```

---

## 🔄 Topic 4: VPC Architecture — Final Picture

```
                        INTERNET
                           │
                    Internet Gateway
                           │
            ┌──────────────┴──────────────┐
            │         VPC (10.0.0.0/16)   │
            │                             │
            │   ┌─── Public Subnet ───┐   │
            │   │   10.0.1.0/24       │   │
            │   │   ┌─────────────┐   │   │
            │   │   │ Web Server  │   │   │
            │   │   │ EC2 + EIP   │   │   │
            │   │   └─────────────┘   │   │
            │   │   ┌─────────────┐   │   │
            │   │   │NAT Gateway  │   │   │
            │   │   └──────┬──────┘   │   │
            │   └──────────┼──────────┘   │
            │              │              │
            │   ┌─── Private Subnet ──┐   │
            │   │   10.0.2.0/24       │   │
            │   │   ┌─────────────┐   │   │
            │   │   │  App Server │   │   │
            │   │   │  EC2        │   │   │
            │   │   └─────────────┘   │   │
            │   │   ┌─────────────┐   │   │
            │   │   │  Database   │   │   │
            │   │   │  RDS        │   │   │
            │   │   └─────────────┘   │   │
            │   └─────────────────────┘   │
            │                             │
            └─────────────────────────────┘
```

---

## 🔥 DevOps Insight

> **Real production VPC checklist:**
> - [ ] VPC CIDR does not overlap with office network or other VPCs
> - [ ] Subnets spread across minimum 2 AZs for HA
> - [ ] Web servers in public subnet with ALB
> - [ ] App servers in private subnet — no public IP
> - [ ] Databases in private subnet — accessible only from app servers
> - [ ] NAT Gateway in each AZ (for HA)
> - [ ] Flow Logs enabled — for debugging and audit
> - [ ] VPC Endpoints — for private access to S3 and DynamoDB

---

## ✅ Checklist for This File

- [ ] Understand what VPC is and why we need it
- [ ] Know all VPC components — IGW, NAT, Route Table, SG, NACL
- [ ] Understand public vs private subnet difference
- [ ] Know when to use IGW vs NAT Gateway
- [ ] Know Security Group vs NACL difference
- [ ] Created a VPC from scratch on AWS console
- [ ] Launched EC2 in public subnet — SSH works
- [ ] Launched EC2 in private subnet — can reach internet via NAT

---

> Previous: [← How Google.com Works](./04-How-Google-Works-OSI.md)
> Next: [Networking Interview Q&A →](./06-Networking-Interview-QA.md)
