---
title: Filesystem Kernel Module Hardening
description: Disabling unused filesystem kernel modules
published: true
date: 2026-03-15T15:07:02.910Z
tags: kernel, module, filesystem, kernel code, cves
editor: markdown
dateCreated: 2026-03-15T15:07:02.910Z
---

# Filesystem Kernel Module Hardening Guide
This guide explains why unused filesystem kernel modules should be disabled, how to determine which modules are safe to disable, and provides automated scripts for both disabling and re‑enabling modules.

## 📌 Overview
Linux supports many filesystem types through kernel modules. These modules extend the kernel’s ability to read, write, and manage different filesystem formats (e.g., CIFS, NFS, EXFAT, FAT, GFS2).
While useful, these modules increase the attack surface. If a module has a known vulnerability and is not needed by the system, it should be disabled to prevent attackers from loading it and hiding malicious activity inside the kernel.

⚠️ Security Risk Summary
Unused filesystem modules with known CVEs should be disabled to reduce kernel attack surface.

Attackers can exploit vulnerable modules to:
- Load malicious kernel code
- Hide processes or files
- Maintain persistence undetected
- Bypass monitoring tools
Disabling unused modules is a CIS‑recommended hardening step.

🛑 Modules With Known CVEs
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 
|  |  | 



📁 Filesystem Modules Your System Uses
Your system uses:
- ext4 (root filesystem)
- cifs (network shares)
- fuse (AppImages, snaps, tools)
- overlay (Docker/containers)
These must not be disabled.

🧪 How to Determine Which Modules Are Safe to Disable
Check mounted filesystems
mount | awk '{print $5}' | sort -u


Check loaded kernel modules
lsmod


A module is safe to disable only if:
- It does not appear in the mount list
- It does not appear in the lsmod list

🟢 Safe-to-Disable Modules (Based on Your System)
These modules are not mounted and not loaded, so they are safe to disable:
- exfat
- fat
- fscache
- gfs2
- nfs_common
- nfsd
- smbfs_common

📦 Script 1 — Automatically Disable Unused Filesystem Modules
<details><summary><strong>Click to expand script</strong></summary>
disable_unused_fs_modules.sh
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


</details>

🔧 Script 2 — Re‑Enable Any Disabled Module
<details><summary><strong>Click to expand script</strong></summary>
enable_fs_module.sh
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <module_name>"
    exit 1
fi

MODULE="$1"
CONF_FILE="/etc/modprobe.d/${MODULE}.conf"

echo "=== Re-enabling module: $MODULE ==="

# Remove the denylist file
if [ -f "$CONF_FILE" ]; then
    sudo rm "$CONF_FILE"
    echo "Removed: $CONF_FILE"
else
    echo "No denylist file found for $MODULE"
fi

# Try loading the module
echo "Loading module..."
sudo modprobe "$MODULE" 2>/dev/null

if lsmod | grep -qw "$MODULE"; then
    echo "SUCCESS: $MODULE is now loaded."
else
    echo "WARNING: Could not load $MODULE. It may not exist or may require a reboot."
fi


</details>

📘 How the Scripts Work
Script 1
- Reads mounted filesystems
- Reads loaded kernel modules
- Compares them to the CIS list
- Disables only unused modules
- Creates /etc/modprobe.d/<module>.conf files
Script 2
- Removes the denylist file
- Loads the module back into the kernel
- Confirms successful loading

🛡️ Safety Notes
Never disable modules that appear in mount or lsmod.

- Disabling ext4, cifs, fuse, or overlay can break your system.
- Always reboot after disabling modules to confirm stability.
- Re‑enabling modules is safe and reversible.

📄 Summary
This guide provides:
- A clear explanation of filesystem module hardening
- A safe method to determine which modules can be disabled
- Automated scripts to disable unused modules
- A recovery script to re‑enable modules when needed
This ensures CIS compliance while maintaining system stability.

If you'd like, I can also generate a Wiki.js sidebar navigation, tags, or a version with diagrams to make this even more polished.


<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#doc" class="back">Top 
        <span class="label"> Beginning</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/servers" class="next">Next
      <span class="label">Servers Setup</span>
      </a>
    </div>
  </div>
</li>
