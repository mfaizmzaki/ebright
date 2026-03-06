# Practical Guide | Module 2: Mastering the Filesystem, Users & Permissions

**Objective:** To implement a professional departmental folder structure, manage user security, and master administrative tools within the Ebright Linux environment.

**Target Audience:** First-time learners with little or no Linux experience.

**Estimated Time:** 60 to 75 minutes.

## What You Will Build in This Module
By the end of this practical, your container will include a realistic Ebright departmental data layout:

- `/opt/ebright_data/finance`
- `/opt/ebright_data/teachers`
- `/opt/ebright_data/hq_admin`
- A protected finance folder with group-based access control
- A small automation job that writes heartbeats to `/tmp/heartbeat.txt`

## Before You Start (Beginner Checklist)
Use this checklist before running any command.

- Docker Desktop is installed and running on Windows.
- Your `ebright-practice` container already exists from Module 1.
- You can open Windows PowerShell or Windows Terminal.
- You understand the difference between Windows prompt and Linux prompt.

Prompt reminder:
- Windows PowerShell prompt looks like `PS C:\Users\...`.
- Linux container prompt looks like `root@a1b2c3d4e5f6:/#`.

## How to Use This Guide
- Copy one command at a time.
- Press Enter after each command.
- Read the Verification or Expected result before moving on.
- If your output is different, check the Troubleshooting section at the end.

Command format in this guide:
- Commands shown in code blocks are typed exactly as-is.
- Do not type the `$` symbol (we do not use it here).
- Linux commands must be run inside the Docker Linux prompt.
- Windows commands must be run in PowerShell/Terminal on Windows.

Safety rules for beginners:
- Read each command before pressing Enter.
- Avoid adding `rm -rf` unless specifically instructed.
- If unsure, run `pwd` first to confirm where you are.

## Lab 0: Re-entering the Sandbox
Since we are using Windows, we must first jump back into the Linux container from Module 1.

1. Open Windows PowerShell.
2. Start and enter your practice container:

```bash
docker start -ai ebright-practice
```

**Verification:** Your prompt should look like `root@a1b2c3d4e5f6:/#`.

Checkpoint:
- Run `pwd` and confirm the output is `/` or another Linux-style path.

## Lab 1: Navigating the Filesystem (FHS)
Visualize the Linux "tree" and locate Ebright's critical system folders.

### Task 1.1: Go to Root

```bash
cd /
```

### Task 1.2: Explore Top-Level Folders

```bash
ls
```

Expected examples in output:
- `etc`
- `var`
- `opt`

### Task 1.3: Enter Configuration Directory

```bash
cd /etc
```

### Task 1.4: Enter Log Directory

```bash
cd /var/log
```

Quick verify:

```bash
pwd
```

Expected result: `/var/log`

Checkpoint:
- You can explain that `/etc` stores configuration and `/var/log` stores logs.

## Lab 2: Help & Identity Information
Learn how to identify your environment and get help without Google.

### Task 2.1: Check Current User

```bash
whoami
```

**Expected result:** `root`

### Task 2.2: Check Current Location

```bash
pwd
```

Expected result: your current folder path (for example `/var/log`).

### Task 2.3: Open Manual for `ls`

```bash
man ls
```

Press `q` to exit the manual.

### Task 2.4: Quick Help for `mkdir`

```bash
mkdir --help
```

Checkpoint:
- You can use both `man` and `--help` to learn commands independently.

## Lab 3: Filesystem Operations (The Navigator's Kit)
Build the Ebright departmental structure from scratch.

### Task 3.1: Create Parent Folders

```bash
mkdir -p /opt/ebright_data/finance /opt/ebright_data/teachers /opt/ebright_data/hq_admin
```

### Task 3.2: Create a Dummy File

```bash
touch /opt/ebright_data/finance/report_january.txt
```

Quick verify:

```bash
ls -l /opt/ebright_data/finance
```

### Task 3.3: Copy Report to Teachers Folder

```bash
cp /opt/ebright_data/finance/report_january.txt /opt/ebright_data/teachers/
```

### Task 3.4: Rename Original File

```bash
mv /opt/ebright_data/finance/report_january.txt /opt/ebright_data/finance/archive_january.txt
```

Why this matters:
- `cp` duplicates data.
- `mv` changes location or filename.

Verification:

```bash
ls -R /opt/ebright_data
```

Checkpoint:
- `teachers` contains `report_january.txt`.
- `finance` contains `archive_january.txt`.

## Lab 4: Content Exploration & Text Editing
Edit files and inspect data like a real administrator.

### Task 4.1: View Message of the Day

```bash
cat /etc/motd
```

### Task 4.2: Read Last 5 Lines of a Log

```bash
tail -n 5 /var/log/alternatives.log
```

### Task 4.3: Create a Welcome Note with Nano

```bash
nano /opt/ebright_data/welcome.txt
```

Type this line:
`Welcome to Ebright Linux Server`

Save and exit:
- Press `Ctrl+O`, then press `Enter`.
- Press `Ctrl+X`.

### Task 4.4: Archive Finance Folder

```bash
tar -cvf finance_backup.tar /opt/ebright_data/finance
```

Note:
- Archive file is created in your current directory.
- Run `pwd` if you are unsure where `finance_backup.tar` was saved.

Verification:

```bash
ls -lh finance_backup.tar
```

Checkpoint:
- Archive file `finance_backup.tar` is created.

## Lab 5: Security Logic (Users & Permissions)
Protect sensitive Ebright data from unauthorized access.

