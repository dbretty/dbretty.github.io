---
layout: post
title: Secure File Open With Dialogue
image: 
  path: /assets/img/posts/2023-01-26-secure-file-open-with/secure-file-open-with.png
description: >
  This post will show you how to secure the File Open-With dialogue box that will potentially allow an attacker to access areas of your virtual desktop that you would otherwise not want them to. 
sitemap: true
categories: [Security, VDA, Citrix]
comments: true
---
* 
{:toc}

### Overview

In a multi-user environment you often have several applications installed on a single Windows system and either publish applications to the user or grant them access to a desktop hiding various items from the start menu.  Whilst on the surface this may seem sufficient to stop your users accessing things they should not, I have seen attackers use the File Open dialogue to access applications on a server that they should otherwise not have access to.

The problem here is that if you do not have a load of master images and want that single image nirvana then you need a way to lock apps to only the users that you deem necessary as you will often have **ALL** the apps installed on the same, single master image.

There are a ton of ways to do this, and if it were me I would probably use 1, if not all of them, however for the purpose of this post I am going to show you one step in that process:

Hide the **"Right Click, Open With"** dialogue

### The Security Issue

First let me show you what this specific issue is meant to defend against.

If we log into a Windows 11 machine and open up File Explorer you can see we have 2 files there

![](/assets/img/posts/2023-01-26-secure-file-open-with/01.png)

Now if we right-click on the Text Document and select Open With we get the option to "Choose another app"

![](/assets/img/posts/2023-01-26-secure-file-open-with/02.png)

Clicking this displays a list of all the applications associated with that file extension, from here we can click on "Choose another app on your PC"

![](/assets/img/posts/2023-01-26-secure-file-open-with/03.png)

This will open up a File dialogue and allow us to browse the local drive for any application we deem fit.

Please note that you should have your Local Drives locked down, if you want help doing this please read my article on doing so.
{:.note title="Attention"}

![](/assets/img/posts/2023-01-26-secure-file-open-with/04.png)

So if we now go ahead and open up the text document using Excel

![](/assets/img/posts/2023-01-26-secure-file-open-with/05.png)
![](/assets/img/posts/2023-01-26-secure-file-open-with/06.png)

Then, once open use the Excel search bar to look for "Macros"

![](/assets/img/posts/2023-01-26-secure-file-open-with/07.png)

Give the Macro a name in the pop-up dialogue and click on Create

![](/assets/img/posts/2023-01-26-secure-file-open-with/09.png)

This will give you access to the Visual Basic editor and allow you to write and execute any code you wish, all from opening up a text document.

![](/assets/img/posts/2023-01-26-secure-file-open-with/10.png)

### Registry Fix

Luckily there is a relatively quick fix for this but in my opinion It's not so elegant.

Open up the registry editor

![](/assets/img/posts/2023-01-26-secure-file-open-with/11.png)

Then navigate to the following key

```HKEY_CLASSES_ROOT\*\shellex\ContextMenuHandlers```

![](/assets/img/posts/2023-01-26-secure-file-open-with/12.png)

Locate the ```Open With``` Key and delete it

![](/assets/img/posts/2023-01-26-secure-file-open-with/13.png)

Next time you try and use the right click open with dialogue you will see its gone!

![](/assets/img/posts/2023-01-26-secure-file-open-with/15.png)

### FSLogix To The Rescue

The above method is good, and it works, however, what if:
- As an Administrator you still want the functionality
- You "forget" to implement the fix on a single VDA

This will leave you open to attack and not be something you want.

Let's have a look at how FSLogix can help.

#### History

FSLogix was bought by Microsoft and It's pretty much available now to everyone who is using Virtual Desktops. Whilst this is great and a big win for profiles there is another component to FSLogix that can help us here, its called "App Masking"

This gives us the ability to hide applications, files and registry keys based on group membership. With this in mind we can hide the right click open with key from all users except the Administrators. This means that as an Admin you can still use the functionality but if you are a regular user (as I hope your users are!) you won't see the key as its masked.

To learn more about installing FSLogix click [here](https://learn.microsoft.com/en-us/fslogix/install-ht)

#### FSLogix Fix

Start by opening the FSLogix Rule Editor from one of your VDA's

![](/assets/img/posts/2023-01-26-secure-file-open-with/16.png)

Select a location to save the new rule set

![](/assets/img/posts/2023-01-26-secure-file-open-with/17.png)

Select the option for a "Blank Rule Set"

![](/assets/img/posts/2023-01-26-secure-file-open-with/18.png)

Right click in the pane to the right and select "New Rule"

![](/assets/img/posts/2023-01-26-secure-file-open-with/19.png)

Select "Hiding Rule" from the drop-down and click Browse

![](/assets/img/posts/2023-01-26-secure-file-open-with/21.png)

Select "Registry Key"

![](/assets/img/posts/2023-01-26-secure-file-open-with/22.png)

Browse to the following registry key

```HKEY_CLASSES_ROOT\*\shellex\ContextMenuHandlers```

![](/assets/img/posts/2023-01-26-secure-file-open-with/23.png)

Select OK and accept any changes that FSLogix Apps Masking suggests

![](/assets/img/posts/2023-01-26-secure-file-open-with/24.png)

Click Manage Assignments

![](/assets/img/posts/2023-01-26-secure-file-open-with/26.png)

Select to "Rule set does apply to user/group" for Everyone and add Administrators as a group, selecting "Rule set does not apply to user/group".

Save and copy the FSLogix rules according to the installation guide for FSLogix above and test as a regular user.

![](/assets/img/posts/2023-01-26-secure-file-open-with/15.png)

You will see that FSLogix is "Masking" the registry key from the user and therefore not showing it.

### Conclusion

This is a quick win and a simple one to implement. As with everything like this is it not foolproof and also is not a complete win-win, but it will help to go some way down the path to securing your VDA's and stopping your users getting access to things they should not have access to.