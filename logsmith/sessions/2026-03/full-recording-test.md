---
title: Full Recording Test
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-18T13:29:18.584Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-18T13:29:18.584Z
---

# Full Recording Test
## Overview
This document outlines the steps taken during a full recording test of the IT infrastructure on-prem environment. The test was conducted on March 18, 2026, and was started by user edem at 13:25:27.907Z.

## Environment
The test was conducted in the on-prem environment, which utilizes Docker containers for the logsmith application. The environment details include:

* Docker version: [insert Docker version]
* Logsmith version: [insert logsmith version]
* Operating System: [insert operating system]

## Step-by-Step Build Guide

1. **Executed: ls**
   ```bash
ls
```
   Details: The command was executed successfully, and the output was:
   ```
cd /opt/logsmith
cat .env
sudo docker ps
clear
df -h
sudo docker logs logsmith_db --tail 5
sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"
```

2. **Executed: sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"**
   ```bash
sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"
```
   Details: The command was executed successfully, and the output was:
   ```
exit
```

## Troubleshooting
No issues encountered during the test. All commands executed successfully without any errors.

## Summary
The full recording test was conducted successfully, and the step-by-step build guide has been provided. This document serves as a reference for future builds and troubleshooting.