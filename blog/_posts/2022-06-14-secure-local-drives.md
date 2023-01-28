---
layout: post
title: Secure Local Drive Access
image: 
  path: /assets/img/posts/2022-06-14-secure-local-drives/secure-local-drives.png
description: >
  This post will walk you through securing your local drive access on your end points.<br/><br/>This will help with security as it will prevent your users browsing the local system and 'testing' out access. 
sitemap: true
categories: [Security, VDA, Citrix]
comments: true
---
* 
{:toc}

### Overview

Securing local drive access in an End User Computing environment is essential to ensure that your users do not gain access to applications and tools that they are not entitled to. The purpose of this article is to show you how to lock down that access so that you can be sure your endpoints are secure.

### Standard Drive Lock Down
This is fairly simple and a quick one to implement – it's a simple GPO settings that I'm sure most of you already have in play today.

Go ahead and create a new Group Policy Object and link it to your VDA’s. First thing you are going to want to do is disable this applying to the “VDA Administrators” Group as you will want to be able to access the local drives as an admin.

Click on the ```GPO -> Delegation``` then click the Advanced button. Add your VDA Admin group in there and select “Deny” to apply the group policy. That's it – this will not apply to your Administrators now.

![](/assets/img/posts/2022-06-14-secure-local-drives/01.png)

Next open up the new GPO and go to ```User Configuration -> Policies -> Administrative Templates -> WIndows Components -> File Explorer```. Open up the setting for “Hide these specified drives in My Computer” and Enable that setting with the specific drives

![](/assets/img/posts/2022-06-14-secure-local-drives/02.png)

Click on OK then open up the setting “Prevent access to drives from My Computer”, enable that and set the same drives as you did in the previous setting

![](/assets/img/posts/2022-06-14-secure-local-drives/03.png)

That's it! Stage one is done – simple right? Be assured though that we still have a long way to go.

Jump onto a VDA and refresh group policy using ```gpupdate /force``` and check that the drives have been disabled.

NOTE: If this policy is not working as expected make sure you have Loopback processing turned on for the OU that the VDA’s reside in.

![](/assets/img/posts/2022-06-14-secure-local-drives/04.png)

As you can see here – no local drives. Happy days. However, in the address bar type in ```\\localhost\c$``` and see what it does…

### UNC Paths and the Pitfalls

![](/assets/img/posts/2022-06-14-secure-local-drives/05.png)

There it is – your back into the C Drive. So not we need to go about locking this down and closing it out for the users.

Open up your GPO and go to ```User Configuration -> Policies -> Administrative Templates -> Start Menu and Taskbar```. Then open up the “Remove Run menu from Start Menu” setting and enable it

![](/assets/img/posts/2022-06-14-secure-local-drives/06.png)

Log out and back into your VDA to apply the policy and retest getting to ```\\localhost\c$```

![](/assets/img/posts/2022-06-14-secure-local-drives/07.png)

There you go – another door closed. However, this is a pretty brutal way of doing this as there are legitimate business reasons you would want to enable UNC paths, for example, what if you want to allow access to a “real” network path via the explorer menu bar?

![](/assets/img/posts/2022-06-14-secure-local-drives/08.png)

That's also disabled and this is a pretty poor user experience. So this is the pitfall in my opinion of enabling this setting. Go ahead and set that back to “Not Configured” and we can try to approach this a different way

### Loopback Execute Rights

Lets approach this a different way. Open up the GPO and go to ```Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Application Control Policies -> Applocker -> Executable Rules```.

Make sure you have created the default rules to allow Windows to operate then right click and create a new rule. Click next and select “Deny” for everyone

![](/assets/img/posts/2022-06-14-secure-local-drives/09.png)

Click next and select “Path

![](/assets/img/posts/2022-06-14-secure-local-drives/10.png)

Click next again and in the path put ```\\localhost\*```

![](/assets/img/posts/2022-06-14-secure-local-drives/11.png)

Click on Create.

Then do exactly the same again but instead of localhost for the path enter ```\\127.0.0.1\*```

![](/assets/img/posts/2022-06-14-secure-local-drives/12.png)

Once done you should have 2 new rules created in your GPO to Deny access to applications launched from the localhost and loopback addresses

![](/assets/img/posts/2022-06-14-secure-local-drives/13.png)

So let's test it out. Jump back onto your VDA and update the group policy. Open up explorer and get to the loopback address for localhost and try to start an application up

