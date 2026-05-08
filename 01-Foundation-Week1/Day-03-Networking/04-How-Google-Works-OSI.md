# 📄 04 — How Google.com Works — OSI Model in Action

> Previous: [← Networking Concepts](./03-Networking-Concepts.md)
> Next: [AWS VPC →](./05-AWS-VPC.md)

---

## 📌 Why This Question Matters

> "What happens when you type google.com in your browser?"

This is **the most asked networking question** in DevOps and SRE interviews.
It tests your understanding of DNS, TCP, TLS, HTTP, OSI layers — all in one question.

---

## 🔄 Complete Journey — Step by Step

---

### ⌨️ Step 0 — You Type google.com and Press Enter

```
Browser receives: google.com
Goal: Find the IP address of google.com and load the page
```

---

### 🔍 Step 1 — DNS Resolution (Layer 7 + Layer 3)

Browser needs to convert `google.com` → IP address.

```
1.1 — Browser checks its own DNS cache
      "Did I look up google.com recently?"
      If YES → skip to Step 2 with cached IP
      If NO  → continue

1.2 — OS checks /etc/hosts file
      Any local override for google.com?
      If YES → use that IP
      If NO  → continue

1.3 — OS checks its DNS resolver (/etc/resolv.conf)
      Usually your ISP's DNS or 8.8.8.8 (Google DNS)

1.4 — Recursive DNS Resolver contacts Root DNS Server
      "Who handles .com domains?"
      Root Server → "Ask the .com TLD server"

1.5 — Recursive Resolver asks .com TLD Server
      "Who is authoritative for google.com?"
      TLD Server → "Ask ns1.google.com"

1.6 — Recursive Resolver asks Google's Authoritative DNS
      "What is the IP of google.com?"
      Answer → 142.250.77.46

1.7 — Browser receives IP: 142.250.77.46
      Result cached for TTL duration (e.g., 300 seconds)
```

---

### 🤝 Step 2 — TCP Connection — 3-Way Handshake (Layer 4)

Browser now knows server IP: `142.250.77.46`
Browser wants to connect to port `443` (HTTPS)

```
Your Browser (Port 54321)         Google Server (Port 443)
       │                                   │
       │──── SYN ────────────────────────►│
       │     "Can we connect?"             │
       │                                   │
       │◄─── SYN-ACK ────────────────────│
       │     "Yes, I'm ready"              │
       │                                   │
       │──── ACK ────────────────────────►│
       │     "Great, let's go"             │
       │                                   │
       │     TCP Connection Established    │
```

---

### 🔒 Step 3 — TLS Handshake (Layer 6 — Presentation)

Since URL is HTTPS — a secure encrypted tunnel must be created.

```
Browser                              Google Server
   │                                       │
   │──── Client Hello ───────────────────►│
   │     TLS version, cipher suites        │
   │                                       │
   │◄─── Server Hello ─────────────────────│
   │     Chosen TLS version                │
   │◄─── SSL Certificate ──────────────────│
   │     "I am google.com — here's proof"  │
   │                                       │
   │     Browser verifies certificate      │
   │     Is it signed by trusted CA? ✅    │
   │     Is it for google.com? ✅          │
   │     Is it expired? ❌ No              │
   │                                       │
   │──── Session Key (encrypted) ────────►│
   │                                       │
   │     Encrypted tunnel ready ✅         │
   │◄══════════════════════════════════════►│
```

---

### 📨 Step 4 — HTTP GET Request (Layer 7 — Application)

Now browser sends the actual request through the encrypted tunnel.

```
GET / HTTP/1.1
Host: www.google.com
User-Agent: Mozilla/5.0 (Chrome/120)
Accept: text/html,application/xhtml+xml
Accept-Language: en-US
Connection: keep-alive
Cookie: CONSENT=YES+...
```

---

### 🌍 Step 5 — Routing Through Internet (Layer 3 — Network)

The HTTP request is broken into packets and routed across the internet.

```
Your Router (192.168.1.1)
    ↓
ISP Router (Mumbai)
    ↓
Internet Exchange Point
    ↓ (multiple hops through routers)
    ↓ each router reads destination IP
    ↓ forwards to next router closer to destination
Google's Network Edge (CDN — nearest location)
    ↓
Google's Data Center
```

---

### 🔢 Step 6 — IP Packet Structure (Layer 3)

Each packet carries:

```
┌─────────────────────────────────────┐
│ Source IP:      203.0.113.5         │ ← Your IP
│ Destination IP: 142.250.77.46       │ ← Google's IP
│ TTL:           64                   │ ← Decrements at each router
│ Protocol:      TCP (6)              │
├─────────────────────────────────────┤
│ TCP Header                          │
│ Source Port: 54321                  │ ← Your browser's port
│ Dest Port:   443                    │ ← HTTPS port
├─────────────────────────────────────┤
│ Payload (HTTP request - encrypted)  │
└─────────────────────────────────────┘
```

---

