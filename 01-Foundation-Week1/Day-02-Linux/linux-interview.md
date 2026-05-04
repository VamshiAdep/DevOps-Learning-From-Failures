Q1. Explain Linux file system structure — where do logs, configs, and binaries live?
     Explanation : Linux file systerm start root director * / * => everything lives under it.
                   like Windows (C:\ D:) => In Linux its start with / 
                   /etc :- is where all config files live — nginx config, SSH config, hosts file.
                   /var/log :- is where all logs are stored — first place I check when something breaks.
                   /bin and /usr/bin :- contain all system commands like ls, curl, git.
                   /home :- contains user directories — when I SSH into an EC2 instance I land here.
                   /tmp :- stores temporary files cleared on reboot.
                   /proc :- contains real-time system info like CPU and memory usage.
                   /opt :- is where we install optional software like Jenkins.
                In production — if something breaks, I check /var/log for logs and /etc for configs.

Q2. What is the difference between chmod 755, 644, 600, 400?
     Explanation : chmod controls who can read, write, or execute a file.
                   Three groups — owner, group, others. Each permission has a number — read is 4, write is 2, execute is 1.
                   755 means owner can do everything, others can read and execute — I use this for shell scripts and web server files.
                   644 means owner can read and write, others can only read — I use this for normal config files.
                   600 means only the owner can read and write, nobody else — I use this for SSH private keys. If SSH key is 777, AWS refuses the connection with 'permissions too open' error.
                   400 means read only for owner — I use this for AWS .pem key files downloaded from console. 
 
Q3. You SSH into EC2 and get "Permission denied (publickey)" — what do you check?
     Explanation : When I get 'Permission denied publickey' on EC2, I check 5 things in order.
                    First — key file permission. It must be chmod 600. If it's 777 or 644, SSH rejects it. I run chmod 600 key.pem to fix it.
                    Second — correct username. Ubuntu AMI uses 'ubuntu', Amazon Linux uses 'ec2-user', CentOS uses 'centos'. Wrong username gives this same error.
                    Third — correct IP address. I check if I'm using the public IP, not the private IP.
                    Fourth — security group on AWS console. Inbound rule must allow port 22 from my IP address.
                    Fifth — correct key pair. The .pem file must be the same key that was selected when the instance was launched. A different key won't work even if everything else is correct.

Q4. What is the difference between hard link and soft link?
     Explnation : Both are used to reference a files in server using commands
                   ln file.txt hardlink.txt ### hard link ###
                   ln -s file.txt softlink.txt     # Soft link
                   A hard link points to the same inode — the actual data on disk. If I delete the original file, the hard link still works because the data still exists. Hard links cannot cross different file systems.
                   A soft link is like a shortcut — it points to the file path. If the original file is deleted, the soft link breaks because the path no longer exists. But soft links can work across different file systems.
                   In DevOps, I use soft links to manage versions — for example, linking /usr/local/bin/nginx to the current nginx version. When I upgrade, I just update the soft link instead of changing everything."
                
  ### *Area 2 — Process & System Troubleshooting* ###

  Q5. Your app is consuming 100% CPU — how do you find and fix it?
       Explanation :- When an app is consuming 100% CPU, I follow these steps.
                      First I run 'top' and press P to sort by CPU — this shows me which PID is consuming the most. Or I run 'ps aux --sort=-%cpu | head -10' to see the top 10 processes.
                      Once I find the PID, I check logs with 'journalctl -u appname -f' to understand why — is it in an infinite loop? Handling too many requests? Memory leak?
                      Then I try 'systemctl restart appname' for a graceful restart. If the process is stuck and not responding, I use 'kill -9 PID' to force kill it.
                      After fixing, I check if it comes back — if it does, the issue is in the code or config, not just a one-time spike. I document it for RCA.

Q6. Server is running slow — what is your step by step approach?
     Explnation :- When a server is slow, I check 5 layers in order.
First disk — df -h to see if any partition is full. A full disk causes everything to slow down or crash.
Second memory — free -h to check RAM usage. If swap is being heavily used, it means RAM is exhausted and the system is using disk as slow memory.
Third CPU — top to see load average and which process is consuming most CPU. I compare load average to nproc — if load is higher than CPU count, the server is overloaded.
Fourth network — ss -tulnp to check open connections. Too many connections can slow the server.
Fifth logs — journalctl to look for errors in the last hour that might explain the slowdown.
I check in this order because disk full is the most common production issue and quickest to confirm.

