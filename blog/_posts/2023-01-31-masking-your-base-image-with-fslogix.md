---
layout: post
title: Masking Your Base Image With FSLogix
image: 
  path: /assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/fslogix-base-image-masking.png
description: >
  This post will describe the process of using FSLogix App Masking to hide the core set of tools and utilities that an attacker will try to gain access to in order to exploit your platform.<br/><br/>This will need testing before you implement this into production. 
sitemap: true
categories: [Citrix, VDA, Security]
comments: true
---
* 
{:toc}

### Overview

I presented at the [UK Citrix User Group](https://www.citrixug.org.uk) recently on **10 Quick Wins for Red Team Testing** and one of the subjects I covered was hiding base applications from the users when they do not need access to them.

Now, I know there are a host of tools to do this and that some are better than others (think application whitelisting) but what if there was a quick win? One that may not stop all attacks but will prevent the majority of internal users (yes, not all attacks are external) from even seeing PowerShell, Ping, FTP and a whole host of applications even being installed on your platform.

Enter FSLogix, specifically FSLogix App Masking.

When many hear FSLogix, they immediately think "ahh, yeah the profile tool" but there is another tool in your arsenal, Application Masking.

This post is not intended to give you an "all-in" look at App Masking and what it can do, if you want that then look [here](https://learn.microsoft.com/en-us/fslogix/implement-application-masking-tutorial) but what this will do is show you how to hide common apps that are not required from your standard users to enable a more secure platform.

### What Is App Masking?

According to Microsoft Application Masking manages access to an application, font, printer, or other items. Access can be controlled by user, IP Address range, and other criteria. Application Masking significantly decreases the complexity of managing large numbers of gold images.

Normally this is what it is used for, a group of users "Word Users" for example get access to the installed copy of Word. If you are not in that group then FSLogix App Masking "hides" that application. That's it, gone, like It's not even installed.

I am not going to claim to be clever enough to understand how all of this works under the hood, but, if we applied the same principle to a master image BEFORE we install ANY apps then we could in theory "hide" system applications we don't want users to have access to but leave them there for the Administrators.

### Installation

The first thing you will want to do is install the FSLogix product set onto your master image. You can download FSLogix from [here](https://learn.microsoft.com/en-us/fslogix/install-ht) and just extract the ZIP file onto your master image.

#### FSLogix Apps

Run the ```FSLogixAppsSetup.exe``` file

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/01.png)

Click to agree to the licensing and click ```install```

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/02.png)

#### FSLogix Apps Rule Editor

Run the ```FSLogixAppsRuleEditorSetup.exe``` file

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/03.png)

Click to agree to the licensing and click ```install```

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/04.png)

I normally install the Rule Editor onto the master image in order to be able to select files, folders and registry keys from the platform that the users will be operating from. You can remove this if required once the rules are set up
{:.note title="Attention"}

### Rule Configuration

Once the software is installed on your master image open up ```FSLogix Apps RuleEditor``` from the Start Menu

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/05.png)

Click on ```New``` to create a new rule and save it to a location you can copy the 2 files from later (in my case I am putting it on the desktop)

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/06.png)

You will notice 2 files get created, you will need both of these to implement the rule later. One is the rule definition and the other is the assignments.

Select to create a ```Blank Rule Set``` and click OK

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/07.png)

Right click in the right-hand pain and select ```New Rule```

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/08.png)

Select ```Hiding Rule``` and click ```Browse```

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/09.png)

Select to browse for a file

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/10.png)

Locate the PowerShell executable from the pop-up window that appears

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/11.png)

Click OK and select ```Yes``` to the system instability warning

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/12.png)

Repeat this process for all the programs you want to hide from your users. Below are the programs and printers that I normally add

#### Hiding Rules I Add

| Program | Reasoning | 
|:-----------------|:-----------|
| powershell.exe | PowerShell - the bain of my life with regard to security | 
| powershell_ise.exe | See above! | 
| ftp.exe | Can pipe commands via FTP to the command prompt | 
| fsutil.exe | Can be used for an Applocker Bypass | 
| cmd.exe | Not required for end users **Be careful if you have login scripts** | 
| ping.exe | Also not required by end users | 
| reg.exe | Registry manipulation | 
| regedt32.exe | More registry manipulation | 
| regedit.exe | Yet more registry manipulation | 
| regsvr32.exe | Not required by end users | 

| Printer | Reasoning | 
|:-----------------|:-----------|
| Fax | Really !?! | 
| Microsoft Print to PDF | Normally use other tools | 
| Microsoft XPS Document Writer | Not required | 


#### Hiding Rules As Per Microsoft

Now, Microsoft have been kind enough to provide a list of applications that attackers could potentially use to bypass AppLocker or Windows Defender Application Control. You can find this list below

[https://learn.microsoft.com/nb-no/windows/security/threat-protection/windows-defender-application-control/microsoft-recommended-block-rules](https://learn.microsoft.com/nb-no/windows/security/threat-protection/windows-defender-application-control/microsoft-recommended-block-rules)

This is their take on it

Members of the security community* continuously collaborate with Microsoft to help protect customers. With the help of their valuable reports, Microsoft has identified a list of valid applications that an attacker could also potentially use to bypass WDAC.
{:.note title="Attention"}

With that in mind I tend to add the relevant applications provided by Microsoft to by rule set to prevent internal users accessing these.

Please note that **EACH ENVIRONMENT DIFFERS**, you will have to test this prior to releasing it into production, or you could break lots of your VDA's. Check on each exe file and see if it is feasible to implement in your environment. Test, test and test some more and if you don't, well, that's your own fault then!
{:.note title="Attention"}

Once you are done you should have a fairly robust rule base as you can see below

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/13.png)

Next you will need to deal with the assignments, this is who this rule applies to, and also who it does not!

Click on ```Manage Assignments``` and remove ```Everyone``` from the list.  Then add ```Domain Users``` and select ```Rule set does apply to user/group``` and also add ```BUILTIN\Administrators``` and select ```Rule set does not apply to user/group```

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/14.png)

Click to save the rule and close the rule editor.

### Deployment

To deploy your new rule you will need to copy both files to the following location on all your VDA's

This can be done with a start-up script, part of the image seal process or a whole host of other options, that's up to you.

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/15.png)

### Testing

First let's test as an Administrator to make sure that everything still works as expected

As you can see PowerShell still works

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/16.png)

As does ping

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/17.png)

So, let's switch to a standard user

You can see that Windows can't even see that a registry editor is installed now, if you try and run it, nothing happens

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/19.png)

The same with Windows PowerShell

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/20.png)

So, let's try and run the PowerShell file directly from the file system

![](/assets/img/posts/2023-01-31-masking-your-base-image-with-fslogix/21.png)

As you can see, it's not there, FSLogix App Masking is doing its job and "masking" those files we defined earlier from the user.

### Conclusion

Now I know that there are plenty of smart people out there who may be able to bypass this with all the tools they have available to them. But, if you consider that quite a bit of breakout is done internally by your standard users this is a pretty good win. 

It does not take a lot to set up, does not cost much and can immediately add a layer of security between your users and the base operating system you are deploying to them.