### 🛡️ Step 7 — Firewall Check (Layer 3/4)

Before reaching Google's server — firewall checks:

```
Is source IP blocked? → No ✅
Is destination port 443 allowed? → Yes ✅
Is it a valid TCP connection? → Yes ✅
Pass through → ✅
```

---

### ⚖️ Step 8 — Load Balancer (Layer 4/7)

Google has millions of servers. Load Balancer decides which one handles your request.

```
Incoming request → Google Load Balancer
    ↓
Health check → which servers are healthy?
    ↓
Algorithm → Round Robin / Least Connections
    ↓
Route to → Server-1234 in Data Center
```

---

### ⚙️ Step 9 — Server Processes Request (Layer 7)

Google's web server (nginx) receives your request:

```
nginx receives: GET / HTTP/1.1
    ↓
Check URL → / (homepage)
    ↓
Is it cached? → Yes → return cached HTML
    ↓
Application processes → generates HTML
    ↓
HTTP Response:
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 123456
... (HTML of Google homepage)
```

---

### 🔙 Step 10 — Response Returns to Browser

```
Google Server → Load Balancer → Firewall → Internet →
ISP → Your Router → Your Browser

All steps reversed — same path back
Data is encrypted throughout (TLS)
```

---

### 🖥️ Step 11 — Browser Renders Page (Layer 7)

```
Browser receives HTML
    ↓
Parse HTML → find CSS, JS, images
    ↓
Make additional requests for each resource
(Each resource = same DNS → TCP → TLS → HTTP journey)
    ↓
Render page → display to user 🎉
```

---

## 🔄 OSI Model Mapping — google.com Journey

| OSI Layer | Name | What happened in our journey |
|-----------|------|------------------------------|
| Layer 7 | Application | DNS query, HTTP GET request, HTTP 200 response |
| Layer 6 | Presentation | TLS encryption/decryption of data |
| Layer 5 | Session | TLS session maintained between browser and server |
| Layer 4 | Transport | TCP 3-way handshake, port 443, packet ordering |
| Layer 3 | Network | IP routing through routers, TTL decrements |
| Layer 2 | Data Link | Ethernet frames between routers |
| Layer 1 | Physical | Electrical signals on cables, WiFi radio waves |

---

## ⏱️ How Long Does All This Take?

```
DNS Resolution:      ~20-100ms
TCP Handshake:       ~20-50ms
TLS Handshake:       ~50-100ms
HTTP Request:        ~50-200ms
Server Processing:   ~10-50ms
Data Transfer:       depends on page size

Total: ~150-500ms for a typical page load
```

---

## 🔥 DevOps Insight

> **How to debug slow page loads:**
>
> ```bash
> # Check DNS resolution time
> time nslookup google.com
>
> # Check full request timing
> curl -w "\nDNS: %{time_namelookup}s\nConnect: %{time_connect}s\nTLS: %{time_appconnect}s\nTotal: %{time_total}s\n" -o /dev/null -s https://google.com
> ```
>
> This tells you exactly which step is slow.

---

## ✅ Complete Interview Answer

When asked **"What happens when you type google.com?"** — say this:

> "Several things happen in sequence.
>
> First — DNS resolution. The browser checks its cache, then OS /etc/hosts file, then queries a DNS resolver. The resolver goes through Root DNS → TLD Server → Google's Authoritative DNS and gets the IP address — 142.250.77.46.
>
> Second — TCP 3-way handshake. Browser connects to port 443 on that IP with SYN, SYN-ACK, ACK — establishing a reliable connection.
>
> Third — TLS handshake. Since it is HTTPS, browser and server exchange certificates, verify the certificate is valid and trusted, then create an encrypted tunnel. All data from this point is encrypted.
>
> Fourth — HTTP GET request. Browser sends GET / HTTP/1.1 through the encrypted tunnel with headers like Host, User-Agent, and cookies.
>
> Fifth — the request travels through internet routers, each one reading the destination IP and forwarding the packet closer to Google's network.
>
> Sixth — Google's firewall checks if the request is allowed. Load balancer routes to a healthy server.
>
> Seventh — server processes the request, generates HTML response, sends it back.
>
> Response travels back the same path, browser decrypts it, parses HTML, makes additional requests for CSS, JS, and images, then renders the final page.
>
> In terms of OSI layers — this involves all 7 layers from physical transmission at Layer 1 to HTTP at Layer 7."

---

## ✅ Checklist for This File

- [ ] Can explain DNS resolution — all 7 steps
- [ ] Can explain TCP 3-way handshake — SYN SYN-ACK ACK
- [ ] Can explain TLS handshake — certificates and encryption
- [ ] Know HTTP request structure — method, host, headers
- [ ] Understand how packets route through internet
- [ ] Can map each step to OSI layer
- [ ] Can answer "what happens when you type google.com" in 2 minutes

---

> Previous: [← Networking Concepts](./03-Networking-Concepts.md)
> Next: [AWS VPC →](./05-AWS-VPC.md)
