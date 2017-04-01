---
title: Configuring Azure Media Services telemetry with REST| Azure
description: This article shows you how to use the Azure Media Services telemetry using REST API..
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.assetid: e1a314fb-cc05-4a82-a41b-d1c9888aab09
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
wacn.date: ''
ms.author: juliako
---

# Configuring Azure Media Services telemetry with REST

This topic describes general steps that you might take when configuring the Azure Media Services (AMS) telemetry using REST API. 

>[!NOTE]
>For the detailed explanation of what is AMS telemetry and how to consume it, see the [overview](./media-services-telemetry-overview.md) topic.

The steps described in this topic are:

- Getting the storage account associated with a Media Services account
- Getting the Notification Endpoints
- Creating a Notification Endpoint for Monitoring. 

    To create a Notification Endpoint, set the EndPointType to AzureTable (2) and endPontAddress set to the storage table (for example, https://telemetryvalidationstore.table.core.chinacloudapi.cn/).

- Get the monitoring configurations

    Create a monitoring configuration settings for the services you want to monitor. No more than one monitoring configuration settings is allowed. 

- Add a monitoring configuration

## Get the storage account associated with a Media Services account

###Request

```
GET https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/StorageAccounts HTTP/1.1
x-ms-version: 2.13
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbjbclus001rest-hs.chinacloudapp.cn
Response
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 370
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
x-ms-request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
X-Content-Type-Options: nosniff
DataServiceVersion: 2.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:40 GMT

{"d":{"results":[{"__metadata":{"id":"https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/StorageAccounts('telemetryvalidationstore')","uri":"https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/StorageAccounts('telemetryvalidationstore')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.StorageAccount"},"Name":"telemetryvalidationstore","IsDefault":true,"BytesUsed":null}]}}
```

## Get the Notification Endpoints

###Request

```
GET https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/NotificationEndPoints HTTP/1.1
x-ms-version: 2.13
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbjbclus001rest-hs.chinacloudapp.cn
```

###Response
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 20
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
x-ms-request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
X-Content-Type-Options: nosniff
DataServiceVersion: 2.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:40 GMT

{  
       "d":{  
          "results":[]
       }
}
```

## Create a Notification Endpoint for monitoring

###Request

```
POST https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/NotificationEndPoints HTTP/1.1
x-ms-version: 2.13
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbjbclus001rest-hs.chinacloudapp.cn
Content-Length: 115

{  
       "Name":"monitoring",
       "EndPointAddress":"https://telemetryvalidationstore.table.core.chinacloudapi.cn/",
       "EndPointType":2
}
```

>[!NOTE]
>Don't forget to change the "https://telemetryvalidationstore.table.core.chinacloudapi.cn" value to your storage account.

###Response

```
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 578
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')
Server: Microsoft-IIS/8.5
request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
x-ms-request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:42 GMT

{"d":{"__metadata":{"id":"https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","uri":"https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.NotificationEndPoint"},"Id":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Name":"monitoring","Created":"\/Date(1449033042667)\/","EndPointAddress":"https://telemetryvalidationstore.table.core.chinacloudapi.cn/","EndPointType":2}}
```

## Get the monitoring configurations

### Request

```
GET https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/MonitoringConfigurations HTTP/1.1
x-ms-version: 2.13
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbjbclus001rest-hs.chinacloudapp.cn
```

###Response

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 20
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
x-ms-request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:42 GMT

{"d":{"results":[]}}
```

## Add a monitoring configuration

### Request

```
POST https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/MonitoringConfigurations HTTP/1.1
x-ms-version: 2.13
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbjbclus001rest-hs.chinacloudapp.cn
Content-Length: 133

{  
   "NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4",
   "Settings":[  
      {  
     "Component":"Channel",
     "Level":"Normal"
      }
   ]
}
```

### Response

```
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 825
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
Server: Microsoft-IIS/8.5
request-id: daede9cb-8684-41b0-a921-a3af66430cbe
x-ms-request-id: daede9cb-8684-41b0-a921-a3af66430cbe
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:43 GMT

{"d":{"__metadata":{"id":"https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","uri":"https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.MonitoringConfiguration"},"Id":"nb:mcid:UUID:1a8931ae-799f-45fd-8aeb-9641740295c2","NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Created":"2015-12-02T05:10:43.7680396Z","LastModified":"2015-12-02T05:10:43.7680396Z","Settings":{"__metadata":{"type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.ComponentMonitoringSettings)"},"results":[{"Component":"Channel","Level":"Normal"},{"Component":"StreamingEndpoint","Level":"Disabled"}]}}}
```

## Consuming telemetry information

For information about consuming telemetry information, see [this](./media-services-telemetry-overview.md) topic.

## Stop telemetry

###Request

```
DELETE https://wamsbjbclus001rest-hs.chinacloudapp.cn/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
x-ms-version: 2.13
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbjbclus001rest-hs.chinacloudapp.cn
```