---
title: "Set Upgrade Orchestration Service State"
ms.date: "2018-01-22"
ms.prod: "azure"
ms.service: "service-fabric"
ms.topic: "reference"
applies_to: 
  - "Azure"
  - "Windows Server 2012 R2"
  - "Windows Server 2016"
dev_langs: 
  - "rest-api"
helpviewer_keywords: 
  - "Service Fabric REST API Reference"
author: "rwike77"
ms.author: "ryanwi"
manager: "timlt"
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
# Set Upgrade Orchestration Service State
Update the service state of Service Fabric Upgrade Orchestration Service.

Update the service state of Service Fabric Upgrade Orchestration Service. This API is internally used for support purposes.

## Request
| Method | Request URI |
| ------ | ----------- |
| POST | `/$/SetUpgradeOrchestrationServiceState?api-version=6.0&timeout={timeout}` |


## Parameters
| Name | Type | Required | Location |
| --- | --- | --- | --- |
| [api-version](#api-version) | string | Yes | Query |
| [timeout](#timeout) | integer (int64) | No | Query |
| [UpgradeOrchestrationServiceState](#upgradeorchestrationservicestate) | [UpgradeOrchestrationServiceState](sfclient-v61-model-upgradeorchestrationservicestate.md) | Yes | Body |

____
### api-version
__Type__: string <br/>
__Required__: Yes<br/>
__Default__: 6.0 <br/>
<br/>
The version of this API. This is a required parameter and its value must be "6.0".

Service Fabric REST API version is based on the runtime version in which the API was introduced or was changed. Service Fabric runtime supports more than one version of the API. This is the latest supported version of the API. If a lower API version is passed, the returned response may be different from the one documented in this specification.

Additionally the runtime accept any version that is higher than the latest supported version up to the current version of the runtime. So if the latest API version is 6.0, but if the runtime is 6.1, in order to make it easier to write the clients, the runtime will accept version 6.1 for that API. However the behavior of the API will be as per the documented 6.0 version.


____
### timeout
__Type__: integer (int64) <br/>
__Required__: No<br/>
__Default__: 60 <br/>
__InclusiveMaximum__: 4294967295 <br/>
__InclusiveMinimum__: 1 <br/>
<br/>
The server timeout for performing the operation in seconds. This specifies the time duration that the client is willing to wait for the requested operation to complete. The default value for this parameter is 60 seconds.

____
### UpgradeOrchestrationServiceState
__Type__: [UpgradeOrchestrationServiceState](sfclient-v61-model-upgradeorchestrationservicestate.md) <br/>
__Required__: Yes<br/>
<br/>
Service state of Service Fabric Upgrade Orchestration Service.

## Responses

| HTTP Status Code | Description | Response Schema |
| --- | --- | --- |
| 200 (OK) | A successful response means that the service state of Service Fabric Upgrade Orchestration Service has been updated.<br/> | [UpgradeOrchestrationServiceStateSummary](sfclient-v61-model-upgradeorchestrationservicestatesummary.md) |
| All other status codes | The detailed error response.<br/> | [FabricError](sfclient-v61-model-fabricerror.md) |