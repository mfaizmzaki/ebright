# Lecture Guide | Module 2: Mastering the Filesystem, Users & Permissions

**Objective:** To master server organization, file manipulation, and data security. By the end of this session, the team will be able to manage the Ebright departmental folder structure and navigate the Linux environment with professional efficiency.

**Session Duration:** 2.5 to 3.5 hours  
**Delivery Mode:** Instructor-led with guided terminal labs  
**Tools:** Ubuntu Server (or Docker Ubuntu container), terminal, SSH access, text editor

## Practical Alignment (Module 2)
This lecture is designed to feed directly into `Practical Guide - Module 2.md`.

- Lecture Sections 1 to 3 map to Practical Labs 1 to 3.
- Lecture Sections 4 to 6 map to Practical Labs 4 to 6.
- Lecture Sections 7 to 8 map to Practical Labs 7 to 8.
- Keep examples consistent with `/opt/ebright_data` to reduce learner confusion.

Trainer note:
- When presenting examples, say the command purpose first, run second, and verify third.

## Learning Outcomes
By the end of this module, learners should be able to:
- Explain the Linux Filesystem Hierarchy (FHS) and identify core directories by role.
- Navigate, create, copy, move, and remove files and directories safely.
- Inspect logs and file content efficiently using terminal-native tools.
- Apply Linux user/group/permission logic using practical octal values.
- Perform routine maintenance: disk checks, software updates, process checks, and connectivity tests.
- Use terminal productivity features and basic scheduling with `crontab`.

## Beginner Guardrails
Use these rules repeatedly during delivery:

- One command at a time, then pause for class output check.
- Ask learners to read command intent before pressing Enter.
- If output differs, normalize with `pwd` and `ls` before continuing.
- Avoid destructive operations unless explicitly demonstrated.

## Pre-Class Checklist
- Confirm Ubuntu shell access (local VM, cloud VPS, or Docker container).
- Ensure learners can run commands with `sudo` privileges in the lab environment.
- Prepare a practice folder (example: `/opt/ebright-lab/module2`).
- Verify network tools are installed (`iproute2`, `net-tools`, `dnsutils`, `curl`, `wget`).

Fast pre-flight commands for trainer:

```bash
whoami
pwd
ip a | head -n 10
```

## 1. The Linux Filesystem Hierarchy (FHS)

Understanding the Linux directory structure is the first step toward server mastery. Unlike the drive-letter system in Windows (`C:`, `D:`), Linux organizes everything into a single, logical tree from the root (`/`). Knowing where system configurations, logs, and user data reside is essential for quick troubleshooting.

### 1.1 Core System Directories
These directories contain the essential files required for the operating system to boot and function. Modifying files here usually requires administrative (`sudo`) privileges.

- `/bin` and `/sbin`: Essential binaries (commands) needed for the system to run.
- `/etc`: The configuration center. Contains system-wide settings (Nginx, SSH, users).
- `/boot`: Files needed to start the operating system.
- `/root`: The home directory for the superuser (`root`) account.

### 1.2 Operational and Data Directories
These locations are where the daily life of the server happens, housing website files, staff documents, and temporary system data.

- `/var/log`: The witness. Contains system and application logs.
- `/var/www`: The showroom. Standard location for web files (example: Parent Portal).
- `/home`: The residence. Personal folders for staff (example: `/home/sarah_tutor`).
- `/opt`: The guest room. Optional software or custom Ebright repositories.
- `/tmp`: Temporary files, often cleared during reboot cycles.

### 1.3 Guided Practice (5-8 min)
Ask learners to run:

```bash
ls -ld / /etc /var/log /var/www /home /opt /tmp
```

Discussion points:
- Which locations are user data vs system configuration?
- Which paths should be changed carefully and why?

Facilitator prompts:
- "If you must edit one file for SSH settings, where do you usually look first?"
- "If a service is failing, which directory helps you investigate first?"

## 2. Help, Documentation and System Information

In Linux, you are never truly lost. The system is designed to be self-documenting, with built-in manuals and status commands that explain tools and environment details without requiring internet access.

### 2.1 Documentation Tools
When you encounter an unfamiliar command or option, these tools provide immediate guidance.

- `man [command]`: Full manual with detailed flags and behavior.
- `[command] --help`: Quick-reference usage summary.
- `whatis [command]`: One-line description of a command.
- `info [command]`: Hyperlinked documentation alternative to `man`.

