---
title: Wiki Deployment
description: Deploying wiki
published: true
date: 2026-03-11T18:05:14.539Z
tags: wiki.js, integration
editor: markdown
dateCreated: 2026-03-11T18:05:14.539Z
---

# Wiki.js Deployment

## Docker Compose

Save this as `/home/YOUR_USER/pswiki/docker-compose.yml`:
```yaml
services:
  db:
    container_name: pswiki-db-1
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: wiki
      POSTGRES_USER: wikijs
      POSTGRES_PASSWORD: wikijsrocks
    volumes:
      - pswiki_db-data:/var/lib/postgresql/data
    networks:
      - wiki_net
  wiki:
    container_name: pswiki-wiki-1
    image: requarks/wiki:2
    depends_on:
      - db
    restart: unless-stopped
    user: "0:0"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    ports:
      - "80:3000"
    volumes:
      - /mnt/nas/wiki_uploads:/mnt/wiki_uploads
    networks:
      - wiki_net

volumes:
  pswiki_db-data:
    external: true

networks:
  wiki_net:
```

> **Warning:** The `user: "0:0"` setting runs Wiki.js as root inside the container. 
> This was required to allow write access to the NAS mount. This is acceptable in a 
> homelab environment but review for production use.
{.is-warning}

## Deploy
```bash
cd /home/YOUR_USER/pswiki
sudo docker compose up -d
```

## Generate API Key

1. Go to `https://YOUR_WIKI_DOMAIN/a/api`
2. Click **New API Key**
3. Name it `n8n-integration`
4. Set permissions to **Full Access**
5. Copy and save the key securely

## Verify
```bash
curl -X POST http://YOUR_SERVER_IP/graphql \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ pages { list { id title path } } }"}'
```

A JSON list of your pages confirms the API is working.