## 📖 You read about [Operating System](OperatingSystem.md), now we discuss Linux Operating System

---

## 🐧 What is Linux?

Linux is an open-source operating system based on the Unix architecture that manages system hardware and provides a platform for running applications.

In simple terms, Linux is a type of Operating System, just like Windows or macOS, but it is:

- 🆓 **Open Source** → anyone can view, modify, and use the code  
- ⚙️ **Customizable** → can be modified based on needs  
- 🔒 **Secure** → strong permission and user control system  
- ⚡ **Lightweight** → runs efficiently even on low resources  
- 🌐 **Server Dominance** → most servers run on Linux  

---

## 🧱 What is the Architecture of Linux?

Linux follows a layered architecture where each component interacts with the layer below it to manage system resources and execute tasks efficiently. Linux architecture ensures that user commands are processed through different layers, allowing applications to interact with hardware in a secure and controlled way.

```text
User → Applications → Shell → Kernel → Hardware
```

---

## ⚙️ Components of Linux Architecture

### 👤 User
The user interacts with the system by running commands or using applications.

---

### 📦 Applications
These are programs like Python, Jenkins, Nginx, etc., which require system resources (CPU, memory, disk) to run.

---

### 💻 Shell
The shell acts as an interface between the user and the kernel.

- Takes user commands (like `ls`, `cd`)  
- Interprets them  
- Sends them to the kernel for execution  

---

### 🧠 Kernel (Core of Linux)
The kernel is the most important component of Linux. It sits between applications and hardware and manages all system resources.

The kernel is responsible for:

- Memory management → Allocates and manages system memory efficiently  
- Process management → Schedules processes and controls execution  
- Resource allocation → Distributes CPU, memory, and I/O resources  
- Device management → Controls hardware using device drivers  
- Application interaction → Acts as a bridge between applications and hardware  
- Security → Enforces access control and system-level security  

---

### ⚙️ Hardware
This is the physical layer of the system, including:

- CPU  
- RAM  
- Disk  
- I/O devices  

---

## 🔄 How Linux Executes a Command (`ls -lrta`)

When you enter a command like:

```bash
ls -lrta
```

Linux follows a step-by-step process to execute it. This flow involves the shell, kernel, and hardware working together.

---

## ⚙️ Step-by-Step Execution Flow

### 1. 👤 User Input
You type the command `ls -lrta` in the terminal.

---

### 2. 💻 Shell Receives the Command
The shell (like bash) reads and interprets the command.

- `ls` → command (list directory contents)  
- `-l` → long listing format  
- `-r` → reverse order  
- `-t` → sort by time  
- `-a` → include hidden files  

---

### 3. 🔍 Shell Locates the Command

```bash
/bin/ls
/usr/bin/ls
```

---

### 4. 📞 System Call to Kernel
Once found, the shell makes a system call to the kernel to execute the command.

👉 Control moves from **user space → kernel space**

---

### 5. 🧠 Kernel Executes the Request

The kernel performs multiple operations:

- Creates a new process for `ls`  
- Allocates CPU and memory  
- Reads directory data from disk  
- Applies sorting (`-t`, `-r`)  
- Includes hidden files (`-a`)  
- Formats output (`-l`)  

---

### 6. ⚙️ Hardware Interaction

- Disk → to read file information  
- CPU → to process the command  
- RAM → to temporarily store data  

---

### 7. 📤 Output Returned

```text
Kernel → Shell → Terminal
```

👉 You see the list of files displayed

---

## 🔄 Complete Flow

```text
User → Shell → System Call → Kernel → Hardware → Kernel → Shell → Output
```

## 🌍 Why Engineers or Organizations Use Linux?

- 🌐 **90% of servers run on Linux**  
  AWS EC2, GCP VMs, Kubernetes nodes — all run on Linux underneath  

- 🐳 **DevOps tools depend on Linux**  
  Docker uses the Linux kernel  
  Kubernetes runs on Linux  
  Jenkins is mostly used on Linux  

- ⚙️ **Automation Friendly**  
  Bash scripting makes automation powerful and simple  

- 🔒 **Secure & Stable**  
  Strong permissions and reliable for production systems  

---

### 🎯 Simple Understanding

> "As a DevOps engineer, I live in the terminal. Linux is my home."
       90% of servers in the world run Linux — AWS EC2, GCP VM, Kubernetes nodes — all Linux underneath
       Docker runs on Linux kernel. Kubernetes runs on Linux. Jenkins runs on Linux. Your DevOps career lives on Linux.
       Write in your notes: "As a DevOps engineer, I live in the terminal. Linux is my home." 