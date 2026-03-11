---
title: Server and Virtual Machine Setup
description: Servers and VMs needed for the project.
published: true
date: 2026-03-11T20:09:16.716Z
tags: wazuh, n8n, ollama, siem
editor: markdown
dateCreated: 2026-03-11T15:21:40.951Z
---

# Server & VM Setup{#server}
## Prerequisites

- Proxmox VE installed and accessible
- At least two VMs planned:
  - **Server 1** (192.168.50.20) – Docker host for n8n, Wiki.js, Ollama
  - **Server 2** (192.168.50.25) – Wazuh SIEM

> **Info:** Server 1 was initially provisioned with 4GB RAM but was later upgraded 
> to 10GB to support Ollama running llama3.2:3b alongside other containers.
{.is-info}

## Server 1 Specifications (Final)

| Setting | Value |
|---------|-------|
| RAM | 10GB |
| CPU | 2+ cores |
| OS | Ubuntu 24 |
| IP | 192.168.50.20 |
| Role | Docker host |

## Server 2 Specifications

| Setting | Value |
|---------|-------|
| RAM | 4GB+ |
| OS | Ubuntu 24 |
| IP | 192.168.50.25 |
| Role | Wazuh SIEM |

## Expanding VM RAM in Proxmox

> **Warning:** Shut down the VM before changing RAM allocation.
{.is-warning}

1. Log into **Proxmox VE dashboard**
2. Select the VM in the left sidebar
3. Click **Hardware** tab
4. Select **Memory** → Click **Edit**
5. Change value to **10240 MB** (10GB)
6. Click **OK**
7. Click **Start** to boot the VM
8. Verify on the VM:
```bash
free -h
```
Expected output shows ~9.7Gi total memory.

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc" class="back">Top 
        <span class="label">Overview</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/docker_portainer" class="next">Next
      <span class="label">Docker & Portainer Setup</span>
      </a>
    </div>
  </div>
</li>
