---
title: Automated Security Documentation Pipeline
description: Automated documentation pipeline that monitors and document your home lab environment
published: true
date: 2026-03-11T19:58:34.861Z
tags: documentation, pipeline, self-hosted, automated, monitor
editor: markdown
dateCreated: 2026-03-11T19:58:34.861Z
---

# Automated Security Documentation Pipeline
## Project Overview{#doc}

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

1. [Project Overview](/doc/overview) ← You are here
2. [Server & VM Setup](/doc/servers)
3. [Docker & Portainer Setup](/docker_portainer)
4. [Wiki.js Deployment](/doc/wikijs)
5. [n8n Deployment](/doc/n8n)
6. [Ollama Deployment](/doc/ollama)
7. [Wazuh Configuration](/doc/wazuh)
8. [n8n Workflow Configuration](/doc/n8n_workflow)
9. [NAS & Storage Configuration](/doc/nas_storage)
10. [Troubleshooting Log](/doc/troubleshooting)

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#doc" class="back">Top 
        <span class="label"> Beginning</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/servers" class="next">Next
      <span class="label">Servers Setup</span>
      </a>
    </div>
  </div>
</li>
