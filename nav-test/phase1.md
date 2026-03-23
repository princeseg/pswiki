---
title: Phase 1 - Infrastructure Setup
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-23T11:17:23.606Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-23T11:17:23.606Z
---

# Phase 1 - Infrastructure Setup
## Overview

This document outlines the Phase 1 - Infrastructure Setup for our on-prem environment. The setup was completed on 2026-03-23T11:16:36.553Z by user "edem". The setup involved setting up the base server configuration.

## Environment
The environment type is on-prem, with the following key details:
- Server configuration: To be determined
- Mode: Manual

## Step-by-Step Build Guide

1. **Set up base server configuration**

    ```bash
sudo /usr/bin/systemconfigurator --install --system-name=server1 --server-ip=192.168.1.1 --system-password="password123"
```

2. **Set up firewall configuration**

    ```bash
sudo /usr/bin/ufw enable
sudo /usr/bin/ufw default deny incoming
sudo /usr/bin/ufw allow incoming ssh
```

3. **Set up network configuration**

    ```bash
sudo /usr/bin/nfconfig --show --config
```

## Troubleshooting
No issues encountered during the setup process.

## Summary
The Phase 1 - Infrastructure Setup has been completed. The on-prem environment is now set up with the base server configuration, firewall configuration, and network configuration.

<style>
.config-item { background-color: #fafafa; box-shadow: 0 3px 8px rgba(116,129,141,.1); border-radius: 1px; font-weight: 500; list-style: none; margin-top: 20px; margin-bottom: 10px; transition: border-left-color .3s ease,border-right-color .3s ease }
.navigation { display: flex; justify-content: space-between; align-items: center; padding: 0; font-weight: 700; text-decoration: none }
.nav-back { flex: 1; border-left: 5px solid #e0e0e0; height: 50px; display: flex; justify-content: flex-start; align-items: center; text-align: left; padding-left: 5px }
.nav-back a.back { display: block; text-align: left; padding: 5px; border-radius: 3px; text-decoration: none; color: #1976d2; transition: background-color .3s ease,border-left-color .3s ease }
.nav-next { flex: 1; border-right: 5px solid #e0e0e0; height: 50px; display: flex; justify-content: flex-end; align-items: center; text-align: right; padding-right: 5px }
.nav-next a.next { display: block; text-align: right; padding: 5px; border-radius: 3px; text-decoration: none; color: red; transition: background-color .3s ease,border-left-color .3s ease }
.divider { width: 20px; height: 100%; background: rgba(224,224,224,.8); margin: 0 5px }
.nav-back a.back:hover { background-color: #e3f2fd }
.nav-back:hover { border-left-color: #1976d2 }
.nav-next a.next:hover { background-color: #ffecec }
.nav-next:hover { border-right-color: red }
.label { font-weight: 400; font-style: normal; color: #616161; padding-left: .3rem; border-left: 1px solid #e0e0e0; margin-left: .3rem }
</style>

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <span style="color:#bdbdbd">No previous phase</span>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <span style="color:#bdbdbd">No next phase</span>
    </div>
  </div>
</li>