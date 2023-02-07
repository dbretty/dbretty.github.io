---
layout: post
title: Using CIS in your End User Computing Environment
image: 
  path: /assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/using-cis.png
description: >
  This post will describe the process of using reviewing, configuring and implementing CIS controls in your End User Computing Environment.<br/><br/>Please note that this will cost time if you want to go down the 'free' route but its worth it in the long run! 
sitemap: true
categories: [EUC, Security]
comments: true
---
* 
{:toc}

### Overview

Over the past few years, and more so since the COVID pandemic, security is becoming more and more of an issue to us as professionals who maintain and manage End User Computing Environments. This can often be over whelming and fast become like your chasing your tail as threats never seem to end. There are, however, some excellent resources out there that can help you with this quest and one of them is the Center for Internet Security (CIS). During the rest of this post I will explain what the CIS is, how to implement some of their guidelines (both free and paid for) and try to explain how this will help you in your quest to make your environment more secure.

### What is the Center for Internet Security

First and foremost, you can find their site [here](https://www.cisecurity.org). This is what they say about themselves:

The Center for Internet Security, Inc. (CIS®) makes the connected world a safer place for people, businesses, and governments through our core competencies of collaboration and innovation.
{:.note title="CIS"}

We are a community-driven nonprofit, responsible for the CIS Controls® and CIS Benchmarks™, globally recognized best practices for securing IT systems and data. We lead a global community of IT professionals to continuously evolve these standards and provide products and services to proactively safeguard against emerging threats. Our CIS Hardened Images® provide secure, on-demand, scalable computing environments in the cloud.
{:.note title="CIS"}

In essence, they are a community driven project that will give you configuration settings to make your platforms more secure, be warned however, there are **A LOT** of them (settings I mean).

### What CIS Covers

The CIS Benchmarks as there know cover pretty much everything you will touch from an EUC perspective from the Operating Systems your users land on to the applications they consume. It's about finding what's right for your business needs.

Here is an overview of the benchmarks available that can be found [here](https://www.cisecurity.org/cis-benchmarks/)

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/01.png)

and here is that list filtered on ```Operating Systems``` then ```Microsoft Windows```

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/02.png)

Finally, here is the above list filtered on ```Desktop Software``` then ```Web Browser```

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/03.png)

As you can see there are lots of options available to you, and It's really down to you how far you wish to dive into this rabbit hole.

### CIS Levels

The CIS benchmarks are broken down into 2 levels (1 and 2) designed to give you the flexibility to secure parts or all of your environment to the different security requirements of your business. The details of what the levels cover is shown in the table below.

| Level | Description | 
|:-----------------|:-----------|
| 1 | recommends essential basic security requirements that can be configured on any system and should cause little or no interruption of service or reduced functionality. | 
| 2 | recommends security settings for environments requiring greater security that could result in some reduced functionality. | 

### EUC + CIS Risk

Reading the above you probably think, great, can just put all these Level 1 CIS policies in, and we are secure. Not the case. This is (as with everything security related) just one tool in your belt, but, it will help. There is also the risk of failure. Some settings you will define in your base policies are very intrusive and can break End User Computing environments very quickly so make sure you **TEST, TEST and TEST again** before putting these into production. That said, once you have everything defined and tested it does give you a good policy set for security in your environment that's easy(ish) to manage.

### CIS Hardened Images

What if you are running operating systems in the Public Cloud. Well thankfully rather than applying a bunch of GPO's to them and breaking something fundamental most of the providers have given you access to CIS Hardened Images available directly from them.  To find a list of these look [here](https://www.cisecurity.org/cis-hardened-image-list)

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/04.png)

These give you a great starting point for deploying a secure image straight from your Cloud Provider and testing from there.

### Secure By Design - CIS Group Policy Layout

This is important to understand before you begin actually configuring and new CIS Group Policies as understanding this will provide you a really easy way to manage your policies over time and will also allow you to be very granular in how you apply your exceptions.

Let's look at a typical Organizational Unit layout for a Citrix Cloud DaaS deployment

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/05.png)

