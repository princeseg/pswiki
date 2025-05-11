---
title: Ubuntu Server Migration 
description: Migrating from Test to Production Environment
published: true
date: 2025-05-11T15:33:19.255Z
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

## 🔄 Backup and Transfer Configurations
- #### 📁 System Files (Optional)
  ```sh
  sudo scp -r /etc/ edem@192.168.50.20:/etc/
  sudo scp /etc/passwd /etc/shadow /etc/group /etc/gshadow edem@192.168.50.20:/etc/
  sudo rsync -avz /home/ edem@192.168.50.20:/home/

### 🔎 Identify Running Services
- Run the following to identify all runing services
  ```sh
	systemctl list-units --type=service --state=running
	sudo docker ps -a

## 📤 Export and Transfer Containers

### 🛑 Stop and Export Containers
- Stop all services to ensure consistency of data after export
  ```bash
  docker stop <container_id_or_name>
  docker export -o portainer_backup.tar <portainer_id>
  docker export -o postgres_backup.tar <postgres_id>
  docker export -o wiki_backup.tar <wiki_id>
  docker export -o nginx_backup.tar <nginx_ida

### 🚀 Transfer to Server 2
- Transfer all achived files to Server 2
  ```bash
		scp *.tar edem@192.168.50.20:/home/edem/


### 🔄 Import on Server 2
- Import the images from each container to Server 2
  ```bash
	docker import /home/edem/portainer_backup.tar portainer_image
	docker import /home/edem/postgres_backup.tar postgres_image
	docker import /home/edem/wiki_backup.tar wiki_image
	docker import /home/edem/nginx_backup.tar nginx_image
	sudo docker images

---

## 📁 Migrate Volumes
### ☑️ Check Volumes on Server 1 and note names
- Note number of volumes and names
  ```bash
		sudo docker volume ls
    
### 🗂 Archive Volumes on Server 1
- Archive each volume on Server 1
  ```bash
		tar -czvf volume_1.tar.gz -C /var/lib/docker/volumes/<volume_1>/_data .
		tar -czvf volume_2.tar.gz -C /var/lib/docker/volumes/<volume_2>/_data .
		tar -czvf volume_3.tar.gz -C /var/lib/docker/volumes/<volume_3>/_data .



### 🚚 Transfer to Server 2
- From Server 1, transfer all volumes to Server 2
  ```bash
	scp volume_*.tar.gz edem@192.168.50.20:/home/edem/



#### 🔄 Restore Volumes on Server 2
- On Server 2, restore all volumes to user directory
  ```bash
		docker volume create <volume_name> # if not existing
		tar -xzvf /home/edem/volume_1.tar.gz -C /var/lib/docker/volumes/<volume_1>/_data



## 🏗️ Recreate Docker Compose Files
### 📂 Directory Structure on Server 2
- On Server 2, create these directories		
  ```bash
		mkdir -p ~/nginx-proxy
		mkdir -p ~/pswiki

#### 📜 **nginx-proxy/docker-compose.yml**
- On Server 2, change directory into the nginx-proxy using and create a yml file;
  ```bash
		sudo cd nginx-proxy
    sudo nano docker-compose.yml
---
- Copy the content below into the docker-compose.yml file.
-	Press **Ctrl + X** , **Y**, and **Enter** to exit the nano text editor.
  ```yml
	services:
  app:
  	container_name: nginx-proxy-app-1
    image: jc21/nginx-proxy-manager:latest
    ports:
      - "56380:80"
      - "56881:81"
      - "59443:443"
    volumes:
      - nginx_data:/data
      - nginx_letsencrypt:/etc/letsencrypt
    restart: unless-stopped
	volumes:
  		nginx_data:
      nginx_letsencrypt:
```
```
#### 📜 **nginx-proxy/docker-compose.yml**
---

**`pswiki/docker-compose.yml`**
```yaml
services:
  db:
    image: postgres:15-alpine
    volumes:
      - pswiki_db-data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: wiki
      POSTGRES_USER: wikijs
      POSTGRES_PASSWORD: wikijsrocks

  wiki:
    image: requarks/wiki:2
    depends_on:
      - db
    ports:
      - "3080:3000"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
volumes:
  pswiki_db-data:
    external: true

```

---
### 6. 🚢 Deploy the Stacks

```bash
cd ~/nginx-proxy
sudo docker compose -p nginx-proxy up -d

cd ~/pswiki
sudo docker compose -p pswiki up -d

---
uoiso

### 7. 🔄 Migrating Nginx Proxy Manager with Bind Mounts

Your Nginx Proxy Manager container uses **bind mounts**, not Docker volumes:

```bash
"Source": "/data/compose/2/letsencrypt"
"Source": "/data/compose/2/data"
```

> **✅ SUCCESS**
> `/data/compose/2/data` → main data (includes `database.sqlite`)
> `/data/compose/2/letsencrypt` → SSL certs

**Step 1: Backup:**
```bash
sudo tar -czvf npm_backup.tar.gz -C /data/compose/2/data .
sudo tar -czvf npm_certs_backup.tar.gz -C /data/compose/2/letsencrypt .
```

**Step 2: Transfer:**
```bash
scp npm_backup.tar.gz edem@192.168.50.20:/home/edem/
scp npm_certs_backup.tar.gz edem@192.168.50.20:/home/edem/
```

**Step 3: Restore:**
```bash
sudo tar -xzvf /home/edem/npm_backup.tar.gz -C /var/lib/docker/volumes/nginx-proxy_nginx_data/_data
sudo tar -xzvf /home/edem/npm_certs_backup.tar.gz -C /var/lib/docker/volumes/nginx-proxy_nginx_letsencrypt/_data
```

**Restart Container:**
```bash
sudo docker restart nginx-proxy-app-1
```

---

### 8. 🧩 Wiki.js Troubleshooting

> **❌ ERROR**
> Images not visible externally? Check the base URL.

**Fix:**
1. Log into Wiki.js Admin UI
2. Go to: `Administration > General > Site URL`
3. Change `http://localhost:3000` → `http://192.168.50.20:3080`
4. Save and restart Wiki.js container

---

### 9. ⚠️ Wiki.js Not Loading on Port 80?

If `docker-compose.yml` shows:
```yaml
expose:
  - "80:3000"
```
Replace with:
```yaml
ports:
  - "80:3000"
```

> **✅ SUCCESS**
> This explicitly maps host port 80 to container port 3000.

---

### ✅ Migration Complete!
- All containers and volumes transferred
- Portainer, Nginx Proxy Manager, Wiki.js, and PostgreSQL are live

> **ℹ️ INFO**
> Keep this document updated as a reference for future Docker migrations.











