---
ms.assetid: 
ms.title: Azure Consumption REST APIs
ms.service: consumption
author: vkurpad
ms.author: vikurpad
ms.manager: raphaelc
service_description: To be added..
---


# Azure consumption

The Azure Consumption APIs give you programmatic access to cost and usage data for your Azure resources. These APIs currently only support Enterprise Enrollments and Web Direct  Subscriptions (with a few exceptions). The APIs are continually updated to support other types of Azure subscriptions

## List of Unsupported Subscription Types
MS-AZR-0145P (CSP)
MS-AZR-0146P (CSP)
MS-AZR-159P (CSP)
MS-AZR-0036P (sponsored)
MS-AZR-0143P (sponsored)
MS-AZR-0015P (internal)
MS-AZR-0144P (DreamSpark) 


## REST Operation Groups

| Operation Group | Description |
|-----------------|-------------|
| [Usage Details](~/docs-ref-autogen/consumption/UsageDetails.yml) | Provides operations to get usage information on subscription. |
  [Reservation Details](~/docs-ref-autogen/consumption/ReservationsDetails.yml) | Provides operations to get reservation detailed usage for Pay as you go and Enterprise Subscriptions. |
  [Reservation Summaries](~/docs-ref-autogen/consumption/ReservationsSummaries.yml) | Provides operations to get reservation summary usage for Pay as you go and Enterprise Subscriptions. |

## See also

For more information about using this API in one of the language-specific SDKs, visit the following

[Azure Consumption Python SDK](/python/api/overview/azure/consumption?view=azure-python)

[Azure Consumption CLI](/cli/azure/consumption?view=azure-cli-latest)

[Azure Consumption .NET SDK](/dotnet/api/overview/azure/consumption/management?view=azure-dotnet)

[Azure Consumption NodeJS SDK](/javascript/api/overview/azure/consumption?view=azure-node-2.2.0)

[Azure Consumption Ruby SDK](https://rubygems.org/gems/azure_mgmt_consumption)