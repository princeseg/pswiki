---
title: Ubuntu Server Migration 
description: Migrating from Test to Production Environment
published: true
date: 2025-05-11T14:21:47.607Z
tags: migration, ubuntu, test, production
editor: markdown
dateCreated: 2025-04-29T14:06:04.636Z
---

# Ubuntu Server & Docker Container and Volume Migration

> Throughout this document:
**Server 1** refers to **Test Svr** (Source Server)
**Server 2** refers to **Production Svr** (Destination Server)
{.is-info}


---

## 📌 Pre-Migration Preparation

> **Warning:** Before starting, **backup both servers and verify** the following to avoid data loss!
{.is-warning}

### 🖧 Networking and Access
- Ensure both servers have open terminals:
  - **Server 1 (Test Svr) IP:** `192.168.50.165`
  - **Server 2 (Production Svr) IP:** `192.168.50.20`
- Verify connectivity:
  ```sh
  ping -c 4 192.168.50.20  # From Server 1
  ping -c 4 192.168.50.165 # From Server 2
⚠️ WARNING
**If ping fails, verify firewall rules and allow ICMP traffic.**

### 📊 Environment Check

Disk Space: Ensure **Server 2** has enough free space.

- Docker/Portainer Installation:
  ```sh
  sudo apt update && sudo apt upgrade -y
  sudo apt install docker.io docker-compose curl -y
  
-	Permissions: Ensure matching users and sudo privileges.

### 🔐 SSH Access
- Test SSH connectivity:
  ```sh
  ssh edem@192.168.50.20
  
- If SSH fails::
  ```sh
  sudo passwd root
  sudo nano /etc/ssh/sshd_config

- Update:
  ```sh
  PasswordAuthentication yes
  PermitRootLogin yes

- Restart SSH:
  ```sh
  sudo systemctl restart ssh

### 🔄 Backup and Transfer Configurations
- #### 📁 System Files (Optional)
  ```sh
  sudo scp -r /etc/ edem@192.168.50.20:/etc/
  sudo scp /etc/passwd /etc/shadow /etc/group /etc/gshadow edem@192.168.50.20:/etc/
  sudo rsync -avz /home/ edem@192.168.50.20:/home/

### 🔎 Identify Running Services
- #### 📁 System Files (Optional)
  ```sh
  sudo scp -r /etc/ edem@192.168.50.20:/etc/
  sudo scp /etc/passwd /etc/shadow /etc/group /etc/gshadow edem@192.168.50.20:/etc/
  sudo rsync -avz /home/ edem@192.168.50.20:/home/















