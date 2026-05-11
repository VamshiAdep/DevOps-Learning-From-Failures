# 🎯 Vamshi Adep — DevOps Intern Interview Prep
## Ventura Securities | Thane, Mumbai

> **Referral:** Aditya & Sujeet | **Focus Areas (from Aditya anna):** Linux · AWS (VPC, EC2, S3) · GitHub Actions · Docker · Git
>
> Ventura Securities is a **fintech/stock-broking platform** (NSE, BSE, MCX registered) serving lakhs of traders across India. Their tech team is AWS + Python-first, building real-time trading infrastructure. **Reliability, security, and zero downtime are critical** — keep that context in mind during every answer.

---

## 📋 Your Prep Checklist (Priority Order)

- [ ] Linux commands & troubleshooting (most asked for freshers)
- [ ] AWS — VPC, EC2, S3, IAM (Aditya specifically mentioned this)
- [ ] Git — branching, merge, rebase, PR workflow
- [ ] GitHub Actions — CI/CD pipelines (you already have a guide for this)
- [ ] Docker — images, containers, Dockerfile, networking
- [ ] Able to explain your resume projects confidently
- [ ] Know Ventura's business (trading platform, SEBI-regulated, high availability)

---

## 🏢 About Ventura Securities — Know Your Employer

**What they do:** India's growing online trading & investment platform — stocks, F&O, IPOs, mutual funds, derivatives. Members of NSE, BSE, MCX, NCDEX, NSDL, CDSL.

**Why this matters for your interview:**
- Trading platforms need **99.99% uptime** — market hours are live, downtime = financial loss
- They handle **real money transactions** — security is paramount, IAM & access control matters
- They're a **fintech building on AWS** — cloud-native mindset
- They have a dedicated **fintech team** described as "remote-first, AWS + Python focused"
- Office: I-Think Techno Campus, Pokhran Road, **Thane** — same city as you, great to mention

**Smart thing to say in interview:**
> *"I noticed Ventura is a trading platform where uptime during market hours is critical. That's why I find the DevOps role here exciting — reliability engineering and automated pipelines directly impact business outcomes."*

---

## 1️⃣ LINUX — Most Asked for Freshers

### Essential Commands You MUST Know

```bash
# FILE & DIRECTORY
ls -la                    # List with permissions and hidden files
pwd                       # Print working directory
cd /path/to/dir           # Change directory
mkdir -p dir1/dir2        # Create nested directories
cp -r source/ dest/       # Copy recursively
mv old_name new_name      # Move or rename
rm -rf folder/            # Force delete (use with caution!)
find / -name "*.log"      # Find files by name
find /var -size +100M     # Find files larger than 100MB

# FILE VIEWING & EDITING
cat file.txt              # Print entire file
less file.txt             # Scroll through file
head -n 20 file.txt       # First 20 lines
tail -n 50 file.txt       # Last 50 lines
tail -f /var/log/app.log  # Live log monitoring (VERY important in DevOps)
grep "error" app.log      # Search inside file
grep -r "keyword" /path/  # Search recursively in directory

# PERMISSIONS
chmod 755 script.sh       # rwxr-xr-x
chmod +x deploy.sh        # Make executable
chown user:group file     # Change ownership
ls -la                    # Check permissions

# PROCESS MANAGEMENT
ps aux                    # List all running processes
ps aux | grep nginx       # Find specific process
kill -9 PID               # Force kill process
top                       # Live process monitor
htop                      # Better live monitor (if installed)
systemctl status nginx    # Check service status
systemctl start/stop/restart nginx
systemctl enable nginx    # Auto-start on boot

# DISK & MEMORY
df -h                     # Disk usage (human readable)
du -sh /var/log/          # Size of a directory
free -h                   # RAM usage
lsblk                     # List block devices

# NETWORKING
ifconfig / ip addr        # Show IP address
ping google.com           # Test connectivity
netstat -tuln             # Show open ports
ss -tuln                  # Modern version of netstat
curl http://localhost:8080  # Test HTTP endpoint
wget https://url/file     # Download file
ssh user@IP               # Connect to remote server
scp file.txt user@IP:/path  # Copy file to remote server

# TEXT PROCESSING
awk '{print $1}' file.txt    # Print first column
sed 's/old/new/g' file.txt   # Replace text
sort file.txt | uniq -c      # Count unique lines
wc -l file.txt               # Count lines

# SHELL SCRIPTING BASICS
#!/bin/bash
VAR="hello"
echo $VAR
if [ "$VAR" == "hello" ]; then
    echo "Match!"
fi
for i in 1 2 3; do
    echo "Number: $i"
done
```

