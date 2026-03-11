---
title: Architecture
description: 
published: true
date: 2026-03-11T15:21:40.951Z
tags: n8n
editor: markdown
dateCreated: 2026-03-11T15:21:40.951Z
---

# Automated Security Documentation Pipeline
## Project Overview

This project builds a fully self-hosted, automated documentation pipeline that monitors 
your infrastructure with Wazuh, processes security alerts with AI (Ollama), and 
automatically publishes documentation to Wiki.js.

> **Info:** This pipeline was built on Proxmox VE with all services running in Docker 
> containers managed by Portainer.
{.is-info}

## Architecture

| Component | Role | Server |
|-----------|------|--------|
| Wazuh | Security monitoring & alert generation | Server 2 (192.168.50.25) |
| n8n | Workflow automation | Server 1 (192.168.50.20) |
| Ollama (llama3.2:3b) | Local AI documentation generation | Server 1 (192.168.50.20) |
| Wiki.js | Documentation publishing | Server 1 (192.168.50.20) |
| Postgres (x2) | Databases for n8n and Wiki.js | Server 1 (192.168.50.20) |
| Nginx Proxy | Reverse proxy | Server 1 (192.168.50.20) |
| Asustor NAS | File storage for Wiki.js uploads | 192.168.60.20 |

## Data Flow
```
Wazuh (Server 2)
      ↓ webhook POST
n8n (Server 1)
      ↓ HTTP POST
Ollama/llama3.2:3b (Server 1)
      ↓ AI-generated markdown
Wiki.js GraphQL API (Server 1)
      ↓
Published documentation page
```

## Pages in This Series

1. [Project Overview](/auto-docs/overview) ← You are here
2. [Server & VM Setup](/auto-docs/server-setup)
3. [Docker & Portainer Setup](/auto-docs/docker-portainer)
4. [Wiki.js Deployment](/auto-docs/wikijs)
5. [n8n Deployment](/auto-docs/n8n)
6. [Ollama Deployment](/auto-docs/ollama)
7. [Wazuh Configuration](/auto-docs/wazuh)
8. [n8n Workflow Configuration](/auto-docs/n8n-workflow)
9. [NAS & Storage Configuration](/auto-docs/nas-storage)
10. [Troubleshooting Log](/auto-docs/troubleshooting)