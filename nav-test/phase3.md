---
title: Phase 3 - Monitoring Setup
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-28T11:36:58.918Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-28T11:36:58.918Z
---

# Phase 3 - Monitoring Setup
## Overview
This document outlines the steps taken to set up monitoring using Prometheus and Grafana. The monitoring system is designed to collect metrics and provide real-time visualizations of system performance.

## Environment
This setup was performed on-premises, and the start and completion times are as follows:

* Started: 2026-03-28T11:35:40.412Z
* Completed: 2026-03-28T11:36:00.064Z
* Started by: edem
* Mode: manual

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

## Troubleshooting
No issues encountered during the setup process.

## Summary
This phase of the setup completes the monitoring system, which can now collect metrics and provide real-time visualizations of system performance.

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/nav-test/phase2" class="back">Previous <span class="label">Service Deployment</span></a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <span style="color:#bdbdbd">No next phase</span>
    </div>
  </div>
</li>