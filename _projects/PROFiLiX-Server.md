---
layout:      project
title:       PROFiLiX Server
date:        1 Sept 2024
image:
  path:       /projects/images/PROFiLiXServer.png
caption:     Blazor server side web application to centrally manage your PROFiLiX client deployment.
description: >
  Profiles are hard to manage, there are a ton of options and configuration settings that could be in place and that's not even considering profile sprawl!
  <br/> <br/>
  This tool will allow your admins to centrally manage and view your PROFiLiX client deployment. It allows executed task views as well as profile summary views and supports a full Swagger and ReDoc Api. You can also send remote tasks to perform maintenance on profiles to the end point from the web console.
links:
  - title:   Source
    url:     https://github.com/dbretty/PROFiLiX.Server
  - title:   Change Log
    url:     /PROFiLiXServer/changelog/
featured:    false
---

* 
{:toc}

This Blazor server side web application that allows you to centrally manage your PROFiLiX client deployment. It allows executed task views as well as profile summary views and supports a full Swagger and ReDoc Api. You can also send remote tasks to perform maintenance on profiles to the end point from the web console.

## Installing PROFiLiX Server

### MSI Installer 

To install PROFiLiX Server just download the release from the [GitHub](https://github.com/dbretty/PROFiLiX.Server/releases) repository and extract the msi installer into a directory on your Windows Server. 

Launch the installer.

![](/assets/img/posts/PROFiLiXServer/1.png)

Click Next past the welcome screen.

![](/assets/img/posts/PROFiLiXServer/2.png)

Accept the license agreement and click next.

![](/assets/img/posts/PROFiLiXServer/3.png)

Select the directory you want to install PROFiLiX Server to (Note: the back end database will also reside in this directory), click next.

![](/assets/img/posts/PROFiLiXServer/4.png)

Click on Install.

![](/assets/img/posts/PROFiLiXServer/5.png)

If installed correctly you will see confirmation that the product is installed.

![](/assets/img/posts/PROFiLiXServer/6.png)

Right click the desktop icon and select Run as Administrator.

![](/assets/img/posts/PROFiLiXServer/7.png)

This will launch the application and seed the database for you on first run. Once this is done for the first time you can set the PROFiLiX Server to run as a service as shown in the section below.

![](/assets/img/posts/PROFiLiXServer/8.png)

Thats it. Open a browser and navigate to http://localhost:5120 to use PROFiLiX Server.

### Setting Up The PROFiLiX Server Service

To run PROFiLiX Server as a Windows Service open a command prompt on your Windows Server as Administrator and run the following command.

```SC CREATE <SERVICE_NAME> binPath="<BIN_PATH"```

example:

```SC CREATE PROFiLiX.Server binPath="C:\Program Files\PROFiLiX Server\PROFiLiX.Web.exe"```

### Client Side Configuration

By default the PROFiLiX client will not log to a central server. In order to enable this logging you need to deploy 2 registry keys to the following path ```HKEY_CURRENT_USER\Software\PROFiLiX```

| Value Name | Type | Options |
| --- | --- | --- |
| LogToServer | String | Yes/No |
| LoggingServer | String | Server FQDN |
| LoggingServerPort | String | Listening Port |
| LoggingServerProtocol | String | (http/https) |

Once done the clients will start to log back to the central server.

## Using PROFiLiX Server

### Dashboard Screen

The dashboard screen (shown above) gives you an overview of your profile deployment. You can see the total number of profiles registered along with the total Size for the profiles as well as the temporary data. Next you can see a profile type breakdown. PROFiLiX Server currently supports the following profile types:

- Citrix Profile Management
- Microsoft FSLogix
- Liquidware ProfileUnity
- Local

If you want an additional profile type supported please reach out and I will add it.

Below the profile summary you can see an overview of any tasks that have been executed by PROFiLiX Server.

### Navigation

The Navigation menu on the left allows you to move through various parts of PROFiLiX Server.  Please note that the options displayed will vary depending on the logged in users rights.

![](/assets/img/posts/PROFiLiXServer/9.png)

### User Profile Overview

The User Profile Overview will show the same data as the dashboard as well as a searchable table allowing you to pinpoint a particular profile to see the detail for.

![](/assets/img/posts/PROFiLiXServer/10.png)

### Task Overview

The Task Overview page will show a total and searchable list of all the tasks executed on endpoints by PROFiLiX Server.

![](/assets/img/posts/PROFiLiXServer/11.png)

### Login

To log into PROFiLiX Server you can use the default username of ```admin@profilix.com``` and the password of ```SuperSecretPassword.123``` as this account is seeded for you the first time you run PROFiLiX Server.

### Profile Actions

The Profile Actions page allows you to execute a number of pre-defined tasks on any currently connected PROFiLiX clients. When you select and action and click execute to task will be sent to the client and a relevant entry put into the PROFiLiX Tasks page. You will also see a notification that the task has been started.  Once complete the task will be updated with the relevant status.

![](/assets/img/posts/PROFiLiXServer/12.png)

### System Users

The System Users options allows you to see a list of registered System Users as well as add new users to the PROFiLiX Server with a relevant role.

![](/assets/img/posts/PROFiLiXServer/13.png)

### API

The 2 links for Swagger and ReDoc allow you to browse the API available for PROFiLiX Server and gather data outside of the server side component.

### Manage Your Account

Once logged in you also have the option to manage your account including changing the password, updating the profile and deleting your account from PROFiLiX Server.

![](/assets/img/posts/PROFiLiXServer/14.png)

## Requesting New Features

To request a new feature for PROFiLiX Server please use [this](https://github.com/dbretty/PROFiLiX.Server/issues/new/choose) GitHub feature request form.

## Reporting Bugs

To report a bug in PROFiLiX Server please use [this](https://github.com/dbretty/PROFiLiX.Server/issues/new/choose) GitHub bug form.

If you would like to get in touch with me for any reason or request new features for the utility then you can use [this](/contact) form or email me at [dave@bretty.me.uk](mailto:dave@bretty.me.uk)