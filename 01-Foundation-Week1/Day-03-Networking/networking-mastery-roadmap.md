# Networking Mastery Roadmap — Foundation-First Sequencing
### For AWS, DevOps, SRE, Kubernetes, and DevSecOps

## Why this order

Kubernetes networking (CNI, kube-proxy, Services, Ingress, Cilium, Istio) is not a new
kind of networking — it's the *same* IP routing, NAT, load balancing, DNS, and TCP
concepts, just re-implemented inside a cluster abstraction. If you learn K8s networking
before those fundamentals are automatic, you end up memorizing YAML behavior. If you
learn the fundamentals first, K8s networking becomes "recognize the pattern, map it to
what I already know" instead of "learn a new subject."

So this roadmap is split into two blocks:

- **Block A — Foundation Mastery** (no Kubernetes, no service mesh, no tool zoo).
  Everything here is protocol- and OS-level, plus AWS as the practical playground
  for applying it. You stay here until it's reflexive, not memorized.
- **Block B — Tooling & Kubernetes Expansion.**
  Once Block A is solid, every K8s/mesh/observability concept gets explicitly
  mapped back to the fundamental you already know, so nothing feels new — it
  feels like recognition.

A **Readiness Checklist** sits between the two blocks. Don't move to Block B until
you can pass it without looking anything up.

---

## BLOCK A — FOUNDATION MASTERY
*(This is where you go deep and slow. Everything downstream depends on this.)*

### Phase 1 — Computer Networking Fundamentals

**Core concepts**
- What is networking? LAN, WAN, MAN. Internet architecture.
- Network devices and their exact job: Switch, Hub, Router, Firewall, Gateway,
  Bridge, Load Balancer, Proxy, Reverse Proxy, NAT, PAT.

**OSI Model** — every layer explained with intuition + packet-level behavior:
Physical, Data Link, Network, Transport, Session, Presentation, Application.

**TCP/IP Model**
- Ethernet, Frames, Packets, Segments
- Encapsulation and Decapsulation (what actually gets added/stripped at each hop)

**IP Addressing**
- IPv4, IPv6, CIDR, Subnetting, VLSM
- Public vs Private IP, Broadcast, Network Address, Gateway, Loopback, APIPA

**Transport Layer**
- TCP vs UDP, 3-way handshake, Flow Control, Sliding Window
- Congestion Control, Retransmission, MTU, MSS

**DNS (deep dive)**
- Recursive Resolver, Root Server, TLD, Authoritative Server
- A, AAAA, MX, TXT, CNAME, SRV records, TTL

**HTTP/HTTPS**
- Methods, Status Codes, Headers, Cookies, Sessions, JWT
- TLS, SSL, Certificates, Mutual TLS, HTTP/2, HTTP/3, QUIC

**Routing**
- Static vs Dynamic Routing, BGP, OSPF, RIP, Default Route, Route Table

**Labs:** trace a full request lifecycle by hand (DNS lookup → TCP handshake → TLS
handshake → HTTP request/response) using only `dig`, `curl -v`, and packet capture.

---

### Phase 2 — Linux Networking

**Core concepts**
- Network interfaces, NIC, routing table, ARP, ICMP
- TCP connections and UDP sockets from the OS's point of view

**Commands to master (not just know, master):**
`ip`, `ss`, `netstat`, `tcpdump`, `tshark`, `traceroute`, `ping`, `dig`,
`nslookup`, `curl`, `wget`, `nc`, `telnet`, `nmap`, `arp`

**Labs**
- Capture packets and read a TCP handshake yourself in `tcpdump`/Wireshark
- DNS troubleshooting from the command line only
- Break and fix routing on a VM
- Debug an `iptables`/firewall rule blocking traffic

*Checkpoint: you should be able to explain, from raw `tcpdump` output with no
labels, which packet is the SYN, SYN-ACK, ACK, and where a connection died.*

---

### Phase 3 — AWS Networking (Deep Dive)

Every service below gets: **why it exists → when to use it → traffic flow →
limitations → real production example → interview questions → troubleshooting
scenario.**

- VPC, CIDR Planning, Subnets (public vs private), Route Tables
- Internet Gateway, NAT Gateway, NAT Instance, Egress-only Internet Gateway
- Elastic IP, ENI
- Security Groups vs NACL (stateful vs stateless — this distinction alone
  resolves half of all "why can't I connect" incidents)
- VPC Peering, Transit Gateway, AWS PrivateLink, VPC Endpoints
- Direct Connect, Site-to-Site VPN, Client VPN
- Route53, Route53 Resolver
- Global Accelerator, CloudFront
- ELB family: ALB, NLB, GWLB
- WAF, Shield, Network Firewall

**Labs:** build a VPC from scratch by hand (no Terraform yet) — public/private
subnets, IGW, NAT, route tables, security groups — and break each component
deliberately to see the exact failure signature it produces.

---

## READINESS CHECKLIST — Do not skip to Block B until you can do all of this cold:

- [ ] Draw the full packet path of `https://example.com` from browser to server and
      back, labeling every OSI layer, without notes.
