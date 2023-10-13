---
layout: post
title: Secure Lateral Movement
image: 
  path: /assets/img/posts/2022-07-19-secure-lateral-movement/lateral-movement.png
description: >
  This post will walk you through securing lateral movement in your EUC environment.
sitemap: true
categories: [Security, VDA, Citrix]
comments: true
---
* 
{:toc}

### Overview

This is another lesson I have learned from a recent “Ethical Hack” – lateral movement can get you in trouble fast! First let's take a look at what lateral movement is.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/01.png)

In essence, it means moving sideways or to the side. In terms of an attack on your EUC platform it can have devastating consequences and really fast.

What an attacker will do is get a foothold in your network, and this can be anywhere. Take note of this as its important – it can be in another part of your organization that you do not own! Think “Domain Admins” – then move sideways (or laterally) around your network and into your EUC stack, from the inside.

This is clearly an issue and one that needs addressing as best you can whilst still maintaining usability and administration ability of your platform.

As with anything when dealing with security – it's a long road. This article is again, just a starting point, but one that should give you some control of who and what can access your EUC environment.

Please note, whilst I am going to use Citrix Virtual Apps and Desktops in this post as an example, these principles apply to ALL EUC platforms. Just break the platform down into individual components and go from there.

## Secure Lateral Movement

### Administrator Groups

I am going to start out with a “Simple” environment and then go into how I would minimize lateral movement around that platform.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/02.png)

As you can see from the above we have the following components in our environment:

- Hypervisor
- Citrix NetScalers
- StoreFront
- Director
- Licensing
- Controllers
- FAS
- VDA’s

Now, one of the issues that I see a lot is the good old “Citrix Admins” group. This is the group that you create right at the start of the project to configure your initial site and this gets “inherited” from there on out. At best, I normally see NetScaler, Hypervisor and Citrix Admins.

The problem with this is illustrated below

![](/assets/img/posts/2022-07-19-secure-lateral-movement/03.png)

The red lines above illustrate what an admin enabled user can get to in the event that they can glean those all important credentials. This would be the scope of their lateral movement around your platform only, this does not take into account where they can go from there once they get that foothold.

Now there are hundreds of ways to minimize this risk, but I want to start small, so with that in mind, the below is what we are trying to achieve and with GPO and Group Design only.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/04.png)

So how to we achieve this. First we can create groups **FOR EACH COMPONENT** of the platform. not an overall admin group for the platform as a whole. This will mean a little more group admin in the long run, but it will allow you more security on your platform.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/05.png)

Next we will set up a good OU structure to be able to apply the Group Policy to in order to achieve the desired lock down.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/06.png)

You may notice that NetScaler is not listed above – this is because the way we apply restricted admins to the NetScaler is not via GPO but with an auth policy on the NetScaler itself.

Finally, create a new Group Policy Object and link it to the top level OU for your platform.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/07.png)

So – lets start to build out the Group Policy to restrict access to the components of our estate.

### Direct Access Users

What's this? Well this is a group that you will need to be in if you are NOT an administrator to be able to remote desktop to the server via a non-brokered connection.

Users DO NOT need to be in this group if they connect to the VDA via Citrix StoreFront and Virtual Apps and Desktops as this is a brokered connection. However – if they want to connect to the server directly over RDP they will need to be an Administrator or a member of this group.
{:.note title="Attention"}

For this reason, I like to lock this group down and ensure that it is empty.

Edit your new GPO and go to ```Computer Configuration -> Preferences -> Control Panel Settings -> Local Users and Groups```

Right click and select New -> Local Group

Select Replace, name the group “Direct Access Users” and select to remove all users and groups from this local group

![](/assets/img/posts/2022-07-19-secure-lateral-movement/08.png)

That's Direct Access Users taken care of, so lets move on with the rest of the platform.

### Group Policy 

We will deal with StoreFront initially. Right Click and create a new Local Group. Select “Replace” for the action, pick “Administrators (built-in) as the Group Name. Tick the box “Delete all member groups” and add your new StoreFront group to the Members section.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/09.png)

Click on Apply then select the “Common” tab at the top, put a tick in “Item Level Targeting” and select the Targeting button

![](/assets/img/posts/2022-07-19-secure-lateral-movement/10.png)

Select “New Item” then select “Organizational Unit” from the drop-down

![](/assets/img/posts/2022-07-19-secure-lateral-movement/11.png)

When the search box pops up locate the OU that you will be putting your StoreFront servers in and click OK

![](/assets/img/posts/2022-07-19-secure-lateral-movement/12.png)

Click on Apply then OK.

Repeat this task for ALL of your EUC components only selecting the different group name and different OU in the item level targeting. Once done you will end up with a seperate Administrators group for each of your components but filtered on the specific OU.
{:.note title="Attention"}

There are certain components that will require additional groups in the local Administrators (StoreFront Configuration Replication etc), make sure to add these as well as your new custom group.
{:.note title="Attention"}

![](/assets/img/posts/2022-07-19-secure-lateral-movement/13.png)

So, now you can go and test that this part is applying correctly. Log into your StoreFront server and refresh group policy using gpupdate /force (make sure you have added your account to the NEW admin group!)

![](/assets/img/posts/2022-07-19-secure-lateral-movement/14.png)

As you can see the Administrators group now only has the LOCAL Administrator account (I will deal with managing this account in a later post) and your new Admin group. We can also know that the “Direct Access Users” group is empty as we are enforcing that via GPO.

![](/assets/img/posts/2022-07-19-secure-lateral-movement/15.png)

## Why No Domain Admins?

Well, like I said earlier in this article, this is about lateral movement around your estate and this includes way more that your EUC environment. You likely have a whole host of other infrastructure in your environment and how can you be sure that they are all as secure as you are “trying” to be.

I remove “Domain Admins” so that is someone manages to get into that group somewhere else in the environment then that doesn't necessarily make them an admin on the EUC platform.

I am sure that there are ways to circumvent this but like I said at the start, this is meant to be a starting point!
{:.note title="Attention"}

## What about “Other” Groups?
This is true, there are other groups on your infrastructure that will give a user more than normal rights to the server. I also tend to strip these out via Group Policy unless specifically required.

Do the same as you did for “Direct Access Users” for:

- Backup Operators
- Power Users
- Device Owners
- Hypervisor and NetScaler
  
Make sure you secure these also by adding your new group to the Hypervisor Administrators and also creating a LDAPS Filtered policy for Administrator access to the Citrix ADC’s (link below)

[https://support.citrix.com/article/CTX212422](https://support.citrix.com/article/CTX212422)

## Group Security
Once all this is set up, and you have validated the group members I would highly recommend locking down the ability to add and remove users from your new admin groups and pull changes to these under a change control process. I have used Change Auditor for this in the past, and it works great!

## Conclusion

So, with this in mind we should have gone some way in preventing lateral movement across your EUC platform by ensuring that users need to be specific admins for specific components and preventing the overall “Citrix Admins” group.

Hope this helped some of you out.

