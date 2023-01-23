---
layout: post
title: Secure PowerShell in your EUC environment
image: 
  path: /assets/img/2019-10-15-secure-powershell-in-your-euc-environment/01_banner.png
description: >
  This post will walk you through securing PowerShell in your EUC environment.
sitemap: true
categories: [Security, PowerShell]
---

PowerShell is no doubt one of the most prolific tools used in End User Computing (EUC) for administering your estate.

This is a good thing and often makes thing a lot easier for us as administrators, however, it is also one of the most used tools for pen testing teams to attack your estate.

> The Trickbot malware that targets bank customers. Password harvesters like Mimikatz. “Fileless malware” attacks. All three are popular hacking tools and techniques, but they’re unconnected except for one trait: They all rely in part on manipulating a Windows management tool known as PowerShell to carry out their attacks.

> wired.com – “Microsoft PowerShell is a hot hacking tool”

So, how to we protect ourselves against this and still enable us to do our jobs in a secure and effective manner.

Having been knee-deep in an “ethical” hack of an EUC platform for the past 6 months and also having listened to some of Partick Cobles presentations to say this is a topic that interests me is an understatement.

The remainder of this article will provide you the steps you need to take to secure your PowerShell environment within your EUC estate whilst still leave it usable for your Administrators.

This is **ONLY THE START!** 

I will explain the basics of locking down your PowerShell environment and whilst this is a very good starting point there are so many techniques that you may use to achieve even more security. I did find that for the most part this worked for me.

Also, this is not just specific to a Citrix Environment. These principles can be applied to Citrix, Microsoft, VMware or indeed ANY of the vendors that provide Virtual Apps and Desktops to your users.

First let's have a look at the steps we will run through to achieve this.

