---
title: "Get information about a connection&#160;"
ms.custom: ""
ms.date: "2016-04-19"
ms.prod: "azure"
ms.reviewer: ""
ms.service: "virtual-network"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "reference"
ms.assetid: af389a5f-ce66-4f4b-9fca-25bb846d50f3
caps.latest.revision: 5
author: "georgewallace"
ms.author: "gwallace"
manager: "carmonm"
translation.priority.mt: 
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "pt-br"
  - "ru-ru"
  - "zh-cn"
  - "zh-tw"
---
# Get information about a connection&#160;
Get the details of the specified connection.  
  
## Request  
 See [Connections](../NetworkGatewayREST/connections.md) for headers and parameters that are used by all requests related to connections.  
  
|Method|Request URI|  
|------------|-----------------|  
|GET|`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/microsoft.network/connections/{connection-name}?api-version={api-version}`|  
  
## Response  
 Status code: 200 (OK) if the connection exists; otherwise, 404 (Not Found).  
  
```json  
{  
   "name": "connection1",  
   "id": "/subscriptions/{subscription-id}/resourceGroups/myrg1/providers/microsoft.network/connections/connection1",  
   "location": "West US",  
   "tags": { "key1": "value1" },  
   "etag": "W/\"00000000-0000-0000-0000-000000000000\"",  
   "properties": {  
      "gateway1": {  
         "name": "firstgateway",  
         "id": "/subscriptions/{subscription-id}/resourceGroups/myrg1/providers/microsoft.network/SiteToSite/firstgateway"  
      },  
      "gateway2": {  
         "name": "secondgateway",  
         "id": "/subscriptions/{subscription-id}/resourceGroups/myrg1/providers/microsoft.network/SiteToSite/secondgateway"  
      },  
      "connectionType": "SiteToSite",  
      "connectivityState": "Connected",  
      "allocatedIPAddresses": [ "{ip-address}" ],  
      "ipsecParameters": {  
         "encryptionType": "{encryptionType}",  
         "pfsGroup": {pfsGroup}",  
         "saDataSizeKilobytes": "{saDataSizeKilobytes}",  
         "saLifeTimeSeconds": "{saLifeTimeSeconds}",  
         "hashAlgorithm": "{hashAlgorithm}"  
      }  
   }  
}  
  
```  
  
|Element name|Description|  
|------------------|-----------------|  
|name|The name of the request connection.|  
|location|The location value for the NRP instance that will receive this request. Connections are global objects, so this could be any location.|  
|gateway1|The name of the first gateway in this connection.|  
|gateway2|The name of the second gateway in this connection.|  
|connectionType|The type of this connection. This could be either `SiteToSite`, `PointToSite`, or `Dedicated`.|  
|connectivityState|The current state of this connection. Valid values are `Connected` or `Disconnected`.|  
|allocatedIPAddress|An IP address that has been allocated to a client in a PointToSite connection.|  
|encryptionType|The type of encryption that will be used in a SiteToSite connection.|  
|pfsGroup|The pfs group that will be used in a SiteToSite connection.|  
|saDataSizeKilobytes|The number of kilobytes that can be sent across a SiteToSite connection before the connection’s IPsec SA will need to be renegotiated.|  
|saLifeTimeSeconds|The number of seconds that a SiteToSite connection’s IPsec SA will be valid before it needs to be renegotiated.|  
|hashAlgorithm|The hash algorithm that will be used when negotiating SiteToSite IPsec SAs.|