### 2.2 System and Identity Information
Before making changes, identify your environment and account context.

- `uname -a`: Kernel version and architecture.
- `lsb_release -a`: Ubuntu distribution version details.
- `hostname`: Current server name.
- `whoami`: Current logged-in user.

### 2.3 Guided Practice (8-10 min)

```bash
uname -a
lsb_release -a
hostname
whoami
man ls
ls --help | head -n 10
whatis grep
```

Success criteria:
- Learners can identify the OS version and current account.
- Learners can find command help using at least two documentation tools.

Common confusion to address:
- `man` gives deep detail; `--help` is a quick reminder.
- `whatis` is useful when learners only remember command names vaguely.

## 3. Filesystem Operations: The Navigator's Kit

Navigating a server through text requires a mental map of directory relationships. These commands are your movement and construction toolkit in the terminal.

### 3.1 Navigation Commands
Movement in Linux relies on understanding your current location (working directory) relative to the rest of the tree.

- `pwd`: Print current working directory.
- `cd [path]`: Change directory.
- `cd ..`: Move to parent directory.
- `cd ~`: Jump to your home directory.
- `cd -`: Return to previous directory.

### 3.2 Creation and Manipulation
Building the Ebright folder structure requires consistent use of file and directory management commands.

- `mkdir [name]`: Create a directory.
- `mkdir -p [path]`: Create nested paths in one step.
- `touch [file]`: Create an empty file or update timestamp.
- `cp [src] [dest]`: Copy a file.
- `cp -r [src_dir] [dest_dir]`: Copy a directory recursively.
- `mv [src] [dest]`: Move or rename files/directories.

### 3.3 Deletion and Cleanup
CLI deletions are permanent. There is no recycle bin by default.

- `rm [file]`: Remove a file.
- `rmdir [dir]`: Remove an empty directory.
- `rm -r [dir]`: Remove a directory and contents recursively.
- `rm -rf [dir]`: Force recursive delete with no prompts. Use with extreme caution.

### 3.4 Guided Practice (10-15 min)

```bash
mkdir -p /opt/ebright-lab/module2/{finance,hr,academic}
touch /opt/ebright-lab/module2/finance/budget-2026.txt
cp /opt/ebright-lab/module2/finance/budget-2026.txt /opt/ebright-lab/module2/hr/
mv /opt/ebright-lab/module2/hr/budget-2026.txt /opt/ebright-lab/module2/hr/payroll-draft.txt
ls -R /opt/ebright-lab/module2
```

Success criteria:
- Learners can explain the difference between copy (`cp`) and move/rename (`mv`).
- Learners can confirm final file location without guesswork using `ls -R`.

Facilitator rescue tip:
- If learners lose track of location, run `pwd` then `cd /opt/ebright-lab/module2` to re-anchor.

## 4. Content Exploration, Editing and Archiving

Server operations often involve logs, configuration files, and command output. You need safe inspection habits plus reliable editing and packaging techniques.

### 4.1 Content Inspection
Before editing any file, inspect it first.

- `cat [file]`: Print full file content (best for short files).
- `less [file]`: Scrollable viewer (`q` to quit).
- `head [file]` and `tail [file]`: View beginning or end of file.
- `tail -f [file]`: Follow a log file in real time.
- `grep [text] [file]`: Search for matching text patterns.

### 4.2 Text Editing Tools
Headless servers require terminal-based editors.

- `nano`: Beginner-friendly editor with visible shortcuts (`Ctrl+O`, `Ctrl+X`).
- `vi` / `vim`: Professional standard editor.
- In `vim`: `i` enters insert mode, `Esc` returns to command mode, `:wq` saves and exits.

### 4.3 Archiving and Compression
Archiving bundles files for transport and backup; compression reduces storage usage.

- `tar -cvf [name].tar [files]`: Create archive bundle.
- `tar -xvf [name].tar`: Extract archive.
- `gzip [file]`: Compress file.
- `gunzip [file].gz`: Decompress file.

### 4.4 Guided Practice (12-15 min)

```bash
mkdir -p /opt/ebright-lab/module2/content
echo "Portal check OK" > /opt/ebright-lab/module2/content/status.log
echo "ERROR: timeout" >> /opt/ebright-lab/module2/content/status.log
cat /opt/ebright-lab/module2/content/status.log
grep ERROR /opt/ebright-lab/module2/content/status.log
tar -cvf /opt/ebright-lab/module2/content.tar /opt/ebright-lab/module2/content
gzip /opt/ebright-lab/module2/content.tar
ls -lh /opt/ebright-lab/module2/content.tar.gz
gunzip /opt/ebright-lab/module2/content.tar.gz
tar -xvf /opt/ebright-lab/module2/content.tar -C /opt/ebright-lab/module2
```

