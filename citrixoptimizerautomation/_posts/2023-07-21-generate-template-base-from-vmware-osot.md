---
layout: post
title: Generate Citrix Optimizer Template from VMware OS Optimization Tool
image: 
  path: /assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot07.png
description: >
  This post will show you how to generate a Citrix Optimizer Template from the baselines used in the VMware OS Optimization Tool.<br/><br/>This is one option as to using the PowerShell Citrix Optimizer Automation module and was performed as a test, I by no means disregard the VMware optimization tool or the work that has gone into it, this just offers choice. 
sitemap: true
categories: [PowerShell, VDA, Citrix, VMware, Performance, Optimization]
comments: true
---
* 
{:toc}

### Overview

Writing the Citrix Optimizer Automation module got me thinking about different use cases that it may be useful for as well as different ways that virtual desktops can be optimized. This took me down the path of Citrix Optimizer vs VMware Optimizer and the differences between the 2 products. 

Now, before I start let me state "Both products are bloody brilliant" and both products have their own plus and minus sides. However, as this is a kind of "art of the possible" thing I was wondering if I could take the settings that are recommended by the VMware OS Optimization Tool (OSOT) and create a Citrix Optimizer Template based of them fully automated. 

Read on to find out if it was possible.

### VMware OS Optimization Tool

On launching the OSOT you can see straight away that there is a nice template waiting for you.

![VMware OSOT Main Screen](/assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot01.png)

So, if we select that template and click on "Analyze" the OSOT will check the current virtual machine against the options available in the OSOT.

![VMware OSOT Analyse Screen](/assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot02.png)

VMware have done a great job in breaking the optimizations down into categories as can be seen below. Since Citrix Optimizer does not allow nesting of groups we will need to consider this when we are creating the new template and provide names according to the main section and sub-section of each optimization option.

![VMware OSOT Sections Screen](/assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot03.png)

You can also see that VMware has a "Finalize" option at the top, this will do the usual stuff such as NGen image updates, Clear Event Logs etc - something else we also need to consider in our script.

So, moving on, let's have a look at the settings. Head over to the "Templates" option and expand one of the options. On the right you can see all the detail for that specific optimization as well as 2 key fields:

- Category
- Default Selected

![VMware OSOT Setting](/assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot04.png)

When we are injecting the values into the Citrix Optimizer template we will need to check these and ensure that the setting is "true" for Default Selected and in the "Recommended" Category.

If you select the template and click the "Copy and Edit" button at the bottom of the screen you will get the option to create a new template based on the VMware default one.

![VMware OSOT Copy and Edit](/assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot05.png)

Click ok then head over to ```C:\ProgramData\VMware\VMware Horizon OS Optimization Tool\My Templates``` where you will find an XML file. Copy that file and keep it for importing into Citrix Optimizer.

![VMware OSOT XML File](/assets/img/citrixoptimizerautomation/2023-07-21-generate-template-base-from-vmware-osot/2023-07-21-generate-template-base-from-vmware-osot06.png)

For those of you that are geeks (of which I assume is most of you) and want to have a look in the XML you will find all the settings from the template laid out for you, and it is quite some XML file!

```
<step name="Disable default build-in wallpaper - User Policy" description="Set wallpaper to solid color for current user." useCaseCategory="Background" useCaseLevel="SolidBackground" category="recommended" defaultSelected="true" osReleaseId="" nodeId="47995372-38cd-48ec-85fc-b81bec18c9cc">
  <action skipForHku="true">
    <type>Registry</type>
    <command>ADD</command>
    <params>
      <keyName>HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\System</keyName>
      <valueName>WallPaper</valueName>
      <type>REG_SZ</type>
      <data>
      </data>
    </params>
  </action>
</step>
```

Here is the XML for the setting we were looking at above, you will notice that the 2 options for "Default Selected" and "Recommended" are available in the file as well as all the settings for the optimization.

That's it with VMware OSOT, now we have the file lets loot if we can import that into Citrix Optimizer.

### Create Citrix Optimizer Template Script

#### Function ```Get-RegistryType```

The first function in the script will convert the Registry type in VMware OSOT to one that Citrix Optimizer understands as they handle them differently.

