# Linux Interview Q&A — Complete Guide
## DevOps & SRE · Q1 to Q41 · With Full Answers

---

## 📌 How to Use This File

- Read the concept first
- Read the complete answer out loud — no notes
- Practice until you can say it in 60 seconds naturally
- Rule: **Every answer must include a real command + real scenario**

---

## 📋 Topics Covered

| Area | Questions | Topic |
|------|-----------|-------|
| 🔴 Area 1 | Q1 – Q4 | File System & Permissions |
| 🔴 Area 2 | Q5 – Q9 | Process & System Troubleshooting |
| 🔴 Area 3 | Q10 – Q14 | Log Analysis |
| 🟡 Area 4 | Q15 – Q17 | Systemd & Services |
| 🟡 Area 5 | Q18 – Q20 | Cron, Scripting, Automation |
| 🟡 Area 6 | Q21 – Q24 | SRE Specific |
| 🔴 Area 7 | Q25 – Q29 | System Internals |
| 🔴 Area 8 | Q30 – Q32 | Process & Performance |
| 🔴 Area 9 | Q33 – Q35 | Networking Basics |
| 🔴 Area 10 | Q36 – Q37 | Permissions & Users |
| 🔴 Area 11 | Q38 – Q39 | File Handling |
| 🔴 Area 12 | Q40 – Q41 | SRE Thinking |

---

## 🔴 Area 1 — File System & Permissions

---

## ❓ Q1. Explain Linux file system structure — where do logs, configs, and binaries live?

### 🧠 Concept

Linux file system starts from root `/` — everything lives under it.
Unlike Windows (C:\ D:\) — Linux has one single tree starting from `/`.

---

### 📂 Directory Table

| Directory | What lives here | Real DevOps use |
|-----------|----------------|-----------------|
| `/etc` | All config files | nginx.conf, sshd_config, hosts |
| `/var/log` | All log files | app.log, syslog, auth.log |
| `/bin` `/usr/bin` | System commands | ls, curl, git, docker |
| `/home` | User directories | SSH into EC2 → land here |
| `/tmp` | Temporary files | Cleared on reboot |
| `/proc` | Live system info | CPU, memory, running processes |
| `/root` | Root user home | Admin user files |
| `/opt` | Optional software | Jenkins, custom apps |

---

### 💡 Commands

```bash
ls /                              # see root structure
cat /etc/os-release               # check Linux version
ls /var/log                       # see all log files
cat /var/log/syslog | tail -20    # last 20 lines of system log
```

---

### 🎯 Simple Understanding

- `/etc` → configs
- `/var/log` → logs
- `/home` → user files
- `/bin` → commands
- `/tmp` → temp files
- `/proc` → system info

---

### 🔥 DevOps Insight

> If something breaks:
> - Check logs → `/var/log`
> - Check configs → `/etc`

---

### ✅ Complete Answer to Say

> "Linux file system starts from root slash. Everything is under it.
> /etc is where all config files live — nginx config, SSH config, hosts file.
> /var/log is where all logs are stored — first place I check when something breaks.
> /bin and /usr/bin contain all system commands like ls, curl, git.
> /home contains user directories — when I SSH into an EC2 instance I land here.
> /tmp stores temporary files cleared on reboot.
> /proc contains real-time system info like CPU and memory usage.
> /opt is where we install optional software like Jenkins.
>
> In production — if something breaks, I check /var/log for logs and /etc for configs."

---

## ❓ Q2. What is the difference between chmod 755, 644, 600, 400?

### 🧠 Concept

Every file has 3 permission groups: **Owner | Group | Others**
Each group has 3 permissions: **Read(4) Write(2) Execute(1)**

---

### 🔢 Permission Table

| Number | Owner | Group | Others | Use case |
|--------|-------|-------|--------|----------|
| `777` | rwx | rwx | rwx | ⚠️ Never in production |
| `755` | rwx | r-x | r-x | Shell scripts, web files |
| `644` | rw- | r-- | r-- | Normal config files |
| `600` | rw- | --- | --- | SSH private keys |
| `400` | r-- | --- | --- | AWS .pem keys |

---

### 💡 How to Calculate

```
755 = 7(4+2+1) 5(4+0+1) 5(4+0+1)
    = owner all, others read+execute
```

---

### 💡 Commands

```bash
chmod 755 script.sh          # scripts and web files
chmod 644 config.conf        # normal config files
chmod 600 ~/.ssh/id_rsa      # SSH private key — must be 600
chmod 400 key.pem            # AWS pem key — read only
chmod 777 file.txt           # ⚠️ never in production
```

---

### 🔥 DevOps Insight

> SSH key permissions must be `chmod 600`.
> If it is `777`, AWS will refuse your connection with:
> **"Permissions are too open"** error.

---

### ✅ Complete Answer to Say

> "chmod controls who can read, write, or execute a file.
> Three groups — owner, group, others. Read is 4, write is 2, execute is 1.
>
> 755 — owner can do everything, others can read and execute. I use this for shell scripts and web server files.
>
> 644 — owner can read and write, others can only read. I use this for normal config files.
>
> 600 — only the owner can read and write, nobody else. I use this for SSH private keys. If SSH key is 777, AWS refuses connection with permissions too open error.
>
> 400 — read only for owner. I use this for AWS .pem key files downloaded from console."

---

## ❓ Q3. You SSH into EC2 and get "Permission denied (publickey)" — what do you check?

### 🧠 Concept

This is one of the most common real-world issues. 5 things can cause this.

---

### 🔍 Checklist in Order

```bash
# 1. Check key permission — must be 600
chmod 600 key.pem
ls -la key.pem

# 2. Check correct username
ssh -i key.pem ubuntu@ip        # Ubuntu AMI
ssh -i key.pem ec2-user@ip      # Amazon Linux AMI
ssh -i key.pem centos@ip        # CentOS AMI

# 3. Check correct IP — public IP not private
# Go to AWS console → EC2 → check Public IPv4

# 4. Check security group on AWS console
# Inbound rule: port 22 allowed for your IP

# 5. Check correct key pair
# Must match key selected when instance was launched
```

---

### 🔥 DevOps Insight

> Most common mistake — using wrong username.
> Ubuntu AMI = `ubuntu`, Amazon Linux = `ec2-user`, CentOS = `centos`

---

### ✅ Complete Answer to Say

> "When I get Permission denied publickey on EC2, I check 5 things in order.
>
> First — key file permission. Must be chmod 600. If it is 777 or 644, SSH rejects it.
>
> Second — correct username. Ubuntu uses ubuntu, Amazon Linux uses ec2-user, CentOS uses centos. Wrong username gives this same error.
>
> Third — correct IP. I check if I am using the public IP, not private IP.
>
> Fourth — security group on AWS console. Inbound rule must allow port 22 from my IP.
>
> Fifth — correct key pair. The .pem file must be the same key selected when instance was launched."

---

## ❓ Q4. What is the difference between hard link and soft link?

### 🧠 Concept

Both create a reference to a file — but they work differently underneath.

---

### 💡 Commands

```bash
ln file.txt hardlink.txt        # Hard link — same inode
ln -s file.txt softlink.txt     # Soft link — shortcut
```

---

### 🔄 Comparison Table

| | Hard Link | Soft Link |
|--|-----------|-----------|
| Points to | Same inode (actual data) | File path (shortcut) |
| Original deleted | ✅ Still works | ❌ Breaks |
| Cross filesystem | ❌ No | ✅ Yes |
| Used for | Backups | Config shortcuts |

---

### 💡 Real DevOps Example

```bash
# Soft link — version management
ln -s /opt/nginx-1.24/bin/nginx /usr/local/bin/nginx
# When upgrading — just update the soft link
# No need to change anything else
```

---

### 🔥 DevOps Insight

> Soft links are used in DevOps to manage versions.
> Upgrade? Just point the soft link to the new version.
> Everything else stays the same.

---

### ✅ Complete Answer to Say

