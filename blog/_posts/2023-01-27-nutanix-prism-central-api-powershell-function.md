---
layout: post
title: Nutanix Prism Central API Call from PowerShell
image: 
  path: /assets/img/posts/2023-01-27-nutanix-prism-central-api-powershell-function/api.png
description: >
  This post will show you how to gather information from the Nutanix Prism Central API using PowerShell.<br/><br/>This code can also be wrapped into a function and re-used to get any information back from the API. 
sitemap: true
categories: [PowerShell, Nutanix]
comments: true
---
* 
{:toc}

### Overview

I have often needed information from my Nutanix Clusters that I can use for a report or just programmatically and always use the API that Nutanix has to get this data. 

The reason for this is that it allows me to get the information I want into a variable and then manipulate it with ```PowerShell``` to give people (often management) exactly the information that they require.

Fortunately Nutanix have great documentation of their API and you can find that [here](https://www.nutanix.dev). 

For this code I am using the v3 Prism Central API. Why? Well from Prism Central you can get information about any of the clusters that you have in your organization (think VMware vCenter) and pull that back for use in the remainder of your script.

I have written the code so that you are able to pass in the: 

- API Root Version
- API End Point
- Method
- Payload

So, you should be able to get pretty much any information you want from Prism Central just using this code. All you have to do is check the documentation and find out what the above variables need, as can be seen in the image below.

![](/assets/img/posts/2023-01-27-nutanix-prism-central-api-powershell-function/01.png)

As Nutanix change the API versions you should be able to update your code to the new version by just changing the ```API``` variable you pass in.

I would recommend putting this code inside a ```function``` and passing in the parameters, therefore making the code reusable, but this is just an example of the code that you can use to get the information that you require.

### Variable Descriptions

| Name | Description | Example |
|:-----------------|:-----------|:-----------|
| IP | The IP Address of Prism Central | 10.20.30.40 |
| UserName | The username to connect to Prism Central | admin |
| Password | The password to connect to Prism Central| password |
| ApiRoot | The Root API you wish to query| api/nutanix/v3 |
| API | The API you are want to query | subnets/list |
| Method | The method for the Rest call | post |
| Payload | The payload you wish to send to the API | { length : 0 } |

### PowerShell Code

Below you will find the PowerShell code used to query the Prism Central API

```powershell
# Define Variables
$IP = "192.168.100.10"
$UserName = "admin"
$Password = "SuperSecretPassword"
$ApiRoot = "api/nutanix/v3"
$API = "subnets/list"
$Method = "POST"
$Payload = "{ }"

# Build Credential Object
$credPair = "$($UserName):$($Password)"
$encodedCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($credPair))
$headers = @{ Authorization = "Basic $encodedCredentials" }

# Build Connection Uri
$URL = "https://$($IP):9440/$($ApiRoot)/$API"

try {
    $ApiResult = Invoke-RestMethod -Uri $URL -method $Method -ContentType 'application/json' -SkipCertificateCheck -Headers $headers -Body $Payload
}
catch {
    Write-Error "Error Executing RestAPI Call"
    Exit
}
```

### Example

#### Get List Of Subnets

![](/assets/img/posts/2023-01-27-nutanix-prism-central-api-powershell-function/02.png)

### Conclusion

I have found this small code snippet super useful when I am getting information back from my Nutanix Cluster. It allows me to call this code within a function and just pass in the API end point that I want to query as well as a Payload and Method and pull those results directly back into PowerShell