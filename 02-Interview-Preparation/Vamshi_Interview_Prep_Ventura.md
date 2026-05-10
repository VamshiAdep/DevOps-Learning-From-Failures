# 🎯 Vamshi Adep – Ventura DevOps Intern Interview Prep
### Personalized based on YOUR resume | Referred by Sujeet Kumar (Sr. Manager – DevOps)

---

## 👋 YOUR INTRODUCTION (Say This in Interview)

> Practice this out loud until it feels natural. Keep it under 90 seconds.

---

**"Good [morning/afternoon], I'm Vamshi Adep from Thane, Maharashtra.**

**I completed my B.Sc. in Information Technology from Mulund College of Commerce in 2025.**

**I started my DevOps journey through a hands-on internship at Volody Smart CLM, where I worked on securing Docker images, optimizing Jenkins CI/CD pipelines, and managing AWS IAM roles. I also handled incident management and performed root cause analysis for production issues.**

**After that, I built a complete Microservices CI/CD Automation Project — I containerized apps using Docker, provisioned AWS infrastructure with Terraform, deployed on Kubernetes, and built an end-to-end Jenkins pipeline with Docker Hub integration.**

**Currently, I'm doing freelance DevOps support at Linksolutions, where I'm managing Azure Virtual Machines, networking, storage, and Linux server configuration.**

**I hold the Microsoft Azure AZ-900 certification and the IBM DevOps Fundamentals certification.**

**I'm passionate about automation, reliable infrastructure, and continuous improvement — and I'm excited about the opportunity to contribute to Ventura's DevOps team under Sujeet's guidance."**

---

> ✅ Key points you hit: education → internship → project → current work → certifications → why Ventura
> ❌ Don't read this. Practice until it flows naturally.

---

## 💪 YOUR STRENGTHS vs WHAT VENTURA NEEDS

| Ventura / Sujeet Expects | You Have It? | Your Evidence |
|---|---|---|
| CI/CD Pipelines | ✅ YES | Jenkins at Volody + Microservices project |
| Docker & Containers | ✅ YES | Secured images, multi-container deployments |
| Kubernetes | ✅ YES | Deployments, Services, health checks, EKS basic |
| AWS | ✅ YES | EC2, IAM, S3, VPC, CloudWatch, Terraform |
| Linux | ✅ YES | Ubuntu admin, SSH, troubleshooting, Azure VMs |
| Terraform / IaC | ✅ YES | S3 + DynamoDB backend, AWS provisioning |
| Monitoring | ✅ YES | Prometheus + Grafana |
| Azure | ✅ YES | AZ-900 certified + freelance experience |
| Security mindset | ✅ YES | Docker vulnerability resolution, IAM least privilege |
| Real production experience | ✅ YES | Incident mgmt, RCA, downtime reduction at Volody |

**You are well-prepared. Don't undersell yourself.**

---

## ❓ INTERVIEW QUESTIONS — FROM YOUR RESUME

These questions will DIRECTLY come from what's on your resume. Be ready.

---

### 🐳 DOCKER (You listed it heavily)

**Q1. You mentioned securing Docker images at Volody. What vulnerabilities did you find and how did you fix them?**
> Answer: Talk about image scanning (tools like Trivy or Snyk), removing unnecessary packages, using minimal base images (alpine), not running as root user, fixing outdated dependencies. Be specific about what you actually did.

**Q2. What is the difference between a Docker image and a Docker container?**
> Image = blueprint (read-only layers). Container = running instance of that image. Like a class vs object in programming.

**Q3. What is a multi-stage Docker build and why would you use it?**
> Reduces final image size by separating build environment from runtime. E.g., compile Java in one stage, copy only the JAR to a slim runtime image.

**Q4. How did you manage multi-container deployments?**
> Talk about docker-compose for local, Kubernetes for production. Mention how you handled service communication, environment variables, health checks.

---

### ⚙️ JENKINS & CI/CD (Your Core Strength)

**Q5. Walk me through the Jenkins pipeline you built in your Microservices project.**
> Say: Code pushed to GitHub → Jenkins triggers build → runs tests → builds Docker image → pushes to Docker Hub → deploys to Kubernetes cluster via SSH or kubectl. Mention you used Jenkins Shared Libraries to modularize stages.

**Q6. What are Jenkins Shared Libraries? Why did you use them?**
> Reusable Groovy code stored in a separate repo, imported into pipelines. Avoids code duplication across multiple Jenkinsfiles. Enforces standards across teams.