![](/assets/img/posts/2022-06-14-secure-local-drives/14.png)

As you can see the ability to run applications from the localhost locally has been disabled, let's try this using the loopback address to make sure that this is also disabled as expected

![](/assets/img/posts/2022-06-14-secure-local-drives/15.png)

Perfect – so your users can now no longer run applications if they use localhost or loopback. Let's just make sure that UNC paths still work for valid network shared via explorer

![](/assets/img/posts/2022-06-14-secure-local-drives/16.png)

Great – so we now have the drives locked and also the loopback addresses locked down.

### Google Chrome

Why on earth am I writing about Google Chrome in an article that deals with Local Drive Access?

Well, one of the first things that “Ethical Hackers” will do is open up Google Chrome and type in C: in the address bar

![](/assets/img/posts/2022-06-14-secure-local-drives/17.png)

There you have it – all that good work undone in 2 seconds with Chrome.

Open up your GPO and make sure you have added the Google Chrome ADMX templates. Navigate to ```Computer Configuration -> Policies -> Administrative Templates -> Google Chrome``` and open up the setting “Block access to a list of URL’s”. Enable that and click the “Show” button to add the URL’s

![](/assets/img/posts/2022-06-14-secure-local-drives/18.png)

Add the URL (copied directly from Chrome in your VDA) to the list and be sure to add any others you want to restrict access to

![](/assets/img/posts/2022-06-14-secure-local-drives/19.png)

OK and apply that and then test again by logging out and back into your VDA

![](/assets/img/posts/2022-06-14-secure-local-drives/20.png)

Excellent – that's closed. Now let's try to localhost and loopback and make sure you can't get to then via Chrome

![](/assets/img/posts/2022-06-14-secure-local-drives/21.png)

Damn, same problem as before. Open up your GPO and add the Chrome Loopback into the URL restriction list

![](/assets/img/posts/2022-06-14-secure-local-drives/22.png)

Then jump back onto the VDA, refresh GPO and retest

![](/assets/img/posts/2022-06-14-secure-local-drives/23.png)

OK, so it's not worked. Looking in the registry you can see that the policy has applied to the VDA

![](/assets/img/posts/2022-06-14-secure-local-drives/24.png)

In short, this was something that I could not block using the Chrome URL Blacklist. I am not sure why it's not working as local drives work fine but the loopback address and localhost would always allow to be to get to the local drives.

Time for another approach!

### Admin Shares
So, the reason for logged-in users being able to access loopback and localhost is that the administrative shares default behavior was changed when moving from Windows Server 2003 to 2008. The change that was made was to allow the “Active Logon Account” access to all the administrative shares.

This is what is causing us our issues as any user on the VDA is essentially an “Active Logged On User” and can therefore access all the admin shares. Not good for us!

There is a fix for this detailed below, but first…

Please TEST this in development FIRST as this may affect some of your admin tools accessing the VDA. I have not personally seen anything break when applying this but TEST before you put it into production. I don’t accept responsibility etc etc
{:.note title="Attention"}

This does require a reboot so if you are using non-persistent devices you will need this in your master image.
{:.note title="Attention"}

```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\lanmanserver\DefaultSecurity]
"SrvsvcShareAdminConnect"=hex:01,00,04,80,64,00,00,00,70,00,00,00,00,00,00
00,\
14,00,00,00,02,00,50,00,03,00,00,00,00,00,18,00,03,00,0f,00,01,02,00,00,00\
00,00,05,20,00,00,00,20,02,00,00,00,00,18,00,03,00,0f,00,01,02,00,00,00,00\
00,05,20,00,00,00,25,02,00,00,00,00,18,00,03,00,0f,00,01,02,00,00,00,00,00\
05,20,00,00,00,27,02,00,00,01,01,00,00,00,00,00,05,12,00,00,00,01,01,00,00\
00,00,00,05,12,00,00,00
```

Once the VDA has booted log back in and test getting to your loopback address using Chrome

![](/assets/img/posts/2022-06-14-secure-local-drives/25.png)

You can also test with \\localhost

![](/assets/img/posts/2022-06-14-secure-local-drives/26.png)

What you will also notice is that if you try to access the loopback address now as a normal user you will be prompted for admin credentials, so another win there.

![](/assets/img/posts/2022-06-14-secure-local-drives/27.png)

### Conclusion

So there you have it, my take on how to lock down local drive access in your EUC estate.

Hope some of you found this useful.