Q7. What is the difference between kill, kill -9, and pkill?
     Explanation :- Kill, kill -9, and pkill all stop processes but differently.
'kill PID' sends SIGTERM — a polite request to stop. The process receives it, finishes current work, closes connections, saves state, then exits cleanly. This is always my first choice.
'kill -9 PID' sends SIGKILL — the kernel immediately destroys the process. The process has no chance to clean up. I use this only when the process is frozen and not responding to normal kill.
'pkill nginx' works by process name instead of PID. It sends SIGTERM to all matching processes. Useful when I don't know the PID.
In production I always try 'systemctl restart' first, then kill, then kill -9 as last resort.

Q8. How do you check which process is using port 8080?
     Explanation : To check which process is using port 8080, I use 'ss -tulnp | grep 8080'
                    Alternatively I can use 'lsof -i :8080' which also shows the process and PID.
                
Q9. What is a zombie process? How do you handle it?
     Explanation :- A zombie process is a process that has finished execution but its parent process hasn't read its exit status yet. It stays in the process table as a ghost.
I can find zombies with 'ps aux | grep Z' — they show Z in the STAT column.
Zombies don't consume CPU or memory — they only occupy a slot in the process table. A few zombies are not a problem. But thousands of zombies can exhaust process slots and prevent new processes from starting.
I cannot kill a zombie directly. To clean it, I kill the parent process — then the kernel automatically cleans up all zombie children. I find the parent with 'ps -o ppid= PID' and kill that.

### *Area 3 — Log Analysis* ###
Q10. App crashed at 3 AM — how do you find what happened?
      Explanation :- When an app crashes at 3 AM, I do a time-based investigation.
First I check service logs for that exact time window — 'journalctl -u appname --since 03:00 --until 03:30'. This shows exactly what was happening in those 30 minutes.
Then I grep the application log file for errors, exceptions, or fatal messages around 3 AM.
I also check dmesg for OOM — Out of Memory — messages. If the kernel ran out of RAM, it kills the most memory-hungry process, which is often our app.
I check df -h to see if disk was full — a full disk causes apps to crash silently.
Finally I check if any deployment happened around that time — most 3 AM crashes are caused by a late-night deployment that went wrong.
After finding the cause, I write an RCA — what happened, why, how it was fixed, and what monitoring we add to catch it earlier next time.

Q11. How do you find all ERROR lines from a log file and save to another file?
      Explanation :- To find all ERROR lines and save them, I use:
'grep -i error /var/log/app.log > errors.log'
The -i flag makes it case insensitive — it finds ERROR, Error, and error.
The > redirects output to a new file. This overwrites errors.log each time.

Q12. What is the difference between > and >>? 
      Explanation : Greater than sign overwrites the file completely — old content is deleted and replaced with new output.
Double greater than sign appends — it adds new content at the end without touching the existing content.
In DevOps this matters a lot for cron jobs. If I use single > in a cron job log, every run deletes the previous log — I lose history. I always use >> in cron jobs to keep a full history of all runs.
Single > is useful when I want to create a fresh file or overwrite a config file completely.
A practical rule I follow: for logs always use >>, for config files > is fine.

Q13. How do you save both output and errors of a script to one log file?
      Explanation :- In Linux, file descriptor 1 is standard output — normal output. File descriptor 2 is standard error — error messages.
When I run './script.sh > output.log' it only saves normal output. Errors still print to terminal and are not saved.
To save both, I add '2>&1' at the end — this means 'send file descriptor 2 to the same place as file descriptor 1'.
So './script.sh > output.log 2>&1' saves everything — output and errors — to output.log.
Order matters — '2>&1' must come after '>'. If I write '2>&1 > output.log' in wrong order, it doesn't work as expected.

Q14. How do you monitor logs in real time during a deployment?
      Explanation :- During a deployment, I keep a terminal open watching live logs so I can catch errors immediately.
For systemd services I use 'journalctl -u appname -f' — the -f flag means follow, it shows new log lines as they appear in real time.
For log files I use 'tail -f /var/log/app.log' — same concept, follows the file live.
If the log is noisy I pipe it through grep — 'tail -f app.log | grep -i error' — so I only see error lines in real time.
For Docker I use 'docker logs -f container_name' and for Kubernetes 'kubectl logs -f pod-name'

### *Area 4 — Systemd & Services* ###

