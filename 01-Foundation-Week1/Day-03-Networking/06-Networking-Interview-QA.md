# 📄 06 — Networking Interview Q&A

> Previous: [← AWS VPC](./05-AWS-VPC.md)
> Back to: [Networking Home →](./README.md)

---

## 📌 How to Use This File

- Read each answer once
- Close the file — say the answer out loud
- Practice until you answer in 60 seconds naturally

---

## 🔴 Area 1 — Most Asked in Every Interview

---

## ❓ Q1. What happens when you type google.com in your browser?

### ✅ Complete Answer to Say

> "Several things happen in sequence.
>
> First DNS resolution — browser checks its cache, then /etc/hosts, then queries DNS resolver. Resolver goes Root DNS → TLD → Google's Authoritative DNS and returns the IP address.
>
> Second TCP 3-way handshake — browser connects to port 443 with SYN, SYN-ACK, ACK.
>
> Third TLS handshake — certificates are exchanged, verified, and an encrypted tunnel is created.
>
> Fourth HTTP GET request is sent through the encrypted tunnel.
>
> Fifth the request routes through internet routers, reaches Google's firewall, passes through Load Balancer, reaches the web server.
>
> Server processes and sends back HTTP 200 response with HTML. Browser renders the page.
>
> All 7 OSI layers are involved — physical transmission at Layer 1 up to HTTP at Layer 7."

---

## ❓ Q2. What is the difference between HTTP and HTTPS?

### 🔄 Comparison Table

| | HTTP | HTTPS |
|-|------|-------|
| Port | 80 | 443 |
| Encryption | ❌ None | ✅ TLS encrypted |
| Data visible | ✅ Anyone can read | ❌ Encrypted |
| Certificate needed | ❌ No | ✅ Yes — SSL cert |
| Use in production | ❌ Never | ✅ Always |

### ✅ Complete Answer to Say

> "HTTP and HTTPS both transfer data between browser and server but HTTPS adds security.
>
> HTTP runs on port 80. Data is transmitted in plain text — anyone between browser and server can read it. Never use in production.
>
> HTTPS runs on port 443. It uses TLS to encrypt all data. Even if someone intercepts the packets, they see only encrypted gibberish. Requires an SSL certificate — free with Let's Encrypt or AWS ACM.
>
> In DevOps, we always set up HTTPS. We get SSL certificate from ACM, attach to Load Balancer, and redirect all HTTP traffic to HTTPS."

---

## ❓ Q3. What is DNS? Explain how it works.

### ✅ Complete Answer to Say

> "DNS — Domain Name System — is the phonebook of the internet. It translates domain names like google.com into IP addresses like 142.250.77.46 that computers use.
>
> When I type google.com, the browser first checks its own cache. Then checks /etc/hosts. If not found, it asks the Recursive DNS Resolver — usually the ISP's DNS server.
>
> The resolver asks the Root DNS Server — who handles .com? Root says ask the .com TLD server. TLD says ask Google's Authoritative DNS. Authoritative DNS returns the IP address.
>
> The result is cached for the TTL duration — typically 300 seconds to 24 hours.
>
> In AWS, Route53 is the DNS service. I create A records to point domains to EC2 IPs, and CNAME records to point to Load Balancer DNS names."

---

## ❓ Q4. What is the difference between TCP and UDP?

### 🔄 Comparison Table

| | TCP | UDP |
|-|-----|-----|
| Connection | ✅ 3-way handshake | ❌ Connectionless |
| Reliability | ✅ Guaranteed delivery | ❌ Best effort |
| Order | ✅ In order | ❌ No guarantee |
| Speed | Slower | Faster |
| Use case | HTTP, SSH, databases | DNS, video, gaming |

### ✅ Complete Answer to Say

> "TCP and UDP are transport layer protocols but they work differently.
>
> TCP establishes a connection with a 3-way handshake — SYN, SYN-ACK, ACK. It guarantees delivery — if a packet is lost, it is re-sent. Data arrives in order. Used for HTTP, SSH, databases — where reliability matters.
>
> UDP is connectionless — just sends packets without handshake. No guarantee of delivery or order. But it is faster. Used for DNS lookups, video streaming, gaming — where speed matters more than perfect reliability.
>
> In DevOps — most services use TCP. DNS uses UDP for queries but TCP for zone transfers."