Now if we look at how CIS is generally deployed (using Server OS as an example) you will see the following

- MemberServer_2022_CIS_Policy_Exceptions_v1
- MemberServer_2022_CIS_Preference_Exceptions_v1
- MemberServer_2022_CIS_Policy_Level1_v1
- MemberServer_2022_CIS_Preference_Level1_v1

So, looking at the above list (they will apply bottom up) you can see that CIS Level 1 Preferences and Policies will apply first. These will have **ALL** the reccommended CIS settings configured should they break the platform or not (read note below for reasoning). The 2 GPO's above that for Preference and Policy Exceptions will specifically unpick and CIS setting that breaks your environment or will add to the default CIS policy with specific business needs.

The reason for putting **ALL** the CIS configuration into the base policy is that you are trying to design secure by default. If one of these settings breaks something then you will have to justify that back to the business and get them to accept the risk of undoing a secure setting. Along with this it will not break ALL the servers, only those that use that specific setting. This way you can have a secure baseline GPO applied across your estate and only unpick security where it is absolutely necessary
{:.note title="ATTENTION"}

Another point worth mentioning is that generally the GPO's are filtered against the specific Operating System or role they are targeting. This means you can have the baseline GPO's assigned at a higher level in your Active Directory giving you a broader security posture and know that they will apply only to those operating systems specifically targeted.

With that in mind let's have a look at how we could potentially lay out CIS GPO's for the above OU structure

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/06.png)

Looking at the inheritance on the Cloud Connectors for example

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/07.png)

You can see that **ALL** the default security settings are applied then we apply the ```Global Exceptions``` we have deemed fit for any domain joined machine, then finally just the exceptions for the ```Cloud Connectors```

This is a good win as you are securing everything by default then only unpicking what's required to make your platform work.

### Building CIS Policies

This is that time-consuming part and believe me - I have done this before. The first thing you want to do is head over to the CIS WorkBench and register for a free account [here](https://workbench.cisecurity.org/benchmarks). Once you have that set up and logged in you will see all the available benchmarks available.

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/08.png)

Filter the list using the search box

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/09.png)

Then click on the benchmark you wish to view

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/10.png)

On the left you can see a tree view of every setting that's defined in the benchmark (yes, there are loads!) but from here you can drill down and start to configure these in Group Policy

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/11.png)

The benchmark setting will normally give you the Group Policy location for the setting as well as the registry path, so you can use GPO or another tool to configure these.
{:.note title="ATTENTION"}

This is the part that will take the time as you will have a ton of settings to configure, but it will give you a good secure baseline for your images that (once configured) can easily be applied to other servers in your estate.

Once you have your baseline settings done go ahead and set up your exception policies for each of your relevant OU's and bind them as shown above, reboot the machines and get testing.

### What About Apps

All I have talked about so far is Server and Client Operating Systems, what about the apps that our users run. The same approach can be taken with those in that you define your secure baseline then add exceptions to suit your business needs over the top. This will go along with the Secure By Design reasoning and provide you with a solid, manageable set of policies in the long term.

You will be able to find benchmarks for most of the common applications your users consume in the same location linked to above.

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/12.png)

### The Easy (But Expensive) Path

The above is pretty straight forward from a configuration perspective, but **IT TAKES TIME** to get up and running. There is an easier way but it is expensive. That said, what price would your company pay to be secure compared against the cost of a breach, if you compare the 2 It's not really too much to ask!

If you join the CIS Secure Suite [here](https://www.cisecurity.org/cis-securesuite) you can download the base configurations already configured that you can just import into your existing GPO infrastructure then all you have to do is define the exceptions, and you are good to go!

Told you, easy but not cheap!

![](/assets/img/posts/2023-02-07-using-cis-in-your-euc-environment/13.png)

### Conclusion

In conclusion, do I think its worth the investment in time, YES! This will take a long time to set up, test and get into production but the advantage it will give you from a security standpoint cannot be underestimated. There are people out there that want to damage your infrastructure, as an owner of an EUC environment you are already a target. For the sake of spending some time setting up Group Policy at no code, why would you not do it.