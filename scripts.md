---
layout: page
title: Scripts
description: Here are various scripts that I have used over the years. Hopefully you will find then useful but please test them before running them in production!
---
* 
{:toc}

## Nutanix Scripts

-  **[Add-NutanixCalmFirewallRules.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Nutanix/Add-NutanixCalmFirewallRules.ps1)**. Adds the Nutanix Calm Firewall Rules to an endpoint.
  
## Microsoft Scripts

-  **[Add-ComputerToDomain.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Microsoft/Add-ComputerToDomain.ps1)**. Add a Computer to an Active Directory Domain (Used with Nutanix Calm).
-  **[Configure-DHCP.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Microsoft/Configure-DHCP.ps1)**. Configure the DHCP Role (Used with Nutanix Calm).
-  **[Configure-DNS.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Microsoft/Configure-DNS.ps1)**. Configure the DNS Role (Used with Nutanix Calm).
-  **[Configure-OuStructure.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Microsoft/Configure-OuStructure.ps1)**. Configure base AD OU Structure (Used with Nutanix Calm).
-  **[Configure-WSUS.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Microsoft/Configure-WSUS.ps1)**. Configure Windows Update Services (Used with Nutanix Calm).
-  **[Rename-Computer.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Microsoft/Rename-Computer.ps1)**. Rename a Computer Object in the Domain (Used with Nutanix Calm).
  
## Security Scripts

-  **[Configure-WinRmForTerraform.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Security/Configure-WinRmForPowerShell.ps1)**. Configure WinRM for use with Terraform.
-  **[Disable-LegacyPowerShell.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Security/Disable-LegacyPowerShell.ps1)**. Disable Legacy PowerShell.
-  **[Disable-Smb1.ps1](https://github.com/dbretty/dbretty.Scripts/blob/master/Security/Disable-Smb1.ps1)**. Disable SMB 1.

## NetScaler Scripts

-  **[Always Up Service](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Always%20Up%20Service.conf)**.
-  **[Delivery Controller GSLB Services](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Controller%20GSLB.conf)**.
-  **[Delivery Controller Load Balancer](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Controller%20LB.conf)**.
-  **[Director GSLB Services](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Director%20GSLB.conf)**.
-  **[Director Load Balancer](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Director%20LB.conf)**.
-  **[Licensing GSLB Services](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Licensing%20GSLB.conf)**.
-  **[Licensing Load Balancer](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/Licensing%20LB.conf)**.
-  **[StoreFront GSLB Services](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/StoreFront%20GSLB.conf)**.
-  **[StoreFront Load Balancer](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/StoreFront%20LB.conf)**.
-  **[WEM GSLB Services](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/WEM%20GSLB.conf)**.
-  **[WEM Load Balancer](https://github.com/dbretty/dbretty.Scripts/blob/master/NetScaler/WEM%20LB.conf)**.
  
## Citrix Build Scripts

### Delivery Controller
-  **[Import and Bind Certificate](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Delivery%20Controller/01_import_and_bind_certificate.ps1)**
-  **[Install Controller](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Delivery%20Controller/02_install_controller.ps1)**
-  **[Install Nutanix MCS Plugin](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Delivery%20Controller/03_install_nutanix_mcs_plugin.ps1)**
-  **[Configure CVAD Site](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Delivery%20Controller/04_configure_cvad_site.ps1)**
-  **[Secure Controller](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Delivery%20Controller/05_secure_delivery_controller.ps1)**

### Director
-  **[Install Pre-Reqs](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Director/01_install_director_pre_reqs.ps1)**
-  **[Import and Bind Certificate](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Director/02_import_and_bind_certificate.ps1)**
-  **[Install Director](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Director/03_install_director.ps1)**
-  **[Configure Director](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Director/04_configure_director.ps1)**
-  **[Secure Director](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Director/06_secure_director_iis.cmd)**

### Federated Authentication
-  **[Install FAS](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Federated%20Authentication/01_install_fas.ps1)**
-  **[Configure FAS](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Federated%20Authentication/02_configure_fas.ps1)**

### Licensing
-  **[Install Licensing](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Licensing/01_install_licensing_server.ps1)**
-  **[Install Licensing PoSH](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Licensing/02_install_licensing_posh.ps1)**
-  **[Configure Licensing](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/Licensing/03_configure_licensing_server.ps1)**

### Microsoft SQL
-  **[Install SQL Server](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/SQL/01_install_sql_server.ps1)**

### StoreFront
-  **[Install Pre-Reqs](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/StoreFront/01_install_storefront_pre_reqs.ps1)**
-  **[Import and Bind Certificate](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/StoreFront/02_import_and_bind_certificate.ps1)**
-  **[Install StoreFront](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/StoreFront/03_install_storefront.ps1)**
-  **[Configure StoreFront](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/StoreFront/04_configure_storefront.ps1)**
-  **[Customize StoreFront](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/StoreFront/05_customise_storefront.ps1)**
-  **[Secure StoreFront](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/StoreFront/06_secure_storefront_iis.cmd)**

### WEM
-  **[Install WEM](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/WEM/01_install_wem.ps1)**
-  **[Configure WEM Database](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/WEM/02_configure_wem_database.ps1)**
-  **[Configure WEM Infrastructure](https://github.com/dbretty/dbretty.Scripts/blob/master/Citrix/WEM/03_configure_wem_infrastructure.ps1)**