### Top Linux Interview Questions for Ventura

**Q1: What is the difference between a process and a daemon?**
> A process is any running program. A daemon is a background service process that runs continuously without user interaction — like `nginx`, `sshd`, or `cron`. Daemons typically start at boot and are managed with `systemctl`.

**Q2: How do you check which process is using port 8080?**
```bash
sudo lsof -i :8080
# OR
sudo ss -tuln | grep 8080
# OR
sudo netstat -tuln | grep 8080
```

**Q3: A server's disk is full. How do you troubleshoot?**
```bash
df -h                     # Step 1: Find which partition is full
du -sh /* 2>/dev/null     # Step 2: Find largest directories at root
du -sh /var/log/*         # Step 3: Dig into suspected directory
find / -size +500M        # Step 4: Find large files
# Common culprits: /var/log, /tmp, Docker images, core dumps
```

**Q4: What is crontab? Write a cron job that runs a script every day at 2 AM.**
```bash
# Crontab is Linux's task scheduler
crontab -e        # Edit cron jobs

# Run script every day at 2 AM:
0 2 * * * /home/ubuntu/backup.sh

# Cron syntax: minute hour day month weekday
```

**Q5: Difference between `>` and `>>` in Linux?**
> `>` overwrites the file. `>>` appends to the file.
> ```bash
> echo "hello" > file.txt    # Creates/overwrites
> echo "world" >> file.txt   # Appends
> ```

**Q6: What is SSH and how does key-based authentication work?**
> SSH (Secure Shell) is a protocol to securely connect to remote servers. Key-based auth uses a key pair — private key stays on your machine, public key goes on the server. No password needed. More secure.
> ```bash
> ssh-keygen -t rsa -b 4096          # Generate key pair
> ssh-copy-id user@server_ip          # Copy public key to server
> ssh -i ~/.ssh/id_rsa user@server    # Connect using private key
> ```

**Q7: What does `chmod 755` mean?**
> `7` = owner has read(4)+write(2)+execute(1) = rwx
> `5` = group has read(4)+execute(1) = r-x
> `5` = others have read(4)+execute(1) = r-x
> Common for scripts: owner can modify, everyone can run.

---

## 2️⃣ AWS — VPC, EC2, S3, IAM (Aditya Specifically Mentioned This)

### VPC (Virtual Private Cloud) — Most Important for Fintech

```
VPC Architecture at Ventura would look like:

Internet
    │
[Internet Gateway]
    │
[Public Subnet]          ← Load Balancer, Bastion Host live here
  10.0.1.0/24
    │
[Private Subnet]         ← EC2 App servers, Databases live here
  10.0.2.0/24
    │
[NAT Gateway]            ← Private instances use this to reach internet
```

**Public Subnet vs Private Subnet:**

| Feature | Public Subnet | Private Subnet |
|---------|--------------|----------------|
| Internet access | Direct (via Internet Gateway) | Indirect (via NAT Gateway) |
| Used for | Load balancers, bastion hosts | App servers, databases |
| Route table has | 0.0.0.0/0 → Internet Gateway | 0.0.0.0/0 → NAT Gateway |
| Has public IP | Yes | No |

**Key VPC Components:**
- **Internet Gateway (IGW)** — allows public subnets to talk to internet
- **NAT Gateway** — allows private subnets to initiate outbound internet (e.g., download packages), but no inbound
- **Security Groups** — stateful firewall at instance level (allow rules only)
- **NACLs** — stateless firewall at subnet level (allow + deny rules)
- **Route Table** — defines where traffic goes
- **VPC Peering** — connect two VPCs privately

### VPC Interview Questions

**Q: Why put app servers in a private subnet?**
> Security. If your trading app server is in a public subnet, it has a public IP and is directly reachable from the internet — attack surface is huge. In a private subnet, only the Load Balancer (in public subnet) can reach it. The database is also in private subnet, inaccessible from outside. This is standard for financial applications.

