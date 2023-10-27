---
layout:      project
title:       Nutanix Cloud Platform CLI
date:        1 Feb 2023
image:
  path:       /projects/images/NcpCli.png
caption:     Nutanix Cloud Platform Command Line Interface.
description: >
  Nutanix has a great bunch of v4 Api's being released with every update of Prism Central. This module will allow you to manage and update all of your Prism Central registered clusters from PowerShell
links:
  - title:   PSGallery
    url:     https://www.powershellgallery.com/packages/Nutanix.NcpCli
  - title:   Source
    url:     https://github.com/dbretty/NcpCli
  - title:   Change Log
    url:     /NcpCli/changelog/
featured:    false
---

PowerShell module to automate the management of Nutanix Cloud Platform using the version 4 api's. Simple pre-build cmdlets such as:

```powershell
Get-PrismAlert
Get-PrismAudit
Get-LCMConfig
Get-LCMEntity
```

The reason behind creating this module is to enable fellow Nutanix fans to easily manage their environment using PowerShell as the v4 Api continues to grow. 

## Installing the Module

### PowerShell Support

Nutanix.NcpCli supports Windows PowerShell 5.1 and above.

### Install from the PowerShell Gallery

The Nutanix.NcpCli module is published in the [PowerShell Gallery](https://www.powershellgallery.com/packages/Nutanix.NcpCli/) 

You can install the module by entering the below commands in an elevated PowerShell session:

```powershell
Install-Module -Name Nutanix.NcpCli
Import-Module -Name Nutanix.NcpCli
```

### Updating the Module

If you have installed a previous version of the module from the gallery, you can install the latest update with `Update-Module` and the `-Force` parameter:

```powershell
Update-Module -Name Nutanix.NcpCli -Force
```

## Recommended Content

- [Nutanix Developer Portal](https://nutanix.dev)
- [v4 Api Reference](https://developers.nutanix.com/)