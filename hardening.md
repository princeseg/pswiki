---
title: Filesystem Kernel Module Hardening
description: Disabling unused filesystem kernel modules
published: true
date: 2026-03-15T15:32:45.922Z
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

``` bash
#!/bin/bash

MODULES=(
  exfat
  fat
  fscache
  gfs2
  nfs_common
  nfsd
  smbfs_common
)

MOUNTED=$(mount | awk '{print $5}' | sort -u)
LOADED=$(lsmod | awk '{print $1}')

for module in "${MODULES[@]}"; do

    if echo "$MOUNTED" | grep -qw "$module"; then
        echo "SKIPPED: $module is mounted."
        continue
    fi

    if echo "$LOADED" | grep -qw "$module"; then
        echo "SKIPPED: $module is loaded."
        continue
    fi

    CONF_FILE="/etc/modprobe.d/${module}.conf"

    sudo bash -c "echo 'install $module /bin/false' > $CONF_FILE"
    sudo bash -c "echo 'blacklist $module' >> $CONF_FILE"

    echo "Disabled: $module"
done

echo "Reboot recommended."
```

------------------------------------------------------------------------

# 🔧 Script --- Re‑Enable a Module

``` bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Usage: $0 <module_name>"
    exit 1
fi

MODULE="$1"
CONF_FILE="/etc/modprobe.d/${MODULE}.conf"

sudo rm -f "$CONF_FILE"
sudo modprobe "$MODULE"

echo "Module re-enabled if available."
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

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#doc" class="back">Top 
        <span class="label"> Beginning</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/hardening" class="next">Next
      <span class="label">Top</span>
      </a>
    </div>
  </div>
</li>