> "Hard link points to the same inode — actual data on disk. If I delete the original file, the hard link still works because the data exists. Hard links cannot cross different file systems.
>
> Soft link is like a shortcut — points to the file path. If original is deleted, soft link breaks. But soft links work across different file systems.
>
> In DevOps I use soft links to manage versions — link /usr/local/bin/nginx to the current nginx version. When I upgrade, I just update the soft link."

---

## 🔴 Area 2 — Process & System Troubleshooting

---

## ❓ Q5. Your app is consuming 100% CPU — how do you find and fix it?

### 🧠 Step by Step Approach

```bash
# Step 1 — find which process
top                              # press P to sort by CPU
htop                             # better view

# Step 2 — confirm the process
ps aux --sort=-%cpu | head -10   # top 10 CPU consumers

# Step 3 — check logs for why
journalctl -u appname -f
tail -f /var/log/appname.log

# Step 4 — fix it
systemctl restart appname        # graceful restart first
kill -9 PID                      # force kill if stuck
```

---

### 🔥 DevOps Insight

> Always try `systemctl restart` before `kill -9`.
> Force kill leaves no chance for cleanup — may corrupt data.

---

### ✅ Complete Answer to Say

> "When an app is consuming 100% CPU, I run top and press P to sort by CPU — this shows me which PID is consuming the most.
>
> Once I find the PID, I check logs with journalctl to understand why — infinite loop? Too many requests? Memory leak?
>
> Then I try systemctl restart for a graceful restart. If the process is stuck, I use kill -9 PID to force kill it.
>
> After fixing, I check if it comes back — if it does, the issue is in code or config, not a one-time spike. I document it for RCA."

---

## ❓ Q6. Server is running slow — what is your step by step approach?

### 🧠 The 5-Layer Check

```bash
# Layer 1 — Disk
df -h                            # is any partition full?
du -sh /* | sort -rh | head -5   # what is using most space?

# Layer 2 — Memory
free -h                          # is RAM full? is swap being used?

# Layer 3 — CPU
top                              # which process using most CPU?
uptime                           # load average vs nproc count

# Layer 4 — Network
ss -tulnp                        # too many connections?

# Layer 5 — Logs
journalctl -u appname --since "1 hour ago"
grep -i "error\|warn" /var/log/app.log | tail -50
```

---

### 🔄 Check Order Table

| Step | Command | Checks |
|------|---------|--------|
| 1st | `df -h` | Disk full? |
| 2nd | `free -h` | RAM exhausted? |
| 3rd | `top` | CPU spike? |
| 4th | `ss -tulnp` | Too many connections? |
| 5th | `journalctl` | Errors in logs? |

---

### 🔥 DevOps Insight

> Disk full is the most common production issue.
> Always check `df -h` first — it is the quickest to confirm.

---

### ✅ Complete Answer to Say

> "When a server is slow, I check 5 layers in order.
>
> First disk — df -h. A full disk causes everything to slow down or crash.
>
> Second memory — free -h. If swap is being heavily used, RAM is exhausted.
>
> Third CPU — top to see load average and which process is consuming most CPU.
>
> Fourth network — ss -tulnp. Too many connections can slow the server.
>
> Fifth logs — journalctl to look for errors in the last hour.
>
> I check in this order because disk full is the most common production issue."

---

## ❓ Q7. What is the difference between kill, kill -9, and pkill?

### 🧠 Commands

```bash
kill PID        # sends SIGTERM (15) — graceful shutdown
kill -9 PID     # sends SIGKILL (9)  — force kill immediately
pkill nginx     # sends SIGTERM by process name not PID
killall nginx   # kills ALL processes with that name
```

---

### 🔄 Comparison Table

| Command | Signal | Process can cleanup | Use when |
|---------|--------|-------------------|----------|
| `kill PID` | SIGTERM (15) | ✅ Yes | Normal stop |
| `kill -9 PID` | SIGKILL (9) | ❌ No | Process frozen |
| `pkill name` | SIGTERM (15) | ✅ Yes | Don't know PID |

---

### 🔥 DevOps Insight

> In production always try in this order:
> 1. `systemctl restart appname`
> 2. `kill PID`
> 3. `kill -9 PID` ← last resort only

---

### ✅ Complete Answer to Say

> "kill PID sends SIGTERM — a polite request to stop. The process finishes current work, closes connections, saves state, then exits cleanly. Always my first choice.
>
> kill -9 PID sends SIGKILL — the kernel immediately destroys the process. No chance to clean up. I use this only when the process is frozen.
>
> pkill nginx works by process name instead of PID. Sends SIGTERM to all matching processes. Useful when I don't know the PID."

---

## ❓ Q8. How do you check which process is using port 8080?

### 🧠 Commands

```bash
# Modern way — preferred
ss -tulnp | grep 8080

# Alternative
lsof -i :8080

# Full debug flow
ss -tulnp | grep 8080
# Find PID from output
kill -9 PID
```

---

### 💡 Real Scenario

```bash
# Error: "bind: address already in use"

ss -tulnp | grep 8080
# Output:
# tcp LISTEN *:8080 users:(("java",pid=1234,fd=8))

kill -9 1234
# Now start your app — port is free
```

---

### 🔥 DevOps Insight

> First command when you see:
> **"address already in use"** error during deployment.

---

### ✅ Complete Answer to Say

> "To check which process is using port 8080, I use ss -tulnp | grep 8080.
>
> ss is the modern replacement for netstat. Flags mean: t for TCP, u for UDP, l for listening, n for numeric ports, p for process name.
>
> This shows me the process name and PID. If I need to free the port, I kill the PID.
>
> Alternatively I use lsof -i :8080.
>
> In real work, this is the first command I run when I get address already in use error during deployment."

---

## ❓ Q9. What is a zombie process? How do you handle it?

### 🧠 Concept

```bash
# Zombie = process finished but parent hasn't read its exit status
# Shows as Z in ps aux

ps aux | grep Z          # find zombie processes
```

---

### 🔄 Process States in ps aux

| State | Meaning |
|-------|---------|
| `S` | Sleeping — waiting for input |
| `R` | Running — currently executing |
| `Z` | Zombie — finished but not cleaned |
| `D` | Uninterruptible sleep — waiting for I/O |
| `T` | Stopped |

---

### 💡 Fix Zombie Process

```bash
# Cannot kill zombie directly
# Kill the PARENT process instead

ps -o ppid= PID          # find parent PID
kill -9 PARENT_PID       # kill parent → kernel cleans zombie
```

---

### 🔥 DevOps Insight

> A few zombies = not a problem.
> Thousands of zombies = process table exhausted → new processes cannot start.

---

### ✅ Complete Answer to Say

> "A zombie process is a process that finished execution but its parent hasn't read its exit status yet. It stays in the process table as a ghost — shows Z in ps aux.
>
> Zombies don't consume CPU or memory — only a process table slot. A few are fine. Thousands can exhaust slots and prevent new processes from starting.
>
> I cannot kill a zombie directly. I kill the parent process — then the kernel automatically cleans up all zombie children. I find the parent with ps -o ppid= PID."

---

## 🔴 Area 3 — Log Analysis

---

## ❓ Q10. App crashed at 3 AM — how do you find what happened?

### 🧠 Step by Step Investigation

```bash
# Step 1 — check service logs for that time window
journalctl -u appname --since "03:00" --until "03:30"

# Step 2 — check application log file
grep -i "error\|exception\|fatal" /var/log/app.log | grep "03:"

# Step 3 — check system logs
grep "03:" /var/log/syslog | tail -100

# Step 4 — check kernel messages — OOM killer
dmesg | grep -i "error\|oom\|kill"

# Step 5 — check if disk was full
df -h
du -sh /var/log/

# Step 6 — check recent deployments
git log --since="2024-01-01 02:00" --until="2024-01-01 04:00"
```

---

### 🔥 DevOps Insight

