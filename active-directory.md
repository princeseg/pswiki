---
title: Active Directory Domain Setup
description: Primary Domain Controller Setup
published: true
date: 2025-05-13T21:12:39.909Z
tags: ad dc, redundant, primary dc, secondary dc
editor: markdown
dateCreated: 2025-05-09T09:17:16.534Z
---

# Active Directory Domain Services (AD DS){#active-directory}

## Overview
This guide documents the installation and configuration of Active Directory Domain Services (AD DS) on a Windows Server, transforming it into a Domain Controller (DC). 
## 🏢 Installing Active Directory Domain Services (AD DS)

Installing AD DS allows you to set up a **Domain Controller (DC)** for centralized authentication and resource management.

### 🛠️ Prepare the Server

Before proceeding, ensure:

- ✅ **Windows Server** is installed & updated.  
  📌 Refer to Windows Server on Proxmox **[here](/winsvr)**
- ✅ A **static IP address** is configured (to avoid network conflicts).
-	 **Static IP address configuration**:
   a.  Open **Control Panel > Network & Internet > Network Connections**
   b.  Right-click the active network → Select **Properties**
   c.  Select **Internet Protocol Version 4 (TCP/IPv4)** → Click **Properties**
   d.  Manually set the IP (e.g., `192.168.60.100`)
- ✅ The server is **not already a Domain Controller**.

---

### 📦 Install the AD DS Role

**Using Server Manager:**

1. Open **Server Manager** → Click **Manage** → Select **Add Roles and Features**  
2. Choose **Role-based or feature-based installation** → Click **Next**  
3. Select your server → Click **Next**  
4. Under **Roles**, check **Active Directory Domain Services (AD DS)**  
5. Click **Add Features** when prompted → Click **Next**  
6. Review features → Click **Next** → Click **Install**

> 💡 **AD DS** enables centralized authentication and security policies across the domain.
{.is-info}


---

### 🔼 Promote the Server to a Domain Controller

After installing AD DS, you must promote the server:

1. Open **Server Manager** → Click **Notifications (flag icon)** → Select **Promote this server to a domain controller**
2. Choose one of the following:
   - ✅ **Add a new forest** (if starting fresh)
   - ✅ **Add a domain controller to an existing domain** (if expanding)
3. Enter your domain name (e.g., `edemseg.com`)
4. Configure:
   - ✅ **DNS** & **Global Catalog (GC)** → Enabled
   - ✅ Set **Directory Services Restore Mode (DSRM)** password (for recovery)
5. Click **Next** through the prompts → Complete the installation → **Restart the server**

> 💡 A secondary DC provides redundancy and load balancing.

---

### 🔍 Verify Installation

Run the following commands in **PowerShell** to confirm the setup:

- ✅ **Check domain controllers:**
  ```powershell
  nltest /dclist:edemseg.com
  ```

- ✅ **Verify replication:**
  ```powershell
  repadmin /syncall /e /d /A
  ```

- ✅ **Confirm Active Directory health:**
  ```powershell
  dcdiag /test:replications
  ```

> 💡 Replication ensures that changes propagate across all domain controllers.

---

### 🔹 Troubleshooting & Best Practices

- 💡 **Ensure DNS is properly configured:**
  ```powershell
  dcdiag /test:dns
  ```
- 🔄 Set up **backup policies** for disaster recovery.
- 🔒 Secure domain authentication using **GPOs** (Group Policy Objects) and **RBAC** (Role-Based Access Control) settings.

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#active-directory" class="back">Top 
        <span class="label"> Beginning</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/active-directory/replica" class="next">Next
      <span class="label">Replica DC</span>
      </a>
    </div>
  </div>
</li>


