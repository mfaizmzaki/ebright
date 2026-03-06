# Practical Guide | Module 3: Deployment, Security & Continuity

**Objective:** To execute a safe deployment workflow for the Ebright Parent Portal, monitor live server health, harden network access, and practice continuity operations.

**Target Audience:** Beginner to intermediate learners who completed Modules 1 and 2.

**Estimated Time:** 75 to 90 minutes.

## What You Will Build in This Module
By the end of this practical, you will complete a realistic operations routine:

- Capture a pre-change recovery point (snapshot workflow checklist).
- Validate server health (disk, memory, CPU, load).
- Investigate logs for service and authentication issues.
- Manage Nginx with safe config testing and reload.
- Perform a Git-style deployment update workflow.
- Verify DNS routing behavior.
- Apply baseline UFW firewall hardening rules.
- Run a controlled maintenance command and cancellation.

## Lab Environment Requirements
This module is designed for an **Ubuntu VPS/VM with systemd**.

Required tools:
- `sudo` access
- `nginx`
- `git`
- `ufw`
- `dnsutils` (`dig` command)

Quick install if needed:

```bash
sudo apt update
sudo apt install -y nginx git ufw dnsutils
```

Important environment note:
- If you run this inside a basic Docker container, `systemctl`, `ufw`, and shutdown commands may not behave like a real server.
- For full learning outcome, run these labs on Ubuntu Server (cloud VPS or VM).

## Before You Start (Beginner Checklist)
Use this checklist before running any command.

- [ ] You can SSH into your Ubuntu server.
- [ ] You can run `sudo` commands.
- [ ] You understand this lab uses production-safe sequence: backup/snapshot first.
- [ ] You have a test Git repository URL for deployment simulation.
- [ ] You have a test domain or hostname for DNS checks.

Safety rules:
- One command at a time.
- Verify output before moving forward.
- Do not skip snapshot step before change operations.
- Keep your current SSH session open while applying firewall rules.

## How to Use This Guide
- Copy one command at a time.
- Press Enter after each command.
- Compare output with verification notes.
- If output differs, check the troubleshooting section before continuing.

## Lab 0: Session Pre-Flight (5 min)
Establish identity, host context, and basic service state.

### Task 0.1: Confirm Server Identity

```bash
whoami
hostnamectl
pwd
```

Verification:
- `whoami` should show your current user.
- `hostnamectl` shows Linux host details.

### Task 0.2: Confirm Nginx Baseline State

```bash
sudo systemctl status nginx --no-pager
```

Checkpoint:
- You can tell whether Nginx is `active`, `inactive`, or `failed`.

## Lab 1: Infrastructure Safety (Snapshot and Rollback Readiness) (8-10 min)
In real operations, snapshots are created in the cloud provider console. This lab trains the documentation discipline around that action.

### Task 1.1: Record a Pre-Change Checklist Note
Create a local ops note for this maintenance window:

```bash
mkdir -p ~/ebright-ops
cat << 'EOF' > ~/ebright-ops/prechange-checklist.txt
Change Window: Module 3 Practical
Snapshot Status: CREATED (record snapshot ID from cloud console)
Service Baseline: nginx status captured
Rollback Owner: <name>
Post-Change Verification: portal page, nginx status, firewall status
EOF
cat ~/ebright-ops/prechange-checklist.txt
```

### Task 1.2: Capture Current Deployment and Service Baseline

```bash
sudo systemctl status nginx --no-pager | head -n 15
cd /var/www 2>/dev/null || cd ~
git rev-parse --short HEAD 2>/dev/null || echo "No git repo in current path yet"
```

Checkpoint:
- You have written a pre-change note and captured baseline context.

## Lab 2: System Diagnostics (10-12 min)
Measure core resources before deployment.

### Task 2.1: Disk, Memory, Load

```bash
df -h
free -m
uptime
```

### Task 2.2: CPU and Process Pressure

```bash
top
```

Action:
- Let `top` run for 10 to 15 seconds, then press `q` to exit.

Checkpoint:
- You can identify the highest CPU-consuming process.
- You can identify which filesystem has the highest usage.

## Lab 3: Log Investigation and Error Filtering (12-15 min)
Use logs as evidence, not guesswork.

### Task 3.1: View Latest Nginx Errors

```bash
sudo tail -n 30 /var/log/nginx/error.log
```

### Task 3.2: Search for Failure Patterns

```bash
sudo grep -iE "error|fail|denied" /var/log/nginx/error.log | tail -n 20
sudo grep -i "failed" /var/log/auth.log | tail -n 20
```

### Task 3.3: Live Follow Mode (Optional 2-minute demo)
Terminal A:

```bash
sudo tail -f /var/log/nginx/error.log
```

