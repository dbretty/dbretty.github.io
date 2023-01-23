---
layout: post
title: Citrix NetScaler Always Up Service
image: 
  path: /assets/img/scripts/ha_banner.jpg
description: >
  Configure an Always Up service on a Citrix NetScaler.
sitemap: true
categories: [NetScaler]
---

This script will create an Always Up service on your Citrix NetScaler for use when binding to a port 80 vServer that you wish to redirect to port 443. To implement the following script log into your NetScaler CLI and execute the following commands.

## Citrix NetScaler Always Up Service Commands 

```
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
```