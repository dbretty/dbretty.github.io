---
layout:      project
title:       EUC Profile Buddy Server
date:        1 Jun 2024
image:
  path:       /projects/images/EUCProfileBuddyServer.png
caption:     Blazor web app to centrally manage your EUC Profile Buddy deployment.
description: >
  Profiles are hard to manage, there are a ton of options and configuration settings that could be in place and that's not even considering profile sprawl!
  <br/> <br/>
  This tool will allow your admins to centrally manage and view your EUC Profile Buddy deployment. It allows executed task views as well as profile summary views and supports a full Swagger and ReDoc Api.
links:
  - title:   Source
    url:     https://github.com/dbretty/EUC.Profile.Buddy.Server
  - title:   Change Log
    url:     /EUCProfileBuddyServer/changelog/
featured:    false
---
* 
{:toc}

This Blazor web application will allow you to contrally manage your EUC Profile Buddy deployment. You have the ability to check on tasks executed as well as view a profile summary for the deployed agents across your estate. It also has a full Swagger and ReDoc Api.

## Installing EUC Profile Buddy Server

To install EUC Profile Buddy just download the release from the [GitHub](https://github.com/dbretty/EUC.Profile.Buddy.Server/releases) repository and extract the files into a directory on your Windows Server. When you launch the application it will run on port 8080.

![](/assets/img/posts/EUCProfileBuddyServer/EUC_01.png)

## Setting Up The EUC Profile Buddy Service

To run EUC Profile Buddy Server as a Windows Service open a command prompt on your Windows Server as Administrator and run the following command.

```SC CREATE <SERVICE_NAME> binPath="<BIN_PATH"```

example:

```SC CREATE EUC.Profile.Buddy.Server binPath="C:\WebServer\EUC.Profile.Buddy.Web.exe"```

## Client Side Configuration

By default the EUC Profile Buddy client will not log to a central server. In order to enable this logging you need to deploy 2 registry keys to the following path ```HKEY_CURRENT_USER\Software\EUCProfileBuddy```

- LogToServer (String [Yes/No])
- LoggingServer (String [Server FQDN])

Once done the clients will start to log back to the central server.

## Using EUC Profile Buddy Server

### Dashboard Screen

The first screen shown will be the profile overview dashboard (shown below) where you will see an overview of the profile types deployed as well as the tasks being executed.

### Navigation

On the left hand side you will see a navigation menu that will allow you to browse the various pages available to you.

![](/assets/img/posts/EUCProfileBuddyServer/EUC_02.png)
  
### User Profile Summary

If you click on the ```User Profile Summary``` link you will see a searchable screen for all the user profiles that have been reported back to the server. From here you will be able to see what types of profiles you have in your estate as well as the details for temp data that can be purged.

![](/assets/img/posts/EUCProfileBuddyServer/EUC_03.png)

### Audit Task Information

If you click on the ```Audit Task Information``` link you will see a searchable screen for all the tasks that have been executed by EUC Profile Buddy in your estate. From here you will be able to see what types of task was executed as well as the current status of that task.

![](/assets/img/posts/EUCProfileBuddyServer/EUC_04.png)

### Swagger Api

If you click on the ```Api Swagger``` link you will have access to the Swagger Api for the server. The currently available endpoints are shown below.

![](/assets/img/posts/EUCProfileBuddyServer/EUC_05.png)

### ReDoc Api

If you click on the ```Api ReDoc``` link you will have access to the ReDoc Api documentation.

![](/assets/img/posts/EUCProfileBuddyServer/EUC_06.png)

## Requesting New Features

To request a new feature for EUC Profile Buddy please use [this](https://github.com/dbretty/EUC.Profile.Buddy.Server/issues/new/choose) GitHub feature request form.

## Reporting Bugs

To report a bug in EUC Profile Buddy please use [this](https://github.com/dbretty/EUC.Profile.Buddy.Server/issues/new/choose) GitHub bug form.

If you would like to get in touch with me for any reason or request new features for the utility then you can use [this](/contact) form or email me at [dave@bretty.me.uk](mailto:dave@bretty.me.uk)