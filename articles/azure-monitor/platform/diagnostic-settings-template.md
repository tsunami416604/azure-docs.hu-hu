---
title: Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával
description: Diagnosztikai beállítások létrehozása Resource Manager-sablonnal az Azure platform naplófájljainak továbbításához Azure Monitor naplók, Azure Storage vagy Azure Event Hubs számára.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77672429"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával
A Azure Monitor [diagnosztikai beállításai](diagnostic-settings.md) határozzák meg, hogy hová kell elküldeni az Azure-erőforrások által gyűjtött [platform-naplókat](platform-logs-overview.md) , valamint az Azure-platformtól függenek. Ez a cikk azokat a részleteket és példákat ismerteti, amelyekkel [Azure Resource Manager sablon](../../azure-resource-manager/templates/template-syntax.md) használatával hozhat létre és konfigurálhat diagnosztikai beállításokat a különböző célhelyekre gyűjtött platform-naplók összegyűjtéséhez.

> [!NOTE]
> Mivel nem lehet [diagnosztikai beállítást létrehozni](diagnostic-settings.md) az Azure-beli tevékenység naplóhoz a PowerShell vagy a parancssori felület (például más Azure-erőforrások diagnosztikai beállításai) használatával, hozzon létre egy Resource Manager-sablont a műveletnapló számára a jelen cikkben található információk alapján, és telepítse a sablont a PowerShell vagy a parancssori felület használatával.

## <a name="deployment-methods"></a>Üzembe helyezési módszerek
A Resource Manager-sablonokat bármely érvényes módszerrel üzembe helyezheti, beleértve a PowerShellt és a parancssori felületet is. A tevékenység naplójának diagnosztikai beállításait a parancssori felület vagy `az deployment create` `New-AzDeployment` a PowerShell használatával kell telepíteni egy előfizetésre. Az erőforrás-naplók diagnosztikai beállításait a parancssori felület vagy `az group deployment create` `New-AzResourceGroupDeployment` a PowerShell használatával kell telepíteni egy erőforráscsoporthoz.

További részletekért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) és [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md) . 





## <a name="resource-logs"></a>Erőforrásnaplók
Erőforrás-naplók esetén adjon hozzá egy típusú `<resource namespace>/providers/diagnosticSettings` erőforrást a sablonhoz. A Properties (Tulajdonságok) szakasz a [diagnosztikai beállítások – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)című részben ismertetett formátumot követi. `category` Adja meg a ( `logs` z) szakaszt a gyűjteni kívánt erőforráshoz tartozó egyes kategóriákhoz. Adja hozzá `metrics` a tulajdonságot az erőforrás-metrikák ugyanarra a célhelyre való összegyűjtéséhez, ha az [erőforrás támogatja a metrikákat](metrics-supported.md).

A következő egy olyan sablon, amely egy adott erőforráshoz tartozó erőforrás-napló kategóriát gyűjt egy Log Analytics munkaterületre, a Storage-fiókra és az Event hub-ra.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Példa
Az alábbi példa egy olyan diagnosztikai beállítást hoz létre, amely lehetővé teszi az erőforrás-naplók adatfolyamként való továbbítását egy Event hub, egy Storage-fiók és egy Log Analytics munkaterület számára.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Tevékenységnapló
Az Azure-tevékenység naplójában adjon hozzá egy típusú `Microsoft.Insights/diagnosticSettings`erőforrást. A rendelkezésre álló kategóriák a [tevékenység naplójának kategóriák részében](activity-log-view.md#categories-in-the-activity-log)vannak felsorolva. A következő egy olyan sablon, amely az összes műveletnapló-kategóriát összegyűjti egy Log Analytics munkaterületre, egy Storage-fiókra és az Event hub-ra.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>További lépések
* További információ az [Azure-beli platform-naplókról](platform-logs-overview.md).
* További információ a [diagnosztikai beállításokról](diagnostic-settings.md).
