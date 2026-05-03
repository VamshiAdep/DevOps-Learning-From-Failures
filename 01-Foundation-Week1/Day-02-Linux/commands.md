## 📁 Topic 2: Linux File System Structure

Before learning Linux commands, we first focus on the **file system structure** — understanding where everything lives.

---

## 🔍 View Full Structure

Type this command to see the root directories:

```bash
ls /
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