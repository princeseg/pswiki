---
title: Additional Domain Controller
description: Secondary Active Directory Domain Controller
published: true
date: 2025-05-11T19:34:28.980Z
tags: replication, secondary dc, replica, backup dc
editor: markdown
dateCreated: 2025-05-11T19:24:17.455Z
---


## 🖥️ Install Windows Server & Join the Domain

1. Log in to the **secondary server**.
2. Set a **static IP address**:
   - Open **Control Panel > Network & Internet > Network Connections**
   - Right-click the active network → Select **Properties**
   - Select **Internet Protocol Version 4 (TCP/IPv4)** → Click **Properties**
   - Manually set the IP (e.g., `192.168.60.101`)
   - Set the **Primary DC** as the DNS server (e.g., `192.168.60.100`)

3. Join the **domain**:
   - Right-click **This PC** → Select **Properties**
   - Click **Advanced System Settings > Computer Name**
   - Click **Change**, select **Domain**, enter your domain (e.g., `edemseg.com`)
   - Enter admin credentials when prompted
   - **Restart the server**

---

## 🧱 Install Active Directory Domain Services (AD DS)

1. Open **Server Manager**
2. Click **Manage > Add Roles and Features**
3. Select **Role-based or feature-based installation** → Click **Next**
4. Choose the **secondary server** → Click **Next**
5. Select **Active Directory Domain Services** → Click **Next**
6. Click **Install** and wait for completion

---

## 🚀 Promote the Server to a Domain Controller

1. Go to **Server Manager > Notifications**
2. Click **Promote this server to a domain controller**
3. Select **Add a domain controller to an existing domain**
4. Enter your domain (e.g., `edemseg.com`) → Click **Next**
5. Configure the following:
   - Ensure **DNS** & **Global Catalog (GC)** are checked
   - Enter a **Directory Services Restore Mode (DSRM)** password
6. Click **Next** until installation completes
7. **Restart the server**

---

## 🔁 Verify Replication Between DCs

Run the following in **PowerShell**:

```powershell
repadmin /syncall /e /d /A
```
📌 This forces Active Directory replication between the Primary and Secondary DC.

Check domain controllers:

```powershell
nltest /dclist:edemseg.com
```
📌 This lists all available DCs in the domain.

---

## 🧬 Ensure DNS Replication

1. Open **DNS Manager** (`dnsmgmt.msc`)
2. Expand **Forward Lookup Zones > edemseg.com**
3. Ensure the **new DC** appears in the list
4. Run the following to confirm DNS sync:

```powershell
Get-DnsServerZone -Name "edemseg.com"
```

---

## ✅ Final Verification

- Users should be able to **authenticate** via both DCs
- The secondary DC should appear in **AD replication logs**
- Run:

```powershell
dcdiag /test:replications
```
📌 Confirms replication health between domain controllers.
<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#active-directory" class="back">Back 
        <span class="label">Primary DC</span>
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