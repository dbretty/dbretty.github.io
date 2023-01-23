---
layout: post
title: Enable PowerShell Constrained Language Mode
image: 
  path: /assets/img/scripts/security_banner.jpg
description: >
  Create an environment variable to ensure Constrained Language Mode when running PowerShell.
sitemap: true
categories: [Security, PowerShell]
---

To enable Constrained Language Mode when running PowerShell for all users of your system you can run the following script.

## Enable PowerShell Constrained Language Mode 

```powershell
[Environment]::SetEnvironmentVariable('__PSLockdownPolicy', 4, 'Machine')
```