**Q7. What is the difference between Declarative and Scripted pipelines in Jenkins?**
> Declarative: structured, easier, uses `pipeline {}` block — recommended for most use cases.
> Scripted: uses Groovy directly, more flexible but harder to read.

**Q8. How did you improve deployment efficiency at Volody?**
> Be specific: reduced manual steps, automated testing stages, faster feedback loops, improved release cycles. Use numbers if you remember them (e.g., "reduced deployment time by X%").

---

### ☁️ AWS & TERRAFORM (Strong on your resume)

**Q9. What AWS services did you use and for what purpose?**
> EC2 – virtual servers for app hosting
> IAM – access control, least privilege roles
> S3 – storage, also Terraform remote state backend
> VPC – network isolation, subnets, security groups
> CloudWatch – monitoring and alerting
> DynamoDB – Terraform state locking

**Q10. Explain how you configured Terraform remote backend with S3 and DynamoDB.**
> S3 stores the `.tfstate` file remotely so the team shares state.
> DynamoDB provides state locking — prevents two people running `terraform apply` at the same time which could corrupt state.
```hcl
terraform {
  backend "s3" {
    bucket         = "my-tf-state"
    key            = "prod/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "tf-lock"
  }
}
```

**Q11. What is `terraform plan` vs `terraform apply`?**
> `plan` = dry run, shows what WILL change without making changes.
> `apply` = actually makes the changes. Always run plan first.

**Q12. What is IAM least privilege? How did you implement it at Volody?**
> Give only the permissions a user/role actually needs — nothing more.
> Created specific IAM roles for services (e.g., EC2 can only read from specific S3 bucket, not write). No admin access to service accounts.

---

### ☸️ KUBERNETES (You have real project experience)

**Q13. What Kubernetes resources did you use in your project?**
> Deployment – manages pod replicas, rolling updates
> Service – exposes pods internally (ClusterIP) or externally (NodePort/LoadBalancer)
> Health checks (liveness & readiness probes) – ensure pods only get traffic when ready

**Q14. What is the difference between a Deployment and a Pod in Kubernetes?**
> Pod = single running instance of a container.
> Deployment = manages multiple pods, handles restarts, scaling, rolling updates. You never create pods directly in production.

**Q15. What is the difference between liveness and readiness probes?**
> Liveness = is the container alive? If not, restart it.
> Readiness = is the container ready to receive traffic? If not, remove from Service endpoints temporarily.

**Q16. You mentioned EKS basic — what is EKS?**
> Amazon Elastic Kubernetes Service — managed Kubernetes on AWS. AWS handles the control plane (master nodes). You manage worker nodes. Used for production Kubernetes without managing the underlying infra yourself.

---

### 🔵 AZURE (AZ-900 Certified + Freelance)

**Q17. What did you do in your freelance role at Linksolutions on Azure?**
> Created and configured Windows & Linux VMs, set up static IPs, networking, managed Azure Managed Disks for storage, supported cost optimization with pay-as-you-go. This is real hands-on experience — describe it confidently.

**Q18. What is the difference between Azure and AWS? (Since you know both)**
> Both are cloud platforms. Azure is stronger in Windows workloads and enterprise (Active Directory, .NET). AWS has wider service range and larger market share globally. You're comfortable in both — important point for Ventura.

---

### 📊 MONITORING (Prometheus + Grafana)

**Q19. What is the difference between Prometheus and Grafana?**
> Prometheus = collects and stores metrics (time-series database). It scrapes endpoints.
> Grafana = visualizes those metrics as dashboards. They work together — Prometheus is the data source, Grafana is the UI.

**Q20. What kind of alerts would you set up for a production system?**
> High CPU usage (>80%), memory usage, disk almost full, pod crash loops, service response time too high, deployment failures. In Prometheus you write PromQL rules; AlertManager sends notifications to Slack/email.

---

### 🐧 LINUX (Foundation you must be solid on)

**Q21. How do you troubleshoot a Linux server that is running slowly?**
> Step 1: `top` or `htop` — check CPU and memory usage
> Step 2: `df -h` — check disk space
> Step 3: `iostat` — check disk I/O
> Step 4: `netstat` or `ss` — check network connections
> Step 5: Check application logs in `/var/log/`

**Q22. How do you check which process is using a specific port?**
> `sudo ss -tlnp | grep :8080` or `sudo lsof -i :8080`

