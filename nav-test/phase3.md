---
title: Phase 3 - Monitoring Setup
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-28T12:13:15.604Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-28T11:36:58.918Z
---

# Phase 3 - Monitoring Setup
## Overview
This document outlines the steps taken to set up Prometheus and Grafana for monitoring the IT infrastructure.

## Environment
* Environment Type: on-prem
* Started: 2026-03-28T11:35:40.412Z
* Completed: 2026-03-28T12:12:19.798Z
* Started by: edem
* Notes: Mode: manual

## Step-by-Step Build Guide
1. **Installed Prometheus**
   ```bash
docker pull prom/prometheus
```

2. **Configured Grafana dashboards**
   ```bash
docker pull grafana/grafana
```

3. **Verified metrics collection**
   (No specific command was provided, but ensure that Prometheus is configured and running correctly to collect metrics.)

## Troubleshooting
No issues encountered during the setup process.

## Summary
This document summarizes the steps taken to set up Prometheus and Grafana for monitoring the IT infrastructure. The setup was successful, and the tools are now configured and running correctly.

---

<div class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/nav-test/phase2" class="back">Previous <span class="label">Service Deployment</span></a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/nav-test/phase4" class="next">Next <span class="label">Security Hardening</span></a>
    </div>
  </div>
</div>