```PowerShell
function Get-RegistryType {

    [CmdletBinding()]

    Param (
        [Parameter(
            ValuefromPipelineByPropertyName = $true,mandatory=$false
        )]
        $Type
    )

    switch ($Type)
    {
        "REG_DWORD" {$Return = "Dword"}
        "REG_SZ" {$Return = "String"}
        "REG_MULTI_SZ" {$Return = "MultiString"}
        "REG_BINARY" {$Return = "Binary"}
    }

    Return $Return

}
```

#### Function ```Get-DefaultUserKey```

The second function in the script will convert any Registry entry with ```HKCU``` to ```HKDU```. Why? Well Citrix Optimizer and VMware OSOT differ in how they inject user settings into the default profile. They both inject the settings into ```C:\Users\Default\NTUSER.DAT``` however VMware OSOT will do that based off the ```HKCU``` setting whereby Citrix Optimizer is looking for the ```HKDU``` key.  With that in mind we need to change that before we send it into the automation.

```PowerShell
function Get-DefaultUserKey {

    [CmdletBinding()]

    Param (
        [Parameter(
            ValuefromPipelineByPropertyName = $true,mandatory=$false
        )]
        $KeyName
    )

    $Return = $KeyName.Replace("HKCU", "HKDU")

    Return $Return

}
```

#### The Main Script

Below is the main script, I have tried my best to comment it (not something I am normally very good at) to explain what it is doing.  If you have questions please feel free to ask questions in the comment's section of this post.

```PowerShell
# Define Citrix Optimizer Template Name
$TemplateName = 'Bretty VMware OSOT Import.xml'

# Import the Citrix Optimizer Automation Module
Import-Module ./CitrixOptimizerAutomation

# Create a new Template
Write-Host "Create a new Citrix Optimizer Template: $($TemplateName)"
$Template = New-CitrixTemplate -Path $TemplateName -DisplayName 'Bretty VMware OSOT Import' -Description 'Citrix Optimizer Template based on the export of the VMware OSOT tool' -Author 'Dave Brett'

# Define Path to exported VMware OSOT
$Path = "VMware_OSOT.xml"

# Read Entire VMware OSOT File
[XML]$OSOT = Get-Content $Path

# Loop through the OSOT and get each top level Category
foreach($TopLevel in $OSOT.sequence.group.group){

    # Get the Top Level Name for Citrix Group Name
    $TopLevelName = $TopLevel.name

    # Get the sub groups in the Top Level Category
    $SubLevelGroups = $TopLevel.group

    # Loop through each of the sub level Categories in the Top Level Category
    foreach($SubLevel in $SubLevelGroups){

        # Build the Citrix Optimizer Group Name based on Top Level Group and Sub Level Group Names
        $GroupName = "$($TopLevelName) - $($SubLevel.name)"

        # Add the new Citrix Optimizer Group
        Write-Host "Create a new Group $($GroupName)"
        $Group = New-CitrixTemplateGroup -Path $Template.Path -GroupName $GroupName -GroupDescription $GroupName

        #Get the Section Detail for the Sub Level
        $Section = $Sublevel

        # Loop through each Step in the Sub Level Section
        foreach($object in $Section.step){

            # Get the name and description for the Optimization Step
            $Name = $Object.name
            $Description = $Object.Description

            # Check if its recommended and enabled by default
            if(($Object.category -eq "recommended") -and ($Object.defaultSelected -eq $true)){
                # Set to process
                $Process = $true
            } else {
                # Set to skip
                $Process = $false
            }

            # Get the action detail into a variable
            $Action = $Object.action

            # Registry Add Item swapping HKCU to HKDU
            if(($Action.type -eq "Registry") -and ($Action.command -eq "ADD") -and ($Process)){
                $Params = $Action.params
                Write-Host "Add Registry Value: $($Object.name)"
                if($Params.keyName -like "HKCU*"){
                    $Key = Get-DefaultUserKey -KeyName $Params.keyName
                } else {
                    $Key = $Params.keyName
                }
                if($Params.data -eq "<about:blank>"){
                    $Data = " "
                } else {
                    $Data = $Params.data
                }
                $Registry = New-CitrixTemplateRegistry -Path $Template.Path -GroupName $GroupName -EntryName $Object.Name -EntryDescription $Object.Description -ItemName "$($Params.valueName)" -ItemPath "$($Key)" -ItemValue "$($Data)" -ItemType (Get-RegistryType -Type $Params.type)
            }

            # Registry Delete Value
            if(($Action.type -eq "Registry") -and ($Action.command -eq "DELETEVALUE") -and ($Process)){
                $Params = $Action.params
                Write-Host "Delete Registry Value: $($Object.name)"
                if($Params.keyName -like "HKCU*"){
                    $Key = Get-DefaultUserKey -KeyName $Params.keyName
                } else {
                    $Key = $Params.keyName
                }
                $Registry = New-CitrixTemplateRegistry -Path $Template.Path -GroupName $GroupName -EntryName $Object.Name -EntryDescription $Object.Description -ItemName "$($Params.valueName)" -ItemPath "$($Key)" -DeleteValue
            }

            # Disable Scheduled Tasks
            if(($Action.type -eq "SchTasks") -and ($Process)){
                $Params = $Action.params
                Write-Host "Add Scheduled Task: $($Object.name)"
                $Task = New-CitrixTemplateTask -Path $Template.Path -GroupName $GroupName -TaskName $Object.Name -TaskPath $Params.taskName -TaskDescription $Object.Description -State "Disabled" 
            }

            # Disable Service
            if(($Action.type -eq "Service") -and ($Process)){
                $Params = $Action.params
                Write-Host "Add Service: $($Object.name)"
                $Service = New-CitrixTemplateService -Path $Template.Path -EntryName $Object.Name -ServiceName $Params.serviceName -ServiceDescription $Object.Description -GroupName $GroupName -State "Disabled"
            }   
        }  
    }
}
```

