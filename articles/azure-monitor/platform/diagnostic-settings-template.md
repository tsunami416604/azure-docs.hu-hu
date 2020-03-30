---
title: Diagnosztikai beállítás létrehozása az Azure-ban az Erőforrás-kezelő sablon használatával
description: Diagnosztikai beállításokat hozhat létre egy Resource Manager-sablon használatával az Azure platformnaplók Azure Monitor-naplókba, az Azure storage-ba vagy az Azure Event Hubs-ba történő továbbításához.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672429"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Diagnosztikai beállítás létrehozása az Azure-ban erőforrás-kezelő sablon használatával
[Az](diagnostic-settings.md) Azure Monitor diagnosztikai beállításai határozzák meg, hogy hová küldje az Azure-erőforrások és az Azure platform által gyűjtött [platformnaplókat.](platform-logs-overview.md) Ez a cikk részleteket és példákat tartalmaz egy [Azure Resource Manager-sablon](../../azure-resource-manager/templates/template-syntax.md) használatával diagnosztikai beállítások létrehozásához és konfigurálásához a platformnaplók különböző célhelyekre történő gyűjtéséhez.

> [!NOTE]
> Mivel nem hozhat [létre diagnosztikai beállítást](diagnostic-settings.md) az Azure-tevékenységnaplóhoz a PowerShell vagy a CLI használatával, például más Azure-erőforrások diagnosztikai beállításaival, hozzon létre egy Erőforrás-kezelő sablont a tevékenységnaplóhoz a jelen cikkben szereplő információk használatával, és telepítse a sablont a PowerShell vagy a CLI használatával.

## <a name="deployment-methods"></a>Telepítési módszerek
Az Erőforrás-kezelő sablonok at bármilyen érvényes módszerrel telepítheti, beleértve a PowerShellt és a CLI-t. A tevékenységnapló diagnosztikai beállításait a `az deployment create` CLI `New-AzDeployment` vagy a PowerShell előfizetésére kell telepíteni. Az erőforrásnaplók diagnosztikai beállításait a CLI vagy `az group deployment create` `New-AzResourceGroupDeployment` a PowerShell használatával erőforráscsoportra kell telepíteni.

A részleteket az [Erőforrások üzembe helyezése Erőforrás-kezelő sablonokkal és az Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) használatával, valamint [az Erőforrások erőforrás-kezelői sablonokkal és az Azure CLI-vel való üzembe helyezés.](../../azure-resource-manager/templates/deploy-cli.md) 





## <a name="resource-logs"></a>Erőforrásnaplók
Erőforrásnaplók esetén adjon hozzá egy `<resource namespace>/providers/diagnosticSettings` típusú erőforrást a sablonhoz. A tulajdonságok szakasz a [Diagnosztikai beállítások – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)című szakaszban ismertetett formátumot követi. Adja `category` meg `logs` a szakaszban az egyes kategóriák érvényes erőforrás, amely a gyűjteni kívánt. Adja `metrics` hozzá a tulajdonságot az erőforrás-metrikák ugyanazon célokhoz való gyűjtéséhez, ha az erőforrás támogatja a [mutatókat.](metrics-supported.md)

A következőkben egy sablon, amely egy adott erőforrás erőforrásnapló-kategóriáját gyűjti egy Log Analytics-munkaterületre, tárfiókba és eseményközpontba.

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
A következő példa egy diagnosztikai beállítást hoz létre egy automatikus skálázási beállításhoz, amely lehetővé teszi az erőforrásnaplók streamelését egy eseményközpontba, egy tárfiókba és egy Log Analytics-munkaterületre.

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
Az Azure-tevékenységnaplóhoz adjon hozzá `Microsoft.Insights/diagnosticSettings`egy típusú erőforrást. A rendelkezésre álló kategóriák a [Tevékenységnapló Kategóriák listájában](activity-log-view.md#categories-in-the-activity-log)találhatók. A következőkben egy sablon, amely összegyűjti az összes tevékenységnapló-kategóriák egy Log Analytics-munkaterület, tárfiók és az eseményközpont.


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
* További információ [az Azure platformnaplóiról.](platform-logs-overview.md)
* További információ a [diagnosztikai beállításokról](diagnostic-settings.md).
