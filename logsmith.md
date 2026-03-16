---
title: Logsmith Platform
description: 
published: true
date: 2026-03-16T03:17:53.180Z
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