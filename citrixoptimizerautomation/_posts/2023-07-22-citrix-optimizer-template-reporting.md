---
layout: post
title: Citrix Optimizer Reporting Tool
image: 
  path: /assets/img/citrixoptimizerautomation/2023-07-22-citrix-optimizer-template-reporting/2023-07-22-citrix-optimizer-template-reporting00.png
description: >
  This post will show you how to generate a Citrix Optimizer Template report in Markdown using the Citrix Optimizer Automation PowerShell Module.<br/><br/>The report is generated in Markdown and you can use any of you preferred VSCode etc conversion tools to move that to a PDF for management. 
sitemap: true
categories: [PowerShell, VDA, Citrix, Performance, Optimization]
comments: true
---
* 
{:toc}

### Overview

Ever been asked by the boss

I know we optimize our virtual machine master images for performance, but what settings do we actually apply?
{:.note title="Attention"}

I have, and it's at that point that I start to trawl through Citrix Optimizer and take hundreds of screenshots of every optimization in place and put them into a Word doc to send over. That's what this is for. A simple automated way to produce a markdown report based off **ANY** Citrix Optimizer Template that you run across your master images.

### Citrix Optimizer Automation

First you will need the Citrix Optimizer Automation PowerShell module. You can install it by following the below.

#### Install from the PowerShell Gallery

The Citrix Optimizer Automation module is published in the [PowerShell Gallery](https://www.powershellgallery.com/packages/CitrixOptimizerAutomation/) 

You can install the module by entering the below commands in an elevated PowerShell session:

```powershell
Install-Module -Name CitrixOptimizerAutomation
Import-Module -Name CitrixOptimizerAutomation
```

#### Updating the Module

If you have installed a previous version of the module from the gallery, you can install the latest update with `Update-Module` and the `-Force` parameter:

```powershell
Update-Module -Name CitrixOptimizerAutomation -Force
```

### Getting a Report

Once you have the module installed it's as simple as running the below command passing in the path to the XML file (Citrix Optimizer Template) you want the report for.

```powershell
New-CitrixTemplateReport -Path $Path "Bretty VMware OSOT Import.xml" -All
```

That command will take the XML file and produce a report for every setting contained within it.  If you don't want it all, say you just want the registry entries defines you would type in the following.

```powershell
New-CitrixTemplateReport -Path $Path "Bretty VMware OSOT Import.xml" -Registry
```

The switches available are

| Switch | Description |
| :--- | :--- |
| All | Report on every entry defined |
| Registry | Report the Registry Entries |
| ScheduledTask | Report the Scheduled Task Entries |
| Service | Report the Service Entries |
| PowerShell | Report the PowerShell Entries |

If you want more that one entry type then you can combine the switches as below.

```powershell
New-CitrixTemplateReport -Path $Path "Bretty VMware OSOT Import.xml" -Registry -ScheduledTask -PowerShell
```

### Examples Of Output

Below are some screenshots of what you can expect to see in the report.

#### Metadata Section

![Metadata Section](/assets/img/citrixoptimizerautomation/2023-07-22-citrix-optimizer-template-reporting/2023-07-22-citrix-optimizer-template-reporting01.png)

#### Registry Entry

![Registry Section](/assets/img/citrixoptimizerautomation/2023-07-22-citrix-optimizer-template-reporting/2023-07-22-citrix-optimizer-template-reporting02.png)

#### Scheduled Task Entry

![Scheduled Task Section](/assets/img/citrixoptimizerautomation/2023-07-22-citrix-optimizer-template-reporting/2023-07-22-citrix-optimizer-template-reporting03.png)

#### Service Entry

![Service Section](/assets/img/citrixoptimizerautomation/2023-07-22-citrix-optimizer-template-reporting/2023-07-22-citrix-optimizer-template-reporting04.png)

#### PowerShell Entry

![PowerShell Section](/assets/img/citrixoptimizerautomation/2023-07-22-citrix-optimizer-template-reporting/2023-07-22-citrix-optimizer-template-reporting05.png)

Thanks for reading !