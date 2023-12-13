---
layout: post
title: Foundation a Nutanix Cluster with Ansible
image: 
  path: /assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/header_image.png
description: >
  This post will show you how you foundation your Nutanix Clusters with Ansible to automate the build of your Nutanix assets.
sitemap: true
categories: [Nutanix, Ansible, Automation]
comments: true
---
* 
{:toc}

### Overview

In my role as a Senior Solutions Architect in Performance Engineering at Nutanix our team does a lot of testing with various configurations of Nutanix AOS, AHV, and different EUC endpoint options. Part of this requires us to re-image our testing clusters to ensure that they are in a clean and standard state ready for us to test on.

This post will walk you through one of the ways you can achieve that using Containers and Ansible enabling you to execute an Ansible Playbook from your local Visual Studio Code IDE and watch as the magic happens!

### Required Components

#### Foundation Virtual Machine

First, you will need a Foundation VM in place. The reason for this is that the Foundation VM is what, well, drives the foundation of the cluster. 

What we will be doing with the workflow is shown below.

![Ansible Flow](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_01.png)

To install a Foundation VM head over to the **[My Nutanix](https://my.nutanix.com/)** portal and open up the **Support and Insights** page. Then select **Downloads** and finally **Foundation**. Here you will see various options for downloading a standalone Foundation VM image. Pick the option that suits the hypervisor you wish to run the VM on, download and set up the appliance following the Nutanix documentation.

![Foundation Downloads](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_02.png)

Once done you should be able to connect to your new Foundation VM on port 8000 ```http://foundation_vm_ip:8000```. 

![Foundation Ready](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_03.png)

Now, at this point you could manually go through the steps of filling out the UI and re-imaging your cluster but where's the fun in that!

#### Container Platform

You will also need a container platform on your laptop in order for VS Code to be able to open your code in a re-usable and consistent container. Doing this will enable you to automatically download and install the required components to build and configure your clusters.

I use a MacBook but Rancher supports MacOS, Windows and Linux making it a good option. Head over to [the Rancher download page](https://rancherdesktop.io/) and go ahead and install it.

#### Visual Studio Code Extension

The last thing you will need is the **Dev Containers** Visual Studio Code extension. 

Click the extensions icon in VS Code, search for Dev Containers and install it.

![Dev Containers Extension](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_04.png)

## Foundation Your Cluster

With all the pre-reqs in place let's go ahead and start to have some VS Code fun!

### Build The Container

Open up a new folder in VS Code and create a top level folder called ```.devcontainer```. When you instruct VSCode to open your folder in a container it will use the definitions scoped out here to build your container for you automatically.

Within the folder you will create 2 files detailed below.

| File | Description | 
|:-----------------|:-----------|
| devcontainer.json | Tells VS Code how to access (or create) a development container with a well-defined tool and runtime stack | 
| DockerFile | Changes made in the Dockerfile will persist even upon a rebuild of the dev container | 

More information on these file definitions can be found [here](https://code.visualstudio.com/docs/devcontainers/create-dev-container)

In these files you will need to define what Docker Container variant you want to install as well as the version of Ansible and any other tools you want in your Container. Microsoft have some good documentation on creating Dev Containers [here](https://code.visualstudio.com/docs/devcontainers/create-dev-container) but if you want a quick start set of files I have created both in my GitHub Repo [here](https://github.com/dbretty/dbretty.Scripts/tree/master/Containers/Nutanix%20Foundation%20With%20Ansible) that you can download and use.

![Dev Container Files](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_05.png)

Once you have these in place you can test your new container by using the command **Dev Containers: Rebuild and Reopen in Container** from VS Code.

![Dev Container Open](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_06.png)

This will build and open your code in a new container and allow you to open a bash terminal within VS Code directly in the container. You can check the container is built and running by looking in Rancher Desktop.

![Dev Container Running](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_07.png)

You will also see that Ansible is installed within the container as part of the build.

![Ansible Running](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_08.png)

### Install Nutanix Ansible Module

Next you will want to install the Nutanix Ansible Module into your container. This will presist unless you rebuild your container (change the DockerFile or devcontainer.json) so you should only need to do this once.

Open a ```bash``` terminal in your container and enter the following commands:

```bash
cd ~
git clone https://github.com/nutanix/nutanix.ansible.git
cd nutanix.ansible
git checkout v1.9.1 -b v1.9.1
python -m venv venv
. venv/bin/activate
ansible-galaxy collection build
ansible-galaxy collection install nutanix-ncp-1.9.1.tar.gz
```
![Nutanix Ansible Installed](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_09.png)

### Define Foundation Playbook

Next you want to define a Playbook that Ansible will use to Foundation your cluster. Luckily for us the kind folk at Nutanix have given us a bunch of examples to use [here](https://github.com/nutanix/nutanix.ansible/blob/main/examples/foundation/image_nodes.yml)

![Nutanix Ansible Playbook Examples](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_10.png)

Create a file in your container called ```filename.yml``` and copy the contents of this file into it substituting the values for those of your environment. Duplicate the ```blocks``` and ```nodes``` sections to mirror what you are going to be re-imaging.

Most of the details in the yml file are self-explanatory but a couple of key fields I would like to point out are below.

| Field | Description | 
|:-----------------|:-----------|
| nutanix_host | This is the IP Address of your Foundation VM | 
| nos_package | This is the Nutanix Installer tar file that you will have uploaded to the Foundation VM as part of the install process | 

### Running The Playbook

Ok, so you have everything defined, and you are ready to Foundation your new cluster. It's as simple as running the following command.

```bash
ansible-playbook playbook_file_name.yml
```

That's it, Ansible will take the contents of your playbook, connect to the Foundation VM, pass all that content over and start the process of re-imaging your new cluster.

## Taking It Further 

Whilst this is great and a nice way to be able to automate the imaging of your Nutanix clusters if you think a little broader there are a number of ways to make this process slicker by introducing more tools into the mix. Some of my thoughts on this are below.

### PowerShell For The Win

If you put PowerShell into your container you are able to use this to automatically build and execute the yml files as well as integrate the script into your reporting tool set (think slack for build notifications). To get PowerShell into your container you can create a bash script to execute on container build with the following content.

```sh
architecture="$(uname -m)"
case ${architecture} in
    x86_64) architecture="amd64";;
    aarch64 | armv8*) architecture="arm64";;
esac

if [ "${architecture}" = "arm64" ]; then
    echo "Architecture is ARM"
    posh="https://github.com/PowerShell/PowerShell/releases/download/v7.3.0/powershell-7.3.0-linux-arm64.tar.gz"
else
    echo "Architecture is X86_64"
    posh="https://github.com/PowerShell/PowerShell/releases/download/v7.3.0/powershell-7.3.0-linux-x64.tar.gz"
fi

echo "Downloading PowerShell from $posh"
curl -L -o /tmp/powershell.tar.gz $posh

echo "Installing PowerShell"
sudo mkdir -p /opt/microsoft/powershell/7
sudo tar zxf /tmp/powershell.tar.gz -C /opt/microsoft/powershell/7
sudo chmod +x /opt/microsoft/powershell/7/pwsh
sudo ln -s /opt/microsoft/powershell/7/pwsh /usr/bin/pwsh
```

Once you have this you can use the ```Invoke-RestMethod``` Cmdlet to pull data back from foundation before starting the Ansible playbook and use the ```extra-vars``` parameter to pass in data you have gathered from PowerShell.

### The Nutanix Installer File

One of the obvious things with the above process is that you are hard coding a Nutanix Installer into the yml file. However, if you look at the Foundation API Reference [here](https://www.nutanix.dev/api_references/foundation/#/ZG9jOjQ1Mg-foundation) you can see there are options for getting available images or even uploading them.

If you think about this with PowerShell you could take the process further and achieve something like the below.

![Nutanix Ansible Playbook Decision](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_11.png)

This gives you the ability to have a single script that will gather information at runtime for the entire build process ensuring that your automation work is re-usable and consistent.

### Going Even Further

One of the issues with running this type of thing in a container is the additional tasks you have to perform once the cluster is initially built. Some typical tasks would be

- Register the cluster with Prism Central
- Create Storage Containers
- Create Networking
- Connect to Citrix with Hosting Connection

With the new API driven Citrix Configuration ability as well as the Nutanix APIs you can now do all of this from within a simple container fully automated. The below is just one way you could do this with PowerShell, Ansible and a Container.

![Nutanix Ansible Playbook Full Run](/assets/img/posts/2023-12-12-foundation-nutanix-cluster-with-ansible-and-powershell/image_12.png)

With the above you can see that it is possible to build an automation wrapper to use with any cluster and achieve an end-to-end automated build from zero to a Citrix ready cluster, all is takes is a little time and some imagination!

## Conclusion

The above may not be perfect. My Ansible skills are slim to say the least as are my PowerShell coding skills, but I have found that this is a good balance of easy to manage code and getting things done in an automated and consistent manner.

Please, if you have a better way to do this I am all ears, feel free to get in touch.