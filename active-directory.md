---
title: Redundant AD Domain Setup
description: Primary & Secondary DC Configuration
published: true
date: 2025-05-09T11:08:18.570Z
tags: ad dc, redundant, primary dc, secondary dc
editor: markdown
dateCreated: 2025-05-09T09:17:16.534Z
---

# Installing Active Directory Domain Services (AD DS) on Windows Server

## Overview
Installing AD DS allows you to set up a **Domain Controller (DC)** for centralized authentication and resource management.

---

## 🔹 **Step 1: Prepare the Server**
Before proceeding, ensure:
- ✅ Windows Server is **installed & updated**. 📌Check Out <a href = "/winsvr">Windows Server on Proxmox</a> Project
- ✅ A **static IP address** is set (to avoid network conflicts).

- ✅ The server **is not already a Domain Controller**.

🔹 Step 2: Install the AD DS Role
Using Server Manager
1️⃣ Open Server Manager → Click Manage → Select Add Roles and Features. 2️⃣ Choose Role-based or feature-based installation → Click Next. 3️⃣ Select your server → Click Next. 4️⃣ Under Roles, check Active Directory Domain Services (AD DS). 5️⃣ Click Add Features when prompted → Click Next. 6️⃣ Review features → Click Next → Click Install.

💡 AD DS enables centralized authentication and security policies across the domain.

🔹 Step 3: Promote the Server to a Domain Controller
After installing AD DS, you must promote the server.

1️⃣ Open Server Manager → Click Notifications (flag icon) → Select Promote this server to a domain controller. 2️⃣ Choose:

✅ Add a new forest (if starting fresh).

✅ Add a domain controller to an existing domain (if expanding). 3️⃣ Enter your domain name (e.g., edemseg.com). 4️⃣ Configure:

✅ DNS & Global Catalog (GC) → Enabled.

✅ Set Directory Services Restore Mode (DSRM) password (for recovery). 5️⃣ Click Next until installation completes → Restart the server.

💡 A secondary DC ensures redundancy and load balancing.

🔹 Step 4: Verify Installation
Run the following commands in PowerShell to confirm setup:

✅ Check domain controllers:

powershell
nltest /dclist:edemseg.com
✅ Verify replication:

powershell
repadmin /syncall /e /d /A

✅ Confirm Active Directory health:

powershell
dcdiag /test:replications
💡 Replication ensures changes propagate across all domain controllers.

🔹 Troubleshooting & Best Practices
💡 Ensure DNS is properly configured (dcdiag /test:dns). 🔄 Set up backup policies for disaster recovery. 🔒 Secure domain authentication using GPOs & RBAC settings.