---
title: Automatikus engedélyezés a Resource Manager-sablon használatával diagnosztikai beállítások |} Microsoft Docs
description: Útmutató Resource Manager sablon használata, amely lehetővé teszi, hogy adatfolyamként küldje el az Event hubs diagnosztikai naplók, vagy olyan tárfiókban tárolja őket a diagnosztikai beállításokat szeretne létrehozni.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2018
ms.author: johnkem
ms.openlocfilehash: 80f427d5acb884be1752f470e2a9d9d04eee5518
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Automatikus engedélyezés a Resource Manager-sablon használatával erőforrás létrehozásakor diagnosztikai beállítások
Ebben a cikkben megmutatjuk, hogyan használható egy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-authoring-templates.md) létrehozásakor erőforrás diagnosztikai beállításainak konfigurálására. Ez lehetővé teszi, hogy automatikusan elindítja a diagnosztikai naplók és a mérni kívánt Event Hubs tárfiókokban archiválás őket, vagy Naplóelemzési elküldi őket egy erőforrás létrehozásakor.

A módszer a Resource Manager-sablon használatával diagnosztikai naplók engedélyezése az erőforrástípushoz függ.

* **Nem-számítási** erőforrások (például a hálózati biztonsági csoportok, a Logic Apps Automation) [cikkben leírt diagnosztikai beállítások](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **Számítási** (ÜVEGVATTA/LAD-alapú) erőforrások használják a [ÜVEGVATTA/LAD konfigurációs fájl ebben a cikkben leírt](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Ebben a cikkben azt konfigurálását ismertetik diagnosztika módszerek használatával.

Az alapvető lépések a következők:

1. Hozzon létre egy sablont, amely leírja a erőforrás létrehozása és engedélyezése a diagnosztika JSON-fájlként.
2. [A sablon bármely olyan telepítési módszerrel telepítéséhez](../azure-resource-manager/resource-group-template-deploy.md).

Az alábbiakban egy példa a sablon JSON-fájl generálása nem számítási és számítási erőforrásokat kell felállításához.

## <a name="non-compute-resource-template"></a>Nem-számítási erőforrás sablon
Nem számítási erőforrásokat akkor két műveletet kell végrehajtania:

1. Paraméterek hozzáadása a paraméterek blob a tárfiók neve, az event hub engedélyezési szabály azonosítója, illetve a OMS Naplóelemzési munkaterület-Azonosítót (amely lehetővé teszi egy tárfiókot, adatfolyamként való küldése az Event Hubs-naplókat a, és/vagy a napló naplókat küld a diagnosztikai naplók archiválása Elemzés).
   
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
2. Az erőforrás legyen diagnosztikai naplók engedélyezése erőforrások tömbben típusú erőforrás hozzáadása `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/[parameters('settingName')]",
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

A Tulajdonságok blob a diagnosztikai beállítások a következő [a jelen cikkben ismertetett formátum](https://docs.microsoft.com/rest/api/monitor/ServiceDiagnosticSettings/CreateOrUpdate). Hozzáadás a `metrics` tulajdonság lehetővé teszi, hogy ezek azonos kimenetek, feltéve, hogy erőforrás metrikáit is küldhet [az erőforrás támogatja az Azure-figyelő metrikák](monitoring-supported-metrics.md).

Ez egy teljes példa, amely hoz létre egy logikai alkalmazást, és bekapcsolja a streaming az Eseményközpontokhoz és a tárolási tárfiókokban.

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
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
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
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
          "name": "Microsoft.Insights/[parameters('settingName')]",
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

## <a name="compute-resource-template"></a>Számítási erőforrás sablon
Ahhoz, hogy a számítási erőforrás diagnosztikai, például egy virtuális gép vagy a Service Fabric-fürt, akkor kell:

1. Az Azure Diagnostics-bővítmény hozzáadása a virtuális gép erőforrás-definícióban.
2. Adja meg a tárolási fiók és/vagy az event hub paraméterként.
3. Adja hozzá a WADCfg XML-fájl tartalmát a XMLCfg tulajdonság, a megfelelő escape-karaktersorozat összes XML-karakter.

> [!WARNING]
> Ezen utolsó lépésében megszerezni a helyes megkapni. [Ebben a cikkben találhat](../virtual-machines/windows/extensions-diagnostics-template.md#diagnostics-configuration-variables) , amely felosztja a diagnosztika konfigurációs séma escape-karakterrel megjelölve, és megfelelő formátumú változók példát.
> 
> 

A teljes folyamat minták leírt [ebben a dokumentumban](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>További lépések
* [További tudnivalók az Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
* [Event hubs az Azure diagnosztikai naplók adatfolyam](monitoring-stream-diagnostic-logs-to-event-hubs.md)

