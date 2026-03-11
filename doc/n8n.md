---
title: n8n Deployment
description: n
published: true
date: 2026-03-11T22:22:29.075Z
tags: 
editor: markdown
dateCreated: 2026-03-11T20:45:42.011Z
---

# n8n Deployment

## Docker Compose (Portainer Stack)

In Portainer → Stacks → Add Stack, name it `n8n` and paste:
```yaml
version: "3.8"

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=YOUR_PASSWORD
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - WEBHOOK_URL=http://YOUR_SERVER_IP:5678/
      - GENERIC_TIMEZONE=America/New_York
      - N8N_SECURE_COOKIE=false
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=n8n_postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n_db
      - DB_POSTGRESDB_USER=n8n_user
      - DB_POSTGRESDB_PASSWORD=YOUR_DB_PASSWORD
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - n8n_postgres

  n8n_postgres:
    image: postgres:15
    container_name: n8n_postgres
    restart: unless-stopped
    environment:
      - POSTGRES_DB=n8n_db
      - POSTGRES_USER=n8n_user
      - POSTGRES_PASSWORD=YOUR_DB_PASSWORD
    volumes:
      - n8n_postgres_data:/var/lib/postgresql/data

volumes:
  n8n_data:
    driver: local
  n8n_postgres_data:
    driver: local
```

> **Warning:** Make sure `N8N_BASIC_AUTH_PASSWORD` and `POSTGRES_PASSWORD` use the 
> same value in both the n8n and n8n_postgres sections.
{.is-warning}

> **Info:** `N8N_SECURE_COOKIE=false` is required when accessing n8n over HTTP on a 
> local network. Not recommended for internet-facing deployments.
{.is-info}

## Access n8n
```
http://YOUR_SERVER_IP:5678
```

Complete the setup form on first launch.

## Troubleshooting: Postgres Volume Issues

If you see `password authentication failed for user "n8n_user"`:

1. Stop the stack in Portainer
2. Delete the postgres volume:
```bash
docker stop n8n n8n_postgres
docker rm n8n n8n_postgres
docker volume rm n8n_n8n_postgres_data
docker volume rm n8n_n8n_data
```
3. Redeploy the stack fresh

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc/wiki" class="back">Top 
        <span class="label"> Wiki.js Deployment</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/ollama" class="next">Next
      <span class="label">Ollama Setup</span>
      </a>
    </div>
  </div>
</li>