**Q23. What is SSH and how does it work?**
> Secure Shell — encrypted protocol to remotely access servers. Uses key pairs (public/private). Public key on server, private key on your machine. `ssh -i key.pem user@server-ip`

---

### 🎯 BEHAVIOURAL / SITUATIONAL (Very Important for Referral Interview)

**Q24. Tell me about a time you resolved a production issue.**
> Use your Volody experience: Incident management → identified issue → performed RCA → applied fix → documented to prevent recurrence. Be specific about what broke and how you fixed it.

**Q25. Why do you want to work at Ventura specifically?**
> "Ventura operates in fintech — trading platforms and real-time financial infrastructure demand extremely reliable, low-latency systems. That's exactly the kind of high-stakes environment where DevOps practices matter most. I want to work on infrastructure where reliability directly impacts real users and their financial decisions. Sujeet's background in multi-environment EKS deployments and Helm charts is also something I specifically want to learn more about."

**Q26. What is one area where you want to improve?**
> Be honest: "I want to deepen my Kubernetes knowledge beyond basics — specifically around Helm chart deployments, RBAC policies, and multi-environment cluster management. I've seen Sujeet's work on EKS and that's exactly the direction I want to grow in."

**Q27. How do you handle a situation where you don't know the answer?**
> "I say I don't know right now, but I describe how I'd approach finding the answer — documentation, hands-on lab, asking a senior. I've always learned fastest by trying things in a safe environment first."

---

## 🏢 QUESTIONS TO ASK SUJEET / INTERVIEWER

Ask at least 2-3 of these — it shows you're serious:

1. "What does a typical day look like for a DevOps intern on your team?"
2. "What is the current tech stack — are you using EKS, Helm, ArgoCD in production?"
3. "What would be my primary project or responsibility during the internship?"
4. "Is there a possibility of a full-time role after the internship based on performance?"
5. "What monitoring stack does the team currently use in production?"

---

## 📌 KEY THINGS TO HIGHLIGHT FROM YOUR RESUME

When the interviewer asks about your experience, always link back to these:

| Story | Key Points to Mention |
|---|---|
| **Volody Internship** | Docker security fixes, Jenkins optimization, IAM least privilege, incident RCA |
| **Microservices Project** | Full pipeline: Terraform → EC2 → Jenkins → Docker → Kubernetes. End-to-end ownership. |
| **Linksolutions Freelance** | Real client work, Azure infra, networking, Linux server management |
| **AZ-900 Certification** | Shows commitment to learning, foundational cloud knowledge validated |

---

## ⚠️ THINGS TO BE CAREFUL ABOUT

- **Don't say "basic" or "just"** — e.g., don't say "I have just basic Kubernetes knowledge." Say "I have hands-on Kubernetes experience from my project and I'm actively deepening it."
- **Don't lie or exaggerate** — Sujeet is a Senior Manager, he will ask follow-up questions. Be honest about depth.
- **CGPI of 6.96** — If asked, say it honestly and immediately pivot: "My academics were average but my practical work and projects have been my real learning."
- **Freelance is part-time** — If asked, clarify it's part-time alongside your job search, not a full commitment.

---

## 📅 YOUR 7-DAY FINAL PREP PLAN

| Day | Focus |
|---|---|
| Day 1 | Revise Docker deeply — images, containers, Dockerfile, security |
| Day 2 | Jenkins pipelines — revise your actual Microservices project steps |
| Day 3 | Kubernetes — revise Deployments, Services, probes, EKS |
| Day 4 | Terraform + AWS — revise remote backend, IAM, EC2 |
| Day 5 | Linux commands + Prometheus/Grafana |
| Day 6 | Practice your introduction OUT LOUD 10 times. Practice all behavioural answers. |
| Day 7 | Research Ventura's tech stack on LinkedIn. Rest. Sleep well. |

---

## ✅ DAY-OF-INTERVIEW CHECKLIST

- [ ] Introduction memorized and practiced
- [ ] Can explain every line on your resume
- [ ] Microservices project explained end-to-end clearly
- [ ] 3 questions ready to ask interviewer
- [ ] Dressed professionally
- [ ] LinkedIn profile updated
- [ ] Calm, confident, honest mindset ✅

---

*You have real experience, real projects, real certifications, and a referral from a Senior Manager. You are prepared. Back it up with confidence. Go get it, Vamshi! 💪*
