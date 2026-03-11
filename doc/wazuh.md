---
title: Wazuh SIEM Configuration
description: Wazuh SIEM is an open‑source security monitoring platform that collects logs, detects threats, and helps you respond to incidents in real time
published: true
date: 2026-03-11T22:37:55.554Z
tags: wazuh, siem, monitor, open source, logs, incident, real time
editor: markdown
dateCreated: 2026-03-11T22:37:55.554Z
---

# Wazuh Configuration

## Install Wazuh

Follow the official Wazuh documentation to install Wazuh manager on Server 2:
```
https://documentation.wazuh.com/current/installation-guide/index.html
```

## Configure Webhook Integration

Add the n8n webhook integration to Wazuh's config file:
```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add this block **before** the closing `</ossec_config>` tag:
```xml
<integration>
  <name>custom-webhook</name>
  <hook_url>http://192.168.50.20:5678/webhook/wazuh-alerts</hook_url>
  <level>7</level>
  <alert_format>json</alert_format>
</integration>
```

> **Info:** `<level>7</level>` means only alerts of severity level 7 and above will 
> be sent to n8n. Adjust this value to control alert volume.
{.is-info}

## Restart Wazuh
```bash
sudo systemctl restart wazuh-manager
sudo systemctl status wazuh-manager
```

Look for **active (running)** in green. The `wazuh-integratord` process must be 
listed in the output for webhooks to work.

## Test Webhook

From Server 2, send a test alert directly to n8n:
```bash
curl -X POST http://192.168.50.20:5678/webhook/wazuh-alerts \
  -H "Content-Type: application/json" \
  -d '{"level": 7, "rule": {"description": "SSH brute force attack detected"}, "agent": {"name": "wazuh-server"}}'
```

Expected response:
```json
{"message":"Workflow was started"}
```

## Monitor Integration Logs
```bash
sudo tail -f /var/ossec/logs/integrations.log
```

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc/ollama" class="back">Top 
        <span class="label">Ollama Deployment</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/n8n_config" class="next">Next
      <span class="label">n8n Integration</span>
      </a>
    </div>
  </div>
</li>