Q15. How do you make a service start automatically after server reboot?
      Explnation : systemctl start' starts the service immediately but does not survive a reboot. If the server restarts, the service stays down.
      'systemctl enable' creates a symlink so the service starts automatically on every boot. But it doesn't start the service right now.
In production I always use 'systemctl enable --now servicename' — this starts it immediately AND sets it to auto-start on reboot. One command, both done.
I verify with 'systemctl is-enabled servicename' — output should say 'enabled'.
I learned this the hard way — a team had Jenkins installed but forgot to enable it. After a server reboot, Jenkins was down and CI/CD pipelines failed. Always enable, not just start.

Q16. Service is failing after restart — how do you debug it?
      Explanation : When a service fails after restart, I follow a 5-step debug process.
First — 'systemctl status appname'. This shows the last error message and exit code. Often the answer is right here.
Second — 'journalctl -u appname --since 10 minutes ago'. Full logs from the last restart attempt.
Third — check config syntax. For nginx, 'nginx -t' validates config. A wrong config after editing is a very common cause.
Fourth — check port conflict. 'ss -tulnp | grep port' — maybe another process took the port during restart.
Fifth — check file permissions. The service user might not have permission to read config or write logs.
If none of these show the issue, I run the startup command directly in terminal — this gives the full unfiltered error output that systemd sometimes hides.

Q17. What is the difference between systemctl restart and systemctl reload?
      Explnation : Restart kills the process completely and starts it fresh. There is a brief downtime — a few seconds when the service is not available. I use restart when I install a new version of the software or make major changes.
Reload sends a signal to the running process to re-read its configuration without killing it. Zero downtime — existing connections are handled, new config is applied. I use reload when I only edit a config file like nginx.conf.
For nginx, the best practice is — always run 'nginx -t' to test config first, then 'systemctl reload nginx' to apply without downtime.
Important — not all services support reload. If a service doesn't support it, reload behaves like restart. I check 'systemctl status servicename' to see what signals it supports.

### *Area 5 — Cron, Scripting, Automation* ###
Q18. How do you schedule a backup script every day at 2 AM?
  Explnation : I use cron to schedule tasks. To run a backup at 2 AM every day, I run 'crontab -e' to edit my cron jobs and add:
'0 2 * * * /home/ubuntu/backup.sh >> /var/log/backup.log 2>&1'
The cron syntax has 5 time fields — minute, hour, day of month, month, day of week — then the command.
0 2 means minute 0 of hour 2 — so exactly 2:00 AM.
Three stars mean every day, every month, every day of week.
I append >> to keep a history of all backup logs. And 2>&1 to capture both output and errors in the same log file.
I use 'crontab -l' to verify the cron job was saved correctly.

Q19. Write a shell script to check if disk usage is above 80% and alert?
      code : #!/bin/bash

# Disk usage alert script
THRESHOLD=80
DISK=$(df -h / | awk 'NR==2 {print $5}' | tr -d '%')

if [ "$DISK" -gt "$THRESHOLD" ]; then
    echo "⚠️  ALERT: Disk usage is ${DISK}% on $(hostname) at $(date)"
    # Send to log
    echo "ALERT: Disk ${DISK}% at $(date)" >> /var/log/disk-alert.log
    # Optional — send email
    # echo "Disk ${DISK}% full" | mail -s "Disk Alert" admin@company.com
else
    echo "✅ Disk usage is ${DISK}% — OK"
fi

Q20. What does 2>&1 mean in a shell script?
      Explnation : In Linux, every process has three standard streams. File descriptor 1 is stdout — normal output. File descriptor 2 is stderr — error messages.
By default when I redirect with '>' only stdout goes to the file. Error messages still print to the terminal.
'2>&1' means 'redirect stderr to the same destination as stdout'. So if stdout is going to a file, stderr goes there too.
Order matters — '> output.log 2>&1' must be in this sequence. If I write '2>&1 > output.log' in the wrong order, stderr goes to terminal and only stdout goes to file.

*Area 6 — SRE Specific*
Q21. What is load average? What does "load average: 2.5, 1.8, 1.2" mean?
     explnation Load average shows the average number of processes waiting for CPU over the last 1 minute, 5 minutes, and 15 minutes.
'load average: 2.5, 1.8, 1.2' means 2.5 processes waiting in last minute, 1.8 in last 5 minutes, 1.2 in last 15 minutes.
The decreasing pattern — 2.5 to 1.8 to 1.2 — means the spike happened recently and the system is recovering. If it was increasing, the problem is getting worse.
To judge if load is high, I compare to nproc — the number of CPU cores. Load equal to nproc means 100% utilized. Load double nproc means processes are queuing. For a 4-core server, load of 2.5 is fine. Load of 8 means processes are waiting.
High load average is a signal — I then use top to find which process is causing it. 

