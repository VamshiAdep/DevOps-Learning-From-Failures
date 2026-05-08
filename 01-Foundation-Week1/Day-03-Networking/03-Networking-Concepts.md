# 📄 03 — Networking Concepts for DevOps Fresher

> Previous: [← How Networks Work](./02-How-Networks-Work.md)
> Next: [How Google.com Works →](./04-How-Google-Works-OSI.md)

---

## 📌 Topics Covered

* IP Address — IPv4 and IPv6
* Public IP vs Private IP
* Ports — what they are and important ones
* Protocols — TCP, UDP, ICMP
* DNS — Deep dive
* CIDR Notation
* Subnetting
* NAT — Network Address Translation
* OSI Model — 7 Layers
* Common Networking Commands

---

## 🔢 Topic 1: IP Address

An IP address is a unique identifier for every device on a network.
Like a home address — every device needs one to send and receive data.

---

### 🧠 IPv4 Address

```
Format: 4 numbers separated by dots
Range:  0-255 for each number
Example: 192.168.1.5

Binary: 11000000.10101000.00000001.00000101
Bits:   32 bits total → 4 billion possible addresses
```

---

### 🔄 IPv4 Classes

| Class | Range | Default Subnet | Use |
|-------|-------|----------------|-----|
| A | 1.0.0.0 – 126.255.255.255 | /8 | Large networks |
| B | 128.0.0.0 – 191.255.255.255 | /16 | Medium networks |
| C | 192.0.0.0 – 223.255.255.255 | /24 | Small networks |

---

### 🔄 Public IP vs Private IP

| | Public IP | Private IP |
|-|-----------|-----------|
| Accessible from | Anywhere on internet | Only within private network |
| Assigned by | ISP / Cloud provider | Router / DHCP |
| Example | 54.210.167.23 | 192.168.1.5 / 10.0.0.5 |
| Cost | Costs money in cloud | Free |

---

### 📋 Private IP Ranges

```
10.0.0.0    – 10.255.255.255    (10.0.0.0/8)
172.16.0.0  – 172.31.255.255    (172.16.0.0/12)
192.168.0.0 – 192.168.255.255   (192.168.0.0/16)
```

👉 These IP ranges are NEVER routed on the public internet.
👉 AWS VPC default CIDR: `10.0.0.0/16`

---

### 🌐 IPv6 Address

```
Format: 8 groups of 4 hexadecimal digits
Example: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
Bits:   128 bits → 340 undecillion addresses
```

👉 IPv4 is still dominant in DevOps today. But know what IPv6 is.

---

### 💡 Commands

```bash
ip addr show                    # show all IP addresses on system
ip addr show eth0               # show specific interface IP
curl ifconfig.me                # show your public IP
hostname -I                     # show all local IPs
```

---

### 🔥 DevOps Insight

> When you create an EC2 instance:
> - Private IP: assigned from your VPC CIDR (e.g., 10.0.1.25) — permanent
> - Public IP: assigned from AWS pool — changes on every restart
> - Elastic IP: static public IP — costs money but stays fixed

---

## 🔌 Topic 2: Ports

A port is a number that identifies a specific service or application on a server.

```
IP address = street address of the building
Port       = apartment number inside the building

192.168.1.5:80   → web server on that machine
192.168.1.5:22   → SSH service on that machine
192.168.1.5:3306 → MySQL database on that machine
```

---

### 📋 Important Ports — Must Know

| Port | Protocol | Service |
|------|----------|---------|
| `22` | SSH | Secure remote login |
| `80` | HTTP | Web traffic (unencrypted) |
| `443` | HTTPS | Web traffic (encrypted) |
| `53` | DNS | Domain name resolution |
| `21` | FTP | File transfer |
| `25` | SMTP | Email sending |
| `3306` | MySQL | MySQL database |
| `5432` | PostgreSQL | PostgreSQL database |
| `6379` | Redis | Redis cache |
| `27017` | MongoDB | MongoDB database |
| `8080` | HTTP-alt | Jenkins, Tomcat, apps |
| `8443` | HTTPS-alt | Alternate HTTPS |
| `2379` | etcd | Kubernetes etcd |
| `6443` | K8s API | Kubernetes API server |
| `10250` | Kubelet | Kubernetes node agent |
| `9090` | Prometheus | Monitoring |
| `3000` | Grafana | Dashboards |

---

### 🔄 Port Ranges

| Range | Type | Who uses |
|-------|------|----------|
| 0 – 1023 | Well-known ports | System services (SSH, HTTP) |
| 1024 – 49151 | Registered ports | Applications (Jenkins, MySQL) |
| 49152 – 65535 | Dynamic/Ephemeral ports | Client connections |

---

### 💡 Commands