---

## ❓ Q5. What is a subnet? What is CIDR notation?

### ✅ Complete Answer to Say

> "A subnet is a smaller network carved out of a larger network. In AWS, I divide my VPC into subnets — public subnets for resources that need internet access and private subnets for resources that should not be directly accessible from internet.
>
> CIDR notation represents an IP address range. For example, 10.0.0.0/16 means the first 16 bits are fixed — the network portion — and the remaining 16 bits can vary. This gives 65,536 possible IP addresses.
>
> 10.0.1.0/24 means first 24 bits fixed, 8 bits variable — 256 IPs.
> 0.0.0.0/0 means no bits fixed — represents the entire internet.
> x.x.x.x/32 means all 32 bits fixed — a single specific IP address.
>
> In AWS security groups, I use /32 to allow only my specific IP for SSH, and 0.0.0.0/0 to allow public web traffic."

---

## ❓ Q6. What is VPC? Why do we need it?

### ✅ Complete Answer to Say

> "VPC — Virtual Private Cloud — is your own isolated private network inside AWS. When you use AWS, you are sharing the same physical infrastructure with millions of other customers. VPC ensures your resources are completely isolated — nobody else can access them by default.
>
> Inside a VPC you define your own IP address range using CIDR, create subnets, control routing with Route Tables, and set up firewalls with Security Groups and NACLs.
>
> Best practice is to put web servers in public subnets — they need to receive internet traffic. And databases in private subnets — they should never be directly accessible from internet. Only the web servers can talk to the database through the private network.
>
> In my project, I provisioned a VPC with public and private subnets using Terraform. Internet Gateway for public subnets, NAT Gateway for private subnets to reach internet for updates."

---

## ❓ Q7. What is the difference between Internet Gateway and NAT Gateway?

### 🔄 Comparison Table

| | Internet Gateway | NAT Gateway |
|-|-----------------|-------------|
| Direction | Inbound + Outbound | Outbound only |
| Used by | Public subnets | Private subnets |
| Internet can initiate | ✅ Yes | ❌ No |
| Cost | Free | ~$0.045/hr + data |
| HA | Built-in | Need one per AZ |

### ✅ Complete Answer to Say

> "Internet Gateway allows two-way communication between your public subnet resources and the internet. Resources in public subnet can receive traffic from internet and send traffic to internet. EC2 instances must have a public IP for this to work.
>
> NAT Gateway allows private subnet resources to initiate outbound connections to internet — for example to run apt update or pull Docker images — but internet cannot initiate connection into private subnet. NAT Gateway sits in a public subnet with an Elastic IP.
>
> Best practice: put databases and backend services in private subnet. They can reach internet through NAT for updates, but nobody from internet can reach them directly."

---

## ❓ Q8. What is the difference between Security Group and NACL?

### 🔄 Comparison Table

| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | EC2 Instance | Subnet |
| State | Stateful | Stateless |
| Default | Deny all inbound | Allow all |
| Rules | Allow only | Allow and Deny |
| Order | All rules evaluated | Rules by number |
| Return traffic | Auto allowed | Must explicitly allow |

### ✅ Complete Answer to Say

> "Security Groups and NACLs are both firewalls in AWS but at different levels.
>
> Security Group works at the EC2 instance level. It is stateful — if I allow inbound traffic on port 80, the return traffic is automatically allowed. I can only write allow rules — no deny. Security groups are my primary defense.
>
> NACL works at the subnet level. It is stateless — I must explicitly allow both inbound and outbound traffic. I can write both allow and deny rules. Rules are evaluated in number order — first match wins.
>
> I use Security Groups for most firewall needs. NACLs add a second layer of defense — for example, blocking a specific IP address range at subnet level."

---

## ❓ Q9. What is OSI model? Name the 7 layers.

### 🔄 OSI Layers Table

| Layer | Number | Name | Examples |
|-------|--------|------|---------|
| Application | 7 | Application | HTTP, HTTPS, DNS, SSH |
| Presentation | 6 | Presentation | TLS/SSL, encryption |
| Session | 5 | Session | Authentication, sessions |
| Transport | 4 | Transport | TCP, UDP, ports |
| Network | 3 | Network | IP, routers |
| Data Link | 2 | Data Link | Ethernet, MAC |
| Physical | 1 | Physical | Cables, WiFi |