**Q: A private EC2 instance can't download packages. What might be wrong?**
> Missing NAT Gateway, or the route table for the private subnet doesn't have a route `0.0.0.0/0 → NAT Gateway`. Also check Security Group outbound rules are open.

**Q: Difference between Security Groups and NACLs?**
| | Security Group | NACL |
|--|---------------|------|
| Level | Instance | Subnet |
| State | Stateful (return traffic auto-allowed) | Stateless (must define both inbound + outbound) |
| Rules | Allow only | Allow AND Deny |
| Default | Deny all inbound, allow all outbound | Allow all |

---

### EC2 (Elastic Compute Cloud)

```bash
# Common EC2 operations you should know
# Launch instance → choose AMI → instance type → VPC/subnet → Security Group → Key pair

# Connect to EC2:
ssh -i my-key.pem ubuntu@ec2-public-ip

# User data script (runs on first boot):
#!/bin/bash
apt-get update
apt-get install -y nginx
systemctl start nginx
```

**EC2 Concepts:**
- **AMI** (Amazon Machine Image) — snapshot/template to launch instances from
- **Instance Types** — t3.micro (free tier), t3.medium, c5.large, etc.
- **EBS** — Elastic Block Store, persistent storage attached to EC2
- **Elastic IP** — Static public IP that stays even if instance restarts
- **Auto Scaling Group** — automatically add/remove instances based on load
- **Load Balancer (ALB)** — distributes traffic across multiple EC2 instances

**EC2 Interview Questions:**

**Q: What is the difference between stopping and terminating an EC2 instance?**
> Stopping: instance shuts down but EBS volume is retained; you can restart it. Data is safe. You stop paying for compute but still pay for storage.
> Terminating: instance is permanently deleted along with its root EBS volume (unless "Delete on termination" is unchecked). Cannot be recovered.

**Q: What is an AMI and why would you create a custom one?**
> AMI is a pre-configured image used to launch EC2 instances. A custom AMI (Golden AMI) is useful when you want all new instances to launch with your software already installed — no need to run setup scripts every time. Faster launch, consistent configuration.

**Q: What is EC2 user data?**
> A script that runs automatically when an EC2 instance first boots. Used to install packages, pull code, start services, etc. Runs as root. Only runs once on first boot by default.

---

### S3 (Simple Storage Service)

```bash
# AWS CLI S3 commands (useful to know)
aws s3 ls                              # List all buckets
aws s3 ls s3://my-bucket/             # List objects in bucket
aws s3 cp file.txt s3://my-bucket/    # Upload file
aws s3 cp s3://my-bucket/file.txt .   # Download file
aws s3 sync ./dist s3://my-bucket/    # Sync folder to bucket
aws s3 rm s3://my-bucket/file.txt     # Delete file
```

**S3 Key Concepts:**
- **Bucket** — container for objects (like a folder in the cloud)
- **Object** — file stored in S3 (up to 5TB)
- **Bucket Policy** — JSON policy controlling who can access the bucket
- **ACL** — Access Control List (older method, avoid for new setups)
- **Versioning** — keep multiple versions of the same object
- **Lifecycle Policy** — auto-move old objects to Glacier, or delete them
- **Static website hosting** — serve HTML/CSS/JS directly from S3
- **Pre-signed URL** — temporary URL to access private objects
- **Storage Classes** — Standard, Infrequent Access, Glacier (cold storage, cheap)

**S3 Interview Questions:**

**Q: How would you use S3 in a CI/CD pipeline?**
> Multiple uses: store build artifacts/binaries, host static frontend websites, store Terraform remote state, store Docker build cache, keep deployment logs and scripts.

**Q: What is S3 versioning and why enable it?**
> Versioning keeps all previous versions of an object. If someone accidentally deletes or overwrites a critical file, you can restore the previous version. Essential for production environments handling config files or deployment artifacts.

**Q: How do you make an S3 object publicly accessible?**
> Three ways: (1) Disable "Block Public Access" on bucket + add bucket policy with `s3:GetObject` for principal `*`, (2) Enable static website hosting, (3) Generate a pre-signed URL for temporary access. For a fintech company, public access is almost never the right answer — use pre-signed URLs instead.

---

### IAM (Identity and Access Management)

