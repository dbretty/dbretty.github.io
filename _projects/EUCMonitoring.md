---
layout:      project
title:       EUC Monitoring
date:        1 Jan 2022
image:
  path:       /projects/images/EUCMonitoring.jpg
caption:     Citrix EUC Monitoring Solution.
description: >
  This framework was developed by the community for the community.
  <br/> <br/>
  It will give you the ability to monitor your Citrix End User Computing environment and push the output to Grafana.
links:
  - title:   PSGallery
    url:     https://www.powershellgallery.com/packages/EUCMonitoring
  - title:   Source
    url:     https://github.com/dbretty/EUCMonitoring
featured:    false
---

To install and run this software follow the below steps

### Pre-requisites 
#### On-Premises

The Server that you want to run this script from must have the XenServer and XenDesktop PowerShell SDK Installed. Install Citrix Studio on the monitoring server and also install the XenServer SDK from the [XenServer](https://www.citrix.com/downloads/xenserver/product-software.html) download page.

#### Cloud
The Server that you want to run this script from must have the Remote [PowerShell SDK for Applications and Desktops Service](http://download.apps.cloud.com/CitrixPoshSdk.exe)

Obtain a Citrix Cloud automation credential as follows:
- Login to https://citrix.cloud.com/
- Navigate to "Identity and Access Management". 
- Click "API Access". 
- Enter a name for Secure Client and click Create Client. 
- Once Secure Client is created, download Secure Client Credentials file (ie. downloaded to C:\Monitoring)

Note the Customer ID located in this same page, this is case senstitive.
```powershell
Set-XDCredentials -CustomerId "%Customer ID%" -SecureClientFile "C:\Monitoring\secureclient.csv" -ProfileType CloudApi -StoreAs "CloudAdmin"
```

NOTE: **xdbrokerprimary/xdbrokerfailover** should be set as the Citrix Cloud Connector, the cloud connectors will proxy the connection directly to the Delivery Controller as they are not directly accessible.

NOTE: xdbrokerprimary/xdbrokerfailover should be set as the Citrix Cloud Connector, the cloud connectors will proxy the connection directly to the Delivery Controller as they are not directly accessible.

### Installation steps

1. Open up PowerShell on the machine you want to use for monitoring.
2. Run the following to locate on PSGallery
```powershell
Find-Module EUCMonitoring
```
3. Run the following command to install the EUC Monitoring Module.
   * Install for **Allusers** (Need to be run as Administrator)
        ```powershell
        Install-Module EUCMonitoring
        ```
   * Install for **Currentuser**
        ```powershell
        Install-Module EUCMonitoring -Scope CurrentUser
        ```
4. Next run the following command pointing to a directory that you want the EUC Monitoring setting files and output HTML to reside.
   * Run this for manual configuration. 
        ```powershell
        Set-EUCMonitoring C:\Monitoring -verbose
        ```
   * Or, for users that want a more interactive dashboard, you can invoke this parameter which will install InfluxDB, Grafana, and NSSM, along with some default dashboards. (This will need to be run as Administrator)
        ```powershell
        Set-EUCMonitoring C:\Monitoring -InstallVisualizationSetup -verbose
        ```
    * There is a corresponding ```-UninstallVisualizationSetup``` that will uninstall the Influx/Grafana/NSSM instances and remove their directories, but leave your remaining files intact. 
5. If manually configuring, go to that directory and rename the **euc-monitoring.json.template** file to **euc-monitoring.json**
6. Edit the json file to reflect your environment.  

7. Finally run the following command pointing to the directory that holds the needed files

```powershell
cd C:\Monitoring
Start-EUCMonitor
```
OR
```powershell
Start-EUCMonitor -JsonFile ".\mysettings.json" -Verbose 
```
OR, if you did the interactive dashboard
```powershell
cd C:\Monitoring
.\Begin-EUCMonitor.ps1
```

8. Open up your new dashboard HTML file from the location you specified in your json for web data output
