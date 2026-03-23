---
title: Logsmith Platform
description: 
published: true
date: 2026-03-23T10:25:42.399Z
tags: 
editor: markdown
dateCreated: 2026-03-16T01:44:47.722Z
---

# Logsmith Platform

Session-based, multi-environment automated documentation platform. Tracks infrastructure activities across on-prem, hybrid, and cloud environments, then uses AI to generate documentation automatically.

---

## Project Phases

| Phase | Title | Status | Link |
|-------|-------|--------|------|
| 1 | Database and Workflows | Completed | [Phase 1](/logsmith/phase1) |
| 2 | Project Management | Completed | [Phase 2](/logsmith/phase2) |
| 3 | CLI Tool | Completed | [Phase 3](/logsmith/phase3) |
| 4 | Web Dashboard | Completed | [Phase 4](/logsmith/phase4) |
| 1 | Delete Feature | Completed | [Phase 1](/logsmith/phase1) |

---

## How It Works

1. **Start a session** — define what you're working on, which systems are involved
2. **Log activities** — record commands, changes, and observations as you work
3. **Stop the session** — AI automatically generates documentation and publishes it here

## Components

| Component | Purpose |
|-----------|---------|
| Postgres (logsmith_db) | Session and activity tracking |
| n8n Workflows | Webhook API for session management |
| Ollama (llama3.2:3b) | AI documentation generation |
| Wiki.js | Documentation publishing |

### Logsmith CLI usage guide
[CLI Usage Guide](/logsmith/cli_usage)