**Core Concepts:**
- **User** — individual person/account with permanent credentials
- **Group** — collection of users with shared policies
- **Role** — temporary credentials assigned to services (EC2, Lambda) or users
- **Policy** — JSON document defining what actions are allowed/denied
- **Principle of Least Privilege** — give only the minimum permissions needed

```json
// Example IAM Policy — S3 read-only for specific bucket
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ]
    }
  ]
}
```

**IAM Interview Questions:**

**Q: What is the difference between an IAM User and an IAM Role?**
> User has permanent long-term credentials (access key + secret). Used for humans or applications that need persistent access.
> Role has temporary credentials and no long-term password. Assumed by AWS services (like EC2 needing S3 access) or users via STS. Best practice: use roles for everything instead of long-term access keys.

**Q: How should an EC2 instance access an S3 bucket securely?**
> Attach an IAM Role to the EC2 instance with an S3 policy. Never hardcode access keys inside the instance. The instance automatically gets temporary credentials via the instance metadata service (IMDS). This is the best practice.

**Q: What is MFA in AWS and why is it important?**
> Multi-Factor Authentication adds a second layer of security to the root account and IAM users. Even if a password is compromised, an attacker can't login without the physical MFA device. For financial companies like Ventura, enabling MFA on all privileged accounts is mandatory.

---

## 3️⃣ GIT — Version Control

### Commands You Must Know

```bash
# SETUP
git config --global user.name "Vamshi Adep"
git config --global user.email "vamshiii2795@gmail.com"

# BASICS
git init                       # Initialize repo
git clone https://repo-url     # Clone repository
git status                     # What's changed
git add .                      # Stage all changes
git add file.txt               # Stage specific file
git commit -m "feat: add login feature"   # Commit
git push origin main           # Push to remote
git pull origin main           # Pull latest changes

# BRANCHING
git branch                     # List branches
git branch feature/login       # Create branch
git checkout feature/login     # Switch branch
git checkout -b hotfix/bug-fix # Create + switch in one command
git merge feature/login        # Merge into current branch
git branch -d feature/login    # Delete branch

# REMOTE
git remote -v                  # Show remote URLs
git fetch origin               # Fetch without merging
git pull origin develop        # Fetch + merge

# HISTORY
git log --oneline              # Compact commit history
git log --oneline --graph      # Visual branch graph
git diff                       # Show unstaged changes
git diff HEAD~1                # Compare with last commit

# UNDO
git restore file.txt           # Discard unstaged changes
git reset HEAD file.txt        # Unstage a file
git reset --soft HEAD~1        # Undo last commit, keep changes staged
git reset --hard HEAD~1        # Undo last commit, DISCARD changes
git revert abc1234             # Create new commit that undoes a commit

# STASH (save work temporarily)
git stash                      # Save current changes
git stash pop                  # Restore saved changes
git stash list                 # List all stashes

# TAGS
git tag v1.0.0                 # Create tag
git push origin v1.0.0         # Push tag
```

### Git Branching Strategy (Explain in Interview)

```
main          ──────●────────────────────●──── (production)
                   /                    /
develop       ────●────●────●──────────●────── (integration)
                       \   \
feature/login          ●────●  (your feature branch)
feature/dashboard            ●────● (another dev's branch)
```

**Gitflow in simple terms:**
- `main` — production code only, always stable
- `develop` — integration branch, all features merge here first
- `feature/*` — individual feature work
- `hotfix/*` — emergency fixes directly from main

### Git Interview Questions

**Q: What is git rebase vs git merge? Which is better?**
> `merge` creates a new "merge commit" that joins two branches — preserves full history, shows when branches merged.
> `rebase` replays your commits on top of the target branch — creates a linear, clean history.
> In teams: use `merge` for integrating feature branches (preserves context). Use `rebase` for cleaning up your local commits before a PR.

**Q: How do you resolve a merge conflict?**
> 1. Run `git pull` or `git merge` → conflict markers appear in files
> 2. Open conflicted file — you'll see `<<<<<<`, `=======`, `>>>>>>>`
> 3. Manually edit the file to keep what you want
> 4. `git add` the resolved file
> 5. `git commit` to complete the merge
> Understanding this is critical in team environments.

**Q: What is a Pull Request (PR)?**
> A PR is a request to merge your feature branch into the main/develop branch. It's a collaboration mechanism — teammates review your code, leave comments, suggest changes. Only after approval does the code get merged. Standard workflow at every company.