> Most 3 AM crashes are caused by:
> 1. A late-night deployment that went wrong
> 2. OOM — Out of Memory — kernel killed the app
> 3. Disk full — app couldn't write logs or data

---

### ✅ Complete Answer to Say

> "When an app crashes at 3 AM, I do a time-based investigation.
>
> First I check service logs for that exact window — journalctl -u appname --since 03:00 --until 03:30.
>
> Then I grep the application log for errors, exceptions, or fatal messages around 3 AM.
>
> I check dmesg for OOM messages — if the kernel ran out of RAM, it kills the most memory-hungry process.
>
> I check df -h — a full disk causes apps to crash silently.
>
> Finally I check if any deployment happened around that time — most 3 AM crashes are caused by a late-night deployment.
>
> After finding the cause, I write an RCA — what happened, why, how fixed, and what monitoring we add."

---

## ❓ Q11. How do you find all ERROR lines from a log file and save to another file?

### 🧠 Commands

```bash
# Basic — find and save errors
grep -i "error" /var/log/app.log > errors.log

# Multiple patterns
grep -iE "error|exception|fatal" /var/log/app.log > errors.log

# Last 100 errors only
grep -i "error" /var/log/app.log | tail -100 > errors.log

# With context — 2 lines before and after each error
grep -i "error" -B 2 -A 2 /var/log/app.log > errors_with_context.log
```

---

### 🔄 grep Flag Meanings

| Flag | Meaning |
|------|---------|
| `-i` | Case insensitive — finds ERROR, Error, error |
| `-n` | Show line numbers |
| `-E` | Extended regex — multiple patterns with `\|` |
| `-B 2` | 2 lines before match |
| `-A 2` | 2 lines after match |

---

### 🔥 DevOps Insight

> Use `-B 2 -A 2` to get context around errors.
> This shows what was happening just before the error — helps find root cause.

---

### ✅ Complete Answer to Say

> "To find all ERROR lines and save them, I use:
> grep -i error /var/log/app.log > errors.log
>
> The -i flag makes it case insensitive. The > redirects output to a new file.
>
> For production debugging I use:
> grep -iE error|exception|fatal app.log | tail -100 > errors.log
>
> I also use -B 2 -A 2 to get 2 lines of context around each error — shows what was happening just before the error."

---

## ❓ Q12. What is the difference between > and >>?

### 🧠 Concept

```bash
# > overwrites — old content is DELETED
echo "first line" > file.txt
echo "second line" > file.txt
cat file.txt
# Output: second line   (first line is GONE)

# >> appends — old content is KEPT
echo "first line" >> file.txt
echo "second line" >> file.txt
cat file.txt
# Output:
# first line
# second line
```

---

### 💡 Real DevOps Examples

```bash
# WRONG — overwrites log every cron run — lose history
0 2 * * * /backup.sh > /var/log/backup.log

# CORRECT — keeps full history
0 2 * * * /backup.sh >> /var/log/backup.log 2>&1
```

---

### 🔥 DevOps Insight

> **Rule to remember:**
> - Logs → always use `>>`  (keep history)
> - Config files → `>` is fine (overwrite)

---

### ✅ Complete Answer to Say

> "Greater than sign overwrites the file completely — old content deleted, replaced with new output.
>
> Double greater than sign appends — adds new content at the end without touching existing content.
>
> In DevOps this matters a lot for cron jobs. If I use single > in a cron job log, every run deletes the previous log — I lose history. I always use >> in cron jobs to keep full history.
>
> Single > is useful when I want to create a fresh file or overwrite a config completely."

---

## ❓ Q13. How do you save both output and errors of a script to one log file?

### 🧠 File Descriptors

```bash
# 0 = stdin  (input)
# 1 = stdout (normal output)
# 2 = stderr (error output)

./script.sh > output.log 2>&1
# > output.log  → send stdout to file
# 2>&1          → send stderr to same place as stdout

# ⚠️ Wrong order — does NOT work correctly
./script.sh 2>&1 > output.log

# Discard everything silently
./script.sh > /dev/null 2>&1
```

---

### 💡 Real Cron Job Example

```bash
# Correct cron with complete logging
0 2 * * * /home/ubuntu/backup.sh >> /var/log/backup.log 2>&1

# Captures:
# Normal output (backup completed)
# Errors (permission denied, disk full)
# All in one file
```

---

### 🔥 DevOps Insight

> **Order matters!**
> `> output.log 2>&1` ✅ correct
> `2>&1 > output.log` ❌ wrong

---

### ✅ Complete Answer to Say

> "File descriptor 1 is stdout — normal output. File descriptor 2 is stderr — error messages.
>
> When I run script.sh > output.log, it only saves normal output. Errors still print to terminal.
>
> To save both, I add 2>&1 at the end — this means send stderr to the same place as stdout.
>
> So ./script.sh > output.log 2>&1 saves everything to output.log.
>
> Order matters — 2>&1 must come after >. I use this in every cron job so errors are captured in the log."

---

## ❓ Q14. How do you monitor logs in real time during a deployment?

### 🧠 Commands

```bash
# For systemd services
journalctl -u appname -f

# For log files
tail -f /var/log/app.log

# Multiple files at once
tail -f /var/log/app.log /var/log/nginx/access.log

# Filter errors in real time
tail -f /var/log/app.log | grep -i "error"

# For Docker
docker logs -f container_name

# For Kubernetes
kubectl logs -f pod-name
```

---

### 🔥 DevOps Insight

> **Best practice during deployment:**
> Always have `tail -f` or `journalctl -f` open in a separate terminal.
> If something breaks — you see it in seconds, not from user complaints.

---

### ✅ Complete Answer to Say

> "During a deployment, I keep a terminal open watching live logs to catch errors immediately.
>
> For systemd services I use journalctl -u appname -f — the -f flag means follow, shows new lines in real time.
>
> For log files I use tail -f /var/log/app.log.
>
> If the log is noisy I pipe through grep — tail -f app.log | grep -i error — so I only see errors live.
>
> For Docker I use docker logs -f container_name and for Kubernetes kubectl logs -f pod-name.
>
> This is the most important habit during deployments — always watch logs live."

---

## 🟡 Area 4 — Systemd & Services

---

## ❓ Q15. How do you make a service start automatically after server reboot?

### 🧠 Commands

```bash
systemctl start nginx          # Start service NOW only
systemctl enable nginx         # Enable auto-start on EVERY reboot
systemctl enable --now nginx   # Do BOTH — start now + enable reboot

# Verify
systemctl is-enabled nginx
# Output: enabled
```

---

### 🔄 start vs enable

| Command | When it runs | Survives reboot |
|---------|-------------|-----------------|
| `systemctl start` | Right now | ❌ No |
| `systemctl enable` | On next reboot | ✅ Yes |
| `systemctl enable --now` | Now + every reboot | ✅ Yes |

---

### 🔥 DevOps Insight

> Real incident: Team installed Jenkins but forgot to enable it.
> After server reboot — Jenkins down, CI/CD pipelines failed.
> **Always `enable --now`, not just `start`.**

---

### ✅ Complete Answer to Say

> "systemctl start starts the service immediately but does not survive a reboot.
>
> systemctl enable creates a symlink so the service starts on every boot. But doesn't start it right now.
>
> In production I always use systemctl enable --now servicename — starts immediately AND sets auto-start on reboot. One command, both done.
>
> I verify with systemctl is-enabled — output should say enabled."

---

## ❓ Q16. Service is failing after restart — how do you debug it?

### 🧠 Step by Step

```bash
# Step 1 — check status and last error
systemctl status appname

# Step 2 — check full logs
journalctl -u appname --since "10 minutes ago"

# Step 3 — check config syntax
nginx -t                         # nginx config test
python -m py_compile app.py      # Python syntax check

# Step 4 — check port conflict
ss -tulnp | grep 8080

# Step 5 — check permissions
ls -la /opt/appname/
ls -la /var/log/appname/

# Step 6 — run manually for full error
/opt/appname/start.sh
```

---

### 🔥 DevOps Insight

