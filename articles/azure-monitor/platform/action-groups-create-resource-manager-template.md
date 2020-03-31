---
title: Műveletcsoportok létrehozása Erőforrás-kezelő sablonokkal
description: Ismerje meg, hogyan hozhat létre egy műveletcsoportot egy Azure Resource Manager-sablon használatával.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669080"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Műveletcsoport létrehozása Erőforrás-kezelő sablonnal
Ez a cikk bemutatja, hogyan használhat egy [Azure Resource Manager-sablont](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) a műveletcsoportok konfigurálásához. A sablonok használatával automatikusan beállíthatja azokat a műveletcsoportokat, amelyek bizonyos típusú riasztásokban újra felhasználhatók. Ezek a műveletcsoportok biztosítják, hogy a megfelelő felek értesítést kapjanak a riasztás aktiválásáról.

Az alapvető lépések a következők:

1. Hozzon létre egy sablont JSON-fájlként, amely leírja a műveletcsoport létrehozását.

2. Telepítse a sablont [bármely telepítési módszerrel.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

Először azt ismertjük, hogyan hozhat létre Erőforrás-kezelő sablont egy olyan műveletcsoporthoz, ahol a műveletdefiníciók kódolva vannak a sablonban. Másodszor, azt ismerteti, hogyan hozhat létre egy sablont, amely a webhook konfigurációs információkat bemeneti paraméterekként, amikor a sablon telepítve van.

## <a name="resource-manager-templates-for-an-action-group"></a>Erőforrás-kezelő sablonok egy műveletcsoporthoz

Ha Erőforrás-kezelő sablonnal szeretne műveletcsoportot létrehozni, hozzon létre egy típusú `Microsoft.Insights/actionGroups`erőforrást. Ezután töltse ki az összes kapcsolódó tulajdonságot. Az alábbiakban két mintasablont olvashat, amelyek egy műveletcsoportot hoznak létre.

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
* További információ a [műveletcsoportokról](../../azure-monitor/platform/action-groups.md).
* További információ [a riasztásokról.](alerts-overview.md)
* Megtudhatja, hogy miként adhat hozzá [riasztásokat az Erőforrás-kezelő sablon használatával.](../../azure-monitor/platform/alerts-activity-log.md)

