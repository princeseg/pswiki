---
title: Bulk User Account Creation
description: Creating bulk users from a csv file
published: true
date: 2025-05-15T00:17:15.164Z
tags: users, user account, csv file, bulk, import
editor: markdown
dateCreated: 2025-05-15T00:15:15.165Z
---

# 🏢 Automated Active Directory User Imports with PowerShell
## 🎯 Welcome !

This guide will walk you through the process of **importing multiple users** into Active Directory Domain Controller (AD DC) using a **CSV file and PowerShell**. It’s an efficient and scalable way to streamline user account creation — especially in bulk deployments.

> **Before You Begin:**  
> Ensure that your AD DC is **already installed and running**.  
> If not, [click here to follow the Active Directory Domain Services installation guide](/active-directory).
{.is-info}

Let's dive in and make user provisioning smooth, fast, and just a little more fun! 🚀


---

## ✅ Requirements

| Item                     | Description                                      |
|--------------------------|--------------------------------------------------|
| OS                       | Windows Server (2016/2019/2022/2025)                  |
| Role                     | Active Directory Domain Services (AD DS)         |
| PowerShell Module        | `ActiveDirectory`                                |
| Permissions              | Domain Admin or delegated user account           |

---

## 🧱 Step-by-Step Setup

### 🛠️ Create Organizational Units (OUs)

Make sure all OUs listed in your CSV exist.

**Example OUs:**

- `OU=Sales,DC=edemseg,DC=com`
- `OU=Marketing,DC=edemseg,DC=com`
- `OU=IT,DC=edemseg,DC=com`
- `OU=Manager,DC=edemseg,DC=com`

#### To Create:

1. Open **Active Directory Users and Computers (ADUC)** from **Tools**
2. Right-click domain → **New > Organizational Unit** 
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
# Import Active Directory module
Import-Module ActiveDirectory

# Import users from CSV
$users = Import-Csv -Path $csvPath
Import-Csv "C:\path\to\csv_file" | ForEach-Object { # Change C:\path\to\csv_file to reflect actula path to CSV File
    # Debugging: Print out the values of each field to check if any are empty or incorrect
    Write-Host "Checking user: $($_.UserName) with FirstName: $($_.FirstName), LastName: $($_.LastName), OU: $($_.OU)"
    
    if (-not $_.FirstName -or -not $_.LastName -or -not $_.UserName -or -not $_.Password -or -not $_.OU) {
        Write-Warning "Skipping user $($_.UserName) due to missing required fields."
        return
    }
    
    Write-Host "Processing user: $($_.UserName)"
    
    # Use the -WhatIf parameter to simulate the action
    New-ADUser -Name "$($_.FirstName) $($_.LastName)" `
        -GivenName $_.FirstName `
        -Surname $_.LastName `
        -SamAccountName $_.UserName `
        -UserPrincipalName "$($_.UserName)@edemseg.com" ` # Change edemseg.com to refelct actual domain.
        -AccountPassword (ConvertTo-SecureString $_.Password -AsPlainText -Force) `
        -Enabled $true `
        -Path $_.OU `
        -PassThru `
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

## ✔️ Post-Import Checks

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
<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/powershell" class="back">Back 
        <span class="label">Project Synopsis</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="#active-directory/users/backup" class="next">Next
      <span class="label">Automate Backup</span>
      </a>
    </div>
  </div>
</li>