---
title: Ubuntu Server Migration 
description: Migrating from Test to Production Environment
published: true
date: 2025-05-11T17:19:42.345Z
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
> ⚠️ **WARNING**
> If ping fails, verify **firewall** rules and allow **ICMP traffic**.
{.is-danger}




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
  docker export -o nginx_backup.tar <nginx_id>

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

## ➡️ Migrating Nginx Proxy Manager with Bind Mounts
- Since Nginx Proxy Manager was deployed in Portainer using **bind mounts** instead of Docker volumes, the encryption-related files were not included during the volume migration process.
- On Server 1 run command to locate files
  ```bash
		sudo docker inspect f7e2fb4adf65 | grep Source

> **Expected Output:**
"Source": "/data/compose/2/letsencrypt" **→ main data (includes `database.sqlite`)**
"Source": "/data/compose/2/data" **→ SSL certs**
{.is-success}

### 📦 Backup on Server 1
- On Server 1 run command to backup files
  ```bash
		sudo tar -czvf npm_backup.tar.gz -C /data/compose/2/data .
		sudo tar -czvf npm_certs_backup.tar.gz -C /data/compose/2/letsencrypt .

### 🚚 Transfer to Server 2
- On Server 1 run command to transfer files to Server 2
  ```bash
		scp npm_backup.tar.gz edem@192.168.50.20:/home/edem/
		scp npm_certs_backup.tar.gz edem@192.168.50.20:/home/edem/

### ♻️ Restore on Server 2
- Switch Server 2 and run command to restore files
  ```bash
    sudo tar -xzvf /home/edem/npm_backup.tar.gz -C /var/lib/docker/volumes/nginx-proxy_nginx_data/_data
    sudo tar -xzvf /home/edem/npm_certs_backup.tar.gz -C /var/lib/docker/volumes/nginx-proxy_nginx_letsencrypt/_data

## 🏗️ Recreate Docker Compose Files
### 📂 Directory Structure on Server 2
- On Server 2, create these directories		
  ```bash
		mkdir -p ~/nginx-proxy
		mkdir -p ~/pswiki

#### 📜 **nginx-proxy/docker-compose.yml**
- On Server 2, change directory into the **nginx-proxy** using and create a yml file;
  ```bash
		sudo cd nginx-proxy
    sudo nano docker-compose.yml

- Copy the text below into the docker-compose.yml file.
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

-	Press **Ctrl + X** , **Y**, and **Enter** to exit the nano text editor.
#### 📜 **pswiki/docker-compose.yml**
- On Server 2, change directory into the **pswiki** using and create a yml file;
  ```bash
		sudo cd ~/pswiki
    sudo nano docker-compose.yml

- Copy the text below into the docker-compose.yml file.
  ```yml
	services:
  db:
  	container_name: pswiki
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
      - "80:3000"
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

## 🚢 Deploy the Stacks
- Deploy both stack to make then accessible in portainer GUI
  ```bash
		cd ~/nginx-proxy
		sudo docker compose -p nginx-proxy up -d

		cd ~/pswiki
		sudo docker compose -p pswiki up -d

### ✅ Migration Complete!
- All containers and volumes transferred
- Portainer, Nginx Proxy Manager, Wiki.js, and PostgreSQL are live

## 🧪 Post-Migration Verification and Configuration

---

### ✅ 1. Verify Stacks and Containers in Portainer

> **ℹ️ INFO**  
> Portainer provides a web-based UI to visually inspect all Docker stacks, containers, volumes, and networks.

**Steps:**

1. Open a browser and go to your Server 2 IP and Portainer port (usually `9000` or `9443`):
   ```
   http://192.168.50.20:9000
   ```

2. Log in with your admin credentials.

3. In the **Portainer Dashboard**, navigate to:
   - **Stacks** — Confirm your stacks (`nginx-proxy`, `pswiki`) are listed.
   - **Containers** — Ensure all required containers (Nginx Proxy Manager, Wiki.js, Postgres, Portainer) are running and healthy.

> **✅ SUCCESS**  
> All stacks should show a green status and all containers should be marked as running.

---

### 🌐 2. Access and Update Nginx Proxy Manager (NPM)

> **⚠️ WARNING**  
> Your old NPM base URL may still be pointing to your previous server’s IP. Update this to reflect the new server IP.

**Steps:**

1. Access NPM via the IP and port defined in your `docker-compose.yml`. For example:
   ```
   http://localhost:56380
   ```

2. Login with your admin email and password.

3. Navigate to:
   ```
   Settings > General
   ```

4. Update the `Hostname` or `Domain Names` to:
   ```
   192.168.50.20
   ```

5. Save the changes.

6. Restart the NPM container if necessary:
   ```bash
   docker restart <nginx-proxy-container-name>
   ```

> **✅ SUCCESS**  
> NPM now recognizes the new IP and can route traffic correctly.

---

### 🔥 3. Configure the Firewall on Server 2

> **❌ ERROR**  
> If you cannot access ports externally, your firewall may be blocking them.

**Steps:**

1. Check which firewall is active. For Ubuntu, it's typically UFW:
   ```bash
   sudo ufw status
   ```

2. Allow required ports:
   ```bash
   sudo ufw allow 56380    # HTTP
   sudo ufw allow 56881    # Admin UI
   sudo ufw allow 59443    # HTTPS
   sudo ufw allow 3080     # Wiki.js
   sudo ufw reload
   ```

3. Confirm rules:
   ```bash
   sudo ufw status numbered
   ```

> **✅ SUCCESS**  
> External devices should now be able to access NPM and Wiki.js on the correct ports.

---

### 📚 4. Verify Wiki.js State and Access

> **ℹ️ INFO**  
> The goal here is to ensure all data (pages, users, files) was preserved during migration.

**Steps:**

1. Open a browser and go to:
   ```
   http://192.168.50.20:3080
   ```

2. Login with your Wiki.js admin credentials.

3. Navigate to:
   ```
   Administration > General > Site URL
   ```

4. Update from:
   ```
   http://localhost:3000
   ```
   To:
   ```
   http://192.168.50.20:3080
   ```

5. Save and **restart the Wiki.js container** if needed:
   ```bash
   docker restart <wiki-container-name>
   ```

6. Confirm:
   - Pages and edits are intact
   - Media (e.g., images) is loading correctly
   - External users can view content via the new IP

> **✅ SUCCESS**  
> Your Wiki.js installation is fully functional, with content and settings preserved.

---

### 🧼 Final Recommendations

- **Back up** your new server using `tar`, `rsync`, or Docker volume snapshots.
- **Document all container and volume paths** for easier future migrations.
- **Secure** external access via HTTPS and limit open ports as needed.



<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/wiki" class="back">Back 
        <span class="label">Wiki Deployment on Docker</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/wiki/nginx/pfsense" class="next">Next
      <span class="label">Firewall Configuration</span>
      </a>
    </div>
  </div>
</li>








