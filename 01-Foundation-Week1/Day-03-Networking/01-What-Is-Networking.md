# 📄 01 — What is Networking & Why DevOps Needs It

> Previous: [README — Networking Home](./README.md)
> Next: [How Networks Work →](./02-How-Networks-Work.md)

---

## 📌 Topics Covered

* What is Networking?
* Why Networking in DevOps?
* Why Networking in Cloud?
* Why Networking in SRE?
* Real world DevOps networking scenarios
* Networking tools every DevOps engineer uses

---

## 🌐 Topic 1: What is Networking?

Networking is the practice of connecting computers and devices so they can communicate and share data with each other.

When you open a website, send a message, deploy an application, or pull a Docker image — you are using a network.

👉 Simple definition:
**Networking = Rules + Cables/Wireless + Devices working together to move data from Point A to Point B.**

---

### 🔄 How Data Moves

```
Your Laptop
    ↓
Router (your home/office)
    ↓
ISP (Internet Service Provider)
    ↓
Internet (thousands of routers)
    ↓
Data Center (where the server lives)
    ↓
Server (running the application)
    ↓
Response comes back the same way
```

---

### 🧠 Key Networking Components

| Component | What it does |
|-----------|-------------|
| **IP Address** | Unique address of every device on a network |
| **Router** | Directs traffic between networks |
| **Switch** | Connects devices within the same network |
| **Firewall** | Controls what traffic is allowed or blocked |
| **DNS** | Translates domain names to IP addresses |
| **Load Balancer** | Distributes traffic across multiple servers |
| **Gateway** | Entry/exit point of a network |

---

## ⚙️ Topic 2: Why Networking in DevOps?

Every DevOps tool you use runs on a network. Understanding networking helps you:

---

### 🐳 Docker Networking

```bash
# Docker containers communicate over networks
docker network ls           # list Docker networks
docker network create mynet # create custom network

# Two containers talking to each other
# Order service → Payment service
# Both on same Docker network
```

👉 Without networking knowledge — you cannot connect containers.

---

### ☸️ Kubernetes Networking

```bash
# Every pod gets an IP address
kubectl get pods -o wide        # see pod IPs

# Services expose pods over network
# Ingress routes external traffic to services
# Network policies control pod-to-pod traffic
```

👉 Without networking knowledge — you cannot debug pod communication issues.

---

### 🔧 CI/CD Pipelines

```
Jenkins server pulls code from GitHub   → network
Jenkins builds Docker image             → network
Jenkins pushes image to Docker Hub      → network
Jenkins deploys to K8s cluster via SSH  → network
```

👉 Every step in CI/CD uses the network.

---

### 🛡️ Security

```bash
# Security groups control what traffic reaches your EC2
# NACLs control subnet-level traffic
# VPC isolates your infrastructure
# Port 22 = SSH, 80 = HTTP, 443 = HTTPS, 3306 = MySQL
```

👉 Without networking knowledge — you cannot secure your infrastructure.

---

## ☁️ Topic 3: Why Networking in Cloud (AWS/GCP)?

When you work in cloud — everything is virtual networking.

---

### 🏗️ AWS Networking Components

```
AWS Region
    └── VPC (Virtual Private Cloud) — your isolated network
            ├── Public Subnet   → accessible from internet
            │       └── EC2 with web server
            ├── Private Subnet  → NOT accessible from internet
            │       └── RDS database (safe)
            ├── Internet Gateway → allows public subnet to reach internet
            ├── NAT Gateway      → allows private subnet to reach internet (outbound only)
            ├── Route Tables     → controls where traffic goes
            └── Security Groups  → firewall for EC2 instances
```

👉 Every AWS deployment requires understanding VPC and subnets.

---

### 🌍 GCP Networking

```
GCP Project
    └── VPC Network (global by default in GCP)
            ├── Subnets (regional)
            ├── Firewall Rules
            └── Cloud Load Balancer
```

---

## 🔍 Topic 4: Why Networking in SRE?

SRE (Site Reliability Engineering) is about keeping systems reliable. Network issues cause the most production incidents.

---

### 🚨 Real SRE Networking Scenarios

| Incident | Networking cause | Fix |
|----------|-----------------|-----|
| App unreachable | Security group blocking port | Add inbound rule |
| Pods can't talk | Wrong K8s network policy | Fix policy |
| Slow response | Network latency between regions | Move to same region |
| Can't SSH to EC2 | Port 22 not open | Add security group rule |
| DB connection failed | Private subnet routing issue | Fix route table |

---

### 🔧 SRE Networking Debug Flow

```bash
# Step 1 — is host reachable?
ping server-ip

# Step 2 — is port open?
telnet server-ip 8080
nc -zv server-ip 8080

# Step 3 — is service responding?
curl http://server-ip:8080/health

# Step 4 — check DNS resolution
nslookup domain.com
dig domain.com

# Step 5 — check routing
traceroute server-ip

# Step 6 — check firewall / security group
# AWS console → Security Groups → check inbound rules
```

---

## 🛠️ Topic 5: Networking Tools Every DevOps Engineer Uses

| Tool | Purpose | Command |
|------|---------|---------|
| `ping` | Check if host is alive | `ping google.com` |
| `curl` | Test HTTP endpoints | `curl http://app:8080/health` |
| `wget` | Download files | `wget https://file.zip` |
| `ss` / `netstat` | Check open ports | `ss -tulnp` |
| `nslookup` / `dig` | DNS lookup | `nslookup google.com` |
| `traceroute` | Trace network path | `traceroute 8.8.8.8` |
| `nc` (netcat) | Test port connectivity | `nc -zv host 8080` |
| `ip addr` | Show network interfaces | `ip addr show` |
| `iptables` / `ufw` | Firewall management | `ufw allow 8080` |

---

## 🎯 Simple Understanding

```
DevOps without networking = Chef without knowing ingredients

Every tool you deploy:
→ runs on a server (has an IP)
→ exposes a port (has a port number)
→ communicates with other services (uses network)
→ needs security rules (firewall / security group)
→ resolves domain names (DNS)
```

---

## 🔥 DevOps Insight

> **The most common production issues are:**
> 1. Wrong port in security group → app unreachable
> 2. DNS not configured → domain not resolving
> 3. Private subnet without NAT → can't pull Docker images
> 4. Network policy too strict → K8s pods can't communicate
>
> All of these require networking knowledge to debug and fix.

---

## ✅ Checklist for This File

- [ ] Understand what networking is in simple terms
- [ ] Know why networking matters in Docker
- [ ] Know why networking matters in Kubernetes
- [ ] Know why networking matters in AWS/GCP cloud
- [ ] Know the basic networking debug flow
- [ ] Know the 5 most used networking tools

---

> Next Step → [📄 How Networks Work](./02-How-Networks-Work.md)
