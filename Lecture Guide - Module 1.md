# Lecture Guide | Module 1: Introduction, Fundamentals & Remote Access

**Objective:** To transition from a "Desktop/GUI" mindset to a "Server/CLI" mindset and establish secure entry points into the Ebright infrastructure.

**Session Duration:** 2 to 3 hours  
**Delivery Mode:** Instructor-led with live terminal demos  
**Tools:** Ubuntu Server (or Docker Ubuntu container), Dockerfile, Docker Compose, Visual Studio Code, VS Code integrated terminal, SSH client

## Learning Outcomes
By the end of this module, learners should be able to:
- Explain why production Linux servers are typically headless.
- Compare GUI and CLI workflows for operational efficiency.
- Describe VPS architecture and shared responsibility in cloud environments.
- Identify the role of the kernel, shell, and user commands in Linux execution.
- Distinguish Docker containers from virtual machines.
- Explain the role of a Dockerfile and Docker Compose in repeatable lab environments.
- Explain how SSH key-based authentication works.
- Demonstrate safe first-step server mindset behaviors.

## Pre-Class Checklist
- Ensure Docker Desktop or a Linux VM is installed.
- Ensure Visual Studio Code is installed.
- Confirm learners can open the VS Code integrated terminal (`Terminal > New Terminal`).
- Prepare an SSH client (`ssh` in PowerShell, Git Bash, or Windows Terminal).
- Confirm internet connectivity for package updates and remote access tests.
- Bring a text editor for note-taking and command logging.

## 1. Introduction to the Linux Ecosystem

This topic builds the foundation for why Linux dominates modern server operations. Before learners run commands, they need to understand the operating environment, the reason servers are usually headless, and why command-line fluency is a core professional skill for reliable infrastructure management.

### 1.1 The "Headless" Concept
- Unlike a PC, a Linux server typically has no monitor, keyboard, or mouse attached.
- It is optimized for **Uptime**: it does not need to "Sleep" or "Hibernate." It is designed to run for years without reboot.
- Most administration is performed remotely through secure protocols.

### 1.2 GUI vs. CLI: The Professional Shift

**GUI (Graphical User Interface):**
- Pros: Intuitive, visual, uses icons and windows.
- Cons: Heavy resource consumption (RAM/CPU), slower for remote management, hard to automate.

**CLI (Command Line Interface):**
- Pros: Extremely lightweight, works over poor internet connections, allows complex automation and scripting.
- Cons: Steeper learning curve, no visual safety net.

**Why CLI for Ebright?** We use the CLI because it manages the server with near-zero overhead, leaving maximum power for the student portal and databases.

### 1.3 The Power of the CLI
- **Low Latency:** Text travels over the internet significantly faster than high-resolution graphics.
- **Resource Efficiency:** Removing the GUI can save roughly 500 MB to 1 GB of RAM, potentially lowering cloud hosting costs.
- **Automation Ready:** CLI workflows can be scripted and repeated consistently.

### 1.4 The Ubuntu Advantage
- Ebright uses **Ubuntu Server LTS (Long Term Support)**.
- LTS means security patches are guaranteed for 5 years (Standard) or up to 10 years (Pro), supporting long-term stability.
- Strong package ecosystem and broad documentation make troubleshooting faster.

### 1.5 Instructor Demo (5-10 min)
Run and explain:

```bash
uname -a
uptime
free -h
```

Talking points:
- How long the server has been running.
- Why memory availability matters on production servers.

## 2. Understanding Cloud Computing & VPS

This topic shifts the learner from traditional hosting assumptions to cloud infrastructure thinking. It explains what control and responsibility increase when moving to a VPS model, and why architecture decisions such as scaling, backup strategy, and IP planning directly affect service reliability.

### 2.1 Transitioning from Managed Hosting
- **Managed Hosting (Old Way):** Provider manages server internals; you mainly manage website files.
- **Cloud Infrastructure / IaaS (New Way):** You are the "landlord" of the software environment. You control OS hardening, service stack, and performance tuning.

### 2.2 What is a VPS (Virtual Private Server)?
- A physical data center server is split into multiple virtual servers via a hypervisor.
- **Privacy and Isolation:** Your OS, files, and RAM are isolated from other tenants.
- **Root Access:** You can install and configure any required services (PostgreSQL, Nginx, custom APIs).

### 2.3 Key Cloud Concepts
- **Shared Responsibility Model:** Provider secures physical infrastructure; Ebright IT secures OS, applications, credentials, and data.
- **Elasticity:** Resize server CPU/RAM quickly as usage grows.
- **Static Public IP:** Stable endpoint that allows DNS records to consistently point to your server.
- **Backups and Snapshots:** Recovery strategy should be planned before making major changes.

### 2.4 Quick Scenario Discussion
Prompt learners:
- If traffic doubles during student admission week, which cloud concept helps first: elasticity, static IP, or snapshots?
- If data is leaked from weak server credentials, who is responsible under shared responsibility?

## 3. System Fundamentals: The Kernel & The Bash Shell

This topic introduces the execution pipeline of Linux systems: user intent, shell interpretation, and kernel-level resource control. Understanding this flow helps learners troubleshoot effectively and predict system behavior when they execute administrative commands.

### 3.1 The Kernel (The Engine Room)
- The core software layer that manages hardware resources: CPU scheduling, RAM allocation, device access, and disk I/O.
- **Check Command:** `uname -sr` (kernel name and release version).

### 3.2 The Shell (The Translator)
- A command-line interpreter between user intent and kernel actions.
- **Bash (Bourne Again SHell):** Popular shell with features like history (`Up Arrow`) and tab completion.
- **Check Command:** `echo $SHELL` (shows current shell executable path).

