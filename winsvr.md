---
title: Windows server on Proxmox
description: Installing Windows server 2025 on proxmox hypervisor
published: true
date: 2025-05-11T19:03:03.162Z
tags: server, windows, 2025
editor: markdown
dateCreated: 2025-05-06T14:49:37.230Z
---

# Deploying Windows Server 2025 on Proxmox

## Prerequisites
Ensure you have access to the Proxmox web UI and have appropriate storage configured for ISO downloads

>**Info:** These steps will guide you through setting up a Windows Server 2025 VM on Proxmox 6 or 7
{.is-info}

## Step-by-Step Instructions

### Upload ISO Files
1. Log into the **Proxmox web UI**
2. Click on the **node name** for dropdown
2. Select a **storage** from the left navigation pane to upload the Windows server `.iso` to. Example **local(pve)**
3. Click **ISO Images** in the left sub-navigation pane
4. **For Proxmox 6**:
   - Download the **Windows Server 2025 ISO**: [Archive.org](https://archive.org/download/windows_server_2025/en_windows_server_2025_all_in_one_24h2_build_26100.1.iso)
   - Download the **VirtIO driver ISO**: [Fedorapeople](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/virtio-win.iso)
   - Upload both files to the Proxmox ISO image library

5. **For Proxmox 7 and above**:
   - Click **Download from URL**, paste the provided links, and click **Query URL** → **Download**

### Create a Virtual Machine (VM)
1. Right-click the **Proxmox node name** → **Create VM**
2. Assign a **unique VM ID** and **Name** → Next
3. Set:
   - Type to **Microsoft Windows**
   - Version to **11/2025**
   - Select the **Server 2025 ISO**
   - Check the box for **Add additional drive for VirtIO drivers** and select the **VirtIO driver ISO** for the ISO Image box

> **Warning:** Ensure correct BIOS settings to prevent boot failures
{.is-warning}

### Configure VM Settings
1. **System Tab**:
   - BIOS: `SeaBIOS`
   - Enable/disable **TPM** and select its **storage device**.
   - Check **Qemu Agent**.
   - Set **SCSI Controller** to `VirtIO SCSI`.

2. **Disks Tab**:
    - Storage device & **Disk size**: Minimum **40GB**.
    - **Bus/Device**: `VirtIO Block`.
    - **Cache**: `Write back`.

3. **CPU Tab**:
    - Set **Cores** to 2 or more.
    - **Type**: `host`.

4. **Memory Tab**:
    - Allocate **4096 MB** or more.

5. **Network Tab**:
    - **Leave defaults** → Next.

6. **Verify the summary** and click **Finish**.

### Verification of VirtIO Drivers
1. Select **Server 2025 VM** → **Hardware**.
2. Check to ensure the presence of **2** CD/DVD Drives, one for **Server ISO** the other for **VirtIO ISO**
IF Not 
	-- Click **Add** → **CD/DVD Drive**.
  -- Select the **VirtIO driver ISO**.
  -- Click **OK**.

### Install Windows Server 2025
1. Right-click **Server 2025 VM** → **Start**.
2. Click **Console** in the left sub-navigation pane.
3. If prompted, **press any key** to boot into the CD/DVD drive.
4. Set **Language & Keyboard** settings → Next.
5. Click `I agree everything will be deleted` → Next.
6. Enter a **Product Key** or click `I don’t have a product key` → Next.
7. Select **desired Windows edition** → Next.
8. Accept the **License Terms**.
9. Click **Load Driver** → Browse VirtIO driver disc.
10. Expand `amd64\2k25` → OK → Next.
11. Select **Unallocated Space** → Next → Install.

>**Success:** Windows will copy files and reboot multiple times.
{.is-success}

### Complete Installation
1. If prompted, enter a **Product Key** or click `Do this later`.
2. Set an **Administrator password** → **Finish**.
3. Click **Keyboard icon** → `Ctrl-Alt-Del`.
4. Log in with the **Administrator password**.
5. Welcome to **Windows Server 2025**! 🎉

---

## Optional Steps (Highly Recommended)
> **Info:** These steps enhance performance and stability
{.is-info}

1. Open **File Explorer** → Navigate to **VirtIO Disc**.
2. Right-click `virtio-win-guest-tools.exe` → **Run as administrator**.
3. Step through the installer, accepting defaults.
4. **Shutdown** the VM.
5. In **Proxmox**, go to **Hardware**.
6. Select **CD/DVD Drive** with VirtIO ISO → **Remove** → Yes.
7. Double-click **remaining CD/DVD drive** → `Do not use any media` → OK.
8. Click **Start** to reboot the VM.
9. Open **Console**.

Congratulations! Your Windows Server 2025 VM is successfully configured. 🚀

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="#migration" class="back">Top 
        <span class="label"> Beginning</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/migration/post" class="next">Next
      <span class="label">Post Migration</span>
      </a>
    </div>
  </div>
</li>