Terminal B:
- Refresh test website or run:

```bash
curl -I http://localhost
```

Then stop follow mode with `Ctrl + C`.

Checkpoint:
- You can find at least one useful line with timestamp and component context.

## Lab 4: Nginx and Service Management (12-15 min)
Apply safe service control workflow.

### Task 4.1: Package Index and Nginx Presence

```bash
sudo apt update
sudo apt install -y nginx
```

### Task 4.2: Validate Config Before Reload

```bash
sudo nginx -t
```

Expected result:
- `syntax is ok`
- `test is successful`

### Task 4.3: Reload and Enable Service

```bash
sudo systemctl reload nginx
sudo systemctl enable nginx
sudo systemctl status nginx --no-pager
```

Checkpoint:
- Nginx is `active (running)` and enabled at boot.

## Lab 5: Git-Based Deployment Workflow (12-15 min)
Simulate a real deployment routine.

### Task 5.1: Prepare Deployment Directory

```bash
sudo mkdir -p /var/www
sudo chown $USER:$USER /var/www
cd /var/www
```

### Task 5.2: Clone a Test Repository
Replace `[URL]` with your training repo URL:

```bash
git clone [URL] html
cd /var/www/html
```

If `html` already exists, use:

```bash
cd /var/www/html
git status
```

### Task 5.3: Safe Update Routine

```bash
git status
git log --oneline -n 5
git pull
```

### Task 5.4: Post-Deploy Verification

```bash
sudo systemctl status nginx --no-pager
curl -I http://localhost
```

Checkpoint:
- You can explain why `git status` is checked before `git pull`.

## Lab 6: DNS Validation (8-10 min)
Confirm domain routing logic and cache behavior.

### Task 6.1: Query DNS
Replace `portal.ebright.edu.my` if needed:

```bash
dig portal.ebright.edu.my +short
nslookup portal.ebright.edu.my
```

### Task 6.2: Compare Resolver Behavior (Optional)

```bash
dig portal.ebright.edu.my
```

Look for:
- Answer section (resolved target)
- TTL values (cache lifetime)

Checkpoint:
- You can describe why different users may see different DNS results for a short period.

## Lab 7: UFW Firewall Hardening (10-12 min)
Apply least-exposure policy without losing remote access.

### Task 7.1: Allow Required Ports First

```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
```

### Task 7.2: Apply Defaults and Enable

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

Type `y` when prompted.

### Task 7.3: Review Active Rules

```bash
sudo ufw status numbered
```

Checkpoint:
- SSH, HTTP, and HTTPS are allowed.
- Inbound policy is deny by default.

Recovery command:
- Remove a wrong rule by number:

```bash
sudo ufw delete <rule-number>
```

## Lab 8: Continuity Operations (5-8 min)
Practice safe shutdown scheduling and cancellation.

### Task 8.1: Schedule Maintenance Shutdown

```bash
sudo shutdown -h +5 "Scheduled Maintenance - Module 3 Practical"
```

### Task 8.2: Cancel Shutdown Immediately

```bash
sudo shutdown -c
```

Verification:
- Command executes without error.
- No shutdown occurs.

Important:
- Do not leave a scheduled shutdown active in classroom environment.

## Lab 9: Extra Lab - Comprehensive Docker Compose Stack (Nginx + Essential Tools) (20-25 min)
This extra lab gives learners a safe, local simulation of a mini production stack using Docker Compose.

Stack components:
- `nginx`: reverse proxy entry point on `http://localhost:8080`
- `app`: demo upstream service (`whoami`)
- `db`: PostgreSQL database
- `cache`: Redis cache
- `adminer`: database web UI on `http://localhost:8081`
- `toolbox` (optional profile): network troubleshooting container

### Task 9.1: Start the Stack
Run from repository root (`ebright` folder):

```bash
docker compose -f asset/module3/docker-compose.yml up -d
```

Verification:

```bash
docker compose -f asset/module3/docker-compose.yml ps
```

Expected:
- `nginx`, `app`, `db`, `cache`, `adminer` should be `Up`.
- Port mapping should show `8080` for nginx and `8081` for adminer.

### Task 9.2: Validate Nginx Routing and Health Endpoint

```bash
curl -I http://localhost:8080
curl http://localhost:8080/healthz
curl http://localhost:8080 | head -n 5
```

Expected:
- `/healthz` returns `ok`.
- Root route returns `whoami` response content via Nginx reverse proxy.

### Task 9.3: Check Service Logs and Health

```bash
docker compose -f asset/module3/docker-compose.yml logs nginx --tail=40
docker compose -f asset/module3/docker-compose.yml logs db --tail=40
docker compose -f asset/module3/docker-compose.yml logs cache --tail=40
```

