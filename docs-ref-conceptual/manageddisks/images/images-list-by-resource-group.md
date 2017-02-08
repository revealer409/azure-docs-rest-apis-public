---
title: "List virtual machine images in a resource group"
ms.date: 2017-01-30
ms.prod: azure
ms.service: managed-disks
ms.topic: reference
ms.assetid: 
author: davidmu
manager: timt
---

# List virtual machine images in a resource group

Lists the virtual machine images in the specified resource group.

For information about getting started with Azure REST operations including request authentication, see [Azure REST API Reference](../../../index.md).

## Request

| HTTP Method | URI|  
| ----------- |----|  
| GET | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/images/?api-version={api-version}` |

| Parameter | Description |
| --------- | ----------- |
| subscriptionId | The identifier of your subscription where the virtual machine images are located. |
| resourceGroup | The name of the resource group that contains the virtual machine images. |
| api-version | The version of the API to use. The current version is 2017-03-30. |
 
## Response 

The response includes an HTTP status code and a set of response headers.  

### Success codes

| Code | Description |
| ---- | ----------- |
| **200 (OK)** | The operation was completed successfully. | 

### Error codes

| Code | Description |
| ---- | ----------- |
| **400 BadRequest** | Invalid parameter value or combination of parameters. |
| **403 Forbidden** | Caller doesn’t have the privilege to call this API. |
| **409 Conflict** | Either another operation is changing the resource in an incompatible way, or the disk is in an incompatible state. Retry the operation. |
| **429 Too Many Requests** | The operation is being throttled. |
| **500 Internal Server Error** |  A server-side error has happened. Retry the operation. |  

This response example includes all optional elements. Your actual response may not include all elements.

```json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage",
      "name": "myImage",
      "type": "Microsoft.Compute/images",
      "location": "West US",
      "properties": {
        "storageProfile": {
          "osDisk": {
            "osType": "Windows",
            "blobUri": "https://mystorageaccount.blob.core.windows.net/osimages/osimage.vhd",
            "snapshot": {
              "id": "subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/mySnapshot1"
            },
            "managedDisk": {
              "id": "subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/myManagedDisk1"
            },
            "osState": "generalized",
            "hostCaching": "readwrite",
            "storageAccountType": "Standard_LRS",
            "diskSizeGB": 20
          },
          "dataDisks": [
            {
              "lun": "1",
              "blobUri": "https://mystorageaccount.blob.core.windows.net/dataimages/dataimage.vhd",
              "snapshot": {
                "id": "subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/mySnapshot2"
              },
              "managedDisk": {
                "id": "subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/myManagedDisk2"
              },
              "hostCaching": "readwrite",
              "storageAccountType": "Standard_LRS",
              "diskSizeInGB": 20
            }
          ]
        },
        "provisioningState": "created"
      }
    }
  ]
}    
```
 