#### Finalize Section

Since Citrix Optimizer works top down I want to add a section at the end to Finalize the virtual machine ready for deployment. I have scripts to do the following that I want to pass in and execute:

- Set Power Plan
- Clear Additional Scheduled Tasks (Think Edge Updater)
- NGEN Updates
- Remove AppX Packages
- Clear Event Logs

You can add any script you like here but just don't include the Citrix Optimizer Specifics, the automation will put that part in for you.

```PowerShell
# Finalize Section
$Group = New-CitrixTemplateGroup -Path $Template.Path -GroupName "Finalize" -GroupDescription "Final Tasks to complete before image seal"
$ScriptedAction = New-CitrixTemplateScript -Path $Template.Path -GroupName "Finalize" -EntryName "Clear Scheduled Tasks" -EntryDescription "Clear out any remaining Scheduled Tasks" -ScriptFile "Script_ClearScheduledTasks.ps1"
$ScriptedAction = New-CitrixTemplateScript -Path $Template.Path -GroupName "Finalize" -EntryName "Remove AppX Packages" -EntryDescription "Removes the AppX Packages for All Users" -ScriptFile "Script_RemoveAppxPackages.ps1"
$ScriptedAction = New-CitrixTemplateScript -Path $Template.Path -GroupName "Finalize" -EntryName "Set Power Plan" -EntryDescription "Sets the Power Plan to High Performance" -ScriptFile "Script_SetPowerPlan.ps1"
$ScriptedAction = New-CitrixTemplateScript -Path $Template.Path -GroupName "Finalize" -EntryName "Run NGEN Update" -EntryDescription "Runs the .NET NGEN Update Routines" -ScriptFile "Script_NGENUpdate.ps1"
$ScriptedAction = New-CitrixTemplateScript -Path $Template.Path -GroupName "Finalize" -EntryName "Clear Event Logs" -EntryDescription "Clears the System Event Logs" -ScriptFile "Script_ClearEventLogs.ps1"  
$Registry = New-CitrixTemplateRegistry -Path $Template.Path -GroupName "Finalize" -EntryName "Disable Show Dynamic Content" -EntryDescription "Diable the Dynamic Content in the Search Box" -ItemName "ShowDynamicContent" -ItemPath "HKDU\Software\Microsoft\Windows\CurrentVersion\Feeds\DSB" -ItemValue "0" -ItemType "Dword"
$Registry = New-CitrixTemplateRegistry -Path $Template.Path -GroupName "Finalize" -EntryName "Disable Dynamic Search Box" -EntryDescription "Diable the Dynamic Search Box" -ItemName "IsDynamicSearchBoxEnabled" -ItemPath "HKCU\Software\Microsoft\Windows\CurrentVersion\SearchSettings" -ItemValue "0" -ItemType "Dword"

```

### Running The Script

<iframe width="860" height="615" src="https://www.youtube.com/embed/No2cR17nnlU" title="Citrix Optimizer Automation" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### Conclusion

This is more of an "art of the possible" and I wanted to see if this was going to even be an option. Turns out it is.

Thanks for reading !