> **Most common causes of service failure after restart:**
> 1. Config file edited with syntax error
> 2. Port conflict — another process took the port
> 3. File permission changed
> 4. Dependency service not started yet

---

### ✅ Complete Answer to Say

> "When a service fails after restart, I follow a 5-step debug process.
>
> First — systemctl status appname. Shows last error and exit code. Often the answer is right here.
>
> Second — journalctl -u appname --since 10 minutes ago. Full logs from the restart attempt.
>
> Third — check config syntax. For nginx, nginx -t validates config. Wrong config after editing is very common.
>
> Fourth — check port conflict. ss -tulnp | grep port.
>
> Fifth — run startup command directly in terminal — gives full unfiltered error that systemd sometimes hides."

---

## ❓ Q17. What is the difference between systemctl restart and systemctl reload?

### 🧠 Commands

```bash
# restart — kills process and starts fresh
systemctl restart nginx
# Downtime: YES — few seconds

# reload — sends SIGHUP signal — re-reads config
systemctl reload nginx
# Downtime: NO — zero downtime
```

---

### 🔄 Comparison Table

| | restart | reload |
|-|---------|--------|
| Kills process | ✅ Yes | ❌ No |
| Downtime | Yes (seconds) | Zero |
| New config applied | ✅ Yes | ✅ Yes |
| Use case | New version installed | Config edit only |
| All services support | ✅ Yes | ❌ Not all |

---

### 🔥 DevOps Insight

> **Best practice for nginx config change:**
> ```bash
> nginx -t              # test config first
> systemctl reload nginx  # apply with zero downtime
> ```

---

### ✅ Complete Answer to Say

> "Restart kills the process completely and starts fresh. Brief downtime — few seconds when service not available. Use when installing a new version or major changes.
>
> Reload sends a signal to the running process to re-read config without killing it. Zero downtime — existing connections handled, new config applied. Use when only editing a config file.
>
> Not all services support reload. If unsupported, reload behaves like restart. I check systemctl status to see what signals the service supports."

---

## 🟡 Area 5 — Cron, Scripting, Automation

---

## ❓ Q18. How do you schedule a backup script every day at 2 AM?

### 🧠 Cron Syntax

```
*  *  *  *  *  command
│  │  │  │  │
│  │  │  │  └── Day of week (0=Sunday, 6=Saturday)
│  │  │  └───── Month (1-12)
│  │  └──────── Day of month (1-31)
│  └─────────── Hour (0-23)
└────────────── Minute (0-59)
```

---

### 💡 Solution

```bash
crontab -e

# Add this line:
0 2 * * * /home/ubuntu/backup.sh >> /var/log/backup.log 2>&1

# Verify it was saved
crontab -l
```

---

### 💡 More Cron Examples

```bash
*/5 * * * *     /health-check.sh        # Every 5 minutes
0 9 * * 1-5     /morning-report.sh      # Weekdays at 9 AM
0 0 1 * *       /monthly-cleanup.sh     # 1st of every month
@reboot         /start-app.sh           # On every reboot
```

---

### 🔥 DevOps Insight

> Always add `>> /var/log/backup.log 2>&1` to cron jobs.
> Without it — if backup fails, you will never know why.

---

### ✅ Complete Answer to Say

> "I use cron to schedule tasks. To run backup at 2 AM every day, I run crontab -e and add:
> 0 2 * * * /home/ubuntu/backup.sh >> /var/log/backup.log 2>&1
>
> Cron syntax has 5 time fields — minute, hour, day of month, month, day of week — then the command.
> 0 2 means minute 0 of hour 2 — exactly 2:00 AM. Three stars mean every day, every month, every day of week.
>
> I append >> to keep history. And 2>&1 to capture both output and errors.
> I verify with crontab -l."

---

## ❓ Q19. Write a shell script to check if disk usage is above 80% and alert

### 🧠 Script

```bash
#!/bin/bash

# Disk usage alert script
THRESHOLD=80
DISK=$(df -h / | awk 'NR==2 {print $5}' | tr -d '%')

if [ "$DISK" -gt "$THRESHOLD" ]; then
    echo "⚠️  ALERT: Disk usage is ${DISK}% on $(hostname) at $(date)"
    echo "ALERT: Disk ${DISK}% at $(date)" >> /var/log/disk-alert.log
else
    echo "✅ Disk usage is ${DISK}% — OK"
fi
```

---

### 🧠 Breaking It Down

```bash
df -h /                          # show disk usage of root partition
awk 'NR==2 {print $5}'          # get 2nd row, 5th column (Use%)
tr -d '%'                        # remove the % sign for comparison
[ "$DISK" -gt "$THRESHOLD" ]    # integer comparison
```

---

### 💡 Add to Cron

```bash
# Run every 5 minutes
*/5 * * * * /scripts/disk-check.sh >> /var/log/disk-alert.log 2>&1
```

---

### 🔥 DevOps Insight

> In real production — instead of just echo, integrate with:
> - Slack webhook
> - PagerDuty
> - AWS SNS alert

---

### ✅ Complete Answer to Say

> "I write a bash script that uses df to check disk usage and compares against a threshold.
>
> df -h gives disk usage. I pipe to awk to extract usage percentage from 5th column of second row. Then tr -d removes the percent sign for integer comparison.
>
> Simple if condition — if disk greater than 80, print alert and log it. Otherwise print OK.
>
> I make it executable with chmod +x and add to cron to run every 5 minutes.
>
> In real production, instead of just echo, I would integrate with Slack webhook or PagerDuty."

---

## ❓ Q20. What does 2>&1 mean in a shell script?

### 🧠 File Descriptors

```bash
# Linux standard file descriptors:
# 0 = stdin  (input)
# 1 = stdout (normal output)
# 2 = stderr (error output)

# 2>&1 = redirect fd2 (stderr) to same place as fd1 (stdout)

./script.sh > output.log 2>&1   # ✅ CORRECT order
./script.sh 2>&1 > output.log   # ❌ WRONG order

# Discard all output completely
./script.sh > /dev/null 2>&1
```

---

### 🔥 DevOps Insight

> **Why order matters:**
> `> output.log` first sets stdout to file.
> `2>&1` then points stderr to wherever stdout currently goes (the file).
> If reversed — stderr points to terminal first, then stdout moves to file.

---

### ✅ Complete Answer to Say

> "In Linux, file descriptor 1 is stdout — normal output. File descriptor 2 is stderr — error messages.
>
> 2>&1 means redirect stderr to the same destination as stdout.
>
> So ./script.sh > output.log 2>&1 saves everything — output and errors — to output.log.
>
> Order matters — 2>&1 must come after >. Wrong order and stderr still goes to terminal.
>
> I use this in every cron job so errors are captured in the log file."

---

## 🟡 Area 6 — SRE Specific

---

## ❓ Q21. What is load average? What does "load average: 2.5, 1.8, 1.2" mean?

### 🧠 Concept

```bash
uptime
# Output:
# 10:30:00 up 5 days, load average: 2.5, 1.8, 1.2
#                                   │    │    └── last 15 min
#                                   │    └──────── last 5 min
#                                   └──────────── last 1 min

nproc           # check number of CPU cores
```

---

### 🔄 How to Interpret

| Scenario | nproc | Load avg | Status |
|----------|-------|----------|--------|
| Healthy | 4 | 2.0 | ✅ 50% used |
| Watch | 4 | 3.5 | ⚠️ 87% used |
| Problem | 4 | 6.0 | 🔴 Overloaded |
| Your example | ? | 2.5→1.2 | Spike passing |

---

### 🔥 DevOps Insight

> **Trend matters more than the number:**
> - 1.2 → 1.8 → 2.5 = **getting worse** → act now
> - 2.5 → 1.8 → 1.2 = **recovering** → monitor

---

### ✅ Complete Answer to Say

> "Load average shows average number of processes waiting for CPU — over last 1 minute, 5 minutes, and 15 minutes.
>
> 2.5, 1.8, 1.2 means 2.5 processes waiting last minute, decreasing to 1.2 over 15 minutes — spike was recent, system is recovering.
>
> To judge if load is high, I compare to nproc. For a 4-core server, load of 2.5 is fine. Load of 8 means processes are queuing — problem.
>
> High load average is a signal — I then use top to find which process is causing it."

---

## ❓ Q22. Disk is full on production server — app is crashing. What do you do RIGHT NOW?

### 🧠 Emergency Response

```bash
# Step 1 — confirm disk full
df -h

# Step 2 — find what is using space
du -sh /* 2>/dev/null | sort -rh | head -10
du -sh /var/log/* | sort -rh | head -10

# Step 3 — safe cleanup options

# Option A — clear old logs
find /var/log -name "*.log" -mtime +7 -delete
find /var/log -name "*.gz" -delete

# Option B — truncate active log (NEVER delete open file)
truncate -s 0 /var/log/app.log

# Option C — clear Docker unused data
docker system prune -f

# Option D — clear package cache
apt clean

# Step 4 — verify space freed
df -h

# Step 5 — restart app
systemctl restart appname
```

---

### 🔥 DevOps Insight

> ⚠️ **NEVER run `rm -rf` blindly on production.**
> Use `truncate -s 0` for active log files — empties without deleting.
> The process keeps its file handle open — won't crash.

---

### ✅ Complete Answer to Say

> "Disk full in production is a P1 incident. I act fast but carefully — never rm -rf blindly.
>
> First I confirm with df -h and identify which partition is full.
>
> Then du -sh /* | sort -rh | head -10 shows the top 10 largest directories. Usually /var/log or Docker images.
>
> For logs I use find /var/log -name *.log -mtime +7 -delete to remove logs older than 7 days. For active log files I use truncate -s 0 — never delete an open file, just empty it.
>
> If Docker is the issue, docker system prune -f removes stopped containers and unused images.
>
> After freeing space I confirm with df -h, restart the app, and add a disk monitoring alert so this never surprises us again."

---

## ❓ Q23. What is the difference between RAM and swap? When does swap usage mean trouble?

### 🧠 Commands

```bash
free -h
# Output:
#               total   used    free    available
# Mem:          8.0G    6.5G    200M    1.2G
# Swap:         2.0G    1.8G    200M
```

---

### 🔄 RAM vs Swap

| | RAM | Swap |
|-|-----|------|
| Type | Physical memory | Disk space |
| Speed | Very fast (nanoseconds) | Very slow (milliseconds) |
| Purpose | Active working memory | Overflow when RAM is full |
| App performance | Fast | Slows app significantly |

---

### 💡 Swap Usage Guide

```bash
# Swap at 10%  — normal — kernel optimization
# Swap at 50%  — RAM insufficient — app is slowing down
# Swap at 90%+ — critical — system may become unresponsive
```

---

### 🔥 DevOps Insight

> In Kubernetes and Docker — set memory limits on containers.
> This prevents one container from consuming all RAM and triggering swap.

---

### ✅ Complete Answer to Say

> "RAM is physical memory — very fast, nanosecond access. Swap is disk space used as overflow when RAM is full — very slow, millisecond access.
>
> When RAM gets full, kernel moves least recently used data from RAM to swap. Some swap usage is normal — kernel optimization.
>
> But high swap usage is a warning sign. At 50% swap, app is noticeably slower. At 90%, system can become unresponsive.
>
> I check with free -h. If I see high swap — fix is to increase RAM, optimize app memory usage, or find and kill a memory leak."

---

## ❓ Q24. How do you find which files are taking most disk space?

### 🧠 Commands

```bash
# Top 10 largest directories from root
du -sh /* 2>/dev/null | sort -rh | head -10

# Drill into specific directory
du -sh /var/* | sort -rh | head -10
du -sh /var/log/* | sort -rh | head -10

# Find largest FILES
find / -type f -size +100M 2>/dev/null
find / -type f -size +1G 2>/dev/null
```

---

### 🔄 du Flag Meanings

| Flag | Meaning |
|------|---------|
| `-s` | Summary — one line per directory |
| `-h` | Human readable (MB, GB) |
| `sort -rh` | Sort by size, largest first |
| `head -10` | Show only top 10 |
| `2>/dev/null` | Hide permission errors |

---

### 🔥 DevOps Insight

> **Usual suspects in production:**
> - `/var/log` — old application logs
> - `/var/lib/docker` — Docker images and volumes
> - `/tmp` — uncleaned temp files

---

### ✅ Complete Answer to Say

> "To find what is eating disk space, I use:
> du -sh /* 2>/dev/null | sort -rh | head -10
>
> du is disk usage. -s gives summary per directory. -h makes it human readable. sort -rh sorts largest first. head -10 shows top 10. 2>/dev/null suppresses permission errors.
>
> This shows the top 10 largest directories. If /var is large, I drill down — du -sh /var/* | sort -rh | head -10.
>
> To find individual large files — find / -type f -size +100M 2>/dev/null.
>
> In production usual suspects are /var/log, /var/lib/docker, and /tmp."

---

## 🔴 Area 7 — System Internals

---

## ❓ Q25. What happens when you run a command in Linux?

### 🧠 Complete Flow

```
You type: ls -la
    ↓
Shell checks if alias or builtin
    ↓
Shell searches PATH directories left to right
    ↓
Finds binary at /usr/bin/ls
    ↓
Shell calls fork() → creates child process
    ↓
Child calls exec() → loads ls binary into memory
    ↓
Kernel schedules process on CPU
    ↓
CPU executes instructions
    ↓
ls reads filesystem via kernel system calls
    ↓
Output written to stdout (terminal)
    ↓
Child process exits → parent shell resumes
```

---

### 💡 Debug Commands

```bash
which ls            # shows where binary lives — /usr/bin/ls
type ls             # tells if alias, builtin, or file
echo $PATH          # shows where shell searches
strace ls           # shows every system call — advanced
```

---

### 🔥 DevOps Insight

> **"command not found" error means:**
> Binary doesn't exist in any PATH directory.
> Either not installed OR directory not in PATH.

---

### ✅ Complete Answer to Say

> "When I type a command like ls, the shell first checks if it is an alias or builtin. If not, it searches each directory in PATH from left to right until it finds the binary.
>
> Once found — ls is at /usr/bin/ls — the shell calls fork() to create a child process. That child calls exec() to load the ls binary and execute it.
>
> The kernel schedules the process on CPU. The process makes system calls to the kernel to read the filesystem. Output is written back to stdout — the terminal.
>
> When ls finishes, child process exits and shell resumes waiting for next command."

---

## ❓ Q26. What is $PATH in Linux?

### 🧠 Concept

$PATH is an environment variable that tells the shell WHERE to look for executable commands.

---

### 💡 Commands

```bash
echo $PATH
# Output:
# /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Add new directory to PATH (current session)
export PATH=$PATH:/opt/myapp/bin

# Make permanent
echo 'export PATH=$PATH:/opt/myapp/bin' >> ~/.bashrc
source ~/.bashrc

# Debug command not found
which kubectl                # shows path if found
ls /usr/local/bin/kubectl    # check if binary exists
echo $PATH                   # check directories being searched
```

---

### 🔥 DevOps Insight

> PATH issues come up when installing:
> - `kubectl` after K8s setup
> - `terraform` after download
> - Custom scripts
>
> Binary is there — shell just can't find it because directory not in PATH.

---

### ✅ Complete Answer to Say

> "$PATH is an environment variable storing a colon-separated list of directories where the shell searches for executable commands.
>
> When I type kubectl, the shell searches each directory in PATH from left to right. First match wins. If not found — command not found error.
>
> I see my PATH with echo $PATH. To add a new directory — export PATH=$PATH:/opt/mytool/bin. For permanent change, I add it to ~/.bashrc and run source ~/.bashrc."

---

## ❓ Q27. Difference between df -h and du -sh

### 🧠 Concept

```bash
df -h               # filesystem level — what OS reports as used
du -sh /var/log     # directory level — actual files in that directory
```

---

### 🔄 Comparison Table

| | `df -h` | `du -sh` |
|-|---------|----------|
| Shows | Filesystem disk usage | Directory/file disk usage |
| Source | Filesystem metadata | Actual files |
| Speed | Instant | Slow on large directories |
| Use case | Is disk full? | What is using space? |

---

### 🔥 The Tricky Scenario — SRE Favorite

```bash
# df shows 100% full
df -h
# /dev/sda1   100%   FULL

# But du totals only add up to 60%
# WHERE IS THE MISSING 40%?

# Answer: Deleted file still held open by a running process
lsof | grep deleted
# Shows: appname PID /var/log/app.log (deleted)

# Fix: restart process OR truncate
truncate -s 0 /var/log/app.log
```

---

### ✅ Complete Answer to Say

> "df -h shows filesystem-level usage — what the OS reports. It reads filesystem metadata and is instant.
>
> du -sh shows actual file and directory sizes by scanning files. I use it to find what is consuming space.
>
> The classic SRE scenario: df shows 100% full but du totals only show 60%. The missing 40% is a deleted file still held open by a running process.
>
> When a file is deleted in Linux, disk space is not released until all processes with that file open close it. I find these ghost files with lsof | grep deleted."

---

## ❓ Q28. What is inode in Linux?

### 🧠 Concept

Every file has two parts — the filename and the inode.
The inode contains all metadata — but NOT the filename and NOT the actual data.

---

### 💡 Commands

```bash
ls -i file.txt              # view inode number
stat file.txt               # full inode details

df -i                       # check inode usage
# If inode 100% — cannot create new files even if disk has space

# Fix inode exhaustion
find /tmp -type f -mtime +7 -delete    # delete old temp files
```

---

### 🔄 Inode Stores vs Does Not Store

| Inode STORES | Inode does NOT store |
|-------------|---------------------|
| Permissions | Filename |
| Owner and group | File content |
| File size | Directory location |
| Timestamps | |
| Pointer to data blocks | |

---

### 🔥 DevOps Insight

> Inode exhaustion happens with millions of tiny files.
> Common in `/tmp` or mail servers.
> **Disk has free space but you cannot create new files.**

---

### ✅ Complete Answer to Say

> "An inode is a data structure storing metadata about a file — but not the filename and not the actual content.
>
> Every file has a unique inode number. The inode stores permissions, owner, group, file size, timestamps, and pointers to actual data blocks.
>
> The filename is stored in the directory — which maps filenames to inode numbers. This is why hard links work — two filenames can point to the same inode.
>
> In DevOps, inodes matter because a filesystem can run out of inodes even when disk space is available. I check inode usage with df -i. Fix is to delete old small files."

---

## ❓ Q29. Why is disk full but no large files visible?

### 🧠 Three Possible Reasons

```bash
# Reason 1 — Deleted file held open by process (most common)
lsof | grep deleted
truncate -s 0 /path/to/file     # fix without killing process

# Reason 2 — Inodes exhausted (not disk space)
df -i
find /tmp -type f | wc -l       # count files
find /tmp -type f -mtime +7 -delete

# Reason 3 — Files hidden under mount point
umount /mountpoint
du -sh /mountpoint              # now shows hidden files
```

---

### 🔥 Full Debug Flow

```bash
df -h           # confirm disk full
df -i           # check inodes
lsof | grep deleted     # deleted but open files
find / -type f -size +100M 2>/dev/null  # large files
du -sh /* 2>/dev/null | sort -rh | head -10  # top dirs
```

---

### ✅ Complete Answer to Say

> "Disk showing full but no large files visible is a classic SRE puzzle. Three main reasons.
>
> First — deleted file held open by a running process. Directory entry removed but disk space not freed until process closes it. Invisible to find and du but consuming space. I find with lsof | grep deleted and fix by truncating the file.
>
> Second — inodes exhausted. Disk space is fine but too many small files used all inodes. Check with df -i. Fix is to delete old small files.
>
> Third — files hidden under a mount point. Files existed before filesystem was mounted on top. I unmount and check.
>
> In production, deleted file held by process is the most common cause."

---

## 🔴 Area 8 — Process & Performance

---

## ❓ Q30. Difference between top and htop

### 🔄 Feature Comparison

| Feature | top | htop |
|---------|-----|------|
| Pre-installed | ✅ Always | ❌ Need to install |
| Color display | ❌ Basic | ✅ Yes |
| Mouse support | ❌ No | ✅ Yes |
| Kill from UI | ❌ Hard | ✅ Easy — F9 |
| Sort columns | Limited | ✅ Click any column |
| Filter by name | ❌ No | ✅ F4 |
| Tree view | ❌ No | ✅ F5 |

---

### 💡 Useful top Shortcuts

```bash
# Inside top:
P       # sort by CPU usage
M       # sort by Memory usage
k       # kill a process (enter PID)
q       # quit
1       # show per-CPU core stats
```

---

### 🔥 DevOps Insight

> `top` is always available — use it on new/minimal servers.
> `htop` is preferred when available — much easier to read and navigate.

---

### ✅ Complete Answer to Say

> "Both top and htop are real-time process monitors but with different capabilities.
>
> top is built into every Linux system — no installation needed. Text-based and basic. I use it when I SSH into a server that doesn't have htop.
>
> htop needs to be installed but is much more user-friendly. Color coding, mouse support, easier sorting, F4 to filter by name, F9 to kill a process, F5 for process tree.
>
> In production I prefer htop for interactive debugging. On minimal systems or scripts, top is the reliable choice."

---

## ❓ Q31. What is nice and renice?

### 🧠 Priority Range

```
-20 ←── Higher priority (more CPU)
  0 ←── Default
+19 ←── Lower priority (less CPU)
```

---

### 💡 Commands

```bash
# nice — start new process with priority
nice -n 10 ./backup.sh          # low priority
nice -n -5 ./critical.sh        # high priority (needs root)

# renice — change running process priority
renice -n 15 -p PID             # lower priority
renice -n -5 -p PID             # raise priority (needs sudo)

# Check current priority
ps aux | grep process            # NI column shows nice value
```

---

### 💡 Real DevOps Use

```bash
# Run backup without affecting app performance
nice -n 19 tar -czvf backup.tar.gz /var/www/

# Jenkins consuming too much CPU — lower its priority
renice -n 10 -p $(pgrep jenkins)
```

---

### 🔥 DevOps Insight

> Background jobs like backups and log compression should always run at `nice 19`.
> Production traffic always gets priority.

---

### ✅ Complete Answer to Say

> "nice and renice control process scheduling priority — how much CPU time a process gets.
>
> Range is -20 to 19. Lower number means higher priority. Higher number means lower priority and yields to others. Default is 0. Only root can set negative values.
>
> nice starts a new process with specific priority — nice -n 10 backup.sh starts backup with low priority.
>
> renice changes priority of an already running process.
>
> In DevOps I use this for background jobs — run them at nice 19 so production traffic always gets priority."

---

## ❓ Q32. What is the uptime command? Connect with load average.

### 🧠 Output Breakdown

```bash
uptime
# 10:30:00 up 15 days, 3:42, 2 users, load average: 1.20, 0.95, 0.80
# │         │           │     │ users  │             │     │     └── 15 min
# │         │           │     │        │             │     └──────── 5 min
# │         │           │     │        │             └────────────── 1 min
# current   uptime      days  users    load average
```

---

### 💡 Interpreting Load Average

```bash
nproc                   # check CPU cores — e.g. 4

# Load 1.20 with 4 cores = 30% — healthy ✅
# Load 4.00 with 4 cores = 100% — at capacity ⚠️
# Load 8.00 with 4 cores = 200% — overloaded 🔴
```

---

### 🔥 DevOps Insight

> **Pattern analysis:**
> `0.80 → 0.95 → 1.20` = increasing → problem getting worse
> `1.20 → 0.95 → 0.80` = decreasing → spike is passing

---

### ✅ Complete Answer to Say

> "uptime shows four things — current time, how long server has been running, logged-in users, and load average.
>
> Load average shows average processes waiting for CPU over last 1 minute, 5 minutes, and 15 minutes.
>
> To interpret — I check nproc for CPU count. If nproc is 4 and load is 2.0, system is at 50% — healthy. If load is 8.0, processes are queuing — problem.
>
> Trend matters — if 1-minute load is higher than 15-minute, problem is getting worse. If lower, it was a spike that is passing.
>
> I check uptime first when a server performance alert comes in."

---

## 🔴 Area 9 — Networking Basics

---

## ❓ Q33. Difference between ping, curl, and telnet

### 🔄 Comparison Table

| Command | Protocol | Layer | Tests |
|---------|----------|-------|-------|
| `ping` | ICMP | Network L3 | Is host alive? |
| `telnet` | TCP | Transport L4 | Is specific port open? |
| `curl` | HTTP/HTTPS | Application L7 | Is web service responding? |
| `nc -zv` | TCP | Transport L4 | Modern telnet alternative |

---

### 💡 Debug Flow

```bash
# App not reachable — debug in order:

# Step 1 — is server alive?
ping 10.0.0.5

# Step 2 — is the port open?
telnet 10.0.0.5 8080
nc -zv 10.0.0.5 8080            # modern way

# Step 3 — is HTTP service responding?
curl -I http://10.0.0.5:8080
curl http://10.0.0.5:8080/health
```

---

### 🔥 DevOps Insight

> Debug in layers — from lower to higher:
> Network → Transport → Application
> This isolates whether it is a network, firewall, or application problem.

---

### ✅ Complete Answer to Say

> "ping, curl, and telnet test connectivity at different network layers.
>
> ping uses ICMP to check if a host is reachable at network layer. Just answers — is this IP alive? Doesn't test ports or services.
>
> telnet tests TCP connectivity to a specific port. If it connects, port is open. If refused, nothing is listening or firewall is blocking.
>
> curl tests at application layer — HTTP or HTTPS. Shows actual response including status code. I use curl -I url for headers and curl url/health for health endpoints.
>
> In DevOps when app is unreachable — ping first, then telnet to check port, then curl to check service response."

---

## ❓ Q34. How do you check open ports in Linux?

### 🧠 Commands

```bash
# Modern — preferred
ss -tulnp

# Filter specific port
ss -tulnp | grep 8080
ss -tulnp | grep nginx

# Alternative
lsof -i :8080

# Old way — still works
netstat -tulnp
```

---

### 💡 Reading ss Output

```bash
ss -tulnp
# Proto  Local Address:Port  Process
# tcp    0.0.0.0:8080        users:(("java",pid=1234))
# tcp    0.0.0.0:22          users:(("sshd",pid=567))
# tcp    127.0.0.1:3306      users:(("mysqld",pid=890))
```

---

### 🔄 ss Flag Meanings

| Flag | Meaning |
|------|---------|
| `-t` | TCP ports |
| `-u` | UDP ports |
| `-l` | Listening ports only |
| `-n` | Numeric port numbers |
| `-p` | Show process name |

---

### 🔥 DevOps Insight

> After every deployment — verify app is actually listening:
> ```bash
> ss -tulnp | grep 8080
> curl http://localhost:8080
> ```

---

### ✅ Complete Answer to Say

> "To check open ports I use ss -tulnp. ss is the modern replacement for netstat.
>
> Flags mean — t for TCP, u for UDP, l for listening only, n for numeric ports, p for process name.
>
> Output shows protocol, address, port, and which process is listening. For specific port I add grep — ss -tulnp | grep 8080.
>
> In DevOps I use this constantly — after deployment to confirm app started, when I get port already in use errors, and for security auditing."

---

## ❓ Q35. What is localhost (127.0.0.1)?

### 🔄 Address Comparison

| Address | Meaning | Accessible from |
|---------|---------|----------------|
| `127.0.0.1` | Loopback — this machine only | Same machine only |
| `0.0.0.0` | All interfaces | Any machine |
| `192.168.x.x` | Private IP | Local network |
| Public IP | Internet facing | Internet |

---

### 💡 Real DevOps Use

```bash
# App bound to localhost — only accessible from same server
curl http://localhost:8080      # ✅ works
curl http://server-ip:8080      # ❌ fails from outside

# App bound to 0.0.0.0 — accessible from anywhere
curl http://server-ip:8080      # ✅ works from anywhere
```

---

### 🔥 DevOps Insight

> **Security rule:**
> - Database → bind to `127.0.0.1` (internal only)
> - Web server → bind to `0.0.0.0` (accept external traffic)

---

### ✅ Complete Answer to Say

> "localhost is a hostname that always resolves to 127.0.0.1 — the loopback address. Always refers to the current machine itself.
>
> When an app listens on localhost, it only accepts connections from the same machine. No external access.
>
> When an app listens on 0.0.0.0, it accepts connections on all network interfaces — local and external.
>
> In DevOps — database should listen on 127.0.0.1 for security. Web server should listen on 0.0.0.0 to accept external traffic.
>
> When curl http://localhost:8080 works but curl http://server-ip:8080 fails — app is bound to localhost only."

---

## 🔴 Area 10 — Permissions & Users

---

## ❓ Q36. Difference between su and sudo

### 🔄 Comparison Table

| | `su` | `sudo` |
|-|----|------|
| Needs | Target user's password | Your own password |
| Scope | Entire shell session | Single command |
| Audit trail | ❌ No | ✅ Yes — /var/log/auth.log |
| Granular control | ❌ No | ✅ Yes — /etc/sudoers |
| Preferred in production | ❌ No | ✅ Yes |

---

### 💡 Commands

```bash
su vamshi               # switch to vamshi — needs vamshi's password
su                      # switch to root — needs ROOT password
sudo apt update         # run as root — needs YOUR password
sudo -u vamshi ls       # run as vamshi — needs your password
```

---

### 🔥 DevOps Insight

> **Why sudo is always preferred over su in production:**
> 1. No need to share root password
> 2. Every command logged in `/var/log/auth.log` — full audit trail
> 3. Granular control — user can run only specific commands

---

### ✅ Complete Answer to Say

> "su switches your entire session to another user. Requires target user's password. After su root, all commands run as root until I type exit.
>
> sudo runs a single command as root using YOUR own password. After that command, you return to normal user.
>
> In production, sudo is always preferred — you don't need root password, every command is logged for audit, and through /etc/sudoers you can give users permission for only specific commands following least privilege."

---

## ❓ Q37. What is umask?

### 🧠 Concept

umask controls default permissions for newly created files and directories.

---

### 💡 How umask Works

```bash
umask
# Output: 0022

# Default max:  files = 666, directories = 777
# umask 022 subtracts 022

# New file:      666 - 022 = 644  (rw-r--r--)
# New directory: 777 - 022 = 755  (rwxr-xr-x)
```

---

### 🔄 Common umask Values

| umask | New file | New directory | Use case |
|-------|----------|---------------|----------|
| `022` | 644 | 755 | Default — standard |
| `027` | 640 | 750 | More restrictive — production |
| `077` | 600 | 700 | Very restrictive — sensitive |

---

### 💡 Change umask

```bash
umask 027                                # change for current session
echo 'umask 027' >> ~/.bashrc            # make permanent
```

---

### 🔥 DevOps Insight

> Production servers should use `umask 027`.
> New files get 640 — only owner and group can read.
> Sensitive files not world-readable by default.

---

### ✅ Complete Answer to Say

> "umask — user file creation mask — controls default permissions when new files and directories are created.
>
> Linux starts with maximum — 666 for files, 777 for directories. umask subtracts from those defaults.
>
> Default umask 022: subtract from 666 = 644 for files, subtract from 777 = 755 for directories.
>
> In production, stricter umask 027 is used — new files get 640, only owner and group can read. Security best practice so sensitive files are not world-readable by default."

---

## 🔴 Area 11 — File Handling

---

## ❓ Q38. Difference between cat, less, and more

### 🔄 Feature Comparison

| Feature | cat | less | more |
|---------|-----|------|------|
| Shows full file at once | ✅ | No | No |
| Scroll up | ❌ | ✅ | ❌ |
| Scroll down | N/A | ✅ | ✅ |
| Search inside | ❌ | ✅ `/pattern` | ❌ |
| Good for large files | ❌ | ✅ | OK |
| Use in scripts | ✅ | ❌ | ❌ |

---

### 💡 less Shortcuts

```bash
less /var/log/syslog
# G       → go to end
# g       → go to start
# /error  → search
# n       → next result
# q       → quit
# Space   → next page
# b       → previous page
```

---

### 🔥 DevOps Insight

> **Rule of thumb:**
> - Small config files → `cat`
> - Large log files → `less`
> - Real-time logs → `tail -f`

---

### ✅ Complete Answer to Say

> "cat prints entire file at once. Good for small files and scripts. For large files, output floods terminal.
>
> more is a basic pager — shows one page at a time, you press space to go forward but cannot scroll back.
>
> less is the most useful — interactive viewer where you can scroll up and down, search with /pattern, jump to end with G, quit with q. Less is more — does everything more does and more.
>
> In DevOps I use cat for small config files, less for reading large log files, and tail -f for real-time monitoring."

---

## ❓ Q39. How to find a file in Linux?

### 🧠 Commands

```bash
# Find by name
find / -name "nginx.conf"
find /etc -name "*.conf"
find . -name "*.log"

# Find by type
find /home -type f -name "*.sh"        # files only
find /var -type d -name "logs"         # directories only

# Find by size
find / -size +100M 2>/dev/null
find / -size +1G 2>/dev/null

# Find by time
find /var/log -mtime +7                # modified more than 7 days ago
find /tmp -mtime +1 -delete           # find and delete old files

# Find and execute action
find /var/log -name "*.log" -exec ls -lh {} \;
find /tmp -mtime +7 -exec rm {} \;
```

---

### 🔥 DevOps Insight

> Always add `2>/dev/null` to suppress permission denied errors.
> Makes output clean and readable.

---

### ✅ Complete Answer to Say

> "find is the most powerful file search command in Linux.
>
> Basic: find /path -name filename. First argument is where to search, -name is the pattern.
>
> I filter by type — -type f for files, -type d for directories.
>
> By size — find / -size +100M finds files larger than 100MB. Useful for disk cleanup.
>
> By time — -mtime +7 finds files modified more than 7 days ago. Combined with -delete, this is how I automate log cleanup.
>
> I always add 2>/dev/null to suppress permission denied errors.
>
> In production I use find daily — disk cleanup, locating config files, and log rotation scripts."

---

## 🔴 Area 12 — SRE Thinking Questions

---

## ❓ Q40. How do you handle a sudden traffic spike?

### 🧠 5-Step SRE Response

```
Sudden spike hits
    │
    ├── Step 1: OBSERVE
    │   ├── top / htop       → CPU spike?
    │   ├── free -h          → RAM exhausted?
    │   ├── ss -tulnp        → too many connections?
    │   └── app logs         → errors? timeouts?
    │
    ├── Step 2: IMMEDIATE RELIEF
    │   ├── kubectl scale deployment app --replicas=10
    │   ├── Add EC2 to load balancer (AWS)
    │   └── Increase container resources
    │
    ├── Step 3: PROTECT THE SERVICE
    │   ├── Rate limiting    → limit requests per IP
    │   ├── Caching          → serve cached responses
    │   └── CDN              → offload static content
    │
    ├── Step 4: MONITOR
    │   ├── Prometheus alerts
    │   └── Grafana dashboard
    │
    └── Step 5: POST INCIDENT
        └── RCA — why did it spike?
```

---

### 🔥 DevOps Insight

> **Key SRE principle:**
> Always have monitoring that alerts BEFORE users notice the problem.
> Proactive > Reactive.

---

### ✅ Complete Answer to Say

> "When a sudden traffic spike hits, I follow a structured approach.
>
> First — observe. top shows CPU, free -h shows memory, ss shows connection count, logs show if there are errors.
>
> For immediate relief — on Kubernetes I scale up with kubectl scale deployment --replicas=10. On EC2 I add instances behind the load balancer.
>
> To protect the service — rate limiting prevents one user consuming all resources. Caching reduces database load.
>
> Throughout, I watch Prometheus and Grafana to see if scaling is working.
>
> After recovery, I write an RCA — was this expected traffic? An attack? Organic growth? The answer determines whether we need autoscaling, caching, or security measures."

---

## ❓ Q41. Difference between vertical scaling and horizontal scaling

### 🧠 Concept

```bash
# Vertical Scaling — Scale UP
# Make the SAME server bigger
# t2.micro → t2.xlarge

# Horizontal Scaling — Scale OUT
# Add MORE servers
# 1 server → 10 servers behind load balancer
```

---

### 🔄 Comparison Table

| | Vertical Scaling | Horizontal Scaling |
|-|-----------------|-------------------|
| How | Bigger server | More servers |
| Downtime | Yes — restart needed | No — add without stopping |
| Limit | Physical hardware limit | Almost unlimited |
| Cost | Expensive at high end | More flexible |
| Failure risk | Single point of failure | Distributed — resilient |
| DevOps preference | Short term fix | Long term solution |

---

### 💡 Real Examples

```bash
# Vertical — quick emergency fix
# AWS: stop instance → change type → start
# Requires downtime

# Horizontal — production standard
kubectl scale deployment app --replicas=5
# AWS Auto Scaling Group adds EC2 automatically
# Zero downtime — load balancer routes to new instances
```

---

### 🔥 DevOps Insight

> **Horizontal scaling is always the production standard because:**
> - No single point of failure
> - Auto-scaling possible — K8s HPA, AWS ASG
> - No downtime when adding capacity

---

### ✅ Complete Answer to Say

> "Vertical scaling means making the existing server bigger — more CPU, more RAM. Like upgrading from t2.small to t2.xlarge on AWS. Simple but has limits — you can only make a server so big. Usually requires downtime to restart.
>
> Horizontal scaling means adding more servers and distributing traffic between them with a load balancer. Nearly unlimited capacity and no downtime — just add instances while existing ones keep running.
>
> In SRE and DevOps, horizontal scaling is the standard. It avoids single point of failure and enables auto-scaling — Kubernetes HPA or AWS Auto Scaling automatically adds and removes instances based on traffic.
>
> Vertical scaling is used as a quick temporary fix during an incident. The long-term solution is always horizontal."

---

## 🔥 The Master Scenario — Production App Down at 9 AM

### ✅ Complete Answer — Memorize This

> "First I update my manager — payment service is down, I am investigating, will update in 10 minutes. Then I post in team channel so nobody else jumps on the same issue simultaneously.
>
> On the server, I check 5 layers in order:
>
> Layer 1 — Service: systemctl status payment-service — is it running or crashed?
>
> Layer 2 — Logs: journalctl -u payment-service -f — what are live logs saying right now?
>
> Layer 3 — Resources: df -h for disk, free -h for RAM, top for CPU — is the server itself the problem?
>
> Layer 4 — Network: ss -tulnp | grep 8080 — is the service listening on its port? curl http://localhost:8080 — does it respond?
>
> Layer 5 — Recent changes: Was there a deployment in the last hour? If yes — first suspect. I rollback with kubectl rollout undo deployment/payment-service or trigger previous Jenkins build.
>
> Once fixed — update manager and team. Then write a quick RCA — what happened, why, how fixed, and what monitoring we add so this gets caught before users notice next time."

*All 41 questions covered. Practice every answer out loud — no notes — 60 seconds each.*
*Next topic → Networking fundamentals.*
