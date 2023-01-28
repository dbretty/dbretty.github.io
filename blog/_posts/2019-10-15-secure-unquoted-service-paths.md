---
layout: post
title: Secure Unquoted Service Paths
image: 
  path: /assets/img/posts/2019-10-15-secure-unquoted-service-paths/unquoted-services.png
description: >
  This post will walk you through a common flaw with service paths and quoting. <br/><br/>Being able to inject an executable into a service path can cause you all sorts of issues with your security teams.
sitemap: true
categories: [Security, Citrix]
comments: true
---
* 
{:toc}

This was brought to my attention recently when being “ethically” hacked and is an easy one to remediate but apparently one that has been around for a while.
{:.note title="Attention"}

### Risk

The remote Windows host contains services installed that use unquoted service paths, which contains at least one whitespace. A local attacker can gain elevated privileges by inserting an executable file in the path of the affected service.

### Problem

Essentially Windows Services have a path defined to the executable such as C:\Program Files\Application\service.exe. The issue is if this path does not have quotes around it then the service will try to start an executable in the parent path first. This could allow someone in you platform the access they require to inject a malicious executable onto your platform and execute it with a service restart.

Now there are loads of ways that you can fix this such as manually “quoting” each path in the registry or contacting the vendors to check if they have a fix for this.

First lets check and see what non Windows services are vulnerable to this exploit.

Open PowerShell on one of your servers that your users connect to and type in the following one liner

```powershell
cmd /c 'wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:windows\" | findstr /i /v """'
```
![Unquoted Services](/assets/img/posts/2019-10-15-secure-unquoted-service-paths/01.png)

There you can see all the services with unquoted paths – here check for any vendor specific services. You could go through and “Quote” all the services manually, but chances are (and as I did) you will get one wrong and or put the quotes in the wrong place and render the server useless.

### Solution 

Luckily, there is a script for this! Have a look at the script linked [here](https://github.com/VectorBCO/windows-path-enumerate/) and run this on your servers that your users are connecting to.

Once run you can re-run the command above and check that there are no non-windows services listed in there and that you have closed this one off the list for your next vulnerability scan.

Quick one but hopefully something useful.
