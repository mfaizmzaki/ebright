# Lecture Guide | Module 3: Deployment, Security & Continuity

**Objective:** To transition from managing files to managing live applications. By the end of this session, the team will be able to deploy the Ebright Parent Portal, monitor server health, and secure the network perimeter.

**Session Duration:** 2.5 to 3.5 hours  
**Delivery Mode:** Instructor-led with guided terminal demos and scenario drills  
**Tools:** Ubuntu Server (or Docker Ubuntu container), SSH access, Git, Docker, Nginx, UFW, terminal log access

## Practical Alignment (Module 3)
This lecture is designed to feed directly into `Practical Guide - Module 3.md`.

- Lecture Sections 1 to 3 map to pre-deployment safety and diagnostics labs.
- Lecture Sections 4 to 5 map to service management and Git/Docker deployment labs.
- Lecture Sections 6 to 8 map to DNS, firewall, and continuity labs.

Trainer note:
- Run each command in a "purpose -> execute -> verify" rhythm to reduce copy-paste behavior.

## Learning Outcomes
By the end of this module, learners should be able to:
- Explain when to use snapshots versus backups and apply the pre-change safety workflow.
- Check disk, memory, CPU, and load health using core Linux diagnostics.
- Investigate service failures using system, web server, and authentication logs.
- Manage software packages and control services with `apt` and `systemctl`.
- Perform safe Git-based and Docker image-based deployment updates on a live server.
- Explain core DNS record types and propagation behavior for domain routing.
- Apply baseline firewall hardening using UFW without locking out SSH access.
- Describe continuity practices using the 3-2-1 backup model and controlled shutdown procedures.

## Beginner Guardrails (Production Mindset)
Repeat these rules throughout delivery:

- Snapshot before risk: always capture recovery state before upgrades or config edits.
- Verify before restart: test service configuration where possible, then restart.
- One change at a time: avoid stacking multiple risky changes in a single window.
- Watch logs live while testing: do not guess root cause.
- Keep SSH open first in firewall changes: avoid self-lockout.

## Delivery Flow (Recommended)
- 10 minutes: Recap quiz from Module 2 + explain today's incident-first mindset.
- 30 minutes: Snapshot/rollback discipline + health checks.
- 35 minutes: Logs and root cause workflow.
- 45 minutes: Nginx/package operations + Git and Docker deployment routines.
- 25 minutes: DNS and UFW hardening sequence.
- 15 minutes: Continuity, shutdown drills, and exit ticket.

## Pre-Class Checklist
- Confirm learners can SSH into the practice server.
- Ensure `nginx`, `git`, `docker`, and `ufw` are installed in the training environment.
- Prepare sample logs or a controlled error scenario for troubleshooting demo.
- Confirm learners have a test domain example (`portal.ebright.edu.my`) for DNS discussion.
- Prepare a rollback example (snapshot or documented restore point).

Fast pre-flight commands for trainer:

```bash
whoami
hostnamectl
sudo systemctl status nginx --no-pager
```

## 1. Infrastructure Safety: Snapshots & Restores

Before major updates or configuration changes on a production server, administrators need a guaranteed rollback path. Snapshots provide that safety net by preserving full machine state at a specific moment.

### 1.1 Understanding Snapshots vs Backups

**Snapshots**
- Point-in-time image of the full virtual machine state (disk, OS, and configuration).
- Best for rapid rollback before risky maintenance.

**Backups**
- Copies of selected data (databases, student files, exports) to another location.
- Best for long-term retention, compliance, and disaster recovery.

### 1.2 The Golden Rule for Ebright
- Always take a snapshot before `apt upgrade` or major Nginx configuration edits on the live Parent Portal server.

### 1.3 Pre-Change Safety Checklist
Before touching production, confirm:

- Snapshot is created and labeled with date/time and change purpose.
- Current service state is documented (`systemctl status nginx --no-pager`).
- Current deployment commit is recorded (`git rev-parse --short HEAD` in app folder).
- Maintenance window and rollback owner are confirmed.
- A verification checklist exists for after change (portal access, login page, API health).

### 1.4 Instructor Scenario (5-8 min)
Scenario prompt:
- "You must update packages and edit Nginx virtual host rules during a short maintenance window. What is step zero?"

Expected learner answer:
- Create snapshot first, then proceed.

