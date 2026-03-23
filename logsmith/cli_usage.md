---
title: Logsmith CLI Usage Guide
description: How to use the Logsmith in Terminal
published: true
date: 2026-03-23T10:28:54.557Z
tags: 
editor: markdown
dateCreated: 2026-03-23T10:28:54.557Z
---

# Logsmith CLI Usage Guide

Logsmith is a session-based infrastructure documentation tool. It tracks your work across on-prem, hybrid, and cloud environments and uses AI to automatically generate Wiki.js documentation when you're done.

---

## Installation

The CLI is installed at `/opt/logsmith/cli/logsmith.py` and symlinked to `/usr/local/bin/logsmith`.

Verify it's available:

```bash
logsmith --help
```

---

## Core Concepts

Before using Logsmith, understand how it organises your work:

- **Project** — A top-level body of work with a goal. Example: `Logsmith Platform`, `Azure Sentinel Setup`. Each project has a landing page on Wiki.js listing all its phases.
- **Phase** — A distinct stage or milestone within a project. Example: `Phase 1 - Database Setup`, `Phase 2 - CLI Tool`. Each phase gets its own Wiki.js documentation page.
- **Session** — A work sitting. A period of time where you did something and want it documented. Multiple sessions can contribute to one phase across multiple days.

```
Project
└── Phase (one Wiki.js page)
    ├── Session 1  (day 1 work)
    ├── Session 2  (day 2 work)
    └── Session 3  (day 3 — stop to generate docs)
```

---

## Quick Start

### 1. Start a session

```bash
logsmith start "My Session Name"
```

### 2. Log what you did

```bash
logsmith log "Installed and configured nginx"
```

### 3. Stop and generate documentation

```bash
logsmith stop
```

Logsmith sends your activities to AI, generates structured documentation, and publishes it to Wiki.js automatically.

---

## Commands Reference

### `logsmith start`

Start a new session.

```bash
logsmith start "Session Name" [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--mode` | `-m` | `manual` (default) or `record` (auto-capture terminal) |
| `--desc` | `-d` | Session description |
| `--env` | `-e` | Environment: `on-prem`, `hybrid`, `cloud`, `multi-cloud` |
| `--tags` | `-t` | Comma-separated tags |
| `--hosts` | | Comma-separated target hosts |
| `--services` | | Comma-separated target services |
| `--project` | `-p` | Project UUID to link this session to |
| `--phase` | | Phase number within the project |
| `--phase-title` | | Phase title |
| `--wiki-path` | | Custom Wiki.js page path |

**Examples:**

```bash
# Simple session
logsmith start "Nginx Configuration"

# Session with description and tags
logsmith start "Docker Setup" --desc "Installing Docker and Portainer" --tags "docker,portainer"

# Session linked to a project phase
logsmith start "Phase 4 - Dashboard" \
  --project 8f592588-3aa1-4101-afe3-bd003d108f4e \
  --phase 4 \
  --phase-title "Dashboard Build" \
  --wiki-path "logsmith/phase4"

# Session in recording mode
logsmith start "Infrastructure Audit" --mode record
```

---

### `logsmith log`

Log an activity to the active session. Use this in manual mode to record what you did.

```bash
logsmith log "Activity title" [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--desc` | `-d` | Detailed description |
| `--command` | `-c` | Command that was run |
| `--severity` | `-s` | `info`, `low`, `medium`, `high`, `critical` |
| `--source` | | `manual`, `cli`, `api`, `wazuh`, `n8n` |
| `--host` | `-h` | Target hostname |
| `--service` | | Target service name |

**Examples:**

```bash
# Basic activity
logsmith log "Installed nginx"

# Activity with command and description
logsmith log "Configured reverse proxy" \
  --command "nano /etc/nginx/sites-available/default" \
  --desc "Set up proxy pass to port 3000"

# High severity activity
logsmith log "Firewall rule change" \
  --severity high \
  --desc "Opened port 443 for HTTPS traffic"

# Activity targeting a specific host and service
logsmith log "Restarted web server" \
  --host "192.168.50.20" \
  --service "nginx"
```