Checkpoint:
- You can identify one normal startup line for Postgres and Redis.

### Task 9.4: Connect to PostgreSQL from Inside the DB Container

```bash
docker exec -it ebright-m3-postgres psql -U ebright -d ebright -c "SELECT now();"
```

Expected:
- Query returns current timestamp.

### Task 9.5: Validate Redis Connectivity

```bash
docker exec -it ebright-m3-redis redis-cli ping
```

Expected:
- Output: `PONG`

### Task 9.6: Optional Toolbox for Network Diagnostics
Start optional troubleshooting container profile:

```bash
docker compose -f asset/module3/docker-compose.yml --profile tools up -d
docker exec -it ebright-m3-toolbox sh -c "dig nginx +short; nc -zv db 5432; nc -zv cache 6379"
```

Expected:
- Internal DNS resolves service names.
- Port checks to db/cache succeed.

### Task 9.7: Controlled Restart and Recovery Drill

```bash
docker compose -f asset/module3/docker-compose.yml restart nginx
docker compose -f asset/module3/docker-compose.yml ps
curl -I http://localhost:8080
```

Checkpoint:
- Service recovers and HTTP response is available again.

### Task 9.8: Cleanup

```bash
docker compose -f asset/module3/docker-compose.yml down
```

Optional full reset (removes volumes/data):

```bash
docker compose -f asset/module3/docker-compose.yml down -v
```

Trainer note:
- Use `down -v` only when intentionally resetting database/cache data.

## Mini Incident Drill (Optional, 10 min)
Scenario: Website reports `502 Bad Gateway`.

Execute the incident workflow:

1. Check health:

```bash
df -h
free -m
uptime
```

2. Check service state:

```bash
sudo systemctl status nginx --no-pager
```

3. Check logs:

```bash
sudo tail -n 40 /var/log/nginx/error.log
```

4. Validate config and recover:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

5. Confirm endpoint:

```bash
curl -I http://localhost
```

Success criteria:
- You can provide one evidence-based root-cause hypothesis.
- You can verify service recovery with command output.

## Practical Wrap-Up Checklist
Before ending session, confirm:

- [ ] Pre-change checklist note created.
- [ ] Health diagnostics completed and interpreted.
- [ ] Error log filtering performed.
- [ ] Nginx syntax tested before reload.
- [ ] Git deployment workflow completed (`status` -> `pull` -> verify).
- [ ] DNS lookup commands executed.
- [ ] UFW policy applied and reviewed.
- [ ] Shutdown scheduling and cancellation tested.
- [ ] Docker Compose extra lab completed (Nginx + db + cache + tools).

## Troubleshooting (Common Beginner Issues)
1. `systemctl` fails with "System has not been booted with systemd".
- Cause: You are likely inside a minimal container.
- Fix: Run this module on a real Ubuntu VM/VPS.

2. `ufw` command not found.
- Cause: Package not installed.
- Fix: `sudo apt update && sudo apt install -y ufw`.

3. `dig` command not found.
- Cause: `dnsutils` missing.
- Fix: `sudo apt install -y dnsutils`.

4. `git pull` fails due to local changes.
- Cause: Uncommitted file modifications in deployment folder.
- Fix: Review with `git status`, then resolve by commit/stash/reset according to team policy.

5. Nginx reload fails after config change.
- Cause: Syntax error in config.
- Fix: Run `sudo nginx -t`, identify bad file/line, correct it, then reload again.

6. Lost SSH access after firewall update.
- Cause: Port 22 was not allowed before enable.
- Fix: Use cloud console emergency access, add `ufw allow 22`, then review rules.

7. Docker Compose stack starts but `curl http://localhost:8080` fails.
- Cause: Nginx container not healthy yet or port 8080 is already used.
- Fix: Check `docker compose ... ps`, inspect `logs nginx`, or free port 8080.

8. `docker exec ... psql` fails with authentication error.
- Cause: Wrong DB username/password/database name.
- Fix: Use `ebright / ebright123 / ebright` as defined in `asset/module3/docker-compose.yml`.

## Extra Practice (Optional, 10 Minutes)
1. Export key health metrics to a timestamped report:

```bash
mkdir -p ~/ebright-ops/reports
{
  date
  echo "--- Disk ---"
  df -h
  echo "--- Memory ---"
  free -m
  echo "--- Load ---"
  uptime
} > ~/ebright-ops/reports/health-$(date +%F-%H%M).txt
ls -lh ~/ebright-ops/reports
```

2. Practice fast log filtering:

```bash
sudo grep -iE "error|warn|fail" /var/log/nginx/error.log | tail -n 30
```

3. Verify firewall again after a simulated rule adjustment:

```bash
sudo ufw status numbered
```
