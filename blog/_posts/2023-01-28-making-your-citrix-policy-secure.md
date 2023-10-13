---
layout: post
title: Making Your Citrix Policy Secure - By Default
image: 
  path: /assets/img/posts/2023-01-28-making-your-citrix-policy-secure/citrix-policies.png
description: >
  This post will outline the reasons why you should take a look at your Citrix Policies that come out the box and how to secure them.
sitemap: true
categories: [Citrix, VDA, Security]
comments: true
---
* 
{:toc}

### Overview

Is your Citrix Platform Secure? Did you inherit your platform and have just built on what's there policy wise to suit the business needs? If so, maybe it's time to take a look at what you have policy wise and what your users can actually do!

Citrix Policy by default is **NOT** secure, your platform will be left open and attackers **WILL** take advantage of this. This is done to make life easy for the installers of the platform and will make it extremely user-friendly, but, you should understand what this means for you and your business.
{:.note title="Attention"}

### Default Citrix Policies

So, let's have a look at what Citrix will give you out of the box. This is with a default install of the product on-premises or in the cloud.

| Policy Section | Default Configuration | 
|:-----------------|:-----------|
| Client Drives | All client drives will be mapped into the Citrix session. Local, CD Drives and Network are all included here | 
| Printing | All client printers will be mapped into the Citrix session | 
| Security | Only Basic encryption is enabled for the ICA session | 
| Session Watermarking | No watermarking will be in place | 
| USB Drives | All USB client drives will be mapped into the Citrix session, even plug and play devices | 

### The Issue

As you can see from the above, if you have left your Citrix policy as default you are setting yourself up for a world of pain down the line. Some of the examples that you could be left open to are:

- Attackers can copy and paste all data out of the session
- Attackers can drag and drop files into and out of the Citrix session
- Attackers can insert USB drives and then copy data to them
- Attackers can print out any data they desire from your platform
- Attackers can screenshot their session then take that data elsewhere

This is obviously an issue and should be addressed.  Read on...

### The Solution

**BE SECURE BY DEFAULT**, really that's it.  Set up a secure policy by default and grant additional access by exception.

Now I realize that this is not always easy and can often be met with a bunch of challenges (I had to personally fight for nearly a year to block copy and paste as it "stopped" the business working fast apparently) but its one way you can get a handle on your environment and really know what your users can and cannot do.

### The Policy

Citrix have provided you with an excellent starting point for a secure by default policy.

If you log into Citrix Cloud (same for on-premises) and navigate to the ```Policies``` section

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/01.png)

Then click on ```Templates```

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/02.png)

You will see a template called ```Security and Control```

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/03.png)

Click on that and then click on ```Create policy from template```

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/04.png)

Click on next accepting the ```Template default settings```

Assign it to ```All objects in the site```

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/05.png)

Give the new template a name and a description

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/06.png)

Click on ```Finish``` and you will have your new template ready to edit.

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/07.png)

So, that's a great baseline. Now click on that template and select edit and update the settings to **INCLUDE** the following settings. Each section has been divided up into its own table to help filter them out if you don't want all the settings.

**SOME OF THESE ARE SET BY DEFAULT**, however, I prefer to set them manually in case Citrix decide to change their default stance on policy settings. It means you don't have to trawl the policy updates each release to ensure you are still secure
{:.note title="Attention"}

#### File Redirection

| Policy | Setting | 
|:-----------------|:-----------|
| Allow file transfer between desktop and client | Prohibited | 
| Client floppy drives | Prohibited | 
| Download file from desktop | Prohibited | 
| Host to client redirection | Prohibited | 
| Preserve client drive letters | Prohibited | 
| Read-only client drive access | Prohibited | 
| Special folder redirection | Prohibited | 
| Upload file to desktop | Prohibited | 

#### Local App Access

| Policy | Setting | 
|:-----------------|:-----------|
| Allow local app access | Prohibited | 

#### Port Redirection

| Policy | Setting | 
|:-----------------|:-----------|
| Auto connect client COM ports | Prohibited | 
| Auto connect client LPT ports | Prohibited | 

#### Printing

| Policy | Setting | 
|:-----------------|:-----------|
| Auto-create generic universal printer | Prohibited | 
| Auto-create PDF Universal printer | Prohibited | 

#### Security

| Policy | Setting | 
|:-----------------|:-----------|
| SecureICA minimum encryption level | RC5 128 Bit | 

Make sure you update your Delivery Groups to reflect this
{:.note title="Attention"}

#### Session Watermark

| Policy | Setting | 
|:-----------------|:-----------|
| Enable session watermark | Enabled | 
| Include client IP Address | Enabled | 
| Include logon user name | Enabled | 
| Include VDA host name | Enabled | 
| Include VDA IP Address | Enabled | 
| Watermark custom text | Your custom text for water marking | 

#### USB Devices

| Policy | Setting | 
|:-----------------|:-----------|
| Client USB device redirection | Prohibited | 

Once you have set all these up save and close the policy. Enable it and set this as the default for your platform, and you will have closed a bunch of security risks with a single policy.

### Enabling Exceptions

Once your baseline policy is in place you can create separate policies for enabling the clipboard for example or enabling printing and then rank them higher and filter them on groups. This will enable you to add access by exception rather than by default.

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/08.png)

### Filtering

To set up access over and above your default secure settings you will need to use filtering. Below are some of the filters I use the most and a brief reason why, these are by no means a comprehensive list of what you can achieve but may provide a good starting point.

#### User or Group

This is the most common to be honest. You can assign a policy such as ```Enable Clipboard``` to a specific set of users in your domain. Quick note on this though, if possible control the group admin to a **VERY SPECIFIC** group of people using ACL's. These groups grow fast!

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/09.png)

#### Client IP Address

This one is useful if you want to allow certain policies to internal users only but not to external users. Simply add your internal networks to this then boom, you're done, all internal users can now copy and paste or copy files in and out but external users cannot. This is assuming you have sufficient building security and that the **ONLY** devices on your internal network are trusted and managed.

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/10.png)

#### Access Control

I have used this one **LOADS** and it is so useful. If you are licensed and have a NetScaler for external access you can enable EPA and based on the result of that scan, apply applicable policies.

For example, a user can log in externally from their corporate laptop, pass the EPA and as the device is managed and trusted you can enable Copy and Paste. However, if that same user logs on from their own device, EPA will fail and the Copy and Paste policy will not apply. Same user, different device and context and therefore different policy.  Super powerful!

![](/assets/img/posts/2023-01-28-making-your-citrix-policy-secure/11.png)

### Conclusion

Citrix Policy is powerful! You should 100% take a look at your policies you have in place right now and check with your SecOps teams to ensure that your users cannot do things you would not want them to. Trust me, your Citrix environment is one of the first things they will go after and if your policy is not set right, it's a quick win for the attackers!