### ✅ Complete Answer to Say

> "OSI model — Open Systems Interconnection — is a framework showing how data travels across a network in 7 layers.
>
> Layer 1 Physical — actual physical transmission — cables, WiFi signals.
> Layer 2 Data Link — node to node delivery — Ethernet, MAC addresses.
> Layer 3 Network — routing between networks — IP addresses, routers.
> Layer 4 Transport — end to end delivery — TCP, UDP, port numbers.
> Layer 5 Session — managing connections — authentication, sessions.
> Layer 6 Presentation — data formatting and encryption — TLS, SSL.
> Layer 7 Application — user-facing protocols — HTTP, HTTPS, DNS, SSH.
>
> Memory trick: All People Seem To Need Data Processing — A P S T N D P — 7 to 1.
>
> In DevOps I mostly work with Layer 3 (IP, routing), Layer 4 (ports, TCP), and Layer 7 (HTTP, application protocols)."

---

## ❓ Q10. What are common HTTP status codes?

### 📋 Status Codes Table

| Code | Meaning | When you see it |
|------|---------|----------------|
| `200` | OK | Everything fine |
| `201` | Created | Resource created successfully |
| `301` | Moved Permanently | URL has changed |
| `400` | Bad Request | Wrong data sent by client |
| `401` | Unauthorized | Login required |
| `403` | Forbidden | Not allowed |
| `404` | Not Found | Resource doesn't exist |
| `500` | Internal Server Error | App crashed |
| `502` | Bad Gateway | Upstream server failed |
| `503` | Service Unavailable | Server overloaded |
| `504` | Gateway Timeout | Upstream server too slow |

### ✅ Complete Answer to Say

> "HTTP status codes tell you what happened with a request. They are grouped in hundreds.
>
> 2xx means success — 200 is the standard OK, 201 is resource created.
>
> 3xx means redirect — 301 is permanent redirect, 302 is temporary.
>
> 4xx means client error — 400 bad request, 401 authentication needed, 403 permission denied, 404 not found.
>
> 5xx means server error — 500 app crashed on server side, 502 bad gateway usually means nginx cannot reach the backend app, 503 service unavailable means server is overloaded, 504 gateway timeout means backend took too long to respond.
>
> In DevOps, 502 and 503 are the most common production issues. 502 means your app is down or not listening. 503 means it is overwhelmed."

---

## 🟡 Area 2 — Good to Know

---

## ❓ Q11. What is a Load Balancer? Types in AWS?

### ✅ Complete Answer to Say

> "A Load Balancer distributes incoming traffic across multiple servers so no single server is overwhelmed. It also does health checks and removes unhealthy servers from rotation automatically.
>
> In AWS there are three types.
>
> ALB — Application Load Balancer — works at Layer 7, HTTP/HTTPS. Can route based on URL path — /api to one group, /web to another. Best for web applications. Integrates with ACM for SSL.
>
> NLB — Network Load Balancer — works at Layer 4, TCP/UDP. Handles millions of requests per second with very low latency. Used for gaming, IoT, or when you need static IPs.
>
> CLB — Classic Load Balancer — old, being deprecated. Avoid for new projects.
>
> In my project I use ALB for HTTP traffic and attach SSL certificate from ACM for HTTPS."

---

## ❓ Q12. What is the difference between ping, curl, and telnet?

### 🔄 Comparison Table

| Command | Protocol | Tests |
|---------|----------|-------|
| `ping` | ICMP | Is host reachable? |
| `telnet` | TCP | Is port open? |
| `curl` | HTTP/HTTPS | Is web service responding? |

### ✅ Complete Answer to Say

> "All three test connectivity but at different layers.
>
> ping uses ICMP to check if a host is reachable at network layer. Just answers — is this IP alive?
>
> telnet tests TCP connectivity to a specific port. If it connects, port is open. Used to check if a service is listening.
>
> curl tests at application layer — sends an actual HTTP request and shows the response. I use curl -I to check headers and status code, and curl health-endpoint to verify app is running.
>
> In DevOps debugging — ping first to confirm host is alive, telnet to confirm port is open, curl to confirm app is responding. This isolates whether the problem is network, firewall, or application."

