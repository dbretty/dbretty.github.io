---
layout: post
title: Citrix StoreFront NetScaler Load Balancer
image: 
  path: /assets/img/scripts/ha_banner.jpg
description: >
  Configure a NetScaler Citrix StoreFront Load Balancer.
sitemap: true
categories: [NetScaler, StoreFront]
---

Citrix StoreFront can be load balanced behind a Citrix NetScaler to allow for high availability and resilience. To implement the following script log into your NetScaler CLI and execute the following commands.

## StoreFront NetScaler Load Balancer Commands 

```
# You will not need to do this part if you already have an AlwaysUp service present on your NetScaler
# ***************************************************************************************************

# Add Servers for always up service
add server always_up 127.0.0.1

# Add Services
add service svc_always_up always_up HTTP 80 -gslb NONE -maxClient 0 -maxReq 0 -cip DISABLED -usip NO -useproxyport YES -sp OFF -cltTimeout 180 -svrTimeout 360 -CKA NO -TCPB NO -CMP NO

# Add Responder Policies
add responder action res_act_http_to_https redirect "\"https://\" + HTTP.REQ.HOSTNAME.HTTP_URL_SAFE + HTTP.REQ.URL.PATH_AND_QUERY.HTTP_URL_SAFE" -responseStatusCode 302
add responder policy res_pol_http_to_https HTTP.REQ.IS_VALID res_act_http_to_https

# Add Always Up Monitor
add lb monitor mon_always_up PING -LRTM DISABLED -destIP 127.0.0.1

# Bind Always Up Monitor to Service
bind service svc_always_up -monitorName mon_always_up

# ***************************************************************************************************

# Add Servers for StoreFront
add server storefront_svr_01 **INSERT_STOREFRONT_SERVER_IP**
add server storefront_svr_02 **INSERT_STOREFRONT_SERVER_IP**

# Add Service Group
add serviceGroup svc_grp_storefront_443 SSL -maxClient 0 -maxReq 0 -cip ENABLED X-Forwarded-For -usip NO -useproxyport YES -cltTimeout 180 -svrTimeout 360 -CKA NO -TCPB NO -CMP NO

# Add Load Balancers
add lb vserver vsvr_storefront_80 HTTP **INSERT_STOREFRONT_LOAD_BALANCER_IP** 80 -persistenceType NONE -cltTimeout 180
add lb vserver vsvr_storefront_443 SSL **INSERT_STOREFRONT_LOAD_BALANCER_IP** 443 -persistenceType SOURCEIP -timeout 30 -cltTimeout 180

# Add Rewrite Action
# Update the /Citrix/StoreWeb if you have provisioned a store with a different name to "Store"
# For example, if your store is called Bretty change it to /Citrix/BrettyWeb
add rewrite action rw_act_storefront replace HTTP.REQ.URL "\"/Citrix/StoreWeb\""
add rewrite policy rw_pol_storefront "HTTP.REQ.URL.EQ(\"/\")" rw_act_storefront

# Bind HTTP Server to Always Up Service and put redirect on
bind lb vserver vsvr_storefront_80 svc_always_up
bind lb vserver vsvr_storefront_80 -policyName res_pol_http_to_https -priority 100 -gotoPriorityExpression END -type REQUEST

# Bind Load Balancers to Service Group
bind lb vserver vsvr_storefront_443 svc_grp_storefront_443

# Bind Load Balancer to rewrite policy for redirect
bind lb vserver vsvr_storefront_443 -policyName rw_pol_storefront -priority 100 -gotoPriorityExpression END -type REQUEST

# Add Custom Monitors
# Update the Store if you have provisioned a store with a different name to "Store"
# For example, if your store is called Bretty change it to Bretty
add lb monitor mon_storefront_443 STOREFRONT -scriptName nssf.pl -dispatcherIP 127.0.0.1 -dispatcherPort 3013 -LRTM DISABLED -secure YES -storename Store

# Bind Service Group to StoreFront Server
bind serviceGroup svc_grp_storefront_443 citrix_storefront_server 443

# Bind Service Group to Monitor
bind serviceGroup svc_grp_storefront_443 -monitorName mon_storefront_443

# Set Service Group Backend SSL Profile
set ssl serviceGroup svc_grp_storefront_443 -sslProfile ns_default_ssl_profile_backend

# Set Load Balancer Front End SSL Profile
set ssl vserver vsvr_storefront_443 -sslProfile ns_default_ssl_profile_secure_frontend

# Bind Certificate to Load Balancer
bind ssl vserver vsvr_storefront_443 -certkeyName **INSERT_STOREFRONT_CERTIFICATE_NAME_HERE**
```