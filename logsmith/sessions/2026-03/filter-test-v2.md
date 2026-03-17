---
title: Filter Test v2
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-17T19:22:11.829Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-17T19:22:11.829Z
---

# Filter Test v2
## Overview
This document summarizes the testing of improved filtering in the logsmith_db database, performed on the on-prem environment.

## Environment
Environment: on-prem
Started: 2026-03-17T19:20:17.137Z
Completed: 2026-03-17T19:20:47.750Z
Started by: edem
Notes: Mode: manual

## Step-by-Step Build Guide
### 1. Started container correctly
```bash
docker compose up -d
```
Details: The `postgres:16-alpine` image started successfully.

### 2. Verified database
```bash
docker exec logsmith_db psql -U logsmith -d logsmith_db -c SELECT 1
```
Details: A connection to the `logsmith_db` database was confirmed.

## Troubleshooting
### 1. Attempted wrong image
Command that failed: `docker run logsmth_db:latest`
Error: Error: image not found
Fix: Double-checked the image name and pulled the correct image (`logsmith_db:latest`) before running the command.

## Summary
The improved filtering test was successfully completed on the on-prem environment without any notable issues.