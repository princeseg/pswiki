---
title: Azure Sentinel Phase 1 - Workspace Setup
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-16T16:28:11.889Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-16T16:28:11.889Z
---

# Session: Azure Sentinel Phase 1 - Workspace Setup
## Overview
This document outlines the setup of an Azure Sentinel workspace, which is a critical component of the Azure Sentinel solution. The workspace is where Sentinel collects and processes data from various sources, and it serves as the central hub for threat detection and incident response.

## Environment
The setup was performed in the cloud, specifically in the East US 2 region.

## Timeline
| Time | Event |
| --- | --- |
| 2026-03-16T16:23:38.996Z | Session started |
| 2026-03-16T16:25:08.920297Z | Created Azure resource group `rg-sentinel-prod` |
| 2026-03-16T16:26:01.499764Z | Deployed Log Analytics workspace `law-sentinel-prod` |
| 2026-03-16T16:26:42.241237Z | Enabled Microsoft Sentinel on workspace |

## Activities Performed
The following activities were performed during the session:

### 1. Created Azure Resource Group

* Command: `az group create --name rg-sentinel-prod --location eastus2`
* Description: Created a new Azure resource group named `rg-sentinel-prod` in the East US 2 region.
* Severity: Info
* Source: Manual
* Hostname: Portal Azure
* Occurred at: 2026-03-16T16:25:08.920297+00:00

### 2. Deployed Log Analytics Workspace

* Command: `az monitor log-analytics workspace create --resource-group rg-sentinel-prod --workspace-name law-sentinel-prod --retention-time 90`
* Description: Created a new Log Analytics workspace named `law-sentinel-prod` with a 90-day retention period for Sentinel data ingestion.
* Severity: Medium
* Source: Manual
* Hostname: Portal Azure
* Occurred at: 2026-03-16T16:26:01.499764+00:00

### 3. Enabled Microsoft Sentinel on Workspace

* Command: 
* Description: Onboarded the Sentinel solution onto the Log Analytics workspace.
* Severity: High
* Source: Manual
* Hostname: Portal Azure
* Occurred at: 2026-03-16T16:26:42.241237+00:00

## Issues and Observations
No issues or observations were noted during the session.

## Summary
The Azure Sentinel workspace setup was successfully completed. The workspace was created with a resource group, Log Analytics workspace, and Microsoft Sentinel enabled. The setup was performed manually, and all commands were executed using the Azure CLI. The setup is now ready for data ingestion and threat detection.

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#azure-sentinel-phase-1-workspace-setup" class="back">Top
        <span class="label"> Top of Page</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/azure-sentinel" class="next">Project Home
      <span class="label">Azure Sentinel SIEM Deployment</span>
      </a>
    </div>
  </div>
</li>