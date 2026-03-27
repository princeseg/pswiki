---
title: Phase 1 - Infrastructure Setup
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-27T00:10:56.208Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-27T00:10:56.208Z
---

# Phase 1 - Infrastructure Setup
## Overview
On March 27, 2026, at 00:09:41.451Z, the infrastructure setup began. The following steps were completed:

## Environment
This document outlines the setup of the on-prem infrastructure. Key details include:

* Server configuration
* Environment type: on-prem

## Step-by-Step Build Guide

### Step 1: Set up base server configuration

```bash
#!/bin/bash
# Update package list
sudo apt update -y
# Upgrade package list
sudo apt full-upgrade -y
# Install necessary packages
sudo apt install -y apache2
# Configure Apache
sudo systemctl enable apache2
sudo systemctl start apache2
```

### Step 2: Configure firewall rules

```bash
#!/bin/bash
# Allow incoming traffic on port 80
sudo ufw allow http
# Allow incoming traffic on port 443
sudo ufw allow https
# Enable the firewall
sudo ufw enable
```

## Troubleshooting
No issues encountered during the setup process.

## Summary
The infrastructure setup was completed successfully on March 27, 2026, at 00:10:04.673Z. The following essential steps were taken to set up the base server configuration:

1. Updated package list and upgraded package list.
2. Installed necessary packages (Apache).
3. Configured Apache.
4. Configured firewall rules (allowing incoming traffic on ports 80 and 443).

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <span style="color:#bdbdbd">No previous phase</span>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/nav-test/phase1" class="next">Next <span class="label">Infrastructure Setup</span></a>
    </div>
  </div>
</li>