### Task 5.1: Create Finance Group

```bash
groupadd finance
```

### Task 5.2: Create Staff User

```bash
useradd -m sarah_admin
```

Quick verify:

```bash
ls -ld /home/sarah_admin
```

### Task 5.3: Restrict Folder Access

```bash
chmod 770 /opt/ebright_data/finance
```

### Task 5.4: Change Group Ownership

```bash
chown root:finance /opt/ebright_data/finance
```

Verification:

```bash
ls -ld /opt/ebright_data/finance
id sarah_admin
```

Checkpoint:
- Permission should show `drwxrwx---` for the finance folder.
- Ownership should show `root finance`.

## Lab 6: Terminal Productivity & Scheduling
Use shortcuts and automation to save time.

### Task 6.1: Pipe Output with `grep`

```bash
ls /etc | grep network
```

### Task 6.2: Save Command History

```bash
history > my_commands.txt
```

Quick verify:

```bash
tail -n 5 my_commands.txt
```

### Task 6.3: Create a Cron Job

```bash
crontab -e
```

If prompted, choose `1` for Nano.

Add this line at the bottom:

```bash
* * * * * date >> /tmp/heartbeat.txt
```

Save and exit Nano.

Verification (wait 1 to 2 minutes):

```bash
tail -n 5 /tmp/heartbeat.txt
```

Checkpoint:
- You can see timestamp lines being appended every minute.

## Lab 7: Storage & Software Management
Maintain server health and install useful tools.

### Task 7.1: Check Disk Space

```bash
df -h
```

Tip:
- Focus on the `Use%` column to quickly spot storage pressure.

### Task 7.2: Set Timezone to Malaysia

```bash
timedatectl set-timezone Asia/Kuala_Lumpur
```

### Task 7.3: Install `tree` Utility

```bash
apt update && apt install tree -y
```

### Task 7.4: Visualize Ebright Structure

```bash
tree /opt/ebright_data
```

Checkpoint:
- You can see a clean tree output of finance, teachers, and hq_admin folders.

## Lab 8: Networking & Power Operations
Test connectivity and close your session safely.

### Task 8.1: Show Internal IP Address

```bash
ip a
```

### Task 8.2: Internet Connectivity Test

```bash
ping -c 3 google.com
```

### Task 8.3: DNS Resolution Check

```bash
nslookup google.com
```

Expected result:
- You should see resolved IP addresses in the output.

### Task 8.4: Exit Linux Session

```bash
exit
```

Checkpoint:
- Your prompt returns to Windows PowerShell (`PS C:\Users\...`).

## Lab Summary
- To resume this environment later:

```bash
docker start -ai ebright-practice
```

**Crucial Takeaway:** You have successfully built a secured, departmental filesystem that mimics a real-world Ebright production server.

## Extra Practice (Optional, 10 Minutes)
If you finish early, practice these admin drills:

1. Create a new department folder and test permissions.

```bash
mkdir -p /opt/ebright_data/admissions
chown root:finance /opt/ebright_data/admissions
chmod 770 /opt/ebright_data/admissions
ls -ld /opt/ebright_data/admissions
```

2. Search your own command history for permission-related commands.

```bash
history | grep -E "chmod|chown|groupadd|useradd"
```

3. Add one line to `welcome.txt` using redirection.

```bash
echo "Please follow access policy for finance data." >> /opt/ebright_data/welcome.txt
cat /opt/ebright_data/welcome.txt
```

## Troubleshooting (Common Beginner Issues)
1. `Error response from daemon: No such container: ebright-practice`.
- Cause: Container was deleted or name is different.
- Fix: Recreate it using Module 1 command:

```bash
docker run -it --name ebright-practice ubuntu bash
```

2. `man: command not found`.
- Cause: Minimal container image may not include manual pages.
- Fix:

```bash
apt update && apt install man-db -y
```

3. `timedatectl` fails in Docker.
- Cause: Many containers do not run full systemd services.
- Fix: Continue lab and treat timezone step as demonstration.

4. `nslookup: command not found`.
- Cause: DNS utilities are not installed by default.
- Fix:

```bash
apt update && apt install dnsutils -y
```

5. `crontab: command not found`.
- Cause: Cron package is missing in minimal container.
- Fix:

```bash
apt update && apt install cron -y
service cron start
```

6. `groupadd` or `useradd` says the name already exists.
- Cause: You may have run the lab previously.
- Fix: Use a different username/group or delete old test account/group.

7. `Operation not permitted` or `Permission denied` appears unexpectedly.
- Cause: Folder ownership and permissions may not match expected values.
- Fix: Re-run the security commands in order:

```bash
chown root:finance /opt/ebright_data/finance
chmod 770 /opt/ebright_data/finance
ls -ld /opt/ebright_data/finance
```

## Completion Checklist
- Re-entered Linux sandbox successfully.
- Navigated key FHS locations (`/`, `/etc`, `/var/log`).
- Used command help tools (`man`, `--help`).
- Built and verified Ebright departmental folder structure.
- Created, copied, renamed, and archived files.
- Applied group ownership and `770` permissions.
- Practiced pipeline, redirection, and cron automation.
- Performed storage and network health checks.
- Exited safely back to Windows prompt.

## Self-Check Questions
Use these to confirm understanding before moving to Module 3.

1. What is the difference between `cp` and `mv`?
2. Why does `770` protect departmental data better than `755`?
3. Which folder usually stores system logs?
4. Which two commands help you learn unknown command syntax quickly?
