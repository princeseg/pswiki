---
title: Record Mode Test
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-16T21:18:21.737Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-16T21:18:21.737Z
---

# Session: Record Mode Test
==========================

## Overview
------------

This session document records the activities performed during a record mode test on our on-prem environment. The test was initiated on March 16, 2026, at 21:15:45.909Z and completed at 21:17:03.154Z.

## Environment
--------------

* Environment: on-prem
* Start Time: 2026-03-16T21:15:45.909Z
* End Time: 2026-03-16T21:17:03.154Z

## Timeline
----------

| Time       | Event                       |
|------------|-----------------------------|
| 2026-03-16T21:15:45.909Z | Session started by edem      |
| 2026-03-16T21:17:02.878174+00:00 | "whoami" executed            |
| 2026-03-16T21:17:02.96781+00:00 | "date" executed              |
| 2026-03-16T21:17:03.063137+00:00 | "sudo docker ps --format..." executed |

## Activities Performed
----------------------

The following activities were performed during the record mode test:

### 1. "whoami" executed

* Output: `edem`
* Command: `whoami`
* Severity: Info
* Source: CLI
* Hostname: pswiki
* Occurred At: 2026-03-16T21:17:02.878174+00:00

### 2. "date" executed

* Output: `Mon Mar 16 09:16:11 PM UTC 2026`
* Command: `date`
* Severity: Info
* Source: CLI
* Hostname: pswiki
* Occurred At: 2026-03-16T21:17:02.96781+00:00

### 3. "sudo docker ps --format..." executed

* Output:
```
[sudo] password for edem:
NAMES                STATUS
logsmith_dashboard   Up 5 hours
pgadmin              Up 10 hours
pswiki-wiki-1        Up 10 hours
pswiki-db-1          Up 10 hours
n8n                  Up 10 hours
n8n_postgres         Up 10 hours
logsmith_db          Up 10 hours (healthy)
ollama               Up 10 hours
nginx-proxy-app-1    Up 10 hours
portainer            Up 10 hours
```
* Command: `sudo docker ps --format "table {{.Names}}\t{{.Status}}"`
* Severity: Info
* Source: CLI
* Hostname: pswiki
* Occurred At: 2026-03-16T21:17:03.063137+00:00

## Issues and Observations
-------------------------

None reported.

## Summary
----------

The record mode test was completed successfully. The activities performed during the test are documented above. The test did not reveal any issues or observations.