### 3.3 The Hierarchy of Operation
- **User:** Types `mkdir backup`
- **Shell:** Parses command and issues system calls.
- **Kernel:** Allocates file system resources.
- **Hardware:** Physical disk stores metadata and directory entry.

### 3.4 Hands-On Command Set (10-15 min)

```bash
pwd
ls -la
mkdir -p lab/module1
cd lab/module1
touch notes.txt
echo "Module 1 practice" > notes.txt
cat notes.txt
```

Expected outcomes:
- Learner confirms current directory awareness.
- Learner creates, navigates, and verifies file system changes.

## 4. The Sandbox: Docker Containers vs. Virtual Machines (VM)

This topic clarifies where to practice safely and how to choose the right isolation model. By comparing VMs and containers, learners understand performance trade-offs and why Docker is an ideal low-risk environment for hands-on experimentation.

### 4.1 Virtual Machines (Hardware Emulation)
- A VPS behaves like a high-performance VM with a full guest OS and virtualized hardware.
- Strong isolation but heavier resource footprint.

### 4.2 Docker Containers (OS-Level Virtualization)
- Containers share the host kernel and package only app code plus dependencies.
- Fast startup, low overhead, ideal for repeatable labs.

### 4.3 The "Practice Without Fear" Workflow
- In this session, Docker is a safe sandbox.
- If a destructive command is run in the container, reset by recreating the container.
- This protects both your laptop and Ebright production environments.

### 4.4 Demo Commands

```bash
docker compose -f asset/module1/docker-compose.yml up -d --build
docker compose -f asset/module1/docker-compose.yml ps
docker exec -it ebright-practice-ssh bash
cat /etc/os-release
exit
docker compose -f asset/module1/docker-compose.yml down
```

Talking points:
- `Dockerfile` defines the image build instructions.
- `asset/module1/docker-compose.yml` defines runtime configuration like ports and service names.
- Compose improves consistency for classroom labs compared to one-off commands.

## 5. Remote Access Theory: SSH & Security Protocols

This topic covers the security baseline for operating remote servers. Learners are introduced to encrypted access, key-based authentication, and fundamental hardening practices so remote administration remains secure, auditable, and resilient against common attack paths.

### 5.1 What is SSH (Secure Shell)?
- Encrypted protocol for secure remote command execution and administration.
- **Standard Port:** `22`.

### 5.2 The "Lock and Key" (Asymmetric Encryption)
- **Private Key (`id_rsa`):** Kept on your laptop and never shared.
- **Public Key (`id_rsa.pub`):** Stored on the server at `~/.ssh/authorized_keys`.

### 5.3 The SSH Handshake Process
- **Client:** Requests authentication.
- **Server:** Sends challenge data for proof of key ownership.
- **Client:** Proves identity using private key operations.
- **Server:** Grants access without transmitting your private key.

### 5.4 Basic SSH Hardening Principles
- Use key-based auth instead of password-only auth.
- Disable direct root login over SSH where possible.
- Restrict source IPs with firewall rules.
- Rotate compromised keys immediately.
- Keep `openssh-server` updated.

### 5.5 Practice Commands

```bash
ssh-keygen -t ed25519 -C "ebright-admin"
ls -la ~/.ssh
ssh user@SERVER_IP
```

Note:
- `ed25519` is a modern, efficient key type recommended for most use cases.

## 6. Transition to Practical: The "Server Mindset"

This topic focuses on operational discipline. It prepares learners to think carefully before running commands, use privileges responsibly, and adopt habits that reduce avoidable outages, security incidents, and configuration drift in real environments.

### 6.1 Core Server Mindset Principles
- **Case Sensitivity:** `ebright.txt` and `Ebright.txt` are different files.
- **Least Privilege:** Use `sudo` only when needed instead of remaining root all the time.
- **Silence Can Mean Success:** Many Linux commands output nothing on success.
- **Read Before Enter:** One wrong command can impact an entire environment.
- **Document Every Change:** Keep a simple ops log for traceability and rollback.

## Common Beginner Mistakes and Prevention
- Running destructive commands in the wrong path.
- Prevention: run `pwd` before `rm`, `mv`, `cp`, or script execution.
- Using root for all tasks.
- Prevention: work as standard user and elevate with `sudo` only when required.
- Copy-pasting commands blindly from the internet.
- Prevention: parse each flag and test in sandbox first.
- Ignoring updates.
- Prevention: schedule regular patch windows.

## Mini Lab: First 20 Minutes on a New Server
1. Check identity and host details:

```bash
whoami
hostnamectl
ip a
```

2. Check update status and uptime:

```bash
uptime
sudo apt update
sudo apt list --upgradable
```

3. Verify secure access basics:

```bash
ls -la ~/.ssh
sudo ss -tulpen | grep :22
```

Success criteria:
- Learner can identify account, OS, network IP, and SSH listener.
- Learner can explain at least two immediate hardening actions.

## Knowledge Check (Exit Ticket)
1. Why is CLI preferred over GUI on production servers?
2. In the shared responsibility model, who secures application credentials?
3. What is the difference between a VM and a container in one sentence?
4. What is the role of `authorized_keys`?
5. Why should `sudo` be used sparingly?

## Module 1 Summary
- Linux server administration prioritizes uptime, efficiency, and automation.
- VPS gives Ebright full control and full operational responsibility at OS level.
- Kernel executes, shell translates, and user intent drives system behavior.
- Docker provides a safe training sandbox.
- Dockerfile and Docker Compose make sandbox setup repeatable and team-friendly.
- SSH with key-based auth is foundational for secure remote operations.
- A strong server mindset is careful, minimal, and documented.

