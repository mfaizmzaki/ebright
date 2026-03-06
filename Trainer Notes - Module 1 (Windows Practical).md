# Trainer Notes | Module 1 (Windows Practical)

**Reference File:** `Practical Guide - Module 1.md`  
**Audience:** New Linux learners using Windows  
**Total Duration:** 60 to 90 minutes  
**Delivery Mode:** Instructor-led with live demo + learner follow-along

## Trainer Outcomes
By the end of this practical session, learners should be able to:
- Start Docker Desktop and run a Linux container sandbox.
- Verify Linux kernel and shell basics.
- Explain case sensitivity and Linux command feedback behavior.
- Generate SSH key pairs on Windows and identify private/public keys.
- Explain how a `Dockerfile` and `asset/module1/docker-compose.yml` create a repeatable SSH training environment.
- Start, verify, and stop a Compose service used for SSH testing.

## Suggested Session Timeline
1. Orientation and setup expectations: 5 to 10 minutes
2. Pre-Lab Docker installation check: 10 to 15 minutes
3. Lab 1 Sandbox setup: 10 to 15 minutes
4. Lab 2 System fundamentals: 10 minutes
5. Lab 3 Server mindset drills: 10 to 15 minutes
6. Lab 4 SSH key generation: 10 to 15 minutes
7. Lab 5 Optional Compose + SSH lab: 15 to 20 minutes
8. Wrap-up and Q&A: 5 minutes

## Instructor Runbook

### 1. Orientation and Ground Rules (5 to 10 min)
Talking points:
- Learners are safe to make mistakes inside the Docker sandbox.
- This session uses the VS Code integrated terminal as the default terminal.
- Differentiate terminal contexts early:
- `PS C:\Users\...` means Windows host terminal (including VS Code terminal).
- `root@...:/#` means Linux container shell.
- Ask learners to run commands one-by-one, not in large pasted blocks.

Quick instructor check:
- Confirm everyone can open VS Code and start a terminal from `Terminal > New Terminal`.

### 2. Pre-Lab Docker Installation Check (10 to 15 min)
Learner action:
```bash
docker --version
```

Expected outcome:
- A version line appears, for example `Docker version 28.x.x`.

If learners fail here:
- Docker not installed or not started.
- Ask learner to open Docker Desktop and wait for whale icon to become steady.
- Restart terminal after Docker starts.

### 3. Lab 1 Sandbox Setup (10 to 15 min)
Learner action:
```bash
docker run -it --name ebright-practice ubuntu bash
```

Expected outcome:
- Prompt changes from Windows style to Linux style:
- From `PS C:\Users\...`
- To `root@<container-id>:/#`

Reinforcement prompt:
- Ask: "Are we in Windows or Linux now? How do you know?"

Common issue:
- `Conflict. The container name ... is already in use.`

Fix:
```bash
docker start -ai ebright-practice
```

### 4. Lab 2 System Fundamentals (10 min)
Learner action:
```bash
uname -sr
echo $SHELL
```

Expected outcome:
- `uname -sr` returns Linux kernel name/version.
- `echo $SHELL` returns `/bin/bash`.

Teaching cue:
- Kernel = engine that controls resources.
- Shell = interpreter that converts user commands into actions.

### 5. Lab 3 Server Mindset Drills (10 to 15 min)
Learner action:
```bash
touch ebright.txt
touch Ebright.txt
ls
mkdir backups
ls
```

Expected outcome:
- Both `ebright.txt` and `Ebright.txt` exist separately.
- `backups` directory appears.
- `mkdir backups` usually gives no success message.

Teaching cue:
- Linux is case-sensitive.
- Silence after a command often means success.

Intervention tip:
- If `mkdir: cannot create directory 'backups': File exists`, explain idempotency and proceed.

### 6. Lab 4 SSH Key Generation on Windows (10 to 15 min)
Important:
- Learners must open a new VS Code terminal tab for this step.
- Do not run inside Linux container shell.

