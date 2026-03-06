# Ebright Linux Crash Course: Training Overview

This document defines the two core pillars of the 1-day Linux Server Maintenance course, designed to equip the Ebright IT and Administration team with practical, beginner-friendly skills for independent cloud server management.

## Quick Navigation
- Full timetable: [README](README.md)
- Module 1 Lecture: [Lecture Guide - Module 1](Lecture%20Guide%20-%20Module%201.md)
- Module 1 Practical: [Practical Guide - Module 1](Practical%20Guide%20-%20Module%201.md)
- Module 2 Lecture: [Lecture Guide - Module 2](Lecture%20Guide%20-%20Module%202.md)
- Module 2 Practical: [Practical Guide - Module 2](Practical%20Guide%20-%20Module%202.md)

## Pillar 1: System Identity, Access & Administration

**Focus:** Establishing Infrastructure Foundations and Organizational Hierarchy.

The Pillar 1 session is designed to break the reliance on graphical interfaces and establish a professional "Server Mindset" through the Command Line Interface (CLI). The team will explore the Linux ecosystem-from the Kernel to the Cloud-learning to manage Virtual Private Servers (VPS) and secure them via SSH. The goal is to ensure the team can confidently navigate the filesystem, monitor system vitals, and implement a robust departmental user/group structure that secures Ebright's internal data.

Delivery principle for Pillar 1:
- Explain first, demonstrate second, and verify learner output immediately after each command block.

### Included Modules

#### Module 1: Introduction, Fundamentals & Remote Access

- Linux Ecosystem: Transitioning to "headless" operation, the power of CLI over GUI, and the long-term stability of Ubuntu Server LTS.
- Cloud Computing and VPS: Moving from managed hosting to IaaS, understanding the shared responsibility model, and leveraging root access on isolated virtual servers.
- System Fundamentals: Exploring Linux kernel architecture, the Bash shell interpreter, and the hierarchy between user commands and hardware execution.
- The Sandbox: Comparing Docker containers and virtual machines (VM) for high-performance, non-destructive practice.
- Remote Access Theory: Securing connections via SSH using asymmetric encryption (keys) and understanding the authentication handshake process.
- The Server Mindset: Applying essential operational rules such as case sensitivity, proper `sudo` usage, and the "no feedback means success" principle.

#### Module 2: Mastering the Filesystem, Users & Permissions

- Filesystem Hierarchy (FHS): Understanding the Linux tree structure and the roles of core system and data directories.
- Help, Documentation, and Information: Using self-documentation tools and confirming system identity (`uname`, `hostname`, `whoami`).
- Filesystem Operations: Practicing navigation, file and directory creation, and safe manipulation (copy, move, delete).
- Content Exploration and Text Editing: Inspecting file content, using terminal editors (`nano` and `vi`), and handling archive and compression workflows.
- Security Logic (Users and Permissions): Provisioning accounts, managing departmental groups, applying octal permission logic, and granting `sudo` privileges.
- Terminal Productivity and Task Scheduling: Using command history, efficiency shortcuts, piping and redirection, and automation with `crontab`.
- Storage, Time, and Software Management: Monitoring disk health, managing time/timezones, and installing software using `apt`.
- Process and Network Operations: Monitoring processes in real time, troubleshooting connectivity, handling remote transfers, and using safe power operations (`reboot` and `shutdown`).

Practical output for Module 2:
- Learners build `/opt/ebright_data` with departmental folders (`finance`, `teachers`, `hq_admin`).
- Learners apply group-based access control using `chown` and `chmod 770`.
- Learners practice automation basics with `crontab` and heartbeat logging.

## Pillar 2: Deployment, Security & Continuity

**Focus:** Application Delivery, Monitoring, and System Stability.

The 2nd session transitions from system-level administration to application-level management. We focus on procedures required to deploy website updates, monitor system health, and implement security hardening to ensure continuity of Ebright's parent portal and student databases.

### Included Modules

#### Module 3: Deployment, Security & Continuity

- System Snapshots: Performing server-wide snapshots as a primary restore point before configuration changes.
- System Diagnostics: Monitoring system vitals with `df -h` (disk), `free -m` (memory), and `top` (processes).
- Log Management: Troubleshooting application errors through real-time log analysis with `tail -f`.
- Software Stack: Managing application packages and installing Nginx and PostgreSQL via `apt`.
- Version Control: Using Git for deployment, including cloning repositories and updating production code with `git pull`.
- Network Identity: Managing DNS records (A, CNAME, MX) to support correct domain routing.
- Perimeter Hardening: Configuring UFW firewall rules to limit network access to essential web traffic.
- Data Protection: Establishing automated backup protocols and understanding snapshots versus off-site storage.
- Operational Readiness: Applying emergency recovery procedures, safe power operations (`reboot`, `shutdown`), and internal escalation workflows.

## Learning Outcome

By the end of the day, the Ebright IT team will have moved from being passive observers of a managed host to active administrators of their own cloud infrastructure. They will possess the technical "Survival Skills" needed to keep Ebright's digital services stable, secure, and accessible.

Beginner readiness indicators:
- Can identify whether they are in Windows PowerShell or Linux shell before running commands.
- Can verify command results using `pwd`, `ls`, `id`, and `ls -ld`.
- Can explain why group permissions are used for departmental data protection.