Debrief prompt:
- "If the website fails after change, what is your first action: guess, hotfix, or rollback plan review?"

## 2. System Diagnostics: Monitoring Server Health

A stable application depends on three critical resources: disk, memory, and CPU. If any resource saturates, the Parent Portal can become slow, unstable, or unavailable.

### 2.1 Resource Monitoring Commands

- `df -h`: Disk usage overview. Full disks can block database writes and logs.
- `free -m`: Memory and swap in MB. Persistent high swap suggests RAM pressure.
- `top` / `htop`: Real-time CPU and process usage.
- `uptime`: Shows uptime and load average (short, medium, long-term system stress).

### 2.2 Quick Interpretation Guide
- Disk warning level: less than 15 percent free on critical partitions (`/`, `/var`).
- Memory warning level: swap continuously growing during normal load.
- CPU warning level: sustained high CPU with rising request latency.
- Load average context: compare load against available CPU cores, not as a standalone number.

### 2.3 Guided Demo (10-12 min)

```bash
df -h
free -m
uptime
top
```

Discussion prompts:
- Which partition is closest to full?
- Is swap usage unusually high?
- Is load average reasonable for the server size?

Success criteria:
- Learners can identify one potential bottleneck from command output.

Checkpoint question:
- "If disk usage is 98 percent but CPU is low, which problem do you solve first and why?"

## 3. Advanced Troubleshooting: Log Management

In Linux operations, logs are evidence. Professional troubleshooting means reading logs to understand sequence and cause, not relying on trial-and-error.

### 3.1 Critical Log Locations

- `/var/log/syslog`: General OS and service events.
- `/var/log/nginx/error.log`: Primary source for web app access failures.
- `/var/log/auth.log`: Login attempts and authentication events (useful for brute-force detection).

### 3.2 Log Analysis Tools

- `tail -f [file]`: Follow live updates while reproducing an issue.
- `grep -i "error" [file]`: Filter large logs for relevant lines.

Useful variants:
- `grep -iE "error|fail|denied" [file]`: Search multiple failure keywords.
- `tail -n 100 [file]`: Start from the latest 100 lines before live following.

### 3.3 Structured Log Triage Pattern
Use this order to avoid random troubleshooting:

1. Confirm when issue started (ask user or check monitoring timeline).
2. Start with application-facing log (`/var/log/nginx/error.log`).
3. Correlate with system log (`/var/log/syslog`) in the same timeframe.
4. Check authentication log only if access/security symptoms exist.
5. Form one hypothesis, test, then validate.

### 3.4 Guided Demo (10-15 min)

```bash
sudo tail -f /var/log/nginx/error.log
```

In a second terminal:

```bash
sudo grep -i "error" /var/log/nginx/error.log | tail -n 20
sudo grep -i "failed" /var/log/auth.log | tail -n 20
```

Success criteria:
- Learners can locate a probable error line and explain why it matters.

Trainer tip:
- Start with newest lines (`tail`) before scanning full logs.

Beginner trap to address:
- Not every line containing "warning" is the root cause. Confirm timestamp and component.

## 4. The Web Stack: Nginx & Software Management

The Ebright platform runs on a LEMP-style architecture. Keeping services healthy requires both package lifecycle management and service control discipline.

### 4.1 Package Management with Apt

- `sudo apt update`: Refresh package index (what versions are available).
- `sudo apt install nginx`: Install web server package.

### 4.2 Service Control with Systemd

- `systemctl status nginx`: Check service state and recent logs.
- `systemctl restart nginx`: Apply config changes or recover from failed state.
- `systemctl reload nginx`: Reload config gracefully without full process restart.
- `systemctl enable nginx`: Auto-start on reboot.

### 4.3 Safe Nginx Change Sequence
Use this exact sequence for config updates:

1. Edit configuration.
2. Test syntax: `sudo nginx -t`.
3. Apply with `sudo systemctl reload nginx` when possible.
4. If reload fails or service is unhealthy, inspect `systemctl status nginx` and logs.

### 4.4 Guided Demo (10-12 min)

```bash
sudo apt update
sudo apt install -y nginx
sudo nginx -t
sudo systemctl status nginx --no-pager
sudo systemctl reload nginx
sudo systemctl enable nginx
```

Success criteria:
- Learners can confirm Nginx is active and enabled.