---

### `logsmith stop`

Stop the active session and trigger AI documentation generation. The session is marked complete and a Wiki.js page is published.

```bash
logsmith stop [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--notes` | `-n` | Closing notes for the session |

**Examples:**

```bash
# Stop with no notes
logsmith stop

# Stop with closing notes
logsmith stop --notes "All services verified and running"
```

> **Note:** Use `stop` when the phase is fully complete. If you need a break and want to continue later, use `logsmith session pause` instead.

---

### `logsmith status`

Show the currently active session.

```bash
logsmith status
```

**Example output:**
```
  Active session: Nginx Configuration
  Session ID:     a4ce1816-cfa1-47e1-96fc-a52b603da4a2
  Mode:           MANUAL

  Log activities:  logsmith log "What you did"
  Stop session:    logsmith stop
```

---

## Session Commands

### `logsmith session pause`

Pause the active session without generating documentation. Your progress and all logged activities are saved. Resume later to continue where you left off.

> **Note:** In recording mode you cannot pause directly. Type `exit` in the recording shell first — you will be shown a menu with the option to pause.

```bash
logsmith session pause [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--notes` | `-n` | Reason for pausing |

**Examples:**

```bash
# Pause with no notes
logsmith session pause

# Pause with a reason
logsmith session pause --notes "Waiting for firewall change approval"
```

---

### `logsmith session resume`

Resume any session — paused, completed, or failed — and continue adding activities to it. You can resume a session in a different mode from how it was originally started.

```bash
logsmith session resume <SESSION_ID> [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--mode` | `-m` | `manual` (default) or `record` |
| `--notes` | `-n` | Notes on why you're resuming |

**Examples:**

```bash
# Resume a session in manual mode
logsmith session resume a4ce1816-cfa1-47e1-96fc-a52b603da4a2

# Resume a manual session in recording mode
logsmith session resume a4ce1816-cfa1-47e1-96fc-a52b603da4a2 --mode record

# Resume with a note
logsmith session resume a4ce1816-cfa1-47e1-96fc-a52b603da4a2 \
  --notes "Continuing after approval received"
```

> **Tip:** Use `logsmith session list` to find session IDs.

> **Mode flexibility:** You can resume a session in any mode regardless of how it was originally started. A session started manually can be resumed in record mode and vice versa.

---

### `logsmith session list`

List sessions so you can find one to resume.

```bash
logsmith session list [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--status` | `-s` | Filter by: `all` (default), `active`, `paused`, `completed`, `failed` |

**Examples:**

```bash
# List all sessions
logsmith session list

# List only paused sessions
logsmith session list --status paused

# List completed sessions
logsmith session list --status completed
```

**Example output:**
```
  Sessions:
  ID                                     Name                           Status       Date
  -------------------------------------- ------------------------------ ------------ ----------
  5e3bd8a5-d67d-4bb5-8c99-a28d7c27c644   Session Continuity Test        completed    2026-03-21
  3ace14e4-a29e-461c-b20a-f102c0c85e60   Nginx Configuration            paused       2026-03-20
  a4ce1816-cfa1-47e1-96fc-a52b603da4a2   Docker Setup                   completed    2026-03-19
```

---

### `logsmith session delete`

Permanently delete a session and all its activities. If the session has a published Wiki.js page, that page is also deleted. This is a hard delete and cannot be undone.

```bash
logsmith session delete <SESSION_ID>
```

You will be prompted to confirm before deletion proceeds.

**Example:**

```bash
logsmith session delete a4ce1816-cfa1-47e1-96fc-a52b603da4a2
```

**Example output:**
```
  Delete session: Nginx Configuration
  This will permanently delete the session and all its activities.
  If a Wiki.js page exists it will also be deleted.

  Are you sure? [y/N]: y

  Session deleted: Nginx Configuration
```

> **Tip:** Use `logsmith session list` to find the session ID before deleting.

---

## Project Commands

### `logsmith project create`

