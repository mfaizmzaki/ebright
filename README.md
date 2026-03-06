# Ebright Linux Crash Course

## Introduction
This document provides the official 1-day training timetable for the Ebright Linux Crash Course. It outlines the full session flow across both training pillars, including lecture blocks, practical labs, review activities, and instructor implementation notes.

## Course Overview
The Ebright Linux Crash Course is designed to equip the IT and Administration team with practical skills for independent cloud server management.

Full detailed overview: [Training Overview](Training%20Overview.md)

## Quick Guide Links
- [Lecture Guide - Module 1](Lecture%20Guide%20-%20Module%201.md)
- [Practical Guide - Module 1](Practical%20Guide%20-%20Module%201.md)
- [Lecture Guide - Module 2](Lecture%20Guide%20-%20Module%202.md)
- [Practical Guide - Module 2](Practical%20Guide%20-%20Module%202.md)

### Pillar 1: System Identity, Access & Administration
- Focus: Establishing infrastructure foundations and organizational hierarchy.
- Objective: Build a professional server mindset through CLI operations, VPS administration, SSH security, filesystem navigation, and role-based access controls for internal data protection.
- Coverage: Linux fundamentals, remote access, filesystem hierarchy, users and permissions, automation basics, and core process/network operations.

### Pillar 2: Deployment, Security & Continuity
- Focus: Application delivery, monitoring, and system stability.
- Objective: Transition from system administration to application operations, including deployment workflows, system diagnostics, firewall hardening, backup planning, and recovery readiness.
- Coverage: Snapshots, package management, Git-based deployment, DNS identity, UFW firewall setup, and continuity procedures.

## Learning Outcome
By the end of the day, the Ebright IT team transitions from passive managed-host users to active cloud infrastructure administrators with practical survival skills to keep services stable, secure, and accessible.

General objectives:
- Build confidence using Linux CLI commands for daily server administration tasks.
- Apply secure access and identity practices, including SSH key usage and role-based permissions.
- Perform structured filesystem and user/group management aligned with departmental operations.
- Execute basic deployment and update workflows using package management and Git.
- Monitor system health, interpret core diagnostics, and respond to common operational incidents.
- Implement continuity controls through snapshots, backups, firewall hardening, and recovery readiness.

## Client
Ebright IT & Administration Team

## Date
10th March 2026

## Total Duration
8 Hours (`09:00 - 17:00`)

## Pillar 1: System Identity, Access & Administration

| Time | Session Type | Module | Key Focus |
| --- | --- | --- | --- |
| 09:00 - 09:45 | Lecture | [Module 1: Introduction, Fundamentals & Remote Access](Lecture%20Guide%20-%20Module%201.md) | Ecosystem (GUI vs CLI), Cloud/VPS theory, and SSH handshake/security. |
| 09:45 - 10:30 | Practical | [Module 1: Introduction, Fundamentals & Remote Access](Practical%20Guide%20-%20Module%201.md) | Docker sandbox setup, system verification, and SSH key generation. |
| 10:30 - 10:45 | Break | - | Morning coffee break. |
| 10:45 - 12:00 | Lecture | [Module 2: Mastering the Filesystem, Users & Permissions](Lecture%20Guide%20-%20Module%202.md) | The 8 core topics: FHS, security logic, productivity, and network operations. |
| 12:00 - 13:00 | Practical | [Module 2: Mastering the Filesystem, Users & Permissions](Practical%20Guide%20-%20Module%202.md) | Hands-on: Building Ebright's hierarchy, permissions, and automation. |
| 13:00 - 14:00 | Lunch | - | Networking lunch. |

## Pillar 2: Deployment, Security & Continuity

| Time | Session Type | Module | Key Focus |
| --- | --- | --- | --- |
| 14:00 - 15:00 | Lecture | Module 3: Deployment, Security & Continuity | Snapshots, package management (Apt), Git workflows, and DNS records. |
| 15:00 - 16:15 | Practical | Module 3: Deployment, Security & Continuity | Live lab: Nginx/Git deployment, Firewall (UFW) setup, and Snapshot Recovery. |
| 16:15 - 17:00 | Review | Final | Q&A, Emergency Escalation Protocols, and "Survival" Cheat Sheet distribution. |

## Session Breakdown Summary

### Pillar 1 Session
- Duration: `3.75 Hours` (Excluding Break)
- Emphasis: Transitioning to the "Server Mindset" and establishing the "Departmental Silo" structure to protect Ebright's internal data.

### Pillar 2 Session
- Duration: `3 Hours`
- Emphasis: Shifting from file administration to application delivery. Real-world deployment of the Ebright Parent Portal and perimeter hardening.

## Instructor Notes
- Practical Buffer: The Practical sessions include buffer time for students who may encounter Docker or SSH connectivity issues.
- Quiz Integration: Short 5-minute knowledge checks (Quizzes) should be performed at the start of Module 2 and Module 3 to refresh concepts.
- New Learner Flow: Module 2 now includes beginner checkpoints and troubleshooting patterns to improve classroom pacing and confidence.