Success criteria:
- Learners can inspect file content and filter errors using `grep`.
- Learners can archive and restore a folder successfully.

Facilitator prompts:
- "Why inspect with `cat` or `tail` before editing configs?"
- "Where was the tar archive created, and how can you prove it?"

## 5. Security Logic: Users, Groups and Permissions

Linux is a multi-user operating system. Security follows least privilege: users only get access required for their role.

### 5.1 Identity Management
Admins must provision and revoke access as staffing changes.

- `useradd -m [user]`: Create user with home directory.
- `passwd [user]`: Set or change password.
- `userdel -r [user]`: Remove user and home files.
- `groupadd [group]`: Create group (example: `finance`).

### 5.2 Permission Logic (Octal)
Permissions are combinations of read (`4`), write (`2`), and execute (`1`).

- `7` (`4+2+1`) = full access.
- `5` (`4+0+1`) = read and execute.
- `chmod 770 [file_or_dir]` gives full access to owner and group, none to others.

### 5.3 Ownership Control
Each file has a user owner and group owner.

- `chown [user]:[group] [file]`: Change owner and group.
- `chgrp [group] [file]`: Change group only.

### 5.4 Granting Administrative (Sudo) Privileges
Use administrative access only for trusted staff.

- Ubuntu grants admin rights through the `sudo` group.
- `usermod -aG sudo [user]`: Add existing user to `sudo` group.
- `visudo`: Safest method to edit `/etc/sudoers`.

### 5.5 Guided Practice (12-15 min)

```bash
sudo groupadd registrar
sudo useradd -m lina_registrar
sudo usermod -aG registrar lina_registrar
sudo mkdir -p /opt/ebright-lab/module2/registrar
sudo chown root:registrar /opt/ebright-lab/module2/registrar
sudo chmod 770 /opt/ebright-lab/module2/registrar
id lina_registrar
ls -ld /opt/ebright-lab/module2/registrar
```

Success criteria:
- Learners can map user-to-group membership using `id`.
- Learners can explain ownership and permission output from `ls -ld`.

Mini teaching script for `770`:
- Owner: full access.
- Group: full access.
- Others: no access.

Common mistake:
- Learners often run `chmod` before confirming ownership. Reinforce `chown` then `chmod` when setting department folders.

## 6. Terminal Productivity and Task Scheduling

Professional administrators move fast and automate repetitive work.

### 6.1 Command History and Recall
- `history`: Show numbered command history.
- `!!`: Re-run last command.
- `Ctrl + R`: Search command history interactively.

### 6.2 Efficiency Shortcuts
- `Tab`: Auto-complete commands and paths.
- `Ctrl + C`: Stop current process.
- `Ctrl + L`: Clear terminal screen.

### 6.3 Piping and Redirection
- `|`: Send output from one command to another.
- `>`: Write output to file (overwrite).
- `>>`: Append output to file.
- `tee`: Write output to screen and file simultaneously.

### 6.4 Automation with Crontab
- `crontab -e`: Edit scheduled jobs.
- Syntax: `min hour day month weekday command`.
- Example: `0 0 * * *` runs daily at midnight.

### 6.5 Guided Practice (8-12 min)

```bash
history | tail -n 10
echo "disk snapshot" | tee /tmp/ops-note.txt
df -h | tee -a /tmp/ops-note.txt
cat /tmp/ops-note.txt
crontab -l
```

Practice task:
- Add one cron entry to run `df -h >> /tmp/disk-daily.log` every day at 23:30.

Success criteria:
- Learners can use piping/redirection confidently.
- Learners can describe each cron field in the schedule expression.

Beginner reminder:
- `>` overwrites a file.
- `>>` appends to a file.

## 7. Storage, Time and Software Management

Servers require routine maintenance to remain healthy, accurate, and secure.

### 7.1 Storage and Disk Health
- `df -h`: Disk free space summary in human-readable format.
- `du -sh [dir]`: Total size of a specific directory.