```bash
ss -tulnp                       # show all listening ports
ss -tulnp | grep 8080           # check specific port
lsof -i :8080                   # what process is using port 8080
nc -zv server-ip 8080           # test if port is open
```

---

### 🔥 DevOps Insight

> Common interview question:
> "Which ports should be open in security group for a web app?"
>
> Answer:
> - Port 22 → for SSH (only from your IP)
> - Port 80 → HTTP traffic (from anywhere)
> - Port 443 → HTTPS traffic (from anywhere)
> - App port (8080) → only from Load Balancer security group

---

## 📡 Topic 3: Protocols — TCP, UDP, ICMP

A protocol is a set of rules for how data is sent and received.

---

### 🔄 TCP vs UDP vs ICMP

| Protocol | Full name | Connection | Reliable | Use case |
|----------|-----------|-----------|----------|----------|
| TCP | Transmission Control Protocol | ✅ Yes | ✅ Yes | HTTP, SSH, databases |
| UDP | User Datagram Protocol | ❌ No | ❌ No | DNS, video, gaming |
| ICMP | Internet Control Message Protocol | ❌ No | ❌ No | ping, traceroute |

---

### 💡 Real Examples

```bash
# TCP — reliable, used for SSH
ssh ubuntu@ec2-ip               # TCP port 22

# UDP — fast, used for DNS
dig google.com                  # UDP port 53

# ICMP — used for ping
ping google.com                 # ICMP packets
```

---

## 🔡 Topic 4: CIDR Notation

CIDR = Classless Inter-Domain Routing. A way to express IP address ranges.

---

### 🧠 How CIDR Works

```
Format: IP address / prefix length

192.168.1.0/24
            ↑
            Prefix length = how many bits are fixed (network bits)
            Remaining bits = host bits (number of IPs in range)
```

---

### 🔢 CIDR Quick Reference

| CIDR | Subnet Mask | Total IPs | Usable IPs |
|------|-------------|-----------|------------|
| `/8` | 255.0.0.0 | 16,777,216 | Large network |
| `/16` | 255.255.0.0 | 65,536 | AWS VPC default |
| `/24` | 255.255.255.0 | 256 | 254 usable — small subnet |
| `/28` | 255.255.255.240 | 16 | 14 usable — tiny subnet |
| `/32` | 255.255.255.255 | 1 | Single IP address |
| `0.0.0.0/0` | — | All IPs | Entire internet |

---

### 💡 Real AWS Examples

```
VPC CIDR:              10.0.0.0/16   → 65,536 IPs for entire VPC
Public Subnet:         10.0.1.0/24   → 256 IPs for public resources
Private Subnet:        10.0.2.0/24   → 256 IPs for private resources
Security Group rule:   0.0.0.0/0     → allow from entire internet
Security Group rule:   10.0.0.0/16   → allow only from within VPC
Your IP only:          203.0.113.5/32 → allow only your specific IP
```

---

### 🔥 DevOps Insight

> In AWS Security Groups:
> - `0.0.0.0/0` = allow from ANYWHERE — use only for public ports
> - `10.0.0.0/16` = allow only within your VPC — use for internal services
> - `x.x.x.x/32` = allow only your specific IP — use for SSH access

---

## 🔪 Topic 5: Subnetting

Subnetting = dividing a large network into smaller networks.

---

### 🧠 Why Subnet?

```
Without subnetting:
→ All 65,536 IPs in one flat network
→ No separation between public and private
→ Security nightmare

With subnetting:
→ Public subnet: web servers accessible from internet
→ Private subnet: databases NOT accessible from internet
→ Better security, better organization
```

---

### 💡 Practical Example — AWS VPC Design

```
VPC: 10.0.0.0/16 (65,536 total IPs)
    │
    ├── Public Subnet AZ-1:  10.0.1.0/24  (256 IPs)
    │       └── Web servers, Load Balancers
    │
    ├── Public Subnet AZ-2:  10.0.2.0/24  (256 IPs)
    │       └── Web servers (high availability)
    │
    ├── Private Subnet AZ-1: 10.0.10.0/24 (256 IPs)
    │       └── App servers, Backend
    │
    ├── Private Subnet AZ-2: 10.0.11.0/24 (256 IPs)
    │       └── App servers (high availability)
    │
    ├── DB Subnet AZ-1:      10.0.20.0/24 (256 IPs)
    │       └── RDS, ElastiCache
    │
    └── DB Subnet AZ-2:      10.0.21.0/24 (256 IPs)
            └── RDS replica (high availability)
```

---

### 🔥 DevOps Insight

> Best practice — always have subnets in multiple AZs.
> If one AZ goes down, traffic routes to the other AZ automatically.
> This is how Netflix, Swiggy, Zepto achieve high availability.

---

## 🔄 Topic 6: NAT — Network Address Translation

