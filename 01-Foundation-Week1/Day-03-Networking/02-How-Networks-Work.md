# 📄 02 — How Networks Work

> Previous: [← What is Networking](./01-What-Is-Networking.md)
> Next: [Networking Concepts →](./03-Networking-Concepts.md)

---

> 🎥 **Watch first:** [How Internet Works — Abhishek Veeramalla](https://www.youtube.com/watch?v=_19JDSaxBiY)
> Then read these notes for deeper understanding.

---

## 📌 Topics Covered

* How a request travels from browser to server
* What is DNS and how it works
* What is TCP and how connection is made
* What is TLS/HTTPS and why it matters
* What is HTTP and how data is sent
* How Routers, Firewalls, Load Balancers work
* VPC and Subnets in the journey
* Full request flow — end to end

---

## 🌐 Topic 1: The Big Picture — What Happens When You Open a Website?

Before going into each component, understand the full journey first.

```
You type: https://escbash.com
    ↓
Browser checks local DNS cache
    ↓
DNS Resolver finds IP address of escbash.com
    ↓
TCP connection established (3-way handshake)
    ↓
TLS handshake — secure connection created
    ↓
HTTP request sent to server
    ↓
Request passes through Internet → Routers
    ↓
Firewall checks if request is allowed
    ↓
Load Balancer receives request
    ↓
Load Balancer routes to correct server (EC2 in AWS)
    ↓
Server processes request
    ↓
Response travels back the same path
    ↓
Browser displays the page
```

👉 Every step above is a networking concept. Let's understand each one.

---

## 🔍 Topic 2: DNS — Domain Name System

DNS is the phonebook of the internet. It translates human-readable domain names into IP addresses that computers understand.

---

### 🧠 Why DNS?

```
Humans remember: escbash.com
Computers need:  142.250.77.46

DNS does the translation.
```

---

### 🔄 How DNS Resolution Works

```
You type: escbash.com
    ↓
Step 1: Browser checks its own DNS cache
        (Did I look this up recently?)
    ↓
Step 2: OS checks /etc/hosts file
        (Any local override?)
    ↓
Step 3: Query goes to Recursive Resolver
        (Usually your ISP's DNS server)
    ↓
Step 4: Recursive Resolver asks Root DNS Server
        "Who handles .com domains?"
    ↓
Step 5: Root Server says → ask TLD Server (.com)
    ↓
Step 6: TLD Server says → ask Authoritative Server
        "Authoritative server for escbash.com is at X"
    ↓
Step 7: Authoritative Server returns IP address
        escbash.com = 142.250.77.46
    ↓
Step 8: Browser caches the result (TTL)
    ↓
Step 9: Browser connects to 142.250.77.46
```

---

### 💡 DNS Commands

```bash
nslookup google.com         # basic DNS lookup
dig google.com              # detailed DNS info
dig google.com +short       # just the IP
dig google.com MX           # mail server records
cat /etc/resolv.conf        # see your DNS server
cat /etc/hosts              # local DNS overrides
```

---

### 📋 DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| `A` | Domain → IPv4 address | escbash.com → 142.250.77.46 |
| `AAAA` | Domain → IPv6 address | escbash.com → 2001:db8::1 |
| `CNAME` | Domain → another domain | www → escbash.com |
| `MX` | Mail server for domain | mail.escbash.com |
| `NS` | Nameserver for domain | ns1.escbash.com |
| `TXT` | Text records | SPF, domain verification |

---

### 🔥 DevOps Insight

> In AWS — Route53 is the DNS service.
> When you deploy an app on EC2, you point your domain's A record to the EC2 public IP.
> When using Load Balancer — you point to Load Balancer DNS name using CNAME.

---

## 🔌 Topic 3: TCP — Transmission Control Protocol

TCP is the protocol that ensures data reaches its destination reliably and in order.

---

### 🧠 Why TCP?

```
Internet is unreliable — packets can:
→ Get lost
→ Arrive out of order
→ Get corrupted

TCP solves this by:
→ Breaking data into numbered packets
→ Confirming receipt of each packet
→ Re-sending lost packets
→ Reassembling in correct order
```

---

### 🤝 TCP 3-Way Handshake

Before any data is sent — TCP establishes a connection.

```
Client                          Server
   │                               │
   │──── SYN ─────────────────────►│   "Hello, can we talk?"
   │                               │
   │◄─── SYN-ACK ─────────────────│   "Yes, I'm ready"
   │                               │
   │──── ACK ─────────────────────►│   "Great, let's start"
   │                               │
   │     Connection Established    │
   │                               │
   │──── HTTP Request ────────────►│
   │◄─── HTTP Response ───────────│
```

---

### 🔄 TCP vs UDP

| | TCP | UDP |
|-|-----|-----|
| Connection | ✅ Yes — handshake | ❌ No — fire and forget |
| Reliability | ✅ Guaranteed delivery | ❌ No guarantee |
| Order | ✅ In order | ❌ Out of order possible |
| Speed | Slower | Faster |
| Use case | Web, SSH, email, databases | Video streaming, gaming, DNS |

---

### 💡 Commands

```bash
ss -tulnp                   # show TCP/UDP listening ports
ss -t                       # show TCP connections
ss -u                       # show UDP connections
```

---

## 🔒 Topic 4: TLS — Transport Layer Security (HTTPS)

TLS is what makes HTTPS secure. It encrypts the data between client and server.

---

### 🧠 Why TLS?

```
Without TLS (HTTP):
Browser ──── "username=vamshi&password=1234" ────► Server
(Anyone between can read this — man in the middle attack)

With TLS (HTTPS):
Browser ──── "xK9#mL2$pQ..." (encrypted) ────► Server
(Nobody in between can read the data)
```

---

### 🤝 TLS Handshake (Simplified)

```
Client                              Server
   │                                   │
   │──── Client Hello ────────────────►│
   │     (supported TLS versions)       │
   │                                   │
   │◄─── Server Hello ─────────────────│
   │     (chosen TLS version)           │
   │◄─── Certificate (SSL cert) ───────│
   │                                   │
   │──── Verify certificate ──────────►│
   │     (is it signed by trusted CA?)  │
   │                                   │
   │──── Encrypted session key ───────►│
   │                                   │
   │     Secure encrypted tunnel ready │
   │◄══════ Encrypted data ═══════════►│
```

---

### 🔥 DevOps Insight

> When you set up HTTPS for your app:
> - Get SSL certificate (free with Let's Encrypt or ACM on AWS)
> - Configure nginx to use the certificate
> - Port 443 for HTTPS, Port 80 for HTTP
>
> In AWS — ACM (Certificate Manager) gives free SSL certificates.
> Attach to Load Balancer — done.

---

## 📨 Topic 5: HTTP — HyperText Transfer Protocol

HTTP is the protocol used to transfer data between browser and web server.

---

### 🧠 HTTP Request Structure

```
GET /index.html HTTP/1.1
Host: escbash.com
User-Agent: Mozilla/5.0
Accept: text/html
```

---

### 📋 HTTP Methods

| Method | Purpose | Example |
|--------|---------|---------|
| `GET` | Retrieve data | Get webpage, get user info |
| `POST` | Send data | Submit form, create user |
| `PUT` | Update data | Update user profile |
| `DELETE` | Delete data | Delete a record |
| `PATCH` | Partial update | Update only one field |

---

### 📋 HTTP Status Codes

| Code | Meaning | Example |
|------|---------|---------|
| `200` | OK — success | Page loaded |
| `201` | Created | Resource created |
| `301` | Redirect permanently | Domain moved |
| `400` | Bad Request | Wrong input sent |
| `401` | Unauthorized | Login required |
| `403` | Forbidden | No permission |
| `404` | Not Found | Page doesn't exist |
| `500` | Internal Server Error | App crashed |
| `502` | Bad Gateway | Upstream server failed |
| `503` | Service Unavailable | Server overloaded |

---

### 💡 Commands

```bash
curl -I https://google.com          # show HTTP headers and status code
curl https://api.endpoint/health    # test API endpoint
curl -X POST -d '{"name":"vamshi"}' https://api/users   # POST request
```

---

### 🔥 DevOps Insight

> When deployment fails — curl the health endpoint:
> ```bash
> curl http://localhost:8080/health
> ```
> Status 200 = app is running fine.
> Status 500 = app is running but crashing.
> Connection refused = app is not running at all.

---

## 🔀 Topic 6: Routers — How Traffic is Directed

A router is a device that forwards data packets between networks.

---

### 🧠 How Routers Work

```
Your Laptop (192.168.1.5)
    ↓
Home Router (192.168.1.1)
    ↓ decides where to send based on IP
ISP Router
    ↓
Internet (millions of routers)
    ↓ each router reads destination IP
    ↓ forwards to next router closer to destination
AWS Data Center Router
    ↓
Target Server (10.0.1.25)
```

---

### 💡 Route Tables in AWS

```bash
# In AWS — Route Tables control where traffic goes inside your VPC
# Destination      Target
# 0.0.0.0/0    →  igw-xxx   (all internet traffic → Internet Gateway)
# 10.0.0.0/16  →  local     (VPC traffic stays local)
```

---

## 🛡️ Topic 7: Firewalls — Controlling Traffic

A firewall is a security system that controls which network traffic is allowed in and out.

---

### 🧠 How Firewalls Work

```
Incoming traffic
    ↓
Firewall checks rules:
    ├── Is source IP allowed?
    ├── Is destination port allowed?
    ├── Is protocol allowed?
    └── Is connection state allowed?
    ↓
Allow ✅ or Deny ❌
```

---

### 🔄 Types of Firewalls in DevOps

| Type | Where | What it controls |
|------|-------|-----------------|
| `ufw` / `iptables` | Linux server | OS-level firewall |
| Security Group | AWS EC2 | Instance-level firewall |
| NACL | AWS Subnet | Subnet-level firewall |
| Network Policy | Kubernetes | Pod-level firewall |

---

### 💡 Commands

```bash
# Linux firewall
sudo ufw status
sudo ufw allow 8080
sudo ufw deny 23

# Check security group via AWS CLI
aws ec2 describe-security-groups --group-ids sg-xxx
```

---

## ⚖️ Topic 8: Load Balancer — Distributing Traffic

A Load Balancer distributes incoming traffic across multiple servers so no single server is overwhelmed.

---

### 🧠 How Load Balancer Works

```
Users (millions of requests)
    ↓
Load Balancer (single entry point)
    ├── Server 1 (EC2) → handles 33% of traffic
    ├── Server 2 (EC2) → handles 33% of traffic
    └── Server 3 (EC2) → handles 33% of traffic
```

---

### 🔄 Load Balancing Algorithms

| Algorithm | How it works |
|-----------|-------------|
| Round Robin | Requests go to servers in rotation |
| Least Connections | Route to server with fewest active connections |
| IP Hash | Same client always goes to same server |

---

### 🔥 DevOps Insight

> In AWS — ALB (Application Load Balancer) is the standard.
> - Distributes HTTP/HTTPS traffic
> - Does health checks — removes unhealthy servers automatically
> - Integrates with ACM for SSL termination
> - Works with Auto Scaling Groups

---

## 🏗️ Topic 9: VPC & Subnets — Your Private Network in Cloud

VPC = Virtual Private Cloud. Your own isolated network inside AWS.

---

### 🧠 VPC Concept

```
AWS Cloud (Shared infrastructure)
    └── Your VPC (your isolated network — nobody else can access)
            ├── Public Subnet  (10.0.1.0/24)
            │       ├── EC2 (web server) — has public IP
            │       └── Load Balancer
            └── Private Subnet (10.0.2.0/24)
                    ├── RDS (database) — no public IP, safe
                    └── Backend services
```

---

### 🔄 Public vs Private Subnet

| | Public Subnet | Private Subnet |
|-|--------------|----------------|
| Internet access | ✅ Yes — via IGW | ❌ No direct access |
| Has public IP | ✅ Yes | ❌ No |
| Used for | Web servers, Load Balancers | Databases, Backend apps |
| Security | Less secure | More secure |

---

### 🔥 DevOps Insight

> **Best practice:**
> - Web server → public subnet (needs internet traffic)
> - Database → private subnet (should never be internet-facing)
> - NAT Gateway → allows private subnet to reach internet (for updates)
>   but internet cannot initiate connection to private subnet

---

## 🔄 Topic 10: Full Request Journey — End to End

```
User types: https://escbash.com/scripts
    ↓
1. DNS → finds IP of escbash.com → 54.210.167.x
    ↓
2. TCP 3-way handshake with server on port 443
    ↓
3. TLS handshake → encrypted tunnel created
    ↓
4. HTTP GET /scripts request sent over encrypted tunnel
    ↓
5. Packet travels through Internet routers
    ↓
6. Reaches AWS — hits Internet Gateway
    ↓
7. Route Table → routes to Load Balancer in public subnet
    ↓
8. Firewall (Security Group) → checks if port 443 is allowed ✅
    ↓
9. Load Balancer → picks healthy EC2 instance
    ↓
10. EC2 processes request → fetches data from RDS (private subnet)
    ↓
11. Response travels back same path — encrypted
    ↓
12. Browser decrypts and displays page 🎉
```

---

## ✅ Checklist for This File

- [ ] Understand how DNS translates domain to IP
- [ ] Know DNS record types — A, CNAME, MX, TXT
- [ ] Understand TCP 3-way handshake
- [ ] Know TCP vs UDP difference
- [ ] Understand what TLS/HTTPS does
- [ ] Know HTTP methods — GET POST PUT DELETE
- [ ] Know important HTTP status codes — 200 404 500 502
- [ ] Understand how Load Balancer distributes traffic
- [ ] Understand VPC — public vs private subnet
- [ ] Can explain full request journey end to end

---

> Previous: [← What is Networking](./01-What-Is-Networking.md)
> Next: [Networking Concepts →](./03-Networking-Concepts.md)
