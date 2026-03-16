---
title: Terminal Recording Test
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-16T20:46:46.782Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-16T20:46:46.782Z
---

# Session: Terminal Recording Test
## Overview
This document outlines the details of a terminal recording test conducted on an on-prem environment. The test was initiated on March 16, 2026, and completed on the same day.

## Environment
The test was conducted on an on-prem environment, utilizing the `pswiki` hostname.

## Timeline
The session timeline is as follows:

| Time | Event |
| --- | --- |
| 20:43:07.443Z | Session started by edem. |
| 20:44:36.716Z | Session completed. |

## Activities Performed
The following activities were performed during the session:

### Executed Commands

1. **docker ps**
   ```bash
docker ps
```
   Output:
   ```
permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
```

   Severity: Info

   Source: CLI

   Hostname: pswiki

   Occurred At: 2026-03-16T20:44:36.266953+00:00

2. **sudo docker ps**
   ```bash
sudo docker ps
```
   Output:
   ```
[sudo] password for edem:
CONTAINER ID   IMAGE                             COMMAND                  CREATED         STATUS                  PORTS                                                                                                                               NAMES
eab99c28f882   dashboard-logsmith_dashboard      "docker-entrypoint.s…\"   5 hours ago     Up 5 hours              0.0.0.0:8585->8585/tcp, [::]:8585->8585/tcp                                                                 
```

   Severity: Info

   Source: CLI

   Hostname: pswiki

   Occurred At: 2026-03-16T20:44:36.347038+00:00

3. **cat /etc/hostname**
   Output:
   ```
pswiki
```

   Severity: Info

   Source: CLI

   Hostname: pswiki

   Occurred At: 2026-03-16T20:44:36.450066+00:00

4. **uname -a**
   Output:
   ```
Linux pswiki 6.8.0-106-generic #106-Ubuntu SMP PREEMPT_DYNAMIC Fri Mar  6 07:58:08 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
```

   Severity: Info

   Source: CLI

   Hostname: pswiki

   Occurred At: 2026-03-16T20:44:36.530683+00:00

5. **df -h**
   Output:
   ```
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              995M  5.2M  990M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   48G   22G   24G  49% /
tmpfs                              4.9G     0  4.9G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  200M  1.6G  11% /boot
//192.168.60.20/wiki_uploads        22T   17G   22T   1% /mnt/nas/wiki_uploads
tmpfs                              9
```

   Severity: Info

   Source: CLI

   Hostname: pswiki

   Occurred At: 2026-03-16T20:44:36.634259+00:00

## Issues and Observations
No major issues were observed during the test. The test was completed successfully.

## Summary
The terminal recording test was conducted on the `pswiki` hostname on March 16, 2026, and completed on the same day. The test resulted in the capture of 5 commands, including `docker ps`, `sudo docker ps`, `cat /etc/hostname`, `uname -a`, and `df -h`. The test did not reveal any major issues.