---
title: Intelligent Filter Test
description: Logsmith auto-generated session documentation
published: true
date: 2026-03-17T18:28:56.111Z
tags: logsmith
editor: markdown
dateCreated: 2026-03-17T18:28:56.111Z
---

# Session: Intelligent Filter Test
## Overview
This document outlines the test session for intelligent activity filtering on-prem, including the step-by-step build guide and troubleshooting steps.

## Environment
### Overview
The test was performed on-prem using the following components:
- Logsmith container
- Specific image name

## Step-by-Step Build Guide
1. **Fixed Image Name and Started Container Successfully**

    To start the Logsmith container with the correct image name, follow these steps:

    ```bash
# Navigate to the project directory
cd /path/to/project
# Start the Logsmith container with the correct image name
docker run -d --name logsmith --image <image-name> logsmith
```

    This command initializes the Logsmith container with the correct image name.

## Troubleshooting
### Attempted to Start Logsmith Container with Wrong Image Name

    **Issue:** The Logsmith container could not be started due to an incorrect image name.

    **Fix:** Ensure the correct image name is used in the `docker run` command.

    ```bash
# Correct the image name in the command
docker run -d --name logsmith --image <correct-image-name> logsmith
```

    If the issue persists, verify the image name and its format.

## Summary
The session for intelligent activity filtering on-prem was successfully completed. The build guide and troubleshooting steps are included in this document for future reference.