---
title: enable_fs_modules.sh script
description: Bash script to create and run enable_fs_modules.sh script
published: true
date: 2026-03-15T18:50:54.700Z
tags: 
editor: markdown
dateCreated: 2026-03-15T18:50:54.700Z
---

# 🔧 Creating and Running the enable_fs_modules.sh script{#top}


This guide explains how to create a bash script named **`enable_fs_modules.sh`**, grant it execute permission, and run it on a Linux system.

The script allows administrators to **re-enable filesystem kernel modules** that were previously disabled using modprobe blacklist configurations.

---

## 📌 Overview

When filesystem modules are disabled, they are typically blocked using configuration files in:
```bash
/etc/modprobe.d
```

The **enable script** removes those denylist files and attempts to **reload the kernel module**.

---

## 1️⃣ Navigate to a Directory for Scripts

It is best practice to store administrative scripts in a dedicated directory.

```bash
mkdir -p ~/scripts
cd ~/scripts
```
## 2️⃣ Create the Bash Script File

Use a text editor such as nano to create the script.

```bash
nano enable_fs_modules.sh
```
## 3️⃣ Add the Script Content

Paste the following code into the file.
```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <module_name>"
    exit 1
fi

MODULE="$1"
CONF_FILE="/etc/modprobe.d/${MODULE}.conf"

echo "=== Re-enabling module: $MODULE ==="

# Remove the denylist configuration file
if [ -f "$CONF_FILE" ]; then
    sudo rm "$CONF_FILE"
    echo "Removed configuration: $CONF_FILE"
else
    echo "No denylist configuration found for $MODULE"
fi

# Attempt to load the module
echo "Loading module..."
sudo modprobe "$MODULE"

# Verify module loaded
if lsmod | grep -qw "$MODULE"; then
    echo "SUCCESS: $MODULE module is now loaded."
else
    echo "WARNING: Module could not be loaded. A reboot may be required."
fi
```
## 4️⃣ Save the Script

If using nano, press:

```
CTRL + O
```
Press Enter to save the file.

Exit the editor with:

```
CTRL + X
```
## 5️⃣ Grant Execute Permission

Make the script executable.

```
chmod +x enable_fs_modules.sh
```

Verify permissions:

```
ls -l enable_fs_modules.sh
```
Expected output:

```
-rwxr-xr-x enable_fs_modules.sh
```
## 6️⃣ Run the Script

To re-enable a module, run the script followed by the module name.

Example for module **exfat**:

```
./enable_fs_modules.sh exfat
```

If root privileges are required:

```
sudo ./enable_fs_modules.sh exfat
```
## 7️⃣ Verify the Module Is Loaded

Confirm that the module is active.

```
lsmod | grep exfat
```

If the module appears in the output, it has been successfully loaded.

## 🔁 Examples Usage

1. Enable the **fat** filesystem module:

```
sudo ./enable_fs_modules.sh fat
```

2. Enable the **gfs2** cluster filesystem module:

```
sudo ./enable_fs_modules.sh gfs2
```
## ✅ Result

After running the script:

- The module denylist configuration is removed

- The kernel module is reloaded

- Filesystem support is restored if required

This process ensures filesystem modules can be safely re-enabled when needed while maintaining system hardening controls.
<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/hardening/script" class="back">Top 
        <span class="label"> Disable Modules</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="#top" class="next">End
      <span class="label">Top of Page</span>
      </a>
    </div>
  </div>
</li>

