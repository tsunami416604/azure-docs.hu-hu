---
title: Műveleti csoportok létrehozása Resource Manager-sablonokkal
description: Megtudhatja, hogyan hozhat létre műveleti csoportot Azure Resource Manager sablon használatával.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669080"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Műveleti csoport létrehozása Resource Manager-sablonnal
Ez a cikk bemutatja, hogyan konfigurálhat egy [Azure Resource Manager sablont](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) a műveleti csoportok konfigurálásához. A sablonok használatával automatikusan beállíthat olyan műveleti csoportokat, amelyek bizonyos típusú riasztásokban újra felhasználhatók. Ezek a műveleti csoportok biztosítják, hogy az összes megfelelő fél értesítést kapjon, ha riasztást vált ki.

Az alapszintű lépések a következők:

1. Hozzon létre egy sablont JSON-fájlként, amely leírja, hogyan kell létrehozni a műveleti csoportot.

2. Telepítse a sablont [bármely üzembe helyezési módszer](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)használatával.

Először is leírjuk, hogyan hozhat létre Resource Manager-sablont egy olyan műveleti csoporthoz, ahol a műveleti definíciók a sablonban nehezen kódoltak. Másodszor, leírjuk, hogyan hozhat létre olyan sablont, amely a webhook konfigurációs adatait bemeneti paraméterekként veszi igénybe a sablon telepítésekor.

## <a name="resource-manager-templates-for-an-action-group"></a>A műveleti csoport Resource Manager-sablonjai

Ha Resource Manager-sablonnal szeretne létrehozni egy műveleti csoportot, létre kell hoznia egy típusú `Microsoft.Insights/actionGroups`erőforrást. Ezután töltse ki az összes kapcsolódó tulajdonságot. A következő két minta sablon hozzon létre egy műveleti csoportot.

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
* További információ a [műveleti csoportokról](../../azure-monitor/platform/action-groups.md).
* További információ a [riasztásokról](alerts-overview.md).
* Megtudhatja, hogyan adhat hozzá [riasztásokat Resource Manager-sablonok használatával](../../azure-monitor/platform/alerts-activity-log.md).

