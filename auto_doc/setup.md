---
title: Docker & Portainer Setup
description: Initial Setup
published: true
date: 2026-03-11T17:58:08.722Z
tags: docker, portainer, shared network
editor: markdown
dateCreated: 2026-03-11T17:12:24.763Z
---

# Docker & Portainer Setup

## Install Docker on Server 1
```bash
sudo apt update
sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

## Install Portainer
```bash
docker volume create portainer_data
docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

Access Portainer at: `http://YOUR_SERVER_IP:9443`

## Create Shared Network

All containers that need to communicate must be on the same Docker network.
```bash
sudo docker network create shared_network
```

> **Info:** This network was created later in the project to connect n8n and Ollama 
> containers which were deployed in separate stacks.
{.is-info}

## Connect Existing Containers to Shared Network
```bash
sudo docker network connect shared_network ollama
sudo docker network connect shared_network n8n
```

## Verify Network Connectivity
```bash
sudo docker network inspect shared_network
```

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/auto_doc" class="back">Top 
        <span class="label">Server & VM Setup </span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/auto_doc/setup/wiki" class="next">Next
      <span class="label">Wiki Deployment</span>
      </a>
    </div>
  </div>
</li>