**Q: What is `.gitignore`?**
> A file that tells Git which files/folders to NOT track. Examples: `node_modules/`, `.env`, `*.log`, build outputs. Important for security — never commit secrets or credentials.

---

## 4️⃣ DOCKER — Containers & Images

### Core Concepts

```
Without Docker:              With Docker:
"It works on my machine" ←  Same container runs
     ↓                       everywhere — dev, staging,
App breaks in production     production — identical
```

**Docker Architecture:**
- **Image** — read-only blueprint (like a class in OOP)
- **Container** — running instance of an image (like an object)
- **Dockerfile** — instructions to build an image
- **Docker Hub** — public registry to store/pull images
- **Docker Compose** — run multi-container apps with one file

### Essential Commands

```bash
# IMAGES
docker images                          # List local images
docker pull nginx:latest               # Pull image from Docker Hub
docker build -t myapp:v1 .             # Build from Dockerfile in current dir
docker rmi myapp:v1                    # Remove image
docker image prune                     # Remove unused images

# CONTAINERS
docker run nginx                       # Run container (foreground)
docker run -d nginx                    # Run in background (detached)
docker run -d -p 8080:80 nginx         # Map port 8080(host) → 80(container)
docker run -d --name myapp nginx       # Give container a name
docker run -v /host/path:/container/path nginx  # Mount volume
docker ps                              # List running containers
docker ps -a                           # List ALL containers (including stopped)
docker stop myapp                      # Stop container gracefully
docker rm myapp                        # Remove stopped container
docker logs myapp                      # View container logs
docker logs -f myapp                   # Follow live logs
docker exec -it myapp bash             # Shell inside running container
docker inspect myapp                   # Detailed container info

# CLEANUP
docker system prune                    # Remove all unused resources
docker container prune                 # Remove stopped containers
```

### Writing a Dockerfile

```dockerfile
# Example: Dockerize a Node.js app

# Base image
FROM node:18-alpine

# Set working directory inside container
WORKDIR /app

# Copy package files first (layer caching optimization)
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy rest of the app
COPY . .

# Expose port
EXPOSE 3000

# Command to run when container starts
CMD ["node", "server.js"]
```

```dockerfile
# Example: Dockerize a Python app
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "app.py"]
```

### Docker Compose Example

```yaml
# docker-compose.yml — run app + database together
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=postgres
      - DB_PORT=5432
    depends_on:
      - postgres

  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

```bash
docker-compose up -d          # Start all services in background
docker-compose down           # Stop and remove containers
docker-compose logs -f app    # Follow logs for app service
```

### Docker Interview Questions

**Q: What is the difference between a Docker image and a container?**
> An image is a static, read-only blueprint — like a recipe. A container is a running instance of that image — like the dish made from the recipe. You can run multiple containers from the same image. Images are stored; containers are ephemeral (temporary).

**Q: What is layer caching in Docker?**
> Docker builds images in layers. Each instruction in Dockerfile creates a layer. Layers are cached — if nothing changed, Docker reuses the cached layer. That's why in Dockerfiles we copy `package.json` first and install dependencies before copying the app code — if only app code changes, the dependency layer is reused from cache. Speeds up builds significantly.

**Q: How do you reduce Docker image size?**
> - Use Alpine base images (5MB vs 900MB for Ubuntu)
> - Use multi-stage builds (build in one stage, copy only final artifact to second stage)
> - Use `.dockerignore` to exclude `node_modules`, `.git`, test files
> - Combine RUN commands with `&&` to reduce layers
> - Use `--no-cache` when installing packages

**Q: What is the difference between `CMD` and `ENTRYPOINT`?**
> `CMD` provides default command that can be overridden when running `docker run`. `ENTRYPOINT` sets the main command that always runs. Common pattern: use `ENTRYPOINT` for the executable and `CMD` for default arguments.

**Q: You've built a Docker image and it has a vulnerability. How did you handle that at Volody?**
> *(From your resume)* "At Volody, I secured and rebuilt Docker images by resolving vulnerabilities. I used tools to scan images, identified which base images or packages had CVEs, updated to patched versions, and rebuilt the images. This improved container reliability and reduced security risks."

---

## 5️⃣ GITHUB ACTIONS — CI/CD (Already Covered in Depth)

### Quick Reference for Ventura Interview

```yaml
# Simple CI pipeline relevant to a fintech startup
name: Trading App CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test-and-build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest tests/ -v

      - name: Build Docker image
        run: docker build -t ventura-app:${{ github.sha }} .

      - name: Push to registry
        if: github.ref == 'refs/heads/main'
        run: |
          docker tag ventura-app:${{ github.sha }} myregistry/ventura-app:latest
          docker push myregistry/ventura-app:latest
