# Practical Guide | Module 1: Introduction, Fundamentals & Remote Access (Windows Edition)

**Objective:** To install Docker, establish a safe practice environment (The Sandbox), and configure secure, key-based access to the Ebright infrastructure.

**Target Audience:** First-time learners with little or no Linux experience.

**Estimated Time:** 60 to 90 minutes.

## Before You Start (Beginner Checklist)
Use this checklist before running any command.

- You are using Windows 10/11 with administrator access.
- You have a stable internet connection.
- You can open PowerShell or Windows Terminal.
- Virtualization is enabled in BIOS/UEFI (required by Docker Desktop).
- You have at least 8 GB RAM recommended for smoother Docker usage.

If virtualization is disabled, Docker may fail to start.

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

## Pre-Lab: Installing Docker Desktop from the Internet
Before we can start the training, we must install the Docker "Engine" on your Windows computer.

### Step 1: Download the Installer
1. Open your web browser and go to `https://www.docker.com/products/docker-desktop`.
2. Click the large button labeled **Download for Windows**.
3. Save the `.exe` file to your Downloads folder.

### Step 2: Run the Installation
1. Double-click the downloaded `Docker Desktop Installer.exe`.
2. When prompted, ensure the option **Use WSL 2 instead of Hyper-V** is checked (this is much faster and more modern).
3. Follow the remaining on-screen instructions.
4. Once installation is complete, click **Close and restart** (your computer will need to reboot).

### Step 3: First Launch
1. After your computer restarts, search for **Docker Desktop** in the Start Menu and open it.
2. Accept the Docker Subscription Service Agreement.
3. Wait for the engine to start (the whale icon in the bottom-right corner will stop moving).

Quick check in PowerShell:

```bash
docker --version
```

**Expected result:** You should see a Docker version number, for example `Docker version 28.x.x`.

## Lab 1: The Sandbox Setup (Docker on Windows)
Before touching Ebright's live servers, we use Docker to create a disposable Linux environment on your computer where mistakes carry no risk.

### Task 1.1: Preparing Docker
1. Look at your System Tray (bottom-right icons near the clock).
2. Ensure the Docker whale icon is visible and not animated (indicating it is running).
3. If it is not running, search for **Docker Desktop** in the Start Menu and launch it.

### Task 1.2: Launching the Linux Environment
1. Right-click the Start button and select **Windows PowerShell** or **Terminal**.
2. Type the following command exactly and press Enter:

```bash
docker run -it --name ebright-practice ubuntu bash
```

**Verification:** Your command prompt should change from `PS C:\Users\...` to something like `root@a1b2c3d4e5f6:/#`.

**Note:** You are now successfully inside a Linux server.

If Docker asks to pull the image, wait until it completes. This is normal for first run.

Checkpoint:
- You can type `pwd` and get a Linux-style path like `/`.

## Lab 2: Verifying System Fundamentals
Inside your new Linux window, confirm the Engine (Kernel) and the Translator (Shell) you are using.

### Task 2.1: Identify the Kernel
Type this command inside the Linux prompt:

```bash
uname -sr
```

**What this is:** This shows you the core version of the Linux operating system.

### Task 2.2: Identify the Shell
Type this command to see which language the terminal is speaking:

```bash
echo $SHELL
```

**Expected result:** `/bin/bash` (the most popular shell for administrators).

Checkpoint:
- If `uname -sr` returns something like `Linux 6.x.x`, kernel check is successful.
- If `echo $SHELL` returns `/bin/bash`, shell check is successful.

## Lab 3: The Server Mindset Drills
Practice the core Linux rules that differ from Windows.

### Task 3.1: Case Sensitivity Test
In Windows, `MyFile` and `myfile` are the same. In Linux, they are different.

Create two different files:

```bash
touch ebright.txt
touch Ebright.txt
```

List them to verify:

```bash
ls
```

**Observation:** You will see both files listed separately.

### Task 3.2: The No Feedback Rule
Create a folder for backups:

```bash
mkdir backups
```

**Observation:** Notice the terminal just moves to a new line. In Linux, no news is good news. If the command did not show an error, it worked.

Checkpoint:
- Run `ls` again and confirm you can see `backups`, `ebright.txt`, and `Ebright.txt`.

## Lab 4: Remote Access (SSH Key Generation)
Establish secure, password-less entry into the Ebright VPS. Do this on your Windows machine, not inside the Linux container.

