---
title: Ollama Deployment
description: Self-hosted Ollama AI model
published: true
date: 2026-03-11T22:26:42.698Z
tags: ollama, ai, ai model
editor: markdown
dateCreated: 2026-03-11T22:26:42.698Z
---

# Ollama Deployment

## Docker Compose (Portainer Stack)

In Portainer → Stacks → Add Stack, name it `ollama` and paste:
```yaml
version: "3.8"

services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama

volumes:
  ollama_data:
    driver: local
```

## Pull AI Model

After deployment, pull the llama3.2:3b model:
```bash
docker exec -it ollama ollama pull llama3.2:3b
```

> **Info:** llama3.2:3b is approximately 2GB and requires around 2-3GB RAM to run. 
> Server 1 was upgraded to 10GB RAM to accommodate this alongside other services.
{.is-info}

## Verify Ollama
```bash
curl http://YOUR_SERVER_IP:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{"model": "llama3.2:3b", "prompt": "Write a one sentence description of a firewall", "stream": false}'
```

## Connect to n8n Network

Since n8n and Ollama are deployed in separate stacks they need a shared network:
```bash
sudo docker network create shared_network
sudo docker network connect shared_network ollama
sudo docker network connect shared_network n8n
```

## Verify n8n Can Reach Ollama
```bash
sudo docker exec n8n wget -qO- http://ollama:11434/api/tags
```

Should return a JSON list of installed models.

## List Installed Models
```bash
docker exec -it ollama ollama list
```

## Remove a Model
```bash
docker exec -it ollama ollama rm llama3.2:1b
```

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc/n8n" class="back">Top 
        <span class="label">n8n Setup</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/ollama" class="next">Next
      <span class="label">Ollama Deployment</span>
      </a>
    </div>
  </div>
</li>
