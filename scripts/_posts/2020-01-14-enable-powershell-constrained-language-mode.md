---
layout: post
title: Enable PowerShell Constrained Language Mode
image: 
  path: /assets/img/scripts/constrained-language.png
description: >
  This script will set an environment variable on the system to enable PowerShell Constrained Language Mode. It will alter your servers ability to operate PowerShell and should be tested before implementing into your production environment.
sitemap: true
categories: [Security, PowerShell]
comments: true
---
* 
{:toc}

## Script Description
This script will set an environment variable on the system to enable PowerShell Constrained Language Mode.

This can also be achieved using Microsoft GPP to implement and Environment Variable.
{:.note title="Attention"}

## Script Deployment

Open up PowerShell as an Administrator on the system you wish to lock and run the below commands. Be sure to restart all PowerShell sessions after you have run the script.

## Script Contents 

```powershell
[Environment]::SetEnvironmentVariable('__PSLockdownPolicy', 4, 'Machine')
```