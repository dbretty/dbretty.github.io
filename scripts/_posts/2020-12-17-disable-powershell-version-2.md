---
layout: post
title: Disable PowerShell v2
image: 
  path: /assets/img/scripts/security_banner.jpg
description: >
  Disable PowerShell Version 2 from your system.
sitemap: true
categories: [Security, PowerShell]
---

To disable PowerShell Version 2 from your system you can run the following script.

## Disable PowerShell Version 2 

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