Create a new project. Projects group related phases and sessions under a single Wiki.js landing page.

```bash
logsmith project create "Project Name" --path <wiki-path> [OPTIONS]
```

| Option | Short | Description |
|--------|-------|-------------|
| `--path` | `-p` | Wiki.js base path (required) e.g. `azure-sentinel` |
| `--desc` | `-d` | Project description |
| `--tags` | `-t` | Comma-separated tags |
| `--category` | `-c` | Sidebar category (default: `In Progress`) |

**Examples:**

```bash
# Create a basic project
logsmith project create "Azure Sentinel Setup" --path "azure-sentinel"

# Create a project with full details
logsmith project create "Wazuh SIEM Deployment" \
  --path "wazuh-siem" \
  --desc "Full Wazuh SIEM deployment and configuration" \
  --tags "wazuh,siem,security" \
  --category "Security"
```

> **Save the Project ID** from the output — you'll need it when starting sessions linked to this project.

---

### `logsmith project delete`

Permanently delete a project and everything associated with it — all phases, all session activities, and all Wiki.js pages including the project landing page. This is a hard delete and cannot be undone.

```bash
logsmith project delete <PROJECT_ID>
```

You will be prompted to confirm before deletion proceeds.

**Example:**

```bash
logsmith project delete 8f592588-3aa1-4101-afe3-bd003d108f4e
```

**Example output:**
```
  Delete project: Azure Sentinel Setup
  This will permanently delete the project, all its phases,
  all activities, and all Wiki.js pages.

  Are you sure? [y/N]: y

  Project deleted: Azure Sentinel Setup
```

> **Warning:** There is no recovery from this. All sessions and Wiki pages belonging to the project are permanently removed.

---

## Recording Mode

Recording mode automatically captures every command you run and its output, then uses AI to generate documentation from the recording.

### Starting a recording session

```bash
logsmith start "Infrastructure Audit" --mode record
```

Once started, you are inside a recording shell. Work normally — everything is captured.

```
  ╔══════════════════════════════════════════════════╗
  ║  LOGSMITH SESSION STARTED (RECORDING MODE)      ║
  ╠══════════════════════════════════════════════════╣
  ║  Session: Infrastructure Audit                  ║
  ║                                                  ║
  ║  All commands and output are being captured.     ║
  ║  Work normally — everything is recorded.         ║
  ║                                                  ║
  ║  Type 'exit' when done to stop and generate     ║
  ║  documentation automatically.                    ║
  ╚══════════════════════════════════════════════════╝
```

### Stopping a recording session

Type `exit` in the recording shell. Logsmith will stop the recording and present a menu:

```
  Recording stopped.

  What would you like to do?

  [1] Generate documentation and complete session
  [2] Save progress and pause session
  [3] Discard recording and cancel session

  Enter choice:
```

| Choice | What happens |
|--------|-------------|
| **1** | Commands are logged as activities, AI generates documentation, Wiki.js page published. Session marked complete. |
| **2** | Commands are logged as activities, session is paused. No documentation generated. Resume later to continue. |
| **3** | Recording is discarded, no activities logged, session is cancelled. |

### How the parser works

The parser handles both **typed commands** and **pasted command blocks**:

- **Typed commands** are detected by shell prompt patterns (`user@host:/path$` or `#` for root)
- **Pasted blocks** are detected by matching the first word against a whitelist of known command verbs

Commands like `clear`, `exit`, and `history` are automatically filtered out as noise. The AI filtering step then removes any remaining low-value activities before generating documentation.

### Adding commands to the parser whitelist

If a command you use isn't being captured from paste blocks, add it to `COMMAND_VERBS` in `/opt/logsmith/cli/logsmith.py`:

```python
COMMAND_VERBS = {
    ...
    'terraform', 'kubectl', 'helm',  # add your commands here
}
```

---

## Common Workflows

### Single-day manual session

```bash
logsmith start "Configure Fail2Ban"
logsmith log "Installed fail2ban" --command "apt install fail2ban"
logsmith log "Configured jail.local" --command "nano /etc/fail2ban/jail.local"
logsmith log "Enabled and started service" --command "systemctl enable fail2ban"
logsmith stop
```