Q22. Disk is full on production server — app is crashing. What do you do RIGHT NOW?
      Explanation : Disk full in production is a P1 incident. I act fast but carefully — never run rm -rf blindly.
First I confirm with df -h and identify which partition is full.
Then I find what is using space — 'du -sh /* | sort -rh | head -10' shows the top 10 largest directories. Usually it is /var/log or Docker images.
For logs, I use 'find /var/log -name *.log -mtime +7 -delete' to remove logs older than 7 days. For active log files I use 'truncate -s 0' — never delete an open file, just empty it.
If Docker is the issue, 'docker system prune -f' removes stopped containers, unused images, and dangling volumes.
After freeing space I run df -h to confirm, restart the app, and monitor.
After resolution I add a disk monitoring alert — Prometheus alert or cron job — so this never surprises us again. And I write an RCA.

Q23. What is the difference between RAM and swap? When does swap usage mean trouble?
      Explanation : RAM is physical memory — very fast, nanosecond access. Swap is disk space that Linux uses as overflow when RAM is full — very slow, millisecond access.
When RAM gets full, the kernel moves least recently used data from RAM to swap to make space. This is called swapping.
Some swap usage is normal — the kernel pre-emptively moves idle data to swap even when RAM is not completely full, as an optimization.
But high swap usage is a warning sign. At 50% swap, the app is noticeably slower because it is reading from disk instead of RAM. At 90% swap, the system can become almost unresponsive.
I check with 'free -h'. If I see high swap — the fix is either to increase RAM, optimize the app's memory usage, or find and kill a memory leak.
In Kubernetes and Docker, we set memory limits precisely to prevent containers from consuming all RAM and triggering swap

Q24. How do you find which files are taking most disk space?
      Explanation :- To find what is eating disk space, I use 'du -sh /* 2>/dev/null | sort -rh | head -10'.
du stands for disk usage. -s gives a summary per directory. -h makes it human readable in MB and GB. sort -rh sorts by size with largest first. head -10 shows only top 10.
2>/dev/null suppresses permission denied errors so the output is clean.
This shows me the top 10 largest directories from root. If /var is large, I drill down — 'du -sh /var/* | sort -rh | head -10'. Then maybe 'du -sh /var/log/* | sort -rh | head -10'.
To find individual large files — 'find / -type f -size +100M 2>/dev/null' — this finds all files larger than 100 MB.
In production the usual suspects are /var/log (old logs), /var/lib/docker (Docker images and volumes), and /tmp (uncleaned temp files).













































































































### 1.*What happens when you run a command in Linux?*
When a user runs a command like top in Linux, the process starts in user space where the command is typed into the terminal. The shell (such as bash) first interprets the command and searches for the corresponding executable file using the $PATH environment variable, typically locating it in directories like /usr/bin or /bin. Once the binary is found, the shell creates a new process by making system calls like fork() to duplicate itself and exec() to replace the process with the top program. At this point, control moves from user space to kernel space, where the kernel assigns a process ID (PID), allocates memory, schedules CPU time, and manages system resources required for execution. The kernel then interacts with the hardware, such as CPU and memory, to gather real-time system information like CPU usage, running processes, and memory consumption. Finally, this processed data is sent back to user space and displayed on the terminal as the interactive output of the top command.

### *2.It's Monday morning. You get a Slack alert at 9 AM — the payment service is down in production. Users cannot complete transactions. Your manager is already asking for an update.
You have SSH access to the server. Walk me through exactly what you do — step by step — from the moment you log in until the issue is resolved.* ###

First I update my manager — 'payment service is down, I'm investigating, will update in 10 minutes.' Then I post in the team channel so nobody else jumps on the same issue.
On the server, I start with:
1. systemctl status payment-service — is it running or crashed?
2. journalctl -u payment-service -f — what do the live logs say?
3. df -h — is disk full? free -h — is RAM exhausted? top — CPU spike?
4. ss -tulnp | grep 8080 — is the app even listening on its port?
While checking, I also ask the team — was any deployment done in the last 1 hour? If yes, I immediately rollback using kubectl rollout undo or trigger the previous stable build in Jenkins.
Once service is restored, I update the manager and team. Then I write a quick RCA — what happened, why, how we fixed it, and how we prevent it next time.