Trainer callout:
- Teach `reload` for config-only changes and reserve `restart` for deeper recovery.

## 5. Modern Deployment: Git and Docker Workflows

Recommended section timing: 20 to 30 minutes (Git baseline + Docker extension).

Modern teams deploy via Git instead of manual file uploads. This gives version history, traceability, and quick rollback options when updates fail.

### 5.1 Core Deployment Commands

- `git clone [URL]`: Get fresh code copy to deployment path.
- `git pull`: Update existing deployment with latest remote changes.
- `git status`: Check local changes before pull to avoid conflicts.
- `git log --oneline -n 5`: Review recent deployable history quickly.

Quick collaboration note (branching):
- Developers should create feature branches (for example `feature/login-fix`) and open Pull Requests for review before merge.
- Deployment servers should pull from reviewed branches such as `main`/`release`, not directly from personal feature branches.

### 5.2 Guided Deployment Flow (12-15 min)

```bash
cd /var/www
sudo git clone [URL] html
cd /var/www/html
git status
git log --oneline -n 5
git pull
```

Operational reminder:
- Run `git status` before `git pull` to detect local edits that could break deployment.

### 5.3 Deployment Safety Pattern
- Pull code.
- Run post-deploy checks (service status, endpoint test, key page load).
- Keep previous commit hash recorded for rollback reference.

Rollback concept example:
- If latest deployment fails and rollback is approved, return to known-good commit and re-apply service checks.

Success criteria:
- Learners can explain the difference between first deployment (`clone`) and update deployment (`pull`).

### 5.4 Docker Image-Based Deployment Model

For stronger release consistency, teams often deploy Docker images instead of running `git pull` on production servers.

Role split to emphasize:
- **Developer side:** build image from source, tag, and push to registry.
- **Server side:** pull approved image and run container (no source checkout required).

Why this pattern is common:
- Same artifact across environments (dev/staging/prod).
- Faster rollback by switching image tag.
- Less build complexity on production hosts.

### 5.5 Guided Demo: Registry-Based Flow (8-10 min)

Use the same training image naming convention as the practical:

```bash
# Developer side
cd asset/module3/deploy
docker build -t docker.io/ebright-training/module3-portal:latest .
docker login
docker push docker.io/ebright-training/module3-portal:latest

# Server side
docker pull docker.io/ebright-training/module3-portal:latest
docker run -d --name ebright-portal -p 8888:80 docker.io/ebright-training/module3-portal:latest
curl -I http://localhost:8888
```

Trainer callout:
- The deployment server pulls a tested image artifact; it does not rebuild from source during release.

### 5.6 Optional Offline Pattern: `docker save` + `scp` + `docker load`

If registry access is restricted, transfer the image tarball manually:

```bash
# Developer side
docker save -o module3-portal-latest.tar docker.io/ebright-training/module3-portal:latest
scp module3-portal-latest.tar user@SERVER_IP:/tmp/

# Server side
docker load -i /tmp/module3-portal-latest.tar
docker run -d --name ebright-portal -p 8888:80 docker.io/ebright-training/module3-portal:latest
```

Teaching point:
- This keeps deployment artifact-based even without a registry.

## 6. Network Identity: DNS & Domain Routing

DNS maps human-readable names to network endpoints. Correct DNS records ensure students and parents reach the right server consistently.

### 6.1 Essential DNS Record Types

- **A Record:** Maps domain to IPv4 address.
- **CNAME:** Maps one domain name to another domain name.
- **MX Record:** Routes email to designated mail server.
- **TTL (Time to Live):** Controls how long DNS responses are cached.

### 6.2 Instructor Walkthrough (5-8 min)
Use `portal.ebright.edu.my` examples:
- A record points to portal server public IP.
- CNAME could point `www.portal.ebright.edu.my` to `portal.ebright.edu.my`.
- Explain why lower TTL helps during migrations, but increases DNS query frequency.

Knowledge check prompt:
- "Why might a DNS change appear to work for one user but not another immediately?"

### 6.3 Validation Commands (Optional Demo)
Use these checks to verify DNS behavior after changes:

```bash
dig portal.ebright.edu.my +short
nslookup portal.ebright.edu.my
```

Interpretation hint:
- If results differ between networks, cached TTL is often the reason.

## 7. Perimeter Security: Hardening with UFW