### Single-day recording session

```bash
logsmith start "Configure Fail2Ban" --mode record

# Inside recording shell — work normally
apt install fail2ban
nano /etc/fail2ban/jail.local
systemctl enable fail2ban

# Type exit when done, then choose option 1
exit
```

### Multi-day manual phase

**Day 1:**
```bash
logsmith start "Phase 5 - Monitoring Setup" \
  --project 8f592588-3aa1-4101-afe3-bd003d108f4e \
  --phase 5 \
  --phase-title "Monitoring Setup"

logsmith log "Installed Prometheus"
logsmith log "Configured scrape targets"
logsmith session pause --notes "Continuing tomorrow"
```

**Day 2:**
```bash
# Find the session ID
logsmith session list --status paused

# Resume it
logsmith session resume <SESSION_ID>

logsmith log "Installed Grafana"
logsmith log "Created dashboards"
logsmith stop --notes "Monitoring stack fully operational"
```

### Multi-day recording phase

**Day 1:**
```bash
logsmith start "Phase 5 - Monitoring Setup" \
  --mode record \
  --project 8f592588-3aa1-4101-afe3-bd003d108f4e \
  --phase 5 \
  --phase-title "Monitoring Setup"

# Work inside the recording shell...
exit
# Choose option 2 — Save progress and pause session
```

**Day 2:**
```bash
# Find the paused session
logsmith session list --status paused

# Resume in record mode
logsmith session resume <SESSION_ID> --mode record

# Work inside the recording shell...
exit
# Choose option 1 — Generate documentation and complete session
```

### Resume a completed session to add more detail

```bash
# List completed sessions
logsmith session list --status completed

# Resume it
logsmith session resume <SESSION_ID>

logsmith log "Added additional firewall rule" --severity medium
logsmith stop
```

### Switch modes mid-phase

```bash
# Start manually
logsmith start "Complex Setup"
logsmith log "Planned the architecture"
logsmith session pause

# Resume in record mode to capture the actual commands
logsmith session resume <SESSION_ID> --mode record

# Work inside recording shell...
exit
# Choose option 1 to complete
```

---

## Session Lifecycle

```
active → paused    ─────────────────┐
active → completed                   ├──→ active (resumed) → ... → completed
active → failed    ─────────────────┘
active → cancelled
```

| Status | Description |
|--------|-------------|
| `active` | Session is currently in progress |
| `paused` | Session is saved, work in progress, no docs generated yet |
| `completed` | Session is done, documentation published to Wiki.js |
| `failed` | Session encountered an error |
| `cancelled` | Session was discarded |

> Any session except `cancelled` can be resumed and continued.

> Any session can be permanently deleted with `logsmith session delete <SESSION_ID>`.

---

## Tips

- **Pause, don't stop** — if a phase spans multiple days, use `logsmith session pause` instead of `logsmith stop`. Stop triggers doc generation, pause saves progress without generating.
- **Session IDs** — always use `logsmith session list` or `logsmith status` to find session IDs rather than trying to remember them.
- **Recording mode works best** for sessions where you run discrete commands. Highly interactive tools like `htop`, `nano`, or `psql` interactive shell will be captured but their output may be noisy.
- **AI filtering** — Logsmith's AI automatically removes noise (repeated commands, failed attempts, diagnostics) and keeps only essential and troubleshooting activities in the documentation.
- **Wiki.js pages** — documentation is published automatically at the path you specify. If no path is given, Logsmith generates one based on the session name and date.
- **Mode flexibility** — sessions are not locked to a mode. A session started manually can be resumed in recording mode and vice versa.
- **Deleting sessions** — use `logsmith session delete` to remove test sessions or sessions started by mistake. The Wiki.js page is deleted along with the session.
- **Deleting projects** — use `logsmith project delete` to remove an entire project and all its phases. This is irreversible — all Wiki.js pages are also deleted.