```

**Key GitHub Actions points to mention:**
- Workflow file lives in `.github/workflows/`
- Triggered by events: `push`, `pull_request`, `schedule`
- `needs:` for job dependencies
- Secrets stored in repo settings, referenced as `${{ secrets.NAME }}`
- Cache dependencies with `actions/cache@v4`
- At Ventura: GitHub Actions would run tests on every PR, build Docker images, deploy to AWS

---

## 6️⃣ YOUR RESUME — How to Answer Project Questions

### Your Strongest Project: Microservices CI/CD Automation

**Prepare this story (STAR format):**

**Situation:** Needed to automate deployment of a microservices application with multiple containers.

**Task:** Design and implement a complete CI/CD pipeline from code commit to deployment.

**Action:**
1. Containerized each microservice using Docker
2. Provisioned AWS infrastructure with Terraform (remote state on S3 + DynamoDB locking)
3. Built Jenkins pipeline to: pull code → build Docker image → push to Docker Hub → deploy
4. Deployed on Kubernetes with health checks and service discovery
5. Used Jenkins Shared Libraries to avoid code repetition across pipeline stages

**Result:** Automated end-to-end — code push triggers build, test, and deploy automatically. Consistent deployments, no manual steps.

---

### Expected Questions About Your Resume

**Q: You mentioned Terraform remote backend with S3 and DynamoDB. Explain why.**
> Terraform stores state in a `.tfstate` file. If this is on a local machine, team members can't collaborate and state can be lost. Remote backend on S3 stores state centrally, accessible to the whole team. DynamoDB provides state locking — prevents two people from running `terraform apply` simultaneously, which could corrupt state.

**Q: What are Jenkins Shared Libraries?**
> Instead of copy-pasting the same pipeline code across 10 different Jenkinsfiles, Shared Libraries let you define common stages/functions once in a separate Git repo and import them. Like writing a library in programming. At my project, I used it to standardize the build, test, and deploy stages across all microservices.

**Q: What did you do at Volody regarding Docker vulnerabilities?**
> I used image scanning to identify containers with outdated base images or packages with known CVEs (Common Vulnerabilities and Exposures). I updated the Dockerfiles to use patched base images, reinstalled dependencies with secure versions, and rebuilt and redeployed the images. This reduced the attack surface of our running containers.

**Q: You have Azure AZ-900 and experience with both Azure and AWS. How does that help?**
> It gives me a broad cloud perspective. I understand core cloud concepts (compute, storage, networking, IAM) across both platforms. At Ventura, I see you primarily use AWS, and my AWS hands-on with EC2, IAM, VPC, S3, and Terraform means I can contribute from day one. The multi-cloud exposure helps me understand the "why" behind cloud architecture decisions, not just the "how."

---

## 7️⃣ SCENARIO / SITUATIONAL QUESTIONS

These are very likely for a fintech interview:

**Scenario: "Our trading app goes down during market hours. What do you do?"**
> 1. **Immediately check monitoring** — CloudWatch alarms, Grafana dashboards to identify which service failed
> 2. **Check application logs** — `docker logs`, CloudWatch Logs, or ELK stack
> 3. **Check system resources** — CPU, memory, disk on EC2: `top`, `df -h`, `free -h`
> 4. **Check recent deployments** — Did anything deploy in the last 30 minutes? Roll it back.
> 5. **Escalate immediately** — Trading downtime = revenue loss. Notify team lead.
> 6. **Restore service** — Restart crashed container/service, or rollback deployment
> 7. **Post-incident** — Root Cause Analysis (RCA), document what happened and fix permanently

**Scenario: "A developer says 'it works on my machine but not on the server'. How do you help?"**
> This is a classic Docker use case. First check:
> - Are environment variables different? (`.env` files, missing configs)
> - Are there different OS/runtime versions?
> Suggest containerizing the app with Docker so the dev environment and server environment are identical. Also check logs on the server, compare dependency versions.

**Scenario: "How would you set up a deployment pipeline for a Python trading API?"**
> 1. Code pushed to GitHub → triggers GitHub Actions
> 2. Run `pytest` tests — fail fast if tests break
> 3. Build Docker image tagged with commit SHA
> 4. Push to ECR (AWS Container Registry)
> 5. SSH to EC2 and pull new image, restart container (or update K8s deployment)
> 6. Verify health check endpoint returns 200

---

## 8️⃣ HR & BEHAVIORAL QUESTIONS

**Q: Why Ventura Securities specifically?**
> "Ventura is a fintech company where DevOps directly impacts the reliability of a platform handling real financial transactions for thousands of traders. I'm from Thane, same as Ventura's office, and I want to be part of a tech team solving high-stakes infrastructure problems. The fact that you're heavily investing in your tech team, building on AWS with CI/CD automation, aligns exactly with what I've been building experience in."

**Q: Tell me about yourself.**
> "I'm Vamshi Adep, a B.Sc IT graduate from Mulund College of Commerce. I have hands-on DevOps experience from my internship at Volody Smart CLM, where I worked on Docker security, Jenkins CI/CD pipelines, and AWS IAM. I also built a microservices CI/CD project independently using Terraform, Jenkins, Docker, and Kubernetes. I recently completed Azure AZ-900 certification and have strong hands-on skills in Linux, AWS, and containerization. I'm looking to grow as a DevOps engineer in a production environment where my work directly impacts system reliability — and Ventura is exactly that kind of company."

**Q: What's your biggest strength relevant to this role?**
> "Hands-on problem solving. In my Volody internship, I didn't just follow instructions — I took ownership of Docker vulnerability fixes, did root cause analysis on production issues, and actively improved pipeline efficiency. I learn by doing, which is exactly the DevOps mindset."

**Q: Do you have any questions for us?**
> Good questions to ask (shows genuine interest):
> - "What does the current CI/CD workflow look like, and where is the team focused on improving it?"
> - "What AWS services does the team primarily use?"
> - "What would success look like for me in the first 3 months?"
> - "Does the team use Kubernetes in production, or just Docker?"

---

## 9️⃣ QUICK REVISION TABLE — Day Before Interview

| Topic | What to Revise |
|-------|---------------|
| **Linux** | `tail -f`, `ps aux`, `chmod`, `ssh`, `find`, `grep`, crontab |
| **VPC** | Public/private subnet difference, IGW vs NAT, Security Groups vs NACL |
| **EC2** | Stop vs terminate, AMI, user data, Elastic IP |
| **S3** | Bucket policies, versioning, storage classes, pre-signed URL |
| **IAM** | User vs Role, least privilege, attaching role to EC2 |
| **Git** | Branch, merge conflict, rebase vs merge, .gitignore, PR |
| **Docker** | Image vs container, Dockerfile instructions, `docker run -p`, exec into container |
| **GitHub Actions** | Trigger, job, step, uses vs run, secrets, needs |
| **Your Projects** | STAR format for CI/CD project, Docker vulnerability fix at Volody |
| **Ventura** | Trading platform, AWS-first fintech, Thane office, reliability matters |

---

## 🔥 Power Tips for Interview Day

1. **Mention Ventura's business context** in answers — "Since Ventura is a trading platform, uptime is critical, which is why..."
2. **Tie your resume to their stack** — You used Jenkins, they likely use Jenkins or GitHub Actions; you used AWS, they use AWS. Connect the dots.
3. **Be honest about what you don't know** — "I haven't worked with X in production, but I have studied it and here's my understanding..." is far better than bluffing.
4. **Show willingness to learn** — DevOps interns are hired for potential + attitude, not for being an expert.
5. **Bring energy** — Say "I set up Kubernetes deployments with health checks in my project" with confidence. You DID that.
6. **Thank Aditya anna** — If the interview goes well, send a message updating him. He invested time to guide you.

---

> **You have real experience, real projects, and a real referral. Walk in confident, Vamshi. You're prepared. 🚀**

---

*Prep Guide prepared based on Aditya anna's guidance: Linux · AWS (VPC, EC2, S3) · Git · GitHub Actions · Docker*
*Company: Ventura Securities, Thane | Role: DevOps Intern | Referral: Aditya & Sujeet*
