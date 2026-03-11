---
title: n8n Workflow Configuration
description: n8n workflow configuration is how you set a workflow’s behavior. Things like execution order, error‑handling, and runtime options.
published: true
date: 2026-03-11T22:45:56.831Z
tags: configuration, n8n, error-handling, runtime
editor: markdown
dateCreated: 2026-03-11T22:45:56.831Z
---

# n8n Workflow Configuration

## Workflow Overview
```
Webhook → HTTP Request (Ollama) → Filter → Code → HTTP Request1 (Wiki.js)
```

## Step 1 – Create Workflow

1. In n8n click **Create Workflow**
2. Name it `Wazuh Documentation Workflow`

## Step 2 – Webhook Node

- Click **Add first step** → search **Webhook**
- HTTP Method: `POST`
- Path: `wazuh-alerts`
- Click **Publish** to activate

Production URL will be:
```
http://YOUR_SERVER_IP:5678/webhook/wazuh-alerts
```

## Step 3 – HTTP Request Node (Ollama)

Add after Webhook:
- Method: `POST`
- URL: `http://ollama:11434/api/generate`
- Body Content Type: `JSON`
- Body:
```json
{
  "model": "llama3.2:3b",
  "stream": false,
  "prompt": "You are a technical documentation writer. Based on this security alert from Wazuh, write a clear troubleshooting documentation entry with these sections: 1) Alert Summary 2) Possible Cause 3) Investigation Steps 4) Recommended Fix. Here is the alert data: {{ $json.body }}"
}
```

## Step 4 – Filter Node

Add after HTTP Request:
- This filters low-level noise
- Condition: `{{ $json.body.level }}` greater than or equal to `7`

> **Info:** If the Filter blocks all data, temporarily disable it to test the 
> pipeline, then re-enable and fix the condition.
{.is-info}

## Step 5 – Code Node (JavaScript)

Add after Filter. Select **JavaScript** and paste:
```javascript
const response = $input.first().json.response;
const timestamp = Date.now();
const date = new Date().toISOString().slice(0, 16).replace('T', ' ');

const query = `mutation { pages { create( content: ${JSON.stringify(response)}, description: "Auto generated Wazuh security alert", editor: "markdown", isPrivate: false, isPublished: true, locale: "en", path: "security-alerts/alert-${timestamp}", tags: ["wazuh", "security"], title: "Security Alert - ${date}" ) { responseResult { succeeded message } } } }`;

return [{ json: { query } }];
```

## Step 6 – HTTP Request1 Node (Wiki.js)

Add after Code node:
- Method: `POST`
- URL: `http://YOUR_SERVER_IP/graphql`
- Send Headers: **ON**
  - `Authorization`: `Bearer YOUR_API_KEY`
  - `Content-Type`: `application/json`
- Body Content Type: `JSON`
- Specify Body: `Using Fields Below`
- Add Body Field:
  - Name: `query`
  - Value: `{{ $json.query }}` (click `{}` expression toggle)

## Step 7 – Publish

Click **Publish** in the top right corner.

## Test End-to-End
```bash
curl -X POST http://192.168.50.20:5678/webhook/wazuh-alerts \
  -H "Content-Type: application/json" \
  -d '{"level": 7, "rule": {"description": "SSH brute force attack detected"}, "agent": {"name": "wazuh-server"}}'
```

Wait 45-60 seconds then check Wiki.js for a new page under `security-alerts/`.

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc/wazuh" class="back">Top 
        <span class="label">Wazuh Configuration</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/storage" class="next">Next
      <span class="label"> NAS & Storage Setup</span>
      </a>
    </div>
  </div>
</li>
