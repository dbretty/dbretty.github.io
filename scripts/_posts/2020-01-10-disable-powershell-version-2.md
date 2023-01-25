---
layout: post
title: Disable PowerShell Version 2
image: 
  path: /assets/img/scripts/disable-posh2.png
description: >
  This script will disable PowerShell Version 2 on your system. It will alter your servers ability to operate PowerShell and should be tested before implementing into your production environment.
sitemap: true
categories: [Security, PowerShell]
comments: true
---
* 
{:toc}

## Script Description
This script will Disable PowerShell version 2 from your system in order to disable the ability to run PowerShell without any logging.

## Script Deployment

Open up PowerShell as an Administrator on the system you wish to lock and run the below commands. Be sure to restart all PowerShell sessions after you have run the script.

## Script Contents 

```powershell
# Determine where to do the logging
$log = "C:\Windows\Temp\disable_posh_legacy.log"

# Start the transcript for the install
Start-Transcript $Log

# Disable Legacy Powershell Versions
try 
{
    Write-Verbose "Disabling Powershell v2" -Verbose
    Disable-WindowsOptionalFeature -Online -FeatureName microsoftwindowspowershellv2
    Write-Verbose "Powershell v2 Disabled" -Verbose
}
catch 
{
    Write-Verbose "There was a problem disabling Powershell v2 - Please check the logs for more information" -Verbose
}

# Stop Logging
Write-Verbose "Stop logging" -Verbose
Stop-Transcript
```