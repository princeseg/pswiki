---
title: Troubleshooting Log
description: Troubleshooting logs through digging into system and application records to pinpoint where and why some processes failed
published: true
date: 2026-03-11T23:05:39.643Z
tags: logs, troubleshoot, syntax error, cookie error, forbidden, parameter error, json
editor: markdown
dateCreated: 2026-03-11T23:05:39.643Z
---

# Troubleshooting Log

This page documents the key issues encountered during the project and their solutions.

---

## 1. n8n YAML Syntax Error

**Error:** `YAMLSyntaxError: All collection items must start at the same column`

**Cause:** Inconsistent indentation (tabs mixed with spaces) in docker-compose.

**Fix:** Use exactly 2 spaces for each indentation level. Never use tabs in YAML.

---

## 2. n8n Secure Cookie Error

**Error:** `Your n8n server is configured to use a secure cookie`

**Cause:** Accessing n8n over HTTP instead of HTTPS.

**Fix:** Add to n8n environment variables:
```yaml
- N8N_SECURE_COOKIE=false
```

---

## 3. n8n Postgres Authentication Failed

**Error:** `password authentication failed for user "n8n_user"`

**Cause:** Postgres volume contained old credentials from a previous deployment, 
skipping initialization with new credentials.

**Fix:**
```bash
docker stop n8n n8n_postgres
docker rm n8n n8n_postgres
docker volume rm n8n_n8n_postgres_data
docker volume rm n8n_n8n_data
```
Then redeploy the stack fresh.

---

## 4. Webhook Not Registered

**Error:** `The requested webhook "POST wazuh-alerts" is not registered`

**Cause:** Workflow was not published/activated in n8n.

**Fix:** Click the **Publish** button in the top right of the n8n workflow editor.

---

## 5. n8n Cannot Reach Ollama

**Error:** Connection refused when n8n tries to reach Ollama.

**Cause:** n8n and Ollama were deployed in separate Docker stacks with separate networks.

**Fix:**
```bash
sudo docker network create shared_network
sudo docker network connect shared_network ollama
sudo docker network connect shared_network n8n
```

---

## 6. Wiki.js API Returns Forbidden

**Error:** `{"message":"Forbidden"}`

**Cause:** API key did not have write permissions.

**Fix:** In Wiki.js Admin → API Access, regenerate the key with **Full Access** permissions.

---

## 7. Wiki.js Permission Denied on /mnt/nas

**Error:** `EACCES: permission denied, mkdir '/mnt/nas'`

**Cause (1):** Wiki.js storage was configured with path `/mnt/nas/wiki_uploads/images` 
but the container mounted the NAS at `/mnt/wiki_uploads`.

**Fix:** Update storage path in Wiki.js Admin → Storage → Local File System to:
```
/mnt/wiki_uploads/images
```

**Cause (2):** The `/mnt/nas` parent directory was owned by root.

**Fix:**
```bash
sudo chmod 777 /mnt/nas
sudo chown 1000:1000 /mnt/nas
```

**Cause (3):** CIFS mount was using `uid=1003` but Wiki.js runs as `uid=1000`.

**Fix:** Update `/etc/fstab` to use `uid=1000,forceuid,forcegid`.

**Cause (4):** Wiki.js Docker container running as non-root couldn't write to NAS mount.

**Fix:** Add `user: "0:0"` to Wiki.js service in docker-compose.yml then redeploy:
```bash
sudo docker compose down
sudo docker compose up -d
```

---

## 8. n8n JSON Parameter Error

**Error:** `JSON parameter needs to be valid JSON`

**Cause:** The Ollama response contained special characters, quotes and newlines that 
broke n8n's JSON template parsing when embedded directly in the HTTP Request body.

**Fix:** Use a **Code node** before the Wiki.js HTTP Request node to properly escape 
the content using `JSON.stringify()`. Then in HTTP Request use `Using Fields Below` 
with the `query` field referencing `{{ $json.query }}`.

---

## 9. Filter Node Blocking All Data

**Symptom:** HTTP Request1 (Wiki.js) showed "No items were sent on this branch"

**Cause:** Filter node condition referenced a field path that didn't match the 
actual data structure from Ollama's response.

**Fix:** Temporarily disable the Filter node to test the pipeline, then re-enable 
and correct the condition field path.

---

## Key Lessons Learned

- Always verify Docker network connectivity between containers in separate stacks
- CIFS mounts require explicit `uid`, `forceuid`, and `dir_mode` options to work 
  correctly with Docker containers
- n8n's JSON body templating breaks with complex AI-generated text – always use a 
  Code node with `JSON.stringify()` for dynamic content
- Delete Docker volumes completely when changing Postgres credentials
- Wiki.js storage paths must use the **container's internal mount path** not the host path

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc/storage" class="back">Top 
        <span class="label"> NAS & Storage Configuration</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc" class="next">Next
      <span class="label">Automated Security Documentation</span>
      </a>
    </div>
  </div>
</li>