NAT allows private IP addresses to communicate with the internet using a public IP.

---

### 🧠 How NAT Works

```
Private EC2 (10.0.2.5) wants to reach internet
    ↓
NAT Gateway (in public subnet, has public IP: 54.x.x.x)
    ↓
Internet sees: request from 54.x.x.x (not 10.0.2.5)
    ↓
Response comes back to NAT Gateway
    ↓
NAT Gateway forwards response to 10.0.2.5
```

---

### 🔄 Internet Gateway vs NAT Gateway

| | Internet Gateway (IGW) | NAT Gateway |
|-|----------------------|-------------|
| Direction | Both inbound and outbound | Outbound only |
| Used by | Public subnets | Private subnets |
| Internet can initiate | ✅ Yes | ❌ No |
| Cost | Free | Costs per hour + data |

---

### 🔥 DevOps Insight

> Private subnet EC2 needs to:
> - Pull Docker images → needs outbound internet → NAT Gateway
> - Install packages (apt update) → needs outbound internet → NAT Gateway
>
> But nobody from internet should initiate connection to it → NAT Gateway allows this.

---

## 📚 Topic 7: OSI Model — 7 Layers

OSI = Open Systems Interconnection. A conceptual model showing how data travels across a network.

---

### 🔢 The 7 Layers

| Layer | Number | Name | What it does | Example |
|-------|--------|------|-------------|---------|
| Application | 7 | Application | User-facing protocols | HTTP, HTTPS, DNS, SSH |
| Presentation | 6 | Presentation | Data formatting, encryption | SSL/TLS, JPEG, MP3 |
| Session | 5 | Session | Manages connections | Authentication, sessions |
| Transport | 4 | Transport | End-to-end delivery | TCP, UDP, ports |
| Network | 3 | Network | Routing between networks | IP, routers, ICMP |
| Data Link | 2 | Data Link | Node-to-node delivery | Ethernet, MAC address |
| Physical | 1 | Physical | Physical transmission | Cables, WiFi, fiber |

---

### 🧠 Memory Trick

```
All People Seem To Need Data Processing
A  P      S     T  N     D    P
7  6      5     4  3     2    1
```

---

### 💡 DevOps Perspective — Which Layers Matter Most

| Layer | DevOps relevance |
|-------|-----------------|
| Layer 7 — Application | HTTP status codes, API calls, nginx, Load Balancer |
| Layer 4 — Transport | TCP/UDP ports, security groups by port |
| Layer 3 — Network | IP addresses, route tables, VPC, subnets |
| Layer 2 — Data Link | MAC addresses (less relevant for cloud DevOps) |

---

### 🔥 DevOps Insight

> When debugging network issues — think in layers bottom-up:
> 1. L3 — Can I ping the host? (is IP reachable?)
> 2. L4 — Can I telnet to the port? (is port open?)
> 3. L7 — Does curl return 200? (is app responding?)

---

## 📋 Topic 8: Common Networking Commands — Quick Reference

```bash
# IP and interfaces
ip addr show                    # show all network interfaces and IPs
ip route show                   # show routing table
ifconfig                        # older way to show interfaces

# Connectivity testing
ping google.com                 # test if host is alive (ICMP)
ping -c 4 google.com            # ping exactly 4 times
traceroute google.com           # trace path to host
mtr google.com                  # combined ping + traceroute

# Port testing
nc -zv server 8080              # test if port is open
telnet server 8080              # alternate port test
ss -tulnp                       # show all listening ports
ss -tulnp | grep 8080           # check specific port

# DNS
nslookup google.com             # basic DNS lookup
dig google.com                  # detailed DNS info
dig google.com A                # only A records
dig google.com +short           # just the IP

# HTTP testing
curl http://server:8080/health  # test HTTP endpoint
curl -I https://google.com      # show headers only
curl -v https://google.com      # verbose — show full request/response
wget https://file.zip           # download file

# Network stats
netstat -tulnp                  # open ports (older)
ss -s                           # network statistics summary
sar -n DEV 1 5                  # network bandwidth per second
```

---

## ✅ Checklist for This File

- [ ] Understand IPv4 address format and private ranges
- [ ] Know public IP vs private IP difference
- [ ] Know important ports — 22 80 443 8080 3306 6443
- [ ] Understand TCP vs UDP vs ICMP
- [ ] Can read CIDR notation — /16 /24 /32
- [ ] Understand subnetting — why we divide networks
- [ ] Know NAT Gateway vs Internet Gateway
- [ ] Know OSI model — 7 layers and their purpose
- [ ] Can use all networking commands

---

> Previous: [← How Networks Work](./02-How-Networks-Work.md)
> Next: [How Google.com Works — OSI Model →](./04-How-Google-Works-OSI.md)
