---
title: A művelet csoportok létrehozása a Resource Manager-sablonok
description: 'Útmutató: egy művelet csoport létrehozása az Azure Resource Manager-sablon használatával.'
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 9b49d21dad9bb1e48194cc31940c5cd53c909dc0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263099"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>A művelet-csoport létrehozása a Resource Manager-sablon
Ez a cikk bemutatja, hogyan használható egy [Azure Resource Manager sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) művelet csoportok konfigurálásához. Sablonok segítségével automatikusan állíthatja be, amelyek felhasználhatók a meghatározott típusú riasztások művelet csoportok. E művelet csoportok győződjön meg arról, hogy a helyes felek értesítést kap figyelmeztetést.

Az alapvető lépések a következők:

1. Hozzon létre egy sablont, amely ismerteti, hogyan lehet létrehozni a műveleti csoportot JSON-fájlként.

2. A sablon telepítéséhez használatával [bármely olyan telepítési módszerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Először azt ismerteti, hogyan egy művelet csoport, hol találhatók a művelet definíciók kódolva a sablon a Resource Manager-sablonok létrehozásához. Második azt ismertetik, amelyek bemeneti paraméterként webhook konfigurációs adatokat fogad a sablon telepítésekor sablon létrehozásához.

## <a name="resource-manager-templates-for-an-action-group"></a>Egy művelet csoport Resource Manager-sablonok

A Resource Manager sablonnal művelet csoport létrehozásához típusú erőforrás létrehozása `Microsoft.Insights/actionGroups`. Majd adja meg az összes kapcsolódó tulajdonságok. Az alábbiakban a két minta sablonok művelet-csoport létrehozása.

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
* További információ [művelet csoportok](monitoring-action-groups.md).
* További információ [riasztások](monitoring-overview-alerts.md).
* Ismerje meg, hogyan adhat [Resource Manager sablonnal riasztások](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