- [ ] Explain the difference between a Security Group and a NACL using only the
      words "stateful" and "stateless," with an example of a failure each one causes.
- [ ] Given raw `tcpdump` output of a failed connection, identify whether the
      failure is DNS, TCP handshake, TLS handshake, or application-layer.
- [ ] Explain NAT Gateway vs Internet Gateway vs NAT instance from memory, with
      a diagram.
- [ ] Explain what happens, hop by hop, when a private subnet EC2 instance tries
      to reach the internet and fails.
- [ ] Explain BGP well enough to describe (at a high level) why Direct Connect
      and VPN routes can conflict.

If any of these feel shaky, stay in Block A. This is the investment that makes
Block B fast instead of confusing.

---

## BLOCK B — TOOLING & KUBERNETES EXPANSION
*(Only start here once the checklist above is automatic. Every concept below is
explicitly mapped back to something you already know from Block A.)*

### Phase 4 — Kubernetes Networking
*Framed as: "this is the same routing/NAT/DNS/load-balancing you already know,
running inside a cluster abstraction."*

- CNI — think of this as "who assigns IPs and wires up routes, cluster-wide"
  (maps to: IP addressing, routing tables from Phase 1/2)
- Pod networking, Service networking — ClusterIP, NodePort, LoadBalancer
  (maps to: NAT/PAT, Security Groups vs firewall rules)
- Ingress / Egress (maps to: reverse proxy, ALB path/header routing from Phase 3)
- kube-proxy, IPVS, IPTables (maps to: the `iptables` you already debugged in Phase 2)
- CoreDNS (maps to: recursive resolver / authoritative server model from Phase 1)
- Network Policies (maps to: Security Groups/NACL statefulness)
- Overlay networks — Calico, Cilium, Flannel (maps to: VPC peering / overlay
  vs underlay routing)
- MetalLB (maps to: ELB/NLB concepts applied on-prem)
- Service Mesh — Istio, Linkerd, Envoy Proxy (maps to: reverse proxy + TLS/mTLS
  from Phase 1 and 6)

**Traffic flow to trace end-to-end:**
```
Internet
  ↓
Load Balancer   (same ALB/NLB concepts from Phase 3)
  ↓
Ingress         (same reverse-proxy/path-routing concepts from Phase 5)
  ↓
Service         (same NAT/virtual-IP concepts from Phase 1/3)
  ↓
Pod             (same IP addressing/routing concepts from Phase 1/2)
  ↓
Container
  ↓
Application     (same TCP/HTTP concepts from Phase 1)
```

---

### Phase 5 — Production Load Balancing
HAProxy, NGINX, Envoy, Reverse Proxy, Sticky Sessions, Health Checks, SSL
Termination, Path Routing, Header Routing, Rate Limiting, Caching, CDN.

### Phase 6 — DevSecOps Networking
TLS, PKI, Certificates, Certificate Rotation, Secrets Management, Vault, IAM,
Security Groups, Zero Trust, Network Segmentation, WAF, IDS, IPS, DDoS Protection.

### Phase 7 — Production Observability
Prometheus, Grafana, Loki, Tempo, Jaeger, CloudWatch, VPC Flow Logs, ELB/ALB
Logs, CloudTrail, Wireshark.
Troubleshoot: high latency, packet loss, DNS failures, TLS failures, timeouts,
connection resets, 502/503/504, pod communication failures.

### Phase 8 — Real Incident Troubleshooting Labs
Website not loading · ALB 502 · Pod unreachable · DNS broken · Security Group
blocking traffic · NACL issue · Route table issue · NAT Gateway failure ·
Route53 failure · Expired TLS cert · Service Mesh issue · VPC Endpoint failure ·
VPN down · Hybrid network outage.

For each: symptoms → root cause → investigation process → Linux commands →
AWS console checks → logs to inspect → metrics to monitor → resolution.

### Phase 9 — Architecture Thinking
Design networking for: Startup, SaaS, FinTech, Banking, Trading Platform,
Microservices, Multi-region, Disaster Recovery, High Availability, Low Latency
systems — with every decision explained.

---

## Practical Requirements (applies to every topic)
Theory → AWS implementation → Linux implementation → Kubernetes implementation
(Block B only) → packet flow explanation → real production examples →
hands-on labs → mini projects → interview questions → common mistakes →
troubleshooting checklist → best practices.

## Hands-on Lab Tools
AWS Free Tier, EC2, VPC, Docker, Kubernetes (kind/Minikube/EKS — Block B only),
Linux, NGINX, HAProxy, Terraform, GitHub Actions, Wireshark, tcpdump.

## Teaching Style
Senior Principal Cloud Architect / SRE mentor voice, 15+ years production
experience. For every concept: intuition → theory → packet flow → what happens
behind the scenes → hands-on demo → common production failures → how
experienced engineers debug it → ASCII diagrams/flowcharts where useful.

Goal: not memorization — an internal model of how networking actually behaves
in production, deep enough that Block B tooling feels like recognition, not
new material.
