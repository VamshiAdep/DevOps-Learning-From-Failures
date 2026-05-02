# Day 01 - DevOps Fundamentals

---

## 📌 Topics Covered

* What is DevOps
* Why DevOps exists
* SDLC (Software Development Life Cycle)
* Dev vs Ops problem
* Waterfall vs Agile
* What DevOps solves
* Role of a DevOps Engineer

---

## #1. What is DevOps ?

DevOps is a culture and set of practices that improves an organization’s ability to deliver applications quickly, reliably, and efficiently. It is not just about CI/CD or deployment. DevOps focuses on the entire lifecycle of software, ensuring that speed does not compromise quality.

**Eg : escbash.com =>** escbash.com v1.0 is launched, After launching escbash.com v1.0, new feature requirements will emerge. Without DevOps, delivering these updates can take months due to manual processes and coordination gaps. With DevOps, the same updates can be delivered in days through automation, continuous integration, continuous delivery, and real-time monitoring.

---

## #2. Why Devops?

### ❌ Before DevOps

Earlier, teams worked separately:

* 👨‍💻 Developers → wrote code
* 🖥️ SysAdmins → managed servers
* 📦 Build/Release Engineers → deployed code (slow)
* 🧪 QA → tested manually

👉 No one worked together as one team

---

### 😵 Problems

* “It works on my machine” issues
* Slow releases (days or weeks)
* Blame game between teams
* Manual work everywhere
* No shared responsibility

---

DevOps exists to remove silos between teams by bringing developers, operations, and others together, using automation to deliver software faster, with better quality and reliability.

---

## #3. What is SDLC?

SDLC (Software Development Life Cycle) is the step-by-step process used to build, test, deploy, and maintain software.
(SDLC is the process that teams follow to take an idea and turn it into a working software application.)

In DevOps, SDLC is automated and continuous to deliver software faster and more reliably.

---

### #. Stages of SDLC

**Plan → Develop → Build → Test → Release → Deploy**

---

### Eg: escbash.com (Bash Scripting Website)

#### Scenario

Users request new features:

* Dark mode
* Script download button

---

SDLC (Software Development Life Cycle) can be understood using a simple example like a website such as escbash.com. Suppose users request new features like a dark mode and a script download option. First, in the planning stage, these requirements are collected and documented with the of Product Managers. Then developers start building the features by writing code and pushing it to version control (Github). In the build stage, the application is compiled and prepared for execution (building the code). After that, it is tested in a staging environment to ensure the features work correctly and securely (QA). Once everything is verified, a new version is released and deployed to production (Release), where users can access the new features (deployement). After deployment, the system is continuously monitored for performance and issues. In a DevOps approach, many of these steps—especially build, test, and deployment—are automated, which helps deliver features faster, reduce manual effort, and improve overall reliability.

---

## #4. Dev Vs Ops Problem?

The Dev vs Ops problem is the conflict between developers who want to release features quickly and operations teams who want system stability. This leads to delays, failures, and poor collaboration. DevOps solves this by bringing both teams together, encouraging shared responsibility, and using automation for faster and more reliable delivery.

---

## #5. Waterfall vs Agile ?

### 🌊 What is Waterfall Model?

Waterfall is a traditional software development approach where work is done step-by-step in a fixed sequence.

**🔄 Flow:**
Requirements → Design → Development → Testing → Deployment

Each phase must be completed before moving to the next.

---

### ❌ Problems with Waterfall

* Changes are hard once development starts
* Testing happens at the end → bugs found late
* Slow delivery (months or years)
* No continuous feedback from users

---

### 🧠 Example (escbash.com)

* Plan all features (dark mode, downloads, search) at once
* Build everything together
* Test at the end
* Release after months

👉 If something is wrong → huge delay

---

### ⚡ What is Agile Model?

Agile is a modern approach where software is built in small parts (iterations) with continuous feedback.

**🔄 Flow:**
Plan → Develop → Test → Release → Repeat (in small cycles)

👉 Work is divided into small cycles called sprints (1–2 weeks)

---

### ✅ Benefits of Agile

* Faster delivery (small features released quickly)
* Continuous testing → fewer bugs
* Easy to handle changes
* Regular feedback from users

---

### 🧠 Example (escbash.com)

Instead of building everything:

* Week 1 → Dark mode
* Week 2 → Download feature
* Week 3 → Search feature

👉 Each feature is:

* built
* tested
* deployed

👉 Users start using features early

---

### ⚡ Simple Understanding

Waterfall = One big release
Agile = Many small releases

Waterfall is a sequential model where development happens step by step and testing comes at the end, making it slow and less flexible. Agile is an iterative approach where development, testing, and deployment happen continuously in small cycles, allowing faster delivery and easier changes. DevOps complements Agile by automating these processes for faster and reliable releases.

---

## #6. What DevOps Solves?

DevOps solves the problems of slow delivery, manual processes, and lack of collaboration by introducing automation, continuous integration, and shared responsibility, resulting in faster and more reliable software delivery.

### 🔥 DevOps solves these problems:

1. 🤝 No more team gap
   DevOps brings Developers + Ops + QA together

2. ⚡ Slow releases → Fast delivery
   From weeks/months → to hours/days

3. 🤖 Manual work → Automation
   Deployments, testing, builds become automatic

4. 🐞 Late bugs → Early detection
   Continuous testing + monitoring

5. 😵 Blame game → Shared responsibility
   Everyone owns the system

6. 🔄 Unstable systems → Reliable systems
   Monitoring + quick fixes

---

## #7. Role of a DevOps Engineer (Day-to-Day Activities)

As a DevOps intern or fresher, my day-to-day activities mainly involve supporting the development and operations process by ensuring smooth deployments and system reliability. I work with CI/CD pipelines by monitoring builds, checking logs, and fixing basic issues when pipelines fail. I assist in deploying applications to development or staging environments and verify whether the application is running correctly after deployment. When issues occur, I perform basic troubleshooting by checking logs and identifying problems like services not running, port conflicts, or permission errors. I also monitor system performance such as CPU, memory, and disk usage to ensure everything is stable. Along with this, I continuously learn and implement automation by writing simple scripts to handle repetitive tasks like checking disk space or restarting services. I work with Linux servers using SSH, manage files, and understand user permissions. Additionally, I support developers by helping with environment setup and debugging deployment-related issues. As a fresher, my focus is on learning, contributing, and gradually improving my skills; at the same time, I aim to take ownership early in my career by learning in public, building confidence, collaborating effectively with teams, and adapting quickly to new challenges.
