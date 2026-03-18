---
title: Parser Test Session
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-18T14:22:34.349Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-18T14:22:34.349Z
---

# Parser Test Session
## Overview
This document summarizes the execution of a parser test session in an on-prem environment.

## Environment
The test session was run on an on-prem infrastructure with the following key details:
- Date: 2026-03-18
- Time: 14:18:41.154Z to 14:19:38.104Z
- User: edem
- Mode: record
- Terminal recording: 15 commands captured

## Step-by-Step Build Guide

1. **Executed: ls**
   ```bash
   ls
   ```
2. **Executed: cd /opt/logsmith**
   ```bash
   cd /opt/logsmith
   ```
3. **Executed: cat .env**
   ```bash
   cat .env
   ```
4. **Executed: sudo docker ps**
   ```bash
   sudo docker ps
   ```
5. **Executed: df -h**
   ```bash
   df -h
   ```
6. **Executed: sudo docker logs logsmith_db --tail 5**
   ```bash
   sudo docker logs logsmith_db --tail 5
   ```
7. **Executed: sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"**
   ```bash
   sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"
   ```
8. **Executed: ls**
   ```bash
   ls
   ```
9. **Executed: cd /opt/logsmith**
   ```bash
   cd /opt/logsmith
   ```
10. **Executed: cat .env**
    ```bash
    cat .env
    ```
11. **Executed: sudo docker ps**
    ```bash
    sudo docker ps
    ```
12. **Executed: df -h**
    ```bash
    df -h
    ```
13. **Executed: sudo docker logs logsmith_db --tail 5**
    ```bash
    sudo docker logs logsmith_db --tail 5
    ```
14. **Executed: sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"**
    ```bash
    sudo docker exec logsmith_db psql -U logsmith -d logsmith_db -c "\dt"
    ```
15. **Executed: exirt**
    ```bash
    exirt
    ```

## Troubleshooting
- **Error:** Permission denied for `.env` file.
  - **Failed Command:** `cat .env`
  - **Fix:** Ensure that the user executing the command has read access to the `.env` file. Consider adding the user to the `logsmith` group to resolve the permission issue.
- **No issues encountered.**

## Summary
This document provides a detailed summary of the parser test session execution. It includes the exact commands used, the environment details, and troubleshooting steps for any encountered issues.