- [Patch and Upgrade](#patch-and-upgrade)
- [Disable PowerShell v2](#disable-powershell-v2)
- [Constrained Language Mode](#constrained-language-mode)
- [Secure Execution Rights](#secure-execution-rights)

#### Patch and Upgrade
First thing you will need to do is Patch and Upgrade your operating systems. Now, you should be doing this anyway as a standard but if you are not at least upgrade your PowerShell platform to version the latest available. 2 of the reasons to do this are:

- Enhancements in PowerShell Logging (older versions has none)
- Language Mode Enhancements

I will explain about these further down in the post, but in essence, **upgrade PowerShell** 

There are some prerequisites to upgrading, so you should take these into account before you start. Also upgrade everything in your stack (workers, infrastructure, jump boxes)

- .NET Framework 4.5.2 installed, not required for the installation just to enable all the latest PowerShell features.
- Windows 7 or Server 2008R2 machines with PowerShell v3 installed, you must either save the value of PSModulePath or first upgrade to v4 first
- Reboot required for upgrade

So, now you are up to the latest PowerShell lets just check that. You can do this by logging onto any of your servers and running the following command (in PowerShell of course), please note this does not cater for PowerShell Core.

```powershell
$PSVersionTable
```

![](/assets/img/2019-10-15-secure-powershell-in-your-euc-environment/02_psversion.png)

#### Disable PowerShell v2
As I mentioned earlier, PowerShell v2 has no logging. This means that anyone who gains access to your platform can do pretty much what they want with PowerShell and leave no trace on your system, not something you want. Fortunately Microsoft have made it pretty easy to disable this once you are on the later versions. If you want to test prior to see is version 2 is enabled type in the following 2 commands:

```powershell
PowerShell -Version 2
$PSVersionTable
```

![](/assets/img/2019-10-15-secure-powershell-in-your-euc-environment/03_powershell_v2.png)

> Server 2016 may not have the .Net versions installed, so you may not be able to get into v2 already – disable it anyway just in case someone installs the older .Net version.

In order to disable version 2 just use the below PowerShell script then try and execute the above 2 commands again, you should be prevented from accessing version 2.

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

Now that you have disabled v2 lets move on and look at constrained language mode.

#### Constrained Language Mode
What is “Constrained Language Mode?”

PowerShell Constrained Language is a language mode of PowerShell designed to support day-to-day administrative tasks, yet restrict access to sensitive language elements that can be used to invoke arbitrary Windows APIs.

Essentially is locks down the commands you can run within PowerShell and locks out many of the commands that you can use to undertake malicious activity. 

> Enabling this will break certain applications you run so check before implementing this across the board. I do not have this enabled on my master images as it breaks the Base Image Scripting Framework. However, I do have it enabled on the user VDA’s.

To check what language mode you are using enter the following command

```powershell
$ExecutionContext.SessionState.LanguageMode
```
![](/assets/img/2019-10-15-secure-powershell-in-your-euc-environment/04_language_mode.png)

You can see I am running in **FullLanguage** meaning I can pretty much do anything. I can change this in session by typing the following

```powershell
$ExecutionContext.SessionState.LanguageMode = "ConstrainedLanguage"
$ExecutionContext.SessionState.LanguageMode
```

However, the user can just start a new session and get back to FullLanguage. To get round this I put a SYSTEM environment variable in to lock the system to ConstrainedMode. Below is a PowerShell script you can use to achieve this

```powershell
[Environment]::SetEnvironmentVariable('__PSLockdownPolicy', 4, 'Machine')
```

You will see this will add a system variable to enable Constrained Language Mode. To test this has worked you can rerun the same command in a new PowerShell window, and you will see you are locked into constrained mode.

![](/assets/img/2019-10-15-secure-powershell-in-your-euc-environment/05_posh_constrained.png)

With this done, lets move onto securing the run rights of PowerShell.

#### Secure Execution Rights
We now need to control who can execute PowerShell in your environment. There are many ways of doing this but in this case I will use AppLocker to achieve this as it's built in and freely available.

This does apply most restrictive rights so what I do (and this is by no means the only way) is created a group that ALL standard users of my platform go in. In this case its a group called “Standard_Virtualization_User”. This is a Change Auditor locked group so no users can remove themselves from it etc.


Next I will create a Group Policy Object to restrict access to PowerShell based on this group.

NOTE: When you create the new policy for AppLocker make sure you first create the default rules. Here is a link to show you how to create these default rules. This is designed to allow Windows to operate normally.

https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/create-applocker-default-rules

Create a new Group Policy and go to Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Application Control Policies -> AppLocker -> Executable Rules

Right click and create a new rule, click next then select Deny and select your new group.


Click next then select Path


Click next and select Browse Folders and select the PowerShell folder


Click Next, Next and Create


This will create the rule for you. Happy days – PowerShell cannot be run now….but wait! What is I copy the powershell.exe file out of that directory and run it from somewhere else.

Right click and create another rule, click Next, select Deny and select the same group as before


Click next and select File Hash


Click Next and Browse to the PowerShell directory, select powershell.exe and powershell_ise.exe

NOTE: The file has WILL change when these components are patched so make sure you update the rules


Click next and Create and you will see the rules created


So, lets test this out. Log into a Server and run

gpupdate /force
This should pull down the new policy, you can check this by running gpedit.msc and checking the AppLocker Rules, you should see the new rules in there.



If you now log in as your standard user and try to run PowerShell or the ISE you will get an access denied error


NOTE: This can also be delivered via Citrix Workspace Environment Manager and there are some special considerations around that but I will cover those in a different post.

So, thats PowerShell locked down for the Standard Users, lets move onto Setting You Script Execution Policies

Set Script Execution Policies
What is “Script Execution Policies?”

PowerShell’s execution policy is a safety feature that controls the conditions under which PowerShell loads configuration files and runs scripts. This feature helps prevent the execution of malicious scripts.

I want to lock this down globally across all my Servers / Desktops that my users can connect to so I will create a new Group Policy and assign it to the relevant OU.

Go to Computer Configuration -> Policies -> Administrative Templates -> Windows Components -> Windows PowerShell

Then select the option for “Turn on Script Execution” and select the policy that you wish to apply here, I chose “Allow only signed scripts”


Click on Apply and OK – thats it – you have set up the Script Execution Policy for your workers.


So, now that you have the execution policy set up lets move onto logging.

Enable PowerShell Logging
So, by the controls you have put in place already “hopefully” your standard users will not be able to get into PowerShell to execute scripts, BUT, if they do – then what.

Here is where we are going to enable PowerShell logging so that we can track whats happening on your estate and if needs be dig into the output and find out whats gone wrong!

Open up the same policy that you enabled the Execution Policy for and navigate to the same location.

Select the option to “Turn on Module Logging” and Enable it.


The click the “Show” button to select the modules and put a * in there to log for everything


Click “Next Setting” and Enable “Turn on PowerShell Script Block Logging” and dont select the “invocation start / stop” option


Click “Next Setting” twice and Enable “Turn on PowerShell Transcription” and select an output “Write Only” network share you create to save the PowerShell Transcripts to


Click Apply and OK and your platform is now set up to Log PowerShell actions undertaken across the platform.


With this enabled we can now test that its all working as expected. Log into a server and refresh the group policy. The open PowerShell and create a New Directory Locally


Now, if you look in the Transcripts Location on your network you will see the transcript for the session you have just completed, here you will find detailed information about whats happening out there with PowerShell


You can also check in the Applications and Services -> Windows PowerShell eventlog and see all the events logged for the PowerShell Executed on the server. If you dig through these you will be able to find the “Pipeline Execution” event for the command you just issued


Excellent – we have it all logging to a network file share and into the local event log. But what about if you run non-persistent workloads and do not redirect the event logs to a persistent drive (why would you not!).

Or, how are you realistically going to trawl through 100’s of servers event logs in an attack situation to find out whats happening.

Lets move onto capturing PowerShell activity centrally

Capture PowerShell Activity Centrally
We need to be able to capture all of the event logs in a central location so that we can analyze them in the event that we have to investigate an attack or breach.

I am going to use event log forwarding to do this. Now – there are 2 ways in which you can forward event logs

Collector Initiated
Source Initiated
Source Initiated is where the endpoint (sever) pushed the event logs up to a central event log collector. This is great in large environments as it reduces the load on the collector.

Collector initiated is where the event log collector pulls the logs into the central location, this is the method I will be using here to demonstrate centralised logging but feel free to implement either in your environment

First lets designate a collector, in this lab it will be my MDT Server “MDT”

Log into a server that you want to implement central logging and check that WinRM is enabled by typing the following

winrm get winrm/config

If it is not enabled then type the following and select “yes” to the prompts

winrm quickconfig

Next you need to grant the collector access to the local event log for the target, you can do this by running the following PowerShell or by manually adding the computer account

Add-ADGroupMember –identity 'Event Log Readers' –member collector_name$ 

Next we need to head over to the Collector and run the same commands to set up WinRM

winrm quickconfig

Then open up Event Viewer and select the SubScriptions Tab at the bottom, this will prompt you to reconfigure the Windows Event Collector service – select Yes here.


This will set the service to “Running” and also “Automatic (Delayed Start)”


Next right click the subscriptions and select “New Subscription”. Give it a Name and Description plus select the Log you want all the forwarded events to appear


Next click the “Select Computers” icon next to Collector Initiated” and add the computers you want to collect the logs for


Click on Test to ensure the collector and target can communicate


Click OK then click on the icon to “Select the Events” you want to collect. Select all teh event types and PowerShell as the Log


Finally under Advanced Select the Machine Account and Minimise Latency plus the protocol you want to use


Click on OK and the new Subscription will be created for you


If you right click on the Subscription and select “Runtime Status” you should see that the subscription is up and running and Active


Thats it – all the PowerShell events from the source computer will be forwarded to your new Event Log Collector. Go Ahead and create a new Directory Using PowerShell on the monitored Server


If you then check the Forwarded Events log on your collector you will see all the PowerShell Logging being forwarded


If you dig into these events you should be able to get the Pipeline Execution items for the tasks run on your platform


With this in place you will be able to use and number of tools to monitor this event log subscription and ensure that you are alerted and can react to anyone using PowerShell in your EUC environment when they should not be.

Hope this helps some of you out in securing PowerShell in your EUC environment

Thanks,

Dave