### Task 4.1: Generating Your Keys in Windows
1. Open a new PowerShell window (do not use the one where Linux is running).
2. Type the following command:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@ebright.edu.my"
```

3. When asked where to save the key, press Enter to accept the default location.
4. When asked for a passphrase, leave it blank for convenience or enter one for maximum security.

### Task 4.2: Identifying Your Keys
Navigate to your SSH directory (Windows):
- `C:\Users\YourName\.ssh\`

Identify the two files:
- `id_rsa`: Private key. Never share this. It is your physical key.
- `id_rsa.pub`: Public key. This is the lock we will place on the Ebright server.

Useful Windows commands to verify:

```bash
cd $HOME\.ssh
dir
```

Checkpoint:
- You can see both `id_rsa` and `id_rsa.pub` in the file list.

## Lab 5 (Optional): Enable SSH in the Docker Sandbox
Yes, SSH can be enabled in Docker for practice. In this lab, you will also learn how to use a `Dockerfile` and Docker Compose.

Important notes:
- For day-to-day container management, `docker exec` is usually preferred over SSH.
- This lab is for learning remote access flow in a safe local sandbox.
- We will use files in the `asset` folder to build a custom image.

### Task 5.1: Understand the Project Files
This repo now includes:
- `asset/Dockerfile`: builds an Ubuntu image with SSH enabled.
- `docker-compose.yml`: starts the container and maps `localhost:2222` to container port `22`.

Open `asset/Dockerfile` and note:
- `openssh-server` is installed.
- A user named `trainee` is created.
- Password login is enabled for training.
- SSH daemon starts in foreground using `sshd -D`.

### Task 5.2: Build and Start with Docker Compose
In Windows PowerShell (from project root), run:

```bash
docker compose up -d --build
```

Why this matters:
- `--build` builds the image from `asset/Dockerfile`.
- Compose reads `docker-compose.yml` and applies consistent settings.

Verification:

```bash
docker compose ps
```

Expected result:
- Service `ebright-practice-ssh` is `Up`.
- Port mapping shows `0.0.0.0:2222->22/tcp`.

### Task 5.3: Test SSH Login from Windows
Open a new Windows PowerShell window and run:

```bash
ssh trainee@localhost -p 2222
```

When prompted:
1. Type `yes` to trust the host key.
2. Enter password: `ebright123!`

Verification:
- If login succeeds, your prompt changes to something like `trainee@<container-id>:$`.

### Task 5.4: Optional Key-Based SSH Login (Recommended)
In Windows PowerShell, copy your public key text:

```bash
type $HOME\.ssh\id_rsa.pub
```

Copy that full single line.

In the SSH session as `trainee`, run:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
```

Paste the copied public key into `authorized_keys`, then save and exit.

Finish permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

Now test key login from a new Windows PowerShell:

```bash
ssh trainee@localhost -p 2222
```

If your key is loaded and recognized, login should work without password prompt.

### Task 5.5: Practice Compose Lifecycle Commands
Run these commands to understand service lifecycle:

```bash
docker compose stop
docker compose start
docker compose down
```

What each command does:
- `stop`: stops running container but keeps it for later.
- `start`: starts stopped container again.
- `down`: removes container and network created by Compose.

## Lab Summary and Cleanup
- To exit the Sandbox: `exit`
- To start services with Compose: `docker compose up -d`
- To stop services without removing: `docker compose stop`
- To remove all Compose resources: `docker compose down`

## Troubleshooting (Common Beginner Issues)
1. `docker: command not found` in PowerShell.
- Cause: Docker Desktop is not installed correctly or terminal was opened before installation finished.
- Fix: Close terminal, reopen PowerShell, run `docker --version` again.

2. Docker Desktop says WSL 2 is required.
- Cause: WSL is not enabled.
- Fix: Install/enable WSL 2, restart Windows, then reopen Docker Desktop.

3. `docker run` fails because container name already exists.
- Cause: `ebright-practice` already exists from previous run.
- Fix: Use `docker start -ai ebright-practice` instead, or remove old container with `docker rm -f ebright-practice`.

4. `Permission denied (publickey)` during future SSH login.
- Cause: Wrong public key on server or wrong private key used locally.
- Fix: Recheck your `id_rsa.pub` content and ensure it was added to the server user's `authorized_keys`.

5. Confusion between Windows and Linux terminals.
- Rule: If your prompt starts with `root@...:/#`, you are in Linux container.
- Rule: If your prompt starts with `PS C:\Users\...`, you are in Windows PowerShell.

6. SSH connection refused on `localhost:2222`.
- Cause: Compose service is not running, port mapping failed, or build did not complete.
- Fix: Run `docker compose up -d --build`, then check `docker compose ps`.

## Troubleshooting: If Docker Desktop Fails to Start
Common issues for Windows users are often related to system settings called virtualization.

### Problem A: Hardware assisted virtualization must be enabled in the BIOS
If you see this error, your computer's CPU supports virtualization but the feature is currently turned off.

Check status:
1. Press `Ctrl + Shift + Esc` to open Task Manager.
2. Click the **Performance** tab, then click **CPU**.
3. Look for **Virtualization** at the bottom-right.
4. If it says **Disabled**, continue to BIOS steps below.

Enter BIOS:
1. Restart your computer.
2. During startup, repeatedly tap `F2`, `F10`, or `Del` (key depends on your laptop brand).

Enable virtualization:
1. Look for a menu called **Advanced**, **Configuration**, or **Security**.
2. If you have an Intel processor, enable **Intel Virtualization Technology (VT-x)**.
3. If you have an AMD processor, enable **SVM Mode**.

Save and exit:
1. Press `F10` to save changes and restart.
2. Open Docker Desktop again.

### Problem B: WSL 2 installation is incomplete
Open Windows PowerShell as Administrator (Right-click Start, then choose Windows Terminal/PowerShell Admin).

Run:

```bash
wsl --update
```

After update completes:
1. Restart Docker Desktop.
2. If needed, reboot Windows once and try again.

## Completion Checklist
- Docker Desktop installed and running.
- Linux sandbox created successfully.
- Kernel and shell verified.
- Case sensitivity and no-feedback rules practiced.
- SSH key pair generated and identified.
