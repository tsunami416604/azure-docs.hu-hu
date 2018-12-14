---
title: Automatikusan a Resource Manager-sablonnal diagnosztikai beállítások engedélyezése
description: Megtudhatja, hogyan hozhat létre a diagnosztikai beállítások, amely lehetővé teszi, hogy az Event hubs szolgáltatásba a diagnosztikai naplók streamelése vagy a storage-fiókban tárolja őket a Resource Manager-sablon használatával.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 02029170046e3c70965e2f607aa6c00c7ca131e3
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388910"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Automatikusan az diagnosztikai beállítások engedélyezése Resource Manager-sablonnal erőforrás létrehozásakor
Ebben a cikkben bemutatjuk, hogyan használhatja egy [Azure Resource Manager-sablon](../../azure-resource-manager/resource-group-authoring-templates.md) diagnosztikai beállításainak konfigurálása az erőforrás létrehozásakor. Ez lehetővé teszi, hogy automatikusan elindul, a diagnosztikai naplók és mérőszámok az Event Hubs archiválási őket a Storage-fiókban, vagy elküldheti a Log Analytics szolgáltatásba, amikor egy erőforrást hoznak létre streamelési.

> [!WARNING]
> A tárfiókban lévő naplóadatok formátuma 2018. nov. 1-től JSON Lines lesz. [Ebben a cikkben olvashat ennek hatásairól, valamint arról, hogy hogyan frissítheti eszközeit az új formátum kezeléséhez.](./../../monitoring-and-diagnostics/monitor-diagnostic-logs-append-blobs.md) 
>
> 

A metódus a Resource Manager-sablonnal diagnosztikai naplóinak engedélyezéséről az erőforrás típusától függ.

* **Nem – számítási** erőforrások (például a hálózati biztonsági csoportok, a Logic Apps, Automation) [ebben a cikkben leírt diagnosztikai beállítások](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
* **COMPUTE** (WAD/LAD-alapú) erőforrások használják a [WAD/LAD konfigurációs fájl ebben a cikkben leírt](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Ebben a cikkben azt ismertetjük, hogyan a diagnosztika mindkét módszer használatával való konfigurálásához.

Az alapvető lépéseken az alábbiak szerint:

1. Hozzon létre egy sablont, amely ismerteti, hogyan lehet létrehozni az erőforrást, és a diagnosztika JSON-fájlként.
2. [Helyezze üzembe a sablont, minden olyan telepítési módszerrel](../../azure-resource-manager/resource-group-template-deploy.md).

Az alábbiakban egy példát a sablon JSON-fájlt, létre kell hoznia a nem számítási és a számítási erőforrásokat ad.

## <a name="non-compute-resource-template"></a>Nem – számítási erőforrásokat sablon
Nem számítási erőforrások szüksége lesz két műveletet kell végrehajtania:

1. Paraméterek hozzáadása a paraméterek blobot a tárfiók nevét, az event hub engedélyezési szabály azonosítója, illetve a Log Analytics-munkaterület Azonosítójára (archív diagnosztikai naplók egy tárfiókban, a folyamatos átviteli naplók az Event hubs szolgáltatásba, és/vagy naplók küldése a Log Analytics engedélyezése).
   
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
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
2. Az erőforrás, amelynek szeretné engedélyezni, diagnosztikai naplók erőforrások tömbjének típusú erőforrás hozzáadása `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
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

A Tulajdonságok blob a diagnosztikai beállítás a következő [ebben a cikkben ismertetett formátumban](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Hozzáadás a `metrics` tulajdonság lehetővé teszi, hogy ezek azonos kimeneteket, feltéve, hogy erőforrás-metrikák is küldhet [az erőforrás támogatja-e az Azure Monitor-metrikák](../../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Íme egy teljes példa, amely létrehoz egy logikai alkalmazást, és kapcsolja be a streamelés az Event Hubs és a storage-fiókban lévő tárolóhoz.

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
      "defaultValue": "http://azure.microsoft.com/status/feed/"
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
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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

## <a name="compute-resource-template"></a>Számítási erőforrás-sablon
Ahhoz, hogy a diagnosztikát a számítási erőforrás, például egy virtuális gép vagy a Service Fabric-fürtöt, meg kell:

1. Az Azure Diagnostics bővítmény hozzáadása a virtuális gép erőforrás-definícióban.
2. Adja meg a tárolási fiók és/vagy event hub paraméterként.
3. Adja hozzá a WADCfg XML-fájl tartalmát a XMLCfg tulajdonságot használja, minden XML-karaktereket escape-karaktersorozat megfelelően.

> [!WARNING]
> Ez a lépés utolsó kellhet megkapni. [Ebben a cikkben](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) példaként, amely a diagnosztikai konfigurációs séma felosztja a változókat, amelyek az escape-karakterrel, és a formátuma helytelen.
> 
> 

A teljes folyamatot, beleértve a mintákat, leírt [ebben a dokumentumban](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>További lépések
* [További információk az Azure Diagnostics-naplókról](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
* [Stream és az Event Hubs az Azure diagnosztikai naplók](../../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

