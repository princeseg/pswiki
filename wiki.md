---
title: Wiki.js on Docker Deployment
description: How to install Wiki.js on Docker
published: true
date: 2025-05-10T21:21:07.691Z
tags: wiki.js, docker, nginx-proxy-manager, portainer, cloudflare, ssl, token
editor: markdown
dateCreated: 2025-04-27T18:57:33.840Z
---

## Overview
Deploy a private documentation platform (**Wiki.js**) on **Ubuntu server** using **Docker container**, managed via **Portainer GUI** and make it accessible via a custom domain using **Nginx proxy manager**

---

## Environment
-- **Server:** Ubuntu Server 24.04
-- **Container Management:** Docker with Portainer
-- **Reverse Proxy:** Nginx Proxy Manager
-- **Domain Management:** Cloudflare
-- **Firewall:** pfSense

---

## Initial Ubuntu Server Setup
1. Install **Ubuntu Server 24.04**
  📌Check Out <a href = "/proxmox">Ubuntu Server on Proxmox</a> Project
2. Perform system updates:
  `sudo apt update && sudo apt upgrade -y`
3. Install **Docker** and **Docker Compose**
`curl -fsSL https://get.docker.com -o get-docker.sh`
`sudo sh get-docker.sh`
4. Install **Portainer** via Docker with persistent volumes
 -- To Create volume run:
`sudo docker volume create portainer_data`

	-- To access docker via portainer GUI manager, run:
`sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest`

 	-- Run `sudo docker start portainer` if return value of **portainer** is not received
 	-- Run `sudo docker start portainer` 
> Success, if return value is **portainer** {.is-success}
---

## Deploying Wiki.js on Portainer
1. Access portainer on **localhost:9443**.  Example; **192.168.80.12:9443**
2. Creat Username and Password
3. Configure environment variables:
	**a**. Click on **Stacks** in the left sidebar navigation.
	**b**. Click on the **+ Add stack** button.
	**c**. Enter a name for the stack (e.g. **pswiki**).
	**d**. Select **Web editor** as the Build method.
	**f**. In the Web editor below, paste the following block of code:
>version: '2'
services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data
  wiki:
    image: requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "80:3000"
volumes:
  db-data:{.is-info}
4. Click on **Deploy the stack** button.
5. Access Wiki.js on **localhost:80** Example **192.168.80.12:80**
6. Set up login details.

---

## Install Nginx Proxy Manager on Portainer{#nginx}
1. Installed and deployed **Nginx Proxy Manager**
  **a**. Click on **Stacks** in the left sidebar navigation.
	**b**. Click on the **+ Add stack** button.
	**c**. Enter a name for the stack (e.g. **nginx-proxy**).
	**d**. Select **Web editor** as the Build method.
	**f**. In the Web editor below, paste the following block of code:
  >services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt {.is-info}
  2. **WARNING**: Edit ports in Web editor to available ports in your network to avoid **error** notification. See Example below:
  >ports:
      - '**52480**:80'
      - '**52481**:81'
      - '**52443**:443'{.is-warning}
3. Click on **Deploy the stack** button.
4. Access **nginx proxy manager** on **localhost:52481** Example **192.168.80.12:52481**
> port **52841** in the example (192.168.80.12:52841) must be replaced with the same edited port number in the Web editor
{is.error}
6. Login with default details to change administrator credentials:
   **username**: admin@example.com
   **password**: changeme

<li class="config-item">
  <a href="/wiki/nginx" class="configuration">
      Next
    <em>
      Nginx and Cloudflare configuration.
    </em>
  </a>
</li>