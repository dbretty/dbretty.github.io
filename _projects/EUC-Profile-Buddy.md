---
layout:      project
title:       EUC Profile Buddy
date:        1 Jan 2024
image:
  path:       /projects/images/EUCProfileBuddy.png
caption:     Profile Management Utility for End User Computing.
description: >
  Profiles are hard to manage, there are a ton of options and configuration settings that could be in place and that's not even considering profile sprawl!
  <br/> <br/>
  This tool will allow your admins and end users to manage their profile from a system tray application or command line, get specific details about the configuration settings as well as perform common tasks.
links:
  - title:   Source
    url:     https://github.com/dbretty/EUCProfileBuddy
  - title:   Change Log
    url:     /EUCProfileBuddy/changelog/
featured:    false
---
* 
{:toc}

This utility will allow you to manage your End User Computing (EUC) profiles directly from the system tray or the command line. You have the ability to check on profile size and data usage along with clearing out unused files and folders. It will also allow you to perform common tasks on the profile to stop the need for that call to support to resolve issues. There is a profile detail screen to show the user the configuration settings in place driving the profile solution.

## Installing EUC Profile Buddy

To install EUC Profile Buddy just download the release from the [GitHub](https://github.com/dbretty/EUCProfileBuddy/releases/tag/2402.001) repository and run it in the user context. When you launch the application with no command line switches it will run from the system tray by default and you will see the following tray notification.

![](/assets/img/posts/EUCProfileBuddy/EUCProfileBuddy_01.png)

If you double click on the icon it will open up the utility and allow you to start to manage your profile.

## Using EUC Profile Buddy (User Context)

Running EUC Profile Buddy in the user context is simple, its a single exe ```EUCProfileBuddy.exe``` file that you can launch at login using any number of methods. Once launched the user just has to double click the system tray icon to open up the application.

<note>
You will have to have system tray icons available and notifications enabled in order to get the full functionality of EUC Profile Buddy.
</note>

### Profile Detail Screen

The first screen shown will be the profile overview (shown below) where the user is able to see common details about their profile and data usage sizes.

![](/assets/img/posts/EUCProfileBuddy/EUC_02.png)

#### Main Display

From the main display screen they are able to see the following details:

- User Name
- Profile Directory
- Profile Size
- Data Browser
  
#### Additional Details

If you click on the ```Show Detail``` button

![](/assets/img/posts/EUCProfileBuddy/EUC_03.png)

An additional screen will pop up with a scrollable box containing the relevant configuration settings in place for your profile solution. Currently the tool supports:

- Local Profile
- Citrix Profile Management
- Microsoft FSLogix

![](/assets/img/posts/EUCProfileBuddy/EUC_04.png)

You can close this window using the X in the top right of the form.

### Profile Data Browser

On the main screen there is a data browser sorted by folder size. You can double click on any folder in the list to drill down and get additional details about the files and folders within that directory as well as a sorted list of size. 

#### Navigation

The example below shows the AppData folder in my profile.

![](/assets/img/posts/EUCProfileBuddy/EUC_05.png)

To go ```Up``` or ```back a level``` use the back button shown below.

![](/assets/img/posts/EUCProfileBuddy/EUC_06.png)

#### Deleting Files

To remove files and folders from your profile you are able to right click on any item displayed in the data browser and select ```Delete```

![](/assets/img/posts/EUCProfileBuddy/EUC_07.png)

This will prompt you to delete the files.

![](/assets/img/posts/EUCProfileBuddy/EUC_08.png)

Once selected it will remove the data from the profile and recalculate the profile size.

### Common Tasks

There are a number of common tasks available that will allow the user to execute certain actions within their profiles without admin intervention. These are available from the ```Select Action``` drop down at the bottom of the screen then clicking the ```Execute Action``` button.

![](/assets/img/posts/EUCProfileBuddy/EUC_09.png)

#### Clear Temp Files

Clear Temp Files will remove all the temporary files from the profile.  By default the following directories are cleared out:

- AppData\Local\Temp
- AppData\Roaming\Temp
- AppData\Local\Microsoft\Windows\GameExplorer
- AppData\Local\Microsoft\Windows\WER
- AppData\Local\Microsoft\Windows\CrashReports
- AppData\Local\Microsoft\MSOIdentityCRL\Tracing
- AppData\Local\CrashDumps
- AppData\Local\Package Cache
- AppData\Local\D3DSCache
- AppData\Local\Microsoft\Windows\WebCache.old

This list was taken from Aaron's Profile Pruning logic that can be found [here](https://github.com/aaronparker/FSLogix/blob/main/Profile-Cleanup/README.MD) - thanks Aaron!

These can be added to by using the relevant registry keys in the Configure EUC Buddy section of this document.

#### Reset Microsoft Edge

This action will reset Microsoft Edge to its default settings allowing the user to re-configure their browser.

#### Run Custom Scripts

This option will allow you to run custom written clean up scripts in the context of the user. Please note that the user will need the ability to run PowerShell on the system and the script should consider the rights assigned to the user when running.

##### Script Location

All Custom Scripts should be placed in the following directory within the user profile

```C:\Users\UserName\AppData\Local\EUCProfileBuddy```

along with this the script file will need to be referenced in the CustomScripts registry key as details in the Configure EUC Buddy section of this document.

##### Script File Format

EUC Buddy will execute PowerShell scripts only so ensure that your script files have a ```.ps1``` file extension.

## Using EUC Profile Buddy (Admin Context)

EUC Profile Buddy will accept the following command line switches allowing it to be run silently as part of a login, logout or maintenance task within the user context.

### Command Line Switches

#### ClearTemp

```/ClearTemp``` will run the same action as the Clear Temp Files option within the application but with no user input.

#### CustomScripts

```/CustomScripts``` will run the same action as the Run Custom Scripts option within the application but with no user input.

## Configuring EUC Profile Buddy

All configuration for EUC Profile Buddy is done within the registry so it is easy to centrally configure the application using Microsoft Group Policy. The configurable registry values can be found within the following registry key.

```HKEY_CURRENT_USER\Software\EUCProfileBuddy```

### Directories

The Directories value is a ```REG_MULTI_SZ``` type and contains a list of directories that you would like to consider as temp data. These have to be a fully qualified path from the root of the user profile, an example can be seen below.

```AppData\Local\Temp```

### Files

The Files value is a ```REG_MULTI_SZ``` type and contains a list of files that you would like to consider as temp data. These have to be a fully qualified path from the root of the user profile, an example can be seen below.

```AppData\Local\Temp\file1.txt```

### CustomScripts

The CustomScripts value is a ```REG_MULTI_SZ``` type and contains a list of scripts that you would like to run. These scripts need to reside in the directory listed above, an example can be seen below.

```tempscript.ps1```

## Requesting New Features

To request a new feature for EUC Profile Buddy please use [this](https://github.com/dbretty/EUCProfileBuddy/issues/new/choose) GitHub feature request form.

## Reporting Bugs

To report a bug in EUC Profile Buddy please use [this](https://github.com/dbretty/EUCProfileBuddy/issues/new/choose) GitHub bug form.

If you would like to get in touch with me for any reason or request new features for the utility then you can use [this](/contact) form or email me at [dave@bretty.me.uk](mailto:dave@bretty.me.uk)