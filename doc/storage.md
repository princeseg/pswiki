---
title:  NAS & Storage Configuration
description: 
published: true
date: 2026-03-11T23:11:28.888Z
tags: asustor, nas, storage
editor: markdown
dateCreated: 2026-03-11T22:50:18.579Z
---

# NAS & Storage Configuration

## Environment

- **NAS Device:** Asustor NAS
- **NAS IP:** 192.168.60.20
- **Share Name:** wiki_uploads
- **Mount Point:** /mnt/nas/wiki_uploads

## Create Mount Point
```bash
sudo mkdir -p /mnt/nas/wiki_uploads
```

## Configure SMB Credentials
```bash
sudo nano /etc/samba/wikiuser.cred
```

Add:
```
username=wikiuser
password=YOUR_NAS_PASSWORD
domain=WORKGROUP
```

Secure the file:
```bash
sudo chmod 600 /etc/samba/wikiuser.cred
```

## Configure fstab
```bash
sudo nano /etc/fstab
```

Add this line:
```
//192.168.60.20/wiki_uploads /mnt/nas/wiki_uploads cifs credentials=/etc/samba/wikiuser.cred,uid=1000,gid=1000,file_mode=0777,dir_mode=0777,vers=3.0,noperm,forceuid,forcegid 0 0
```

> **Warning:** `uid=1000` must match the UID of the user running Wiki.js inside 
> the Docker container. Verify with:
> `docker exec pswiki-wiki-1 id`
{.is-warning}

## Mount and Verify
```bash
sudo systemctl daemon-reload
sudo mount -a
df -h | grep nas
```

## Fix Parent Directory Permissions
```bash
sudo chmod 777 /mnt/nas
sudo chown 1000:1000 /mnt/nas
```

## Asustor NAS Permissions

In Asustor ADM:
1. Go to **Access Control** → **Shared Folders**
2. Select `wiki_uploads` → **Permissions**
3. Ensure `wikiuser` has **RW (Read/Write)** checked

## Wiki.js Storage Configuration

In Wiki.js Admin → Storage → Local File System:
- Path: `/mnt/wiki_uploads/images`

> **Info:** The path inside Wiki.js uses the container's internal mount path 
> `/mnt/wiki_uploads` not the host path `/mnt/nas/wiki_uploads`.
{.is-info}

<li class="config-item">
  <div class="navigation">
    <div class="nav-back">
      <a href="/doc/n8n_workflow" class="back">Top 
        <span class="label">n8n Workflow Configuration</span>
      </a>
    </div>
    <span class="divider"></span>
    <div class="nav-next">
      <a href="/doc/troubleshooting" class="next">Next
      <span class="label">Troubleshooting Log</span>
      </a>
    </div>
  </div>
</li>
