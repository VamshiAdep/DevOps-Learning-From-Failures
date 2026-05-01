# Day 01 - DevOps Fundamentals

---

## 📌 Topics Covered

* What is DevOps
* Why DevOps exists
* Dev vs Ops problem
* SDLC (Software Development Life Cycle)
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

SDLC (Software Development Life Cycle) can be understood using a simple example like a website such as escbash.com. Suppose users request new features like a dark mode and a script download option. First, in the planning stage, these requirements are collected and documented with the of Product Managers. Then developers start building the features by writing code and pushing it to version control (Github). In the build stage, the application is compiled and prepared for execution (building the code). After that, it is tested in a staging environment to ensure the features work correctly and securely(QA). Once everything is verified, a new version is released and deployed to production(Release), where users can access the new features(deployement). After deployment, the system is continuously monitored for performance and issues. In a DevOps approach, many of these steps—especially build, test, and deployment—are automated, which helps deliver features faster, reduce manual effort, and improve overall reliability.
