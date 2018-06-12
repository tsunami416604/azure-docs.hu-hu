---
title: Tevékenység napló riasztás létrehozása egy Resource Manager sablonnal
description: Értesítés az Azure-erőforrások létrehozásakor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: ancav
ms.component: alerts
ms.openlocfilehash: a1e28f08231ae1fbef3e0d0306e986c1dc9d1d1c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262990"
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Tevékenység napló riasztás létrehozása egy Resource Manager sablonnal
Ez a cikk bemutatja, hogyan használható egy [Azure Resource Manager sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) tevékenység napló riasztások konfigurálása. Sablonok segítségével egyszerűen állíthat be sok riasztást, amely aktiválja az automatikus telepítési folyamat részeként az adott tevékenységre napló esemény feltételek alapján.

Az alapvető lépések a következők:

1. Hozzon létre egy sablont, amely ismerteti, hogyan lehet létrehozni a napló figyelmeztetés a JSON-fájlként.

2. A sablon telepítéséhez használatával [bármely olyan telepítési módszerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Tevékenység napló riasztás Resource Manager-sablon
Tevékenység napló riasztás létrehozása a Resource Manager sablonnal, hozzon létre egy erőforrást a típusú `microsoft.insights/activityLogAlerts`. Majd adja meg az összes kapcsolódó tulajdonságok. Ez a sablont, amely tevékenység napló riasztást hoz létre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Látogasson el a [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) néhány példa a naplófájl értesítési sablonok számára.

> [!NOTE]

> Is létrehozhat tevékenység napló riasztási szabályok használata a felhasználói élmény figyelése > [riasztások (előzetes verzió)](monitoring-overview-unified-alerts.md). Ezek létrehozásával kapcsolatos további információkért lásd: [Ez a cikk](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>További lépések
- További információ [riasztások](monitoring-overview-alerts.md).
- Ismerje meg, hogyan adhat [művelet csoportok Resource Manager sablonnal](monitoring-create-action-group-with-resource-manager-template.md).
- Megtudhatja, hogyan [az előfizetés minden automatikus skálázási motor műveletek figyelése tevékenység napló értesítés létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Megtudhatja, hogyan [az előfizetés összes sikertelen automatikus skálázás méretezési-a/kibővített művelete figyelése tevékenység napló értesítés létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
