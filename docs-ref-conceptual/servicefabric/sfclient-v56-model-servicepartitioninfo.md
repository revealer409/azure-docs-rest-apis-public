---
title: "ServicePartitionInfo"
ms.date: "2017-05-09"
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
# ServicePartitionInfo

Information about a partition of a Service Fabric service.
# Inheritance

The type 'ServicePartitionInfo' is a base type of the polymorphic type model with property 'ServiceKind' as the discriminator.
Depending upon the value of the property the serialized contents on the wire will be one of the derived types listed below.
## Derived Types

| ServiceKind | Derived Type |
| --- | --- | 
| Stateful | [StatefulServicePartitionInfo](sfclient-v56-model-statefulservicepartitioninfo.md) |
| Stateless | [StatelessServicePartitionInfo](sfclient-v56-model-statelessservicepartitioninfo.md) |
