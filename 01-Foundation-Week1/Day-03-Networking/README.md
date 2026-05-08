# 🌐 02 — Networking for DevOps & SRE

> "You cannot debug what you don't understand. Networking is the backbone of every system you will ever deploy."

---

## 📌 Why Networking in DevOps?

Every tool you use in DevOps runs on a network.
Docker containers communicate over networks.
Kubernetes pods talk to each other over networks.
CI/CD pipelines pull code and push images over networks.
AWS VPCs, subnets, security groups — all networking.

**If networking breaks — everything breaks.**

---

## 🗺️ Learning Path — Follow This Order

> ⚠️ Do NOT skip steps. Each file builds on the previous one.

---

### 📖 Step 1 — Start Here

| File | What you will learn |
|------|-------------------|
| [📄 What is Networking & Why DevOps needs it](./01-What-Is-Networking.md) | Foundation — why networking matters in DevOps & SRE |

---

### 📖 Step 2 — Understand How Networks Work

| File | What you will learn |
|------|-------------------|
| [📄 How Networks Work](./02-How-Networks-Work.md) | DNS, TCP, HTTP, Routers, Firewalls — based on Abhishek's video |

> 🎥 Watch first: [How Internet Works — Abhishek Veeramalla](https://www.youtube.com/watch?v=_19JDSaxBiY)
> Then read this file for notes + deeper understanding.

---

### 📖 Step 3 — Core Networking Concepts

| File | What you will learn |
|------|-------------------|
| [📄 Networking Concepts for Fresher](./03-Networking-Concepts.md) | IP, Ports, DNS, CIDR, Subnetting, OSI Model, TCP/UDP, HTTP/HTTPS |

---

### 📖 Step 4 — How Google.com Works

| File | What you will learn |
|------|-------------------|
| [📄 How Google.com Works — OSI Model](./04-How-Google-Works-OSI.md) | End to end journey of a request through OSI layers |

---

### 📖 Step 5 — AWS VPC Deep Dive

| File | What you will learn |
|------|-------------------|
| [📄 AWS VPC — Understand & Create Practically](./05-AWS-VPC.md) | VPC, Subnets, Route Tables, IGW, NAT, Security Groups, NACLs |

---

### 📖 Step 6 — Interview Preparation

| File | What you will learn |
|------|-------------------|
| [📄 Networking Interview Q&A](./06-Networking-Interview-QA.md) | Most asked networking questions in DevOps & SRE interviews |

---

## 📂 Folder Structure

```
02-Networking/
├── README.md                        ← You are here — Start here
├── 01-What-Is-Networking.md         ← Step 1: Foundation
├── 02-How-Networks-Work.md          ← Step 2: How internet works
├── 03-Networking-Concepts.md        ← Step 3: Core concepts
├── 04-How-Google-Works-OSI.md       ← Step 4: OSI model in action
├── 05-AWS-VPC.md                    ← Step 5: AWS VPC practical
└── 06-Networking-Interview-QA.md   ← Step 6: Interview prep
```

---

## ✅ Networking Checklist

- [ ] Understand why networking matters in DevOps
- [ ] Know how a request travels from browser to server
- [ ] Understand OSI model — 7 layers
- [ ] Know IP address, subnet mask, CIDR notation
- [ ] Understand TCP vs UDP
- [ ] Know HTTP vs HTTPS — what TLS does
- [ ] Understand DNS — how domain resolves to IP
- [ ] Know common ports — 22, 80, 443, 8080, 6443, 3306
- [ ] Understand VPC, subnets, route tables
- [ ] Know difference between public and private subnet
- [ ] Understand Security Groups vs NACLs
- [ ] Create a VPC on AWS from scratch — practically
- [ ] Answer "What happens when you type google.com?" — fully

---

*Start with [📄 Step 1 → What is Networking](./01-What-Is-Networking.md)*
