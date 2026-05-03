## 📁 Topic 2: Linux File System Structure

Before learning Linux commands, we first focus on the **file system structure** — understanding where everything lives.

---

## 🔍 View Full Structure

Type this command to see the root directories:

ls l

```bash
Linux File System Structure

When you type `ls /` you see the root of the entire system.

| Directory | Purpose |
|-----------|---------|
| `/etc` | All config files live here (nginx, ssh, hosts) |
| `/var` | Logs and runtime data — always check `/var/log` when something breaks |
| `/home` | User home directories — SSH into EC2 and you land here |
| `/bin` `/usr/bin` | All commands live here (ls, curl, git, etc.) |
| `/tmp` | Temporary files — cleared on every reboot |
| `/proc` | Real-time system info — CPU, memory, running processes |
| `/root` | Home directory of the root (admin) user |
| `/opt` | Optional software — Jenkins, custom apps installed here |
```

---

## 📂 Important Directories

### 📁 /etc
- All configuration files live here  
- Examples: nginx config, ssh config, hosts file  

---

### 📁 /var
- Stores variable data  

#### 📁 /var/log
- All logs are stored here  
- 👉 Always check logs here when something breaks  

---

### 📁 /home
- Contains user files  
- 👉 When you SSH into EC2, you land here  

---

### 📁 /bin and /usr/bin
- Contains system commands (executables)  

👉 Examples:
```bash
ls, curl, git
```

---

### 📁 /tmp
- Stores temporary files  
- Cleared on system reboot  

---

### 📁 /proc
- Contains real-time system information  

👉 Includes:
- CPU details  
- Memory usage  
- Running processes  

---

## 🎯 Simple Understanding

- `/etc` → configs  
- `/var/log` → logs  
- `/home` → user files  
- `/bin` → commands  
- `/tmp` → temp files  
- `/proc` → system info  

---

## 🔥 DevOps Insight

> If something breaks:
> - Check logs → `/var/log`
> - Check configs → `/etc`

## 🧠 Basic System Components (CPU, RAM, Disk) 
Before working with Linux systems, it is important to understand the core system resources: CPU, RAM, and Disk. These are the fundamental components that the operating system manages to run applications efficiently.

---

## ⚙️ CPU (Central Processing Unit)

The CPU is the brain of the computer. It is responsible for executing instructions and processing tasks.

In Linux, whenever we run a command or application, the CPU performs the actual computation.

👉 Example:
When we run a command like `ls`, the CPU processes the instruction and generates the output.

👉 Key Point:
CPU is responsible for **execution and processing**.

---

## 🧩 RAM (Random Access Memory)

RAM is temporary memory used to store data that is actively being used by the system.

It allows fast access to running applications and processes, which improves performance.

👉 Example:
When you open a file or run an application, it is loaded into RAM for faster access.

👉 Key Point:
RAM is responsible for **temporary storage of active data**, and its data is lost when the system is turned off.

---

## 💾 Disk (Storage)

Disk is permanent storage where all system files, applications, and data are stored.

Unlike RAM, the data in disk remains even after the system is restarted.

👉 Example:
User files are stored in `/home`, logs are stored in `/var/log`.

👉 Key Point:
Disk is responsible for **permanent storage**.

---

## 🔄 Difference Between CPU, RAM, and Disk

| Component | Purpose | Speed | Persistence |
|----------|--------|------|-------------|
| CPU | Executes instructions | Very Fast | No storage |
| RAM | Temporary working memory | Fast | Data lost after shutdown |
| Disk | Permanent storage | Slower | Data stored permanently |

---