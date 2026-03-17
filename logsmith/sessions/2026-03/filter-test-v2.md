---
title: Filter Test v2
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-17T19:02:24.861Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-17T19:02:24.861Z
---

# Session: Filter Test v2
## Overview
The Filter Test v2 is a testing session designed to verify the improved filtering functionality in our on-prem environment. This session aims to ensure that the filtering mechanism is working correctly and efficiently.

## Environment
The testing session was performed in an on-prem environment.

## Step-by-Step Build Guide

1. **Listed Running Containers**
Execute the following command to list all running containers:
```bash
docker ps
```

2. **Checked Disk Space**
Verify the available disk space using the following command:
```bash
df -h
```

3. **Attempted Wrong Image**
Try to run a non-existent image using the following command:
```bash
docker run logsmth_db:latest
```
Expected error: "Image not found"

4. **Started Container Correctly**
Run the following command to start the container in detached mode:
```bash
docker compose up -d
```
Expected outcome: The `postgres:16-alpine` image starts successfully.

5. **Cleared Terminal**
Clear the terminal using the following command:
```bash
clear
```

6. **Verified Database**
Connect to the database using the following command:
```bash
docker exec logsmith_db psql -U logsmith -d logsmith_db -c SELECT 1
```
Expected outcome: Connection to the database is confirmed.

## Troubleshooting
No issues encountered.

## Summary
This session successfully tested the improved filtering functionality in our on-prem environment. The testing process involved listing running containers, checking disk space, attempting a wrong image, starting a container correctly, clearing the terminal, and verifying the database connection.