A default server can expose unnecessary ports. Firewall policy should follow least exposure: open only required ports.

### 7.1 Core UFW Commands

- `sudo ufw allow 22`: Keep SSH reachable.
- `sudo ufw allow 80`: Allow HTTP traffic.
- `sudo ufw allow 443`: Allow HTTPS traffic.
- `sudo ufw default deny incoming`: Deny all unsolicited inbound traffic by default.
- `sudo ufw default allow outgoing`: Keep outbound traffic open for updates and APIs.
- `sudo ufw enable`: Activate firewall rules.
- `sudo ufw status numbered`: Show active rules with index numbers.

### 7.2 Safe Enable Sequence (8-10 min)

```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo ufw status numbered
```

Critical warning:
- Always allow SSH (`22`) before enabling firewall.

Success criteria:
- Learners can verify only required public ports are open.

Recovery tip:
- If a wrong rule is added, remove by number using `sudo ufw delete <rule-number>`.

## 8. Data Continuity & Operational Readiness

Continuity is not only about backups. It also includes controlled operations during maintenance and emergency events.

### 8.1 The 3-2-1 Backup Rule

- `3` copies of data: production + two backups.
- `2` media types: for example cloud disk + object storage.
- `1` copy off-site: different geographic region.

### 8.2 Emergency Power Operations

- `shutdown -h +5 "Scheduled Maintenance"`: Notify users and halt in 5 minutes.
- `reboot`: Restart server after updates or recovery actions.
- `shutdown -c`: Cancel scheduled shutdown.

Operational caution:
- Run shutdown and reboot only during approved windows unless incident response requires immediate action.

### 8.3 Guided Demo (5-8 min)

```bash
sudo shutdown -h +5 "Scheduled Maintenance"
sudo shutdown -c
```

Trainer note:
- Demonstrate cancel operation immediately in training to avoid unintended shutdown.

### 8.4 Maintenance Window Runbook (Compact)
Use this mini runbook for controlled operations:

1. Announce maintenance window and expected impact.
2. Confirm snapshot and backup status.
3. Apply one change batch.
4. Validate health, logs, and user-facing checks.
5. Close window with a short change report.

## Incident Drill (Optional, 15 min)
Use this mini incident to connect all sections:

1. Portal returns `502` error.
2. Check health (`df -h`, `free -m`, `uptime`).
3. Inspect logs (`/var/log/nginx/error.log`, `/var/log/syslog`).
4. Validate service state (`systemctl status nginx`).
5. Apply fix and restart service.
6. Confirm firewall and DNS assumptions.
7. Document actions and rollback point.

Extension drill:
- Simulate failed `nginx -t` and ask learners to identify the syntax error line before reload.

## Knowledge Check (Exit Ticket)
1. Why is a snapshot mandatory before risky server changes?
2. Which command helps you monitor CPU usage in real time?
3. Where should you first look when the website is not loading: `/var/log/auth.log`, `/var/log/nginx/error.log`, or `/etc/hosts`?
4. What is the operational difference between `git clone` and `git pull`?
5. Why must port `22` be allowed before enabling UFW?
6. What does TTL influence in DNS behavior?
7. Explain the 3-2-1 backup rule in one sentence.
8. In Docker-based deployment, why does the server run `docker pull` (or `docker load`) instead of `git pull`?

## Module 3 Summary
- Safe operations begin with rollback planning using snapshots.
- Stable applications depend on continuous resource monitoring and log-driven troubleshooting.
- Deployment discipline combines package management, service control, and Git/Docker artifact workflows.
- Network reliability requires correct DNS records and propagation awareness.
- Security and continuity are achieved through firewall hardening, backup strategy, and controlled maintenance operations.

## Trainer Appendix: Common Failure Patterns
- Symptom: Website down, `nginx` inactive.
- Likely checks: `systemctl status nginx`, `nginx -t`, `/var/log/nginx/error.log`.
- Symptom: Website slow, no obvious errors.
- Likely checks: `free -m`, `top`, `uptime`, disk usage on `/var`.
- Symptom: SSH login failures spike.
- Likely checks: `/var/log/auth.log`, UFW status, key authentication setup.
- Symptom: DNS updated but inconsistent access.
- Likely checks: TTL, recursive resolver cache, record correctness (`A` vs `CNAME`).
