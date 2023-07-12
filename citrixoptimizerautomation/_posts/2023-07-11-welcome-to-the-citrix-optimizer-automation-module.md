---
layout: post
title: PowerShell Citrix Optimizer Automation Module
image: 
  path: /assets/img/citrixoptimizerautomation/2023-07-11-welcome-to-the-citrix-optimizer-automation-module/2023-07-11-welcome-to-the-citrix-optimizer-automation-module.png
description: >
  This post will run through what the Citrix Optimizer Automation module is and how to use it.<br/><br/>This is meant as a starting point for the module and to get feedback on the functionality of the module and if it is useful. 
sitemap: true
categories: [PowerShell, VDA, Citrix]
comments: true
---
* 
{:toc}

### Overview

It's no secret that Citrix have a great optimization tool for your base images. You can find it [here](https://support.citrix.com/article/CTX224676/citrix-optimizer-tool) and there are also a bunch of good community driven templates available to add additional functionality to your optimizations.

But, what if you want to automate the creation of a template based on a text file full of registry keys or a CSV file that you have spent ages getting together with all of your optimal setting in. Well, that's where this module comes in, you can automate the creation of your Citrix Optimizer Templates in order to rule out the huge amount of time you will need to spend in the GUI and seamlessly create new templates for your deployment.

### Latest Version

The latest version available via the PSGallery and includes the following cmdlets:

- ```New-CitrixTemplate```
- ```New-CitrixTemplateGroup```
- ```New-CitrixTemplateService```
- ```New-CitrixTemplateTask```
- ```New-CitrixTemplateRegistry```
- ```New-CitrixTemplateScript```

Details of how these ```cmdlets``` work can be found in the MD help files on the [Github Repo](https://github.com/dbretty/CitrixOptimizerAutomation).

I have tried my best to ensure the code is error proof and 'up to standard' but please, if you find issues feel free to report them on Github and I will do my best to resolve them asap.

Thanks for reading.