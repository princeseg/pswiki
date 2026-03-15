---
title: disable_fs_modules.sh script
description: Bash script to create and run disable_fs_modules.sh script
published: true
date: 2026-03-15T18:34:23.667Z
tags: bash, script, disable
editor: markdown
dateCreated: 2026-03-15T17:40:56.921Z
---

# 🧰 Creating and Running the disable_unused_fs_modules.sh Script{#top}

This guide explains how to create a Bash script named **disable_unused_fs_modules.sh**, grant it execute permission, and run it on a Linux server.  
The script helps **disable unused filesystem kernel modules** to reduce the Linux kernel attack surface.

---

## 1️⃣ Navigate to a Directory for Scripts

Create and move into a directory to store administrative scripts.

```bash
mkdir -p ~/scripts
cd ~/scripts
```
## 2️⃣ Create the Bash Script File

Use a text editor such as **nano** to create the script.
```bash
nano disable_unused_fs_modules.sh
```
## 3️⃣ Add the Script Content

Paste the following code into the file.
``` bash
#!/bin/bash

# Filesystem modules CIS wants disabled if unused
MODULES=(
  exfat
  fat
  fscache
  gfs2
  nfs_common
  nfsd
  smbfs_common
)

echo "=== Checking mounted filesystems ==="
MOUNTED=$(mount | awk '{print $5}' | sort -u)
echo "$MOUNTED"

echo "=== Checking loaded kernel modules ==="
LOADED=$(lsmod | awk '{print $1}')
echo "$LOADED"

echo "=== Determining safe modules to disable ==="

for module in "${MODULES[@]}"; do
    echo ""
    echo "--- Checking module: $module ---"

    # Check if module is mounted
    if echo "$MOUNTED" | grep -qw "$module"; then
        echo "SKIPPED: $module is mounted and in use."
        continue
    fi

    # Check if module is loaded
    if echo "$LOADED" | grep -qw "$module"; then
        echo "SKIPPED: $module is loaded and in use."
        continue
    fi

    # Safe to disable
    echo "DISABLING: $module (unused)"

    CONF_FILE="/etc/modprobe.d/${module}.conf"

    sudo bash -c "echo 'install $module /bin/false' > $CONF_FILE"
    sudo bash -c "echo 'blacklist $module' >> $CONF_FILE"

    echo "Created: $CONF_FILE"
done

echo ""
echo "=== Completed. Reboot recommended to apply changes. ==="

```

---

## 4️⃣ Save the Script

If using nano **Press**
```bash
CTRL + O
```
Press Enter to save the file.
Then exit with:
```bash
CTRL + X
```

---

## 5️⃣ Grant Execute Permission

Make the script executable.

```bash
chmod +x disable_unused_fs_modules.sh
```
Verify permissions:
```bash
ls -l disable_unused_fs_modules.sh
```
Expected output:
```bash
-rwxr-xr-x disable_unused_fs_modules.sh
```

---

## 6️⃣ Run the Script

Execute the script.
```bash
./disable_unused_fs_modules.sh
```

If elevated privileges are required:

```
sudo ./disable_unused_fs_modules.sh
```
---

## 7️⃣ Verify Disabled Modules

Check the configuration files created in the modprobe directory.

```bash
ls /etc/modprobe.d/
```
Example output:
```bash
exfat.conf
fat.conf
gfs2.conf
```
Each file prevents the module from loading.

---

## 8️⃣ Reboot the System (Recommended)
```
sudo reboot
```
Rebooting ensures all kernel module changes are applied.

---
## ✅ Result

After running the script:

-  Unused filesystem modules are **blacklisted**
-  Kernel attack surface is **reduced**
-  System hardening aligns with **CIS Linux security recommendations**

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/hardening" class="back">Back 
        <span class="label"> Linux Server Hardening</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/hardening/automate/enable" class="next"> Next 
      <span class="label"> Enable Modules </span>
      </a>
    </div>
  </div>
</li>
