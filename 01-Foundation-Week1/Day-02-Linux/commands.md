### *After giving multiple interviews, here’s what I realized for freshers

Focus is not on theory — it’s on practical skills.

👉 This document covers:

Essential Linux commands
Real-world troubleshooting
What is actually asked in DevOps / SRE interviews* ###

## 📌 Topics Covered

* File & Directory Commands
* File Read & Write
* File Permissions & Ownership
* Hard Link & Soft Link
* I/O Redirection & Piping
* grep, find, awk, sed
* Process Management
* System Monitoring
* User Management
* Package Management
* Text Editors (vim / nano)
* Environment Variables
* Cron Jobs
* tar & gzip (Compression & Archiving)
* lsof & rsync
* ufw Firewall & tmux
* SSH Basics
* Networking Commands
* Systemd (Service Management)


### *Start hands on pratice with understanding* ###


## 📂 Topic 1: File & Directory Commands

These are the most basic and most used commands in Linux. You will use these every single day.

---

### 🔍 Navigation

```bash
pwd                         # Shows current working directory
                            # Use when you are lost in the file system

ls                          # Lists files and directories
ls -lrta                    # Detailed listing
                            # -l → long format (permissions, owner, size)
                            # -r → reverse order
                            # -t → sort by time (newest last)
                            # -a → include hidden files (starting with .)

cd /path                    # Change directory
cd /var/log                 # Example — go to logs folder
cd ~                        # Go to home directory
cd ..                       # Go one level up
```

---

### 📁 Create, Copy, Move, Delete

```bash
mkdir folder_name           # Create a directory
mkdir -p a/b/c              # Create nested directories at once

touch file.txt              # Create an empty file
cp file1 file2              # Copy file
mv file1 file2              # Move or rename file
rm file.txt                 # Delete a file
rm -rf folder               # ⚠️ Delete folder and everything inside — use carefully
```

👉 Key Point:
`rm -rf` is dangerous. There is no recycle bin in Linux. Once deleted — it is gone.

---

### 🔥 DevOps Insight

> `ls -la /var/log` — check all log files with their permissions and sizes when debugging an issue.

---

## 📄 Topic 2: File Read & Write

```bash
cat file.txt                # Display full file content
head -5 file.txt            # Show first 5 lines
tail -5 file.txt            # Show last 5 lines
tail -f logs.txt            # Real-time log monitoring — very important in production debugging

cat > file.txt              # Overwrite file ⚠️ deletes old content
echo "text" >> file.txt     # Append content to file without deleting old content

wc -l file.txt              # Count number of lines in file
```

---

### 🔄 Difference Between head and tail

| Command | What it shows | Use case |
|---------|--------------|----------|
| `head -n` | First n lines | Check file header or config start |
| `tail -n` | Last n lines | Check latest log entries |
| `tail -f` | Live updates | Watch logs in real time during deployment |

---

### 🔥 DevOps Insight

> When a service crashes, the first thing you do is:
> ```bash
> tail -f /var/log/syslog
> journalctl -u servicename -f
> ```
> Watch live logs to find what went wrong.

---

## 🔐 Topic 3: File Permissions & Ownership

Every file in Linux has permissions that control who can read, write, or execute it.

```bash
ls -l
# Example output:
# -rwxr-xr--  1 vamshi devops 1024 May 5 10:00 script.sh
```

---

### 🔍 Permission Structure

```
- rwx r-x r--
│ │   │   └── Others (read only)
│ │   └────── Group (read + execute)
│ └────────── Owner (read + write + execute)
└──────────── File type (- = file, d = directory, l = symlink)
```

---

### 🔣 Permission Symbols

| Symbol | Meaning | Value |
|--------|---------|-------|
| `r` | read | 4 |
| `w` | write | 2 |
| `x` | execute | 1 |
| `-` | no permission | 0 |

---

### 🔢 Common Permission Numbers

| Number | Meaning | Use case |
|--------|---------|----------|
| `777` | Everyone full access | ⚠️ Never use in production |
| `755` | Owner all, others read+execute | Scripts, web files |
| `644` | Owner read+write, others read | Normal config files |
| `600` | Owner read+write only | SSH keys, passwords |
| `400` | Owner read only | Private keys (AWS .pem) |

---

### 🔧 Change Permissions & Ownership

