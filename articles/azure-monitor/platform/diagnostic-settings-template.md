---
title: Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával | Microsoft Docs
description: Diagnosztikai beállítások létrehozása Resource Manager-sablonnal az Azure platform naplófájljainak továbbításához Azure Monitor naplók, Azure Storage vagy Azure Event Hubs számára.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: f65e3c4f9582fcc5c28412d44e513fa6bcb9e870
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "71262335"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával
Az Azure [platform-naplói](platform-logs-overview.md) részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azoktól függő Azure-platformról. Ez a cikk részletesen ismerteti, hogyan lehet [Azure Resource Manager sablont](../../azure-resource-manager/resource-group-authoring-templates.md) használni a diagnosztikai beállítások konfigurálásához, hogy különböző célhelyekre gyűjtsön platform-naplókat. Ez lehetővé teszi, hogy automatikusan elindítsa a platform-naplók gyűjtését egy erőforrás létrehozásakor.


## <a name="resource-manager-template"></a>Resource Manager-sablon
A diagnosztikai beállítások létrehozásához a Resource Manager-sablon két részét kell szerkesztenie. Ezeket a szakaszokat a következő szakaszokban ismertetjük.

### <a name="parameters"></a>Paraméterek
A diagnosztikai beállítás [célhelyei](diagnostic-settings.md#destinations) alapján adja hozzá a paramétereket a Storage-fiók neve, az Event hub engedélyezési szabály azonosítója és log Analytics munkaterület-azonosító paramétereit tartalmazó blobhoz.
   
```json
"settingName": {
  "type": "string",
  "metadata": {
    "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
  }
},
"storageAccountName": {
  "type": "string",
  "metadata": {
    "description": "Name of the Storage Account in which platform logs should be saved."
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
},
"workspaceId":{
  "type": "string",
  "metadata": {
    "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
  }
}
```

### <a name="resources"></a>Segédanyagok és eszközök
Az erőforrás tömbben, amelyhez a diagnosztikai beállítást létre szeretné hozni, adjon hozzá `[resource namespace]/providers/diagnosticSettings` típusú erőforrást. A Properties (Tulajdonságok) szakasz a [diagnosztikai beállítások – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)című részben ismertetett formátumot követi. Adja hozzá a `metrics` tulajdonságot az erőforrás-metrikák ugyanarra a célhelyre való összegyűjtéséhez, ha az [erőforrás támogatja a metrikákat](metrics-supported.md).
   
```json
"resources": [
  {
    "type": "providers/diagnosticSettings",
    "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
    "dependsOn": [
      "[/*resource Id for which resource logs will be enabled>*/]"
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
          "category": "/* log category name */",
          "enabled": true,
          "retentionPolicy": {
            "days": 0,
            "enabled": false
          }
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
  }
]
```



## <a name="example"></a>Példa
Az alábbi példa egy logikai alkalmazást hoz létre, és olyan diagnosztikai beállítást hoz létre, amely lehetővé teszi az erőforrás-naplók adatfolyamként való továbbítását egy Event hub-ba és egy Storage-fiókba.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which resource logs should be saved."
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
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
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
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```


## <a name="next-steps"></a>Következő lépések
* További információ az [Azure-beli platform-naplókról](platform-logs-overview.md).
* További információ a [diagnosztikai beállításokról](diagnostic-settings.md).