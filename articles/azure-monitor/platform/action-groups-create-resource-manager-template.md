---
title: Műveletcsoportok létrehozása a Resource Manager-sablonokkal
description: Ismerje meg, hogyan műveletcsoport létrehozása Azure Resource Manager-sablon használatával.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 638dd8efba5e86bb7e8abb78a41196bfac9524df
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421861"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Műveletcsoport létrehozása a Resource Manager-sablonnal
Ez a cikk bemutatja, hogyan használható egy [Azure Resource Manager-sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Műveletcsoportok konfigurálása. Sablonok használatával automatikusan állíthat be, amelyek felhasználhatók a meghatározott típusú riasztások Műveletcsoportok. A Műveletcsoportok győződjön meg arról, hogy a megfelelő felek értesítést kapnak, ha a riasztás aktiválódik.

Az alapszintű lépések a következők:

1. Hozzon létre egy sablont, amely azt ismerteti, hogyan hozhat létre a műveletcsoport JSON-fájlként.

2. A sablon üzembe helyezése használatával [bármely üzembe helyezési módszer](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Első lépésként azt ismertetjük, hogyan hozhat létre a Resource Manager-sablon, ahol a művelet definíciók módosíthatóként a sablonban műveleti csoport számára. Ezután azt ismertetjük, hogyan hozhat létre egy sablont, amely a bemeneti paramétereket a webhook konfigurációs információkat a sablon üzembe helyezésekor.

## <a name="resource-manager-templates-for-an-action-group"></a>Műveletcsoport Resource Manager-sablonokkal

Resource Manager-sablonnal műveletcsoport létrehozásához hoz létre a típusú erőforrást `Microsoft.Insights/actionGroups`. Ezután, töltse ki az összes kapcsolódó tulajdonságok. Az alábbiakban a két mintául szolgáló sablont, amely a műveletcsoport létrehozása.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>További lépések
* Tudjon meg többet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md).
* Tudjon meg többet [riasztások](alerts-overview.md).
* Ismerje meg, hogyan adhat hozzá [Resource Manager-sablon használatával riasztások](../../azure-monitor/platform/alerts-activity-log.md).