```bash
chmod +x script.sh          # Make file executable
chmod 755 script.sh         # Owner all, others read+execute
chmod 600 ~/.ssh/id_rsa     # SSH key — must be 600 or AWS rejects connection
chmod 400 key.pem           # AWS pem key — read only for owner
chmod 777 file.txt          # ⚠️ Full access to everyone — not safe

chown user file.txt         # Change owner
chown user:group file.txt   # Change owner and group
chgrp group file.txt        # Change group only
```

---

### 🔥 DevOps Insight

> SSH key permissions must be `chmod 600`.
> If it is `777`, AWS will refuse your connection with:
> **"Permissions are too open"** error.

---

## 🔗 Topic 4: Hard Link & Soft Link

```bash
# Hard Link
ln file.txt hardlink.txt
# Same inode — points to same actual data on disk
# If original file deleted → hard link still works

# Soft Link (Symbolic Link)
ln -s file.txt softlink.txt
# Acts like a shortcut
# If original file deleted → soft link breaks
```

---

### 🔄 Hard Link vs Soft Link

| | Hard Link | Soft Link |
|--|-----------|-----------|
| Works after original deleted | ✅ Yes | ❌ No |
| Can link across file systems | ❌ No | ✅ Yes |
| Used for | Backups | Config shortcuts |

---

## 🔀 Topic 5: I/O Redirection & Piping

This is the glue of Linux. Every DevOps engineer uses this 100 times a day.
It is how you combine commands and control where output goes.

---

### ➡️ Redirection Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `>` | Overwrite output to file | `echo "hello" > file.txt` |
| `>>` | Append output to file | `echo "hello" >> file.txt` |
| `2>` | Redirect errors to file | `command 2> error.log` |
| `2>&1` | Combine output + errors | `command > all.log 2>&1` |
| `/dev/null` | Discard all output | `command > /dev/null 2>&1` |

---

### 🔗 Pipe Operator `|`

The pipe `|` sends the output of one command as input to another command.

```bash
ps aux | grep nginx             # Find nginx process from all processes
cat app.log | grep "ERROR"      # Search errors in log file
df -h | grep /dev/sda           # Check specific disk usage
ls -la | tail -10               # Show last 10 files
```

---

### 💡 Practical Examples

```bash
# Save script output and errors to one log file
./deploy.sh > deploy.log 2>&1

# Run cron job silently — no output noise
0 2 * * * /backup.sh > /dev/null 2>&1

# Find errors and save to separate file
grep "ERROR" app.log > errors_only.log

# Append new logs without deleting old ones
echo "Deploy done at $(date)" >> deploy_history.log
```

---

### 🔥 DevOps Insight

> Interview Q: "How do you save both output and errors of a script to a log file?"
>
> Answer:
> ```bash
> ./script.sh > output.log 2>&1
> ```

---

## 🔍 Topic 6: grep, find, awk, sed

These are the most powerful text processing commands in Linux.
Used heavily in log analysis, config management, and automation.

---

### 🔎 grep — Search inside files

```bash
grep "error" app.log                    # Find lines with "error"
grep -i "error" app.log                 # Case insensitive search
grep -n "error" app.log                 # Show line numbers
grep -r "password" /etc/               # Search recursively in folder
grep "error" app.log | tail -20        # Last 20 error lines — very common
grep -v "info" app.log                 # Show lines that do NOT contain "info"
```

---

### 🔎 find — Find files by name, type, size

```bash
find / -name "nginx.conf"              # Find file by name
find /var/log -name "*.log"            # Find all log files
find /home -type f -name "*.sh"        # Find all shell scripts
find /tmp -mtime +7 -delete            # Delete files older than 7 days
```

---

### 🔎 awk — Extract columns from output

```bash
df -h | awk '{print $1, $5}'           # Show disk name and usage %
ps aux | awk '{print $1, $2, $11}'     # Show user, PID, command
```

---

### 🔎 sed — Find and replace in files

```bash
sed -i 's/old/new/g' file.txt         # Replace all "old" with "new" in file
sed -n '10,20p' file.txt              # Print only lines 10 to 20
```

---

### 🔥 DevOps Insight

> First command when app is down:
> ```bash
> grep "ERROR" /var/log/app.log | tail -50
> ```

---

## ⚙️ Topic 7: Process Management

A process is any running program or command in Linux.
As a DevOps engineer, you need to monitor and manage processes constantly.

