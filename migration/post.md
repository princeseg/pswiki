---
title: Post Migration & Verification
description: Configure and verify access to npm and pswiki
published: true
date: 2025-05-11T18:23:14.144Z
tags: configuration, migration, post migration, verification
editor: markdown
dateCreated: 2025-05-11T18:09:53.414Z
---

## 🧪 Post-Migration Notice
After migration, it is crucial to verify that the Production Server is fully operational and accessible before decommissioning the Test Server.
This ensures service continuity, reduces the risk of downtime, and helps maintain high service quality during the transition.
## ✅ Verify Stacks and Containers in Portainer

> **NOTE**  
Portainer provides a web-based UI to visually inspect all Docker stacks, containers, volumes, and networks
{.is-info}

### Steps 

1. Open a browser and go to your Server 2 IP and Portainer port (usually `9000` or `9443`):
   ```
   http://192.168.50.20:9443
   ```

2. Log in with your admin credentials.

3. In the **Portainer Dashboard**, navigate to:
   - **Stacks** — Confirm your stacks (`nginx-proxy`, `pswiki`) are listed.
   - **Containers** — Ensure all required containers (Nginx Proxy Manager, Wiki.js, Postgres, Portainer) are running and healthy.

> **SUCCESS** 
	All stacks should show a green status and all containers should be marked as running.
{.is-success}


## 🌐 Access and Update Nginx Proxy Manager (NPM)


>  **WARNING**  
The old NPM base URL may still be pointing to Server 1's IP. 
Update this to reflect the Server 2's IP
{.is-warning}

**Steps:**

1. Access NPM via the IP and port defined in your `docker-compose.yml`. For example:
   ```
   http://localhost:56881
   ```

2. Login with admin email and password.

3. Navigate to:
   ```
   Host > Proxy Hosts > ⋮ > Edit
   ```

4. Update the `Forward Hostname IP` to:
   ```
   192.168.50.20
   ```

5. Save the changes.

6. Restart the NPM container if necessary:
   ```bash
   sudo docker restart nginx-proxy-app-1
   ```

> **SUCCESS** 
NPM now recognizes the new IP and can route traffic correctly
{.is-success}

## 🧱 Configure the Firewall on Server 2

> **❌ ERROR** 
	If you cannot access ports externally, your firewall may be blocking them.
{.is-danger}

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
### 🧱🔥 Congifure pfsense firewall
**Refer to pfsense configuration [here](/wiki/nginx/pfsense)**
> **SUCCESS**  
> External devices should now be able to access Wiki.js on the ports.
{.is-success}


---

## 📚 Verify Wiki.js State and Access

> **ℹINFO**  
> The goal here is to ensure all data (pages, users, files) was preserved during migration.
{.is-info}


**Steps:**

1. Open a browser and go to:
-	http://pswiki.edemseg.com

2. Login with your Wiki.js admin credentials.

3. Navigate to:
   **Administration** > **General**

4. Confirm:
   - Pages and edits are intact
   - Media (e.g., images) is loading correctly
   - External users can view content via the new IP

> **SUCCESS**  
>  Wiki.js installation is fully functional, with content and settings preserved.
{.is-success}

## 🧼 Final Recommendations

- **Back up** Production Server using `tar`, `rsync`, or Docker volume snapshots.
- **Document all container and volume paths** for easier future migrations.
- **Secure** external access via HTTPS and limit open ports as needed.

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/migration" class="back">Back 
        <span class="label">Migration</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/home#project" class="next">Home
      <span class="label">Project Index</span>
      </a>
    </div>
  </div>
</li>