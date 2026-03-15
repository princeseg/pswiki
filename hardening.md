---
title: Filesystem Kernel Module Hardening
description: Disabling unused filesystem kernel modules
published: true
date: 2026-03-15T15:44:16.908Z
tags: kernel, module, filesystem, kernel code, cves
editor: markdown
dateCreated: 2026-03-15T15:07:02.910Z
---

# 🔐 Filesystem Kernel Module Hardening Guide

This guide explains **why unused filesystem kernel modules should be
disabled**, how to determine which modules are safe to disable, and
provides **automation scripts to disable and re‑enable them safely**.

> Linux supports many filesystem types through kernel modules which extend the kernel's ability to read and write various filesystem formats.
{.is-info}


------------------------------------------------------------------------

## 📌 Overview

Linux filesystems are supported through **kernel modules**.

Examples include:
| Filesystem | Typical Use | Risk Level | CVE Exposure | System Usage |
|-------------|-------------|------------|--------------|--------------|
| ext4 | Default Linux filesystem | Low | Rare | ✅ In Use |
| cifs | Windows network shares | Medium | Occasional | ✅ In Use |
| nfs | Network file systems | Medium | Occasional | ⚠️ Verify |
| exfat | External drives | Medium | Known vulnerabilities | ❌ Not Used |
| fat | Legacy removable media | Medium | Known vulnerabilities | ❌ Not Used |
| gfs2 | Cluster filesystems | High | Multiple historical CVEs | ❌ Not Used |

While useful, **unused modules increase the kernel attack surface**.

------------------------------------------------------------------------

## ⚠️ Security Risk Summary

> Unused filesystem modules with vulnerabilities can allow attackers to
load malicious kernel components.
{.is-warning}

Attackers may:

-   Load malicious kernel code
-   Hide processes or files
-   Maintain persistence
-   Bypass monitoring tools

>Disabling unused modules is recommended by CIS Linux Benchmarks.
{.is-success}

------------------------------------------------------------------------

## 🛑 Modules With Known CVEs

| Module | Filesystem | Typical Purpose | Risk Level | CVE Exposure | System Usage | Recommended Action |
|------|------|------|------|------|------|------|
| exfat | External storage | Support for external drives and removable media | Medium | Known vulnerabilities | ❌ Not Used | Disable if unused |
| fat | Removable media | Legacy filesystem for USB drives and older devices | Medium | Known vulnerabilities | ❌ Not Used | Disable if unused |
| gfs2 | Cluster filesystem | Shared filesystem used in clustered Linux environments | High | Historical CVEs | ❌ Not Used | Disable unless using cluster storage |
| nfsd | NFS server | Provides NFS server functionality for network shares | High | Network attack surface | ❌ Not Used | Disable if NFS server not required |
| fscache | Cache layer | Local caching for network filesystems | Medium | Kernel memory exposure risks | ❌ Not Used | Disable if not required |

------------------------------------------------------------------------

## 📁 Filesystems Used by This System

The following **must NOT be disabled**:
| Module | Purpose | System Function | Risk Level | System Usage | Recommended Action |
|------|------|------|------|------|------|
| ext4 | Root filesystem | Primary Linux filesystem used for system storage | Low | ✅ In Use | Do NOT disable |
| cifs | Network shares | Enables mounting of Windows/SMB network file shares | Medium | ✅ In Use | Keep enabled if network shares are required |
| fuse | Snap/AppImage support | Allows user-space filesystem mounting used by Snap and AppImage packages | Low | ✅ In Use | Keep enabled |
| overlay | Docker container storage | Overlay filesystem used by Docker for container layers | Low | ✅ In Use | Required for Docker containers |

> Disabling these modules can **break** your operating system or containers.
{.is-danger}

------------------------------------------------------------------------

## 🧪 Determine Which Modules Are Safe to Disable

### Check Mounted Filesystems

``` bash
mount | awk '{print $5}' | sort -u
```

### Check Loaded Modules

``` bash
lsmod
```

A module is safe to disable only if:

-   It **does not appear in mount output**
-   It **does not appear in lsmod output**

------------------------------------------------------------------------

## 🟢 Safe‑to‑Disable Modules Example

Based on system analysis:

-   exfat
-   fat
-   fscache
-   gfs2
-   nfs_common
-   nfsd
-   smbfs_common

> These modules can be disabled if they are not required in your
environment.
{.is-success}

------------------------------------------------------------------------

## 📦 Script --- Disable Unused Filesystem Modules
Script: **disable_unused_fs_modules.sh**
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

------------------------------------------------------------------------

## 🔧 Script --- Re‑Enable a Module
Script: **enablefs_modules.sh**
``` bash
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

```

------------------------------------------------------------------------

## 🛡️ Safety Notes

> Never disable modules currently in use.
{.is-warning}

Critical modules include:

-   ext4
-   cifs
-   fuse
-   overlay

Always:

-   verify modules first
-   reboot after changes
-   document all disabled modules


> All changes are reversible using the re-enable script.
{.is-info}

## 📝 Summary
This guide provides:
- A clear explanation of why filesystem module hardening matters
- A safe method to determine which modules can be disabled
- Automated scripts to disable unused modules
- A recovery script to re‑enable modules when needed
This approach ensures compliance with CIS benchmarks while maintaining system stability.