```bash
ps aux                      # Shows all running processes
top                         # Live process monitoring (CPU, memory)
htop                        # Better UI for process monitoring (install separately)

kill PID                    # Stop process by PID number
kill -9 PID                 # Force kill — use when normal kill does not work
pkill nginx                 # Kill process by name
killall nginx               # Kill all processes with that name
pgrep nginx                 # Find PID of a running process
```

---

### 🔥 DevOps Insight

> App consuming 100% CPU?
> ```bash
> top          # find which PID is using high CPU
> kill -9 PID  # force stop it
> ```

---

## 📊 Topic 8: System Monitoring

```bash
df -h                       # Disk usage — check if disk is full
free -h                     # RAM usage — check memory
uptime                      # System load average + how long running
nproc                       # Number of CPU cores
cat /proc/cpuinfo           # Detailed CPU info
cat /proc/meminfo           # Detailed memory info
```

---

### 🔄 When to use what

| Situation | Command |
|-----------|---------|
| App is slow | `top` — check CPU usage |
| App crashes with memory error | `free -h` — check RAM |
| Disk full error | `df -h` — check disk |
| Server running since when | `uptime` |

---

### 🔥 DevOps Insight

> When app slows down — check in this order:
> 1. `df -h` — disk full?
> 2. `free -h` — RAM full?
> 3. `top` — which process consuming CPU?

---

## 👤 Topic 9: User Management

```bash
whoami                      # Shows current logged in user
id                          # Shows user ID and group ID
adduser username            # Create new user
passwd username             # Set password for user
su username                 # Switch to another user
sudo command                # Run command as root/admin
sudo su                     # Switch to root user

cat /etc/passwd             # List all users on system
cat /etc/group              # List all groups
```

---

### 🔥 DevOps Insight

> Never run applications as root.
> Create a separate service user with limited permissions.
> This follows the **least privilege principle** — a core security best practice in DevOps.

---

## 📦 Topic 10: Package Management

```bash
# Ubuntu / Debian (apt)
sudo apt update                         # Update package list
sudo apt install nginx                  # Install package
sudo apt remove nginx                   # Remove package
sudo apt upgrade                        # Upgrade all packages

# CentOS / RHEL (yum)
sudo yum update
sudo yum install nginx
sudo yum remove nginx

# Check if package is installed
which nginx                             # Shows path if installed
nginx --version                         # Check version
```

---

### 🔄 apt vs yum

| | apt | yum |
|-|-----|-----|
| Used on | Ubuntu, Debian | CentOS, RHEL, Amazon Linux |
| Update command | `apt update` | `yum update` |

---

## ✏️ Topic 11: Text Editors — vim & nano

On a Linux server you will not have VS Code.
You must know how to edit files directly in the terminal.

```bash
# nano — easier for beginners
nano file.txt               # Open file
# Ctrl+O → save
# Ctrl+X → exit

# vim — used by most engineers
vim file.txt                # Open file
# i      → insert mode (start typing)
# Esc    → exit insert mode
# :w     → save
# :q     → quit
# :wq    → save and quit
# :q!    → quit without saving
# dd     → delete a line
# yy     → copy a line
# p      → paste
```

---

### 🔥 DevOps Insight

> You will need to edit nginx config, Jenkins files, and Docker configs directly on the server.
> Learn vim basics — it is available on every Linux server by default.

---

## 🌍 Topic 12: Environment Variables

Environment variables are key-value pairs used to store configuration values.
App configs like DB passwords, API keys, and port numbers are passed this way — never hardcoded in code.

```bash
echo $PATH                  # See your PATH variable
echo $HOME                  # Home directory path
echo $USER                  # Current user

export MY_VAR="hello"       # Set environment variable (current session only)
echo $MY_VAR                # Use it

# To make it permanent — add to ~/.bashrc
echo 'export MY_VAR="hello"' >> ~/.bashrc
source ~/.bashrc            # Reload bashrc to apply changes

env                         # Show all environment variables
```

---

### 🔥 DevOps Insight

> In Docker and Kubernetes, environment variables are used to inject secrets and configs into containers without hardcoding them.
>
> Example:
> ```bash
> DB_PASSWORD=secret123
> API_KEY=abc123
> PORT=8080
> ```
> All passed as env vars — never written directly in code.

---

## ⏰ Topic 16: Cron Jobs — Task Scheduling

Cron is how you automate repetitive tasks on a schedule.
Used for: backups, log cleanup, health checks, sending reports.