### 7.2 System Time, Calendar and Uptime
- `date`: Show current system date/time.
- `cal`: Show current month calendar.
- `uptime`: Show runtime duration and load averages.
- `timedatectl`: Inspect or configure timezone (example: `Asia/Kuala_Lumpur`).

### 7.3 Package Management (`apt`)
- `sudo apt update`: Refresh package metadata.
- `sudo apt install [pkg]`: Install software.
- `sudo apt upgrade`: Upgrade installed packages.
- `sudo apt remove [pkg]`: Remove installed software.

### 7.4 Guided Practice (10-12 min)

```bash
df -h
du -sh /var/log
date
timedatectl
sudo apt update
sudo apt list --upgradable
```

Success criteria:
- Learners can identify current disk pressure areas.
- Learners can report timezone and pending package updates.

Container caveat:
- In Docker-based labs, `timedatectl` may be limited if systemd is unavailable. Treat it as a concept demo when required.

## 8. Process and Network Operations

This final section covers runtime visibility and connectivity checks that are critical during outages and incident response.

### 8.1 Process Management
- `top`: Live CPU/RAM process view.
- `ps aux`: Snapshot of running processes.
- `kill [PID]`: Stop process by process ID.

### 8.2 Networking and Connectivity
- `ip a`: Show interface and IP details.
- `ifconfig`: Legacy network interface tool.
- `ping [host]`: Basic reachability test.
- `nslookup [domain]`: DNS query lookup.
- `traceroute [host]`: Path analysis to destination.
- `netstat -tuln`: Open/listening network sockets.

### 8.3 Remote Access and Secure Transfer
- `ssh user@host`: Secure remote shell session.
- `scp [file] user@host:[path]`: Secure file copy.
- `wget [URL]`: Download from web.
- `curl -O [URL]`: Retrieve remote file via URL.

### 8.4 Power Operations
- `halt`: Stop CPU/system operations.
- `poweroff`: Shut down and power off.
- `reboot`: Restart system.
- `shutdown now`: Immediate shutdown.

### 8.5 Guided Practice (12-15 min)

```bash
ps aux | head -n 5
top -b -n 1 | head -n 12
ip a
ping -c 4 8.8.8.8
nslookup ubuntu.com
ss -tuln | head -n 10
ssh -V
```

Safety note:
- Do not run `halt`, `poweroff`, `reboot`, or `shutdown now` during shared classroom sessions unless instructed by the trainer.

Success criteria:
- Learners can verify process health and basic network reachability.
- Learners can distinguish between connectivity, DNS, and service-port checks.

Facilitator prompts:
- "If ping works but domain lookup fails, what is likely broken?"
- "If DNS works but web app is down, which check comes next?"

## Mini Lab: Ebright Department Folder and Permission Setup
1. Create users and group structure (lab-safe names):

```bash
sudo groupadd ebright_staff
sudo useradd -m -G ebright_staff sarah_tutor
sudo useradd -m -G ebright_staff hafiz_admin
```

2. Create shared departmental directory and secure it:

```bash
sudo mkdir -p /opt/ebright/shared
sudo chown root:ebright_staff /opt/ebright/shared
sudo chmod 770 /opt/ebright/shared
ls -ld /opt/ebright/shared
```

3. Validate access behavior:

```bash
id sarah_tutor
id hafiz_admin
```

Success criteria:
- Learners can explain why `770` was selected.
- Learners can identify user owner and group owner from `ls -l` output.
- Learners can verify group membership using `id`.

## Knowledge Check (Exit Ticket)
1. Which directory stores most system-wide configuration files?
2. What is the practical difference between `rm -r` and `rm -rf`?
3. Why is `tail -f` useful during troubleshooting?
4. What does permission `770` mean in plain language?
5. Which command safely grants admin rights to an existing user in Ubuntu?

## Instructor Debrief Checklist
Use this at session close:

- Confirm every learner can explain `cp` vs `mv` in plain language.
- Confirm every learner can read `ls -ld` permission strings at a basic level.
- Confirm learners understand when to use `tail`, `grep`, and `crontab`.
- Capture at least one learner-reported issue and discuss root cause publicly.

## Module 2 Summary
- Linux filesystem literacy improves troubleshooting speed and reduces mistakes.
- Safe file operations depend on path awareness, inspection first, and caution with delete commands.
- User/group/permission controls are the core of practical server security.
- Productivity features (`history`, piping, redirection, `crontab`) increase operational efficiency.
- Routine monitoring of disk, time, packages, processes, and network health prevents many outages.