---

## ❓ Q13. What is NAT?

### ✅ Complete Answer to Say

> "NAT — Network Address Translation — allows devices with private IP addresses to communicate with the internet using a shared public IP.
>
> A private EC2 with IP 10.0.2.5 cannot communicate directly with internet — that IP is not routable on internet. NAT Gateway sits in the public subnet with an Elastic IP. When private EC2 sends traffic, NAT replaces the source IP 10.0.2.5 with its own public IP and sends to internet. When response comes back, NAT translates it back and forwards to 10.0.2.5.
>
> This is exactly how your home router works — your laptop has 192.168.1.x which is private, but your router does NAT so you can browse internet from that private IP."

---

## ❓ Q14. What is a Firewall? Types?

### ✅ Complete Answer to Say

> "A firewall is a security system that controls which network traffic is allowed in and out based on rules.
>
> In DevOps there are several types.
>
> Linux firewall — ufw or iptables — OS level, controls traffic on the server itself.
>
> AWS Security Group — instance level firewall, attached to EC2. Stateful — return traffic automatically allowed. Allow rules only.
>
> AWS NACL — subnet level firewall, applies to all resources in the subnet. Stateless — must allow both directions. Can deny specific IPs.
>
> Kubernetes Network Policy — pod level firewall, controls which pods can talk to which pods.
>
> In production I use Security Groups as primary, NACLs as additional layer, and Network Policies inside Kubernetes cluster."

---

## ❓ Q15. What is VPC Peering?

### ✅ Complete Answer to Say

> "VPC Peering connects two VPCs so their resources can communicate using private IP addresses without traffic going over the internet.
>
> For example, a production VPC and a shared services VPC can be peered. Resources in production VPC can reach monitoring tools in shared VPC using private IPs.
>
> VPC peering is not transitive. If VPC-A is peered with VPC-B, and VPC-B is peered with VPC-C, VPC-A cannot reach VPC-C through VPC-B. Each pair needs its own peering connection.
>
> For connecting many VPCs, AWS Transit Gateway is better than multiple peering connections."

---

## 🔥 Bonus — Common Network Debug Commands

```bash
# DNS debugging
nslookup google.com             # basic lookup
dig google.com                  # detailed
dig google.com +short           # just IP
dig @8.8.8.8 google.com        # use specific DNS server

# Connectivity
ping -c 4 server-ip             # ping 4 times
traceroute server-ip            # trace hops to server
mtr server-ip                   # live traceroute

# Port testing
nc -zv server-ip 8080           # test port open
telnet server-ip 8080           # alternate
ss -tulnp | grep 8080           # local port check

# HTTP testing
curl -I https://domain.com      # headers only
curl -v https://domain.com      # verbose
curl -w "%{http_code}" -o /dev/null -s url  # just status code

# Network timing
curl -w "\nDNS:%{time_namelookup}s TCP:%{time_connect}s TLS:%{time_appconnect}s Total:%{time_total}s\n" \
     -o /dev/null -s https://google.com
```

---

## ✅ Complete Networking Checklist

### Must Know — Every Interview
- [ ] Explain what happens when you type google.com
- [ ] HTTP vs HTTPS — port, encryption, TLS
- [ ] DNS — how resolution works, record types
- [ ] TCP vs UDP — when to use each
- [ ] CIDR notation — /16 /24 /32 0.0.0.0/0
- [ ] VPC — what it is, why we need it
- [ ] Public vs Private subnet — difference
- [ ] IGW vs NAT Gateway — when to use each
- [ ] Security Group vs NACL — key differences
- [ ] OSI model — 7 layers by name
- [ ] HTTP status codes — 200 404 500 502 503
- [ ] Important ports — 22 80 443 8080 3306 6443

### Good to Know
- [ ] Load Balancer types — ALB NLB
- [ ] VPC Peering — what it is
- [ ] NAT — how it works
- [ ] ping vs curl vs telnet — each layer tested
- [ ] Common debug commands

---

> Previous: [← AWS VPC](./05-AWS-VPC.md)
> Back to: [Networking Home →](./README.md)