Learner action:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@ebright.edu.my"
cd $HOME\.ssh
dir
```

Expected outcome:
- Keypair files appear:
- `id_rsa` (private key)
- `id_rsa.pub` (public key)

Security emphasis:
- Never share `id_rsa`.
- `id_rsa.pub` is safe to upload to server.

### 7. Lab 5 Optional: Dockerfile + Compose SSH Lab (15 to 20 min)
Trainer setup note:
- This lab teaches image build (`Dockerfile`) and service orchestration (`asset/module1/docker-compose.yml`) while validating SSH access.

Learner action (from project root):
```bash
docker compose -f asset/module1/docker-compose.yml up -d --build
docker compose -f asset/module1/docker-compose.yml ps
```

Expected outcome:
- Service `ebright-practice-ssh` is `Up`.
- Port mapping includes `0.0.0.0:2222->22/tcp`.

SSH verification from VS Code terminal:
SSH verification from VS Code terminal:
```bash
ssh trainee@localhost -p 2222
```

Expected login flow:
- Learner types `yes` for first host-key prompt.
- Learner enters password `ebright123!`.
- Prompt becomes `trainee@<container-id>:$`.

Optional key-based flow:
```bash
type $HOME\.ssh\id_rsa.pub
```
- Copy key text and paste into container user `~/.ssh/authorized_keys`.
- Set permissions `700` for `.ssh` and `600` for `authorized_keys`.

Compose lifecycle drill:
```bash
docker compose -f asset/module1/docker-compose.yml stop
docker compose -f asset/module1/docker-compose.yml start
docker compose -f asset/module1/docker-compose.yml down
```

Teaching cues:
- `Dockerfile` = how image is built.
- Compose file = how service runs (ports, name, build context).
- Repeatability: all learners run the same environment with one command.

### 8. Wrap-Up and Exit (5 min)
Learner action:
```bash
exit
docker compose -f asset/module1/docker-compose.yml stop
docker compose -f asset/module1/docker-compose.yml down
```

Trainer note:
- Explain `stop` vs `down` in Compose.
- `stop` keeps resources for quick restart.
- `down` removes created resources for clean reset.

## Live Monitoring Checklist for Trainer
- Learner can identify terminal context correctly.
- Learner sees Linux prompt after container launch.
- Learner can run and explain `uname -sr` and `echo $SHELL`.
- Learner confirms case-sensitive filename behavior.
- Learner successfully generates SSH keys and identifies both files.
- Learner can run `docker compose -f asset/module1/docker-compose.yml up -d --build` and confirm service is `Up`.
- Learner can SSH to `localhost:2222` successfully.

## Fast Troubleshooting Card (Instructor Use)
1. Docker command not recognized
- Ensure Docker Desktop is installed and fully started.
- Reopen VS Code terminal.

2. WSL 2 requirement error
- Enable/install WSL 2.
- Reboot and reopen Docker Desktop.

3. Existing container name conflict
- If using old flow, remove stale container; for new flow, use `docker compose -f asset/module1/docker-compose.yml down` then `docker compose -f asset/module1/docker-compose.yml up -d --build`.

4. SSH command not found
- Use latest Windows 10/11 with OpenSSH Client feature enabled.

5. Learner ran SSH keygen inside container
- Stop and rerun in VS Code terminal on Windows host.

6. SSH connection refused on `localhost:2222`
- Verify service status using `docker compose -f asset/module1/docker-compose.yml ps`.
- Rebuild and restart using `docker compose -f asset/module1/docker-compose.yml up -d --build`.

7. Learner runs Compose in wrong folder
- Ensure terminal path is project root and use `-f asset/module1/docker-compose.yml`.

## Optional Extension (If Time Allows)
- Show learners how to display public key content:
```bash
type $HOME\.ssh\id_rsa.pub
```
- Explain this is the value typically copied into server `authorized_keys`.

