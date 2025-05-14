---
title: Automated Windows Server Configuration & Deployment
description: How to Install Windows Server with PowerShell
published: true
date: 2025-05-14T23:26:06.762Z
tags: powershell
editor: markdown
dateCreated: 2025-04-29T23:53:18.793Z
---

# Project Synopsis
✅ Purpose: Automate the setup of a fresh Windows Server installation with predefined configurations, removing the need for manual GUI setup. ✅ Scope: Includes user creation, role assignments, network settings, firewall rules, software installations, and security policies.

Why GUI Won’t Work?
🚫 No direct way to apply bulk configurations via GUI—requires running PowerShell scripts to automate installations & policies. 🚫 Batch automation for multiple servers—PowerShell can deploy identical configurations across many servers, which the GUI would require manual repetition.

Key Features to Automate
✔ User & Group Management: Create admin accounts, set permissions. ✔ Network Configuration: Assign static IP, DNS settings. ✔ Firewall Rules: Apply security policies & port rules. ✔ Software Deployment: Install tools like Docker, IIS, or SQL Server via scripts. ✔ Task Scheduling: Set up automated backups & system updates.

This is a powerful project that will save time, reduce manual errors, and ensure consistency across deployments. 🚀 Let me know if you'd like a sample script to get started!

# 🧾 Preparing Active Directory Domain Controller (AD DC) for User Import via PowerShell

This guide walks you through how to prepare your AD DC for importing users from a CSV file using PowerShell.

---

## ✅ Requirements

| Item                     | Description                                      |
|--------------------------|--------------------------------------------------|
| OS                       | Windows Server (2016/2019/2022)                  |
| Role                     | Active Directory Domain Services (AD DS)         |
| PowerShell Module        | `ActiveDirectory`                                |
| Permissions              | Domain Admin or delegated user account           |

---

## 🧱 Step-by-Step Setup

### 1. Install Active Directory Domain Services (AD DS)

1. Open **Server Manager**.
2. Click **Manage > Add Roles and Features**.
3. Choose **Role-based or feature-based installation**.
4. Select your server.
5. Under **Server Roles**, check **Active Directory Domain Services**.
6. Proceed through the wizard and restart if prompted.

---

### 2. Promote Server to Domain Controller

1. In Server Manager, click the ⚑ icon → **Promote this server to a domain controller**.
2. Choose one:
   - "Add a new forest" – for fresh setup
   - "Add a domain controller to existing domain" – if joining one
3. Input domain name (e.g., `edemseg.com`).
4. Configure DSRM password and DNS settings.
5. Complete wizard and restart.

---

### 3. Create Organizational Units (OUs)

Make sure all OUs listed in your CSV exist.

**Example OUs:**

- `OU=Sales,DC=edemseg,DC=com`
- `OU=Marketing,DC=edemseg,DC=com`
- `OU=IT,DC=edemseg,DC=com`

#### To Create:

1. Open **Active Directory Users and Computers (ADUC)**.
2. Right-click domain → **New > Organizational Unit**.
3. Add required OUs (Sales, Marketing, IT, etc).

---

### 4. Open PowerShell as Administrator

- Use Start Menu: Right-click → **Windows PowerShell (Admin)**
- Or from PowerShell:
  ```powershell
  Start-Process powershell -Verb runAs
  ```

---

### 5. Confirm ActiveDirectory Module is Installed

```powershell
Import-Module ActiveDirectory
```

If it fails:

```powershell
Add-WindowsFeature RSAT-AD-PowerShell
```

---

### 6. Prepare Your CSV File

Example path: `C:\users.csv`

```csv
FirstName,LastName,UserName,Password,OU
Kojo,Mensah,kmensah,P@55w.rd,"OU=Sales,DC=edemseg,DC=com"
Ama,Owusu,aowusu,P@55w.rd,"OU=Marketing,DC=edemseg,DC=com"
Yaw,Addo,yaddo,P@55w.rd,"OU=IT,DC=edemseg,DC=com"
```

---

### 7. Run PowerShell Script with -WhatIf (Simulation)

```powershell
Import-Module ActiveDirectory
$users = Import-Csv -Path "C:\users.csv"

foreach ($user in $users) {
    $securePassword = ConvertTo-SecureString $user.Password -AsPlainText -Force
    New-ADUser -Name "$($user.FirstName) $($user.LastName)" `
               -GivenName $user.FirstName `
               -Surname $user.LastName `
               -SamAccountName $user.UserName `
               -UserPrincipalName "$($user.UserName)@edemseg.com" `
               -AccountPassword $securePassword `
               -Enabled $true `
               -Path $user.OU `
               -ChangePasswordAtLogon $true `
               -WhatIf
}
```

---

### 8. Run Script Without -WhatIf to Import Users

Once confirmed:

```powershell
# Remove -WhatIf to create users
```

---

## ✅ Post-Import Checks

- Open **Active Directory Users and Computers**.
- Browse to each OU.
- Verify users are created.
- Test login using a domain-joined client.

---

## 🛡️ Optional Hardening

- Set stronger passwords or enforce complexity policies.
- Add users to groups using `Add-ADGroupMember`.
- Log all import actions to a file for auditing.

---
