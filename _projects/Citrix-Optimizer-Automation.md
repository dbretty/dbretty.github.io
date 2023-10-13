---
layout:      project
title:       Citrix Optimizer Automation
date:        1 Jan 2023
image:
  path:       /projects/images/CitrixOptimizerAutomation.jpg
caption:     Automation Framework for Citrix Optimizer.
description: >
  It's no secret that Citrix have a great optimization tool for your base images. You can find it [here](https://support.citrix.com/article/CTX224676/citrix-optimizer-tool) and there are also a bunch of good community driven templates available to add additional functionality to your optimizations.
  <br/> <br/>
  But, what if you want to automate the creation of a template? This PowerShell module will allow you to do exactly that!
links:
  - title:   PSGallery
    url:     https://www.powershellgallery.com/packages/CitrixOptimizerAutomation
  - title:   Source
    url:     https://github.com/dbretty/CitrixOptimizerAutomation
  - title:   Change Log
    url:     /CitrixOptimizerAutomation/changelog/
featured:    false
---

PowerShell module to automate the creation of Citrix Optimizer Templates using simple pre-build cmdlets such as:

* New-CitrixTemplate
* New-CitrixTemplateGroup
* New-CitrixTemplateService
* New-CitrixTemplateTask
* New-CitrixTemplateRegistry
* New-CitrixTemplateScript

The reason behind creating this module is the time is takes to build up the template files using the user interface. Often customers have various CSV files and JSON files with the settings you want applied to a master image, with this module you are able to parse those files and inject the settings automatically.

## Installing the Module

### PowerShell Support

Citrix Optimizer Automation supports Windows PowerShell 5.1 and above.

### Install from the PowerShell Gallery

The Citrix Optimizer Automation module is published in the [PowerShell Gallery](https://www.powershellgallery.com/packages/CitrixOptimizerAutomation/) 

You can install the module by entering the below commands in an elevated PowerShell session:

```powershell
Install-Module -Name CitrixOptimizerAutomation
Import-Module -Name CitrixOptimizerAutomation
```

### Updating the Module

If you have installed a previous version of the module from the gallery, you can install the latest update with `Update-Module` and the `-Force` parameter:

```powershell
Update-Module -Name CitrixOptimizerAutomation -Force
```

## Documentation

| **Command** | **Description** |
| :----------- | :----------- |
| [New-CitrixTemplate](Help/New-CitrixTemplate.MD)      | Creates a new Citrix Optimizer template |
| [New-CitrixTemplateGroup](Help/New-CitrixTemplateGroup.MD)   | Creates a new Group |
| [New-CitrixTemplateService](Help/New-CitrixTemplateService.MD)   | Creates a new Windows Service definition |
| [New-CitrixTemplateTask](Help/New-CitrixTemplateTask.MD)   | Creates a new Windows Scheduled Task definition |
| [New-CitrixTemplateRegistry](Help/New-CitrixTemplateRegistry.MD)   | Creates a new Windows Registry definition |
| [New-CitrixTemplateScript](Help/New-CitrixTemplateScript.MD)   | Creates a new PowerShell Script definition |