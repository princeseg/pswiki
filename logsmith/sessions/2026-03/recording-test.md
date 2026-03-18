---
title: Recording Test
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-18T02:53:28.037Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-18T02:53:28.037Z
---

# Recording Test
## Overview
This document captures the recording test results for the build guide, showcasing the execution of specific commands in an on-prem environment.

## Environment
The test was conducted on-prem, utilizing the specified environment.

## Step-by-Step Build Guide
1. **Executing the `ls` command**
    ```bash
ls
```
    Output:
    ```
[?2004l
logsmith.py  __pycache__  test_recording.txt
```
    This command was executed successfully, producing the expected output.

2. **Executing the `docker ps` command**
    ```bash
docker ps
```
    Output:
    ```
[?2004l
permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
```
    The `docker ps` command failed due to a permission denied error.

3. **Executing the `whoami` command**
    ```bash
whoami
```
    Output:
    ```
[?2004l
edem
```
    This command was executed successfully, producing the expected output.

## Troubleshooting
* The `docker ps` command failed with a permission denied error.
  * Failed command:
    ```bash
docker ps
```
  * Fix: Ensure that the Docker API is accessible and the user running the command has the necessary permissions. Verify that the Docker socket is correctly configured and the user has the required privileges to connect to it.
* No other issues were encountered during the test.

## Summary
The recording test was completed successfully, capturing the execution of the `ls`, `docker ps`, and `whoami` commands. The test results are documented for future reference and to aid in the development of the build guide.