```bash
crontab -e                  # Edit your cron jobs (opens in vim/nano)
crontab -l                  # List all existing cron jobs
crontab -r                  # Remove all cron jobs ⚠️ careful
```

---

### 🕐 Cron Syntax

```
*  *  *  *  *  /path/to/script.sh
│  │  │  │  │
│  │  │  │  └── Day of week (0=Sunday, 6=Saturday)
│  │  │  └───── Month (1-12)
│  │  └──────── Day of month (1-31)
│  └─────────── Hour (0-23)
└────────────── Minute (0-59)
```

---

### 💡 Practical Examples

```bash
0 2 * * *       /backup.sh              # Every day at 2 AM
*/5 * * * *     /health-check.sh        # Every 5 minutes
0 0 * * 0       /weekly-cleanup.sh      # Every Sunday at midnight
0 9 * * 1-5     /morning-report.sh      # Every weekday at 9 AM
@reboot         /start-app.sh           # Run once on system reboot
```

---

### 🔥 DevOps Insight

> Interview Q: "How do you run a backup script every day at midnight?"
>
> Answer:
> ```bash
> crontab -e
> # Add this line:
> 0 0 * * * /home/ubuntu/backup.sh > /var/log/backup.log 2>&1
> ```

---

## 🗜️ Topic 13: tar & gzip — Compression & Archiving

Used every day — backup files, transfer logs, package deployments.

```bash
tar -cvf archive.tar folder/            # Create archive
tar -xvf archive.tar                    # Extract archive
tar -czvf archive.tar.gz folder/        # Create compressed archive
tar -xzvf archive.tar.gz               # Extract compressed archive
gzip file.txt                           # Compress single file
gunzip file.txt.gz                      # Decompress file
zip -r archive.zip folder/              # Zip folder
unzip archive.zip                       # Unzip
```

---

### 🧠 Memory Trick for tar flags

| Flag | Meaning |
|------|---------|
| `c` | Create archive |
| `x` | Extract archive |
| `z` | Use gzip compression |
| `v` | Verbose — show progress |
| `f` | Filename — always last |

---

### 🔥 DevOps Insight

> Before deploying a new version — always backup the current one:
> ```bash
> tar -czvf app_backup_$(date +%F).tar.gz /var/www/app/
> ```

---

## 🔧 Topic 14: lsof & rsync — Production Tools

---

### 🔍 lsof — List Open Files & Ports

```bash
lsof -i :8080               # Which process is using port 8080?
lsof -i :80                 # Which process is using port 80?
lsof -u username            # All files opened by a specific user
lsof -p PID                 # All files opened by a specific process
```

---

### 🔄 rsync — File Sync (Better than scp)

```bash
rsync -avz source/ user@server:/destination/        # Sync to remote server
rsync -avz --delete source/ destination/            # Sync and delete extra files
rsync -avz --dry-run source/ user@server:/dest/     # Preview without doing it
```

---

### 🔄 rsync vs scp

| | rsync | scp |
|-|-------|-----|
| Resumes interrupted transfer | ✅ Yes | ❌ No |
| Syncs only changed files | ✅ Yes | ❌ No |
| Better for large files | ✅ Yes | ❌ No |

---

### 🔥 DevOps Insight

> App says "port already in use" error?
> ```bash
> lsof -i :8080       # find which process is using port 8080
> kill -9 PID         # stop it
> ```

---

## 🛡️ Topic 15: ufw Firewall & tmux

---

### 🔒 ufw — Firewall Management

```bash
sudo ufw status                          # Check current firewall rules
sudo ufw enable                          # Enable firewall
sudo ufw disable                         # Disable firewall
sudo ufw allow 8080                      # Open port 8080
sudo ufw allow ssh                       # Allow SSH (port 22)
sudo ufw deny 23                         # Block port 23
sudo ufw allow from 192.168.1.0/24       # Allow from specific IP range
sudo ufw status verbose                  # Detailed firewall rules
```

👉 Key Point:
Always `allow ssh` before enabling ufw — otherwise you will lock yourself out of the server.

---

### 🖥️ tmux — Terminal Multiplexer

tmux lets you run long processes on a server, disconnect, and reconnect later — the process keeps running.

```bash
tmux new -s mysession       # Start new session named "mysession"
tmux attach -t mysession    # Reconnect to existing session
tmux ls                     # List all sessions
tmux kill-session -t name   # Kill a session
```

---

#### tmux Shortcuts (inside a session)

| Shortcut | Action |
|----------|--------|
| `Ctrl+B D` | Detach — session keeps running |
| `Ctrl+B C` | Create new window |
| `Ctrl+B N` | Next window |
| `Ctrl+B %` | Split screen vertically |
| `Ctrl+B "` | Split screen horizontally |

---

### 🔥 DevOps Insight

> Installing Jenkins or running a long deploy on EC2?
> Always use tmux so the process does not stop if your SSH drops:
> ```bash
> tmux new -s jenkins-install
> # run your installation
> # Ctrl+B D to detach safely
> tmux attach -t jenkins-install   # reconnect anytime
> ```

---

## 🔑 Topic 16: SSH Basics

SSH (Secure Shell) is how you connect to remote Linux servers securely.
Jenkins uses SSH to connect to deployment servers. DevOps runs on SSH.

```bash
ssh-keygen -t rsa -b 4096               # Generate SSH key pair

ssh username@server-ip                  # Connect to remote server
ssh -i key.pem ubuntu@ec2-public-ip     # Connect to AWS EC2

scp file.txt ubuntu@server-ip:/home/ubuntu/   # Copy file to remote server

chmod 600 ~/.ssh/id_rsa                 # Private key — must be 600
chmod 400 key.pem                       # AWS pem key — must be 400
```

---

### 🔥 DevOps Insight

> Jenkins connects to deployment servers via SSH.
> If your SSH key permission is wrong — the pipeline fails with:
> **"WARNING: UNPROTECTED PRIVATE KEY FILE!"**

---

## 🌐 Topic 17: Networking Commands

```bash
ip addr                     # Show system IP address
ping google.com             # Check network connectivity
curl google.com             # Fetch HTTP response from server
curl -I google.com          # Show only response headers
wget <url>                  # Download file from internet
nslookup google.com         # DNS lookup — find IP of a domain
dig google.com              # Detailed DNS info
netstat -tulnp              # Show open ports (older systems)
ss -tulnp                   # Show open ports (modern — faster)
traceroute google.com       # Show path packets take to reach server
```

---

### 🔄 curl vs wget

| | curl | wget |
|-|------|------|
| Download file | ✅ | ✅ |
| Test API / HTTP response | ✅ | ❌ |
| Resume interrupted download | ❌ | ✅ |

---

### 🔥 DevOps Insight

> After deploying your app, always verify it is actually running:
> ```bash
> ss -tulnp | grep 8080
> curl http://localhost:8080
> ```

---

## ⚙️ Topic 18: Systemd — Service Management

Systemd is the service manager in Linux.
It controls how services start, stop, and restart — including on server reboot.

```bash
systemctl start nginx                           # Start a service
systemctl stop nginx                            # Stop a service
systemctl restart nginx                         # Restart a service
systemctl status nginx                          # Check if service is running
systemctl enable nginx                          # Auto-start on system reboot
systemctl disable nginx                         # Disable auto-start
systemctl reload nginx                          # Reload config without full restart

journalctl -u nginx                             # Show all logs for a service
journalctl -u nginx -f                          # Live logs — watch in real time
journalctl -u nginx --since "1 hour ago"        # Logs from last 1 hour
```

---

### 🔥 DevOps Insight

> App not starting after server reboot?
> ```bash
> systemctl enable appname    # makes it start automatically on every reboot
> journalctl -u appname -f    # shows exactly why it failed to start
> ```

---

## 🔥 Production Cheat Sheet — Most Used Commands

| Situation | Command |
|-----------|---------|
| App is down — check logs | `journalctl -u appname -f` |
| Find errors in log file | `grep -i "error" app.log \| tail -50` |
| Check disk is full | `df -h` |
| Check RAM usage | `free -h` |
| Which process using high CPU | `top` or `htop` |
| Check if port is open | `ss -tulnp \| grep 8080` |
| SSH key permission | `chmod 600 key.pem` |
| Watch real-time logs | `tail -f /var/log/app.log` |
| Find a config file | `find / -name "nginx.conf"` |
| Replace text in file | `sed -i 's/old/new/g' file.txt` |
| Archive a folder | `tar -czvf backup.tar.gz folder/` |
| Port already in use | `lsof -i :8080` then `kill -9 PID` |
| Schedule a task | `crontab -e` |
| Keep process alive after SSH drop | `tmux new -s session` |
| Save output + errors to log | `./script.sh > out.log 2>&1` |



*Day 02 complete. Next → Day 03: Networking.*
