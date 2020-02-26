---
title: Log Analytics munkaterület létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat | Microsoft Docs
description: Log Analytics-munkaterületek létrehozásához és konfigurálásához Azure Resource Manager sablonokat is használhat.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 5fb33a592601a0511fa2523ee0cf8114bc66ca99
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591959"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Log Analytics munkaterület kezelése Azure Resource Manager sablonok használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure Resource Manager sablonokkal](../../azure-resource-manager/templates/template-syntax.md) log Analytics munkaterületeket hozhat létre és konfigurálhat Azure monitor. A sablonokkal végrehajtható feladatok például a következők:

* Munkaterület létrehozása, többek között az árképzési csomag és a kapacitás foglalásának beállítása
* Megoldás hozzáadása
* Mentett keresések létrehozása
* Számítógépcsoport létrehozása
* Telepített Windows-ügynökkel rendelkező számítógépek IIS-naplók gyűjtésének engedélyezéséhez
* A Linux és Windows-számítógépekről teljesítményszámlálók gyűjtése
* Syslog események gyűjtésére a Linux rendszerű számítógépek 
* Windows-Eseménynapló eseményeinek gyűjtése
* Egyéni naplók gyűjtése a Windows rendszerű számítógépről
* A log analytics-ügynök hozzáadása egy Azure virtuális gépen
* Konfigurálja a log analytics index adatokat gyűjteni, az Azure diagnostics használatával

Ez a cikk olyan sablon-mintákat tartalmaz, amelyek a sablonokkal végrehajtható egyes konfigurációkat szemléltetik.

## <a name="api-versions"></a>API-verziók

A következő táblázat felsorolja az ebben a példában használt erőforrások API-verzióját.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület   | munkaterületek    | 2017-03-15 – előzetes verzió |
| Keresés      | savedSearches | 2015-03-20 |
| Adatforrás | adatforrások   | 2015-11-01 – előzetes verzió |
| Megoldás    | megoldások     | 2015-11-01 – előzetes verzió |

## <a name="create-a-log-analytics-workspace"></a>Log Analytics munkaterület létrehozása

A következő példa egy munkaterületet hoz létre egy sablon használatával a helyi gépről. A JSON-sablon úgy van konfigurálva, hogy csak az új munkaterület nevét és helyét írja elő. Más munkaterület-paraméterekhez megadott értékeket használ, például a [hozzáférés-vezérlési módot](design-logs-deployment.md#access-control-mode), az árképzési szintet, a megőrzést és a kapacitás foglalási szintjét.

A kapacitás foglalása esetén a kiválasztott kapacitás foglalását az adatfeldolgozáshoz adja meg az SKU `CapacityReservation` és egy GB értékkel a `capacityReservationLevel`tulajdonsághoz. Az alábbi lista a konfiguráláskor a támogatott értékeket és viselkedést ismerteti.

- A foglalási korlát beállítása után 31 napon belül nem válthat másik SKU-ra.

- A foglalási érték beállítása után 31 napon belül megnövelheti azt.

- A `capacityReservationLevel` értéke csak 100-es többszörös értékben állítható be a 50000-es maximális értékkel.

- Ha megnövelte a foglalási szintet, az időzítő alaphelyzetbe áll, és a frissítéstől számított további 31 napig nem módosítható.  

- Ha módosítja a munkaterület bármely más tulajdonságát, de a foglalási korlátot azonos szintre tartja, az időzítő nem áll alaphelyzetbe. 

### <a name="create-and-deploy-template"></a>Hozzon létre, és a sablon üzembe helyezése

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
      "pricingTier": {
        "type": "string",
        "allowedValues": [
          "pergb2018",
          "Free",
          "Standalone",
          "PerNode",
          "Standard",
          "Premium"
          ],
        "defaultValue": "pergb2018",
        "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
        }
      },
      "location": {
        "type": "String",
        "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2" 
        ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
        }
      }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
          "name": "[parameters('pricingTier')]"
                },
                "retentionInDays": 120,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
          }
       ]
    }
    ```

> [Information] a kapacitás-foglalási beállításokhoz használja ezeket a tulajdonságokat a "SKU" alatt:

>   "Name": "CapacityReservation",

>   "capacityReservationLevel": 100


2. Szerkessze a sablont az igényeknek. Tekintse át a [Microsoft. OperationalInsights/munkaterületek sablonjának](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) hivatkozását, hogy megtudja, milyen tulajdonságokat és értékeket támogat a rendszer. 
3. Mentse ezt a fájlt **deploylaworkspacetemplate. JSON** néven egy helyi mappába.
4. Készen áll a sablon üzembe helyezésére. A munkaterület létrehozásához használja a PowerShellt vagy a parancssort, és adja meg a munkaterület nevét és helyét a parancs részeként. A munkaterület nevének globálisan egyedinek kell lennie az összes Azure-előfizetésen belül.

   * A PowerShell használatához használja az alábbi parancsokat a sablont tartalmazó mappából:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * A parancssorban használja a következő parancsokat a sablont tartalmazó mappából:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló:<br><br> ![Ha üzembe helyezés kész eredményének](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Log Analytics munkaterület konfigurálása

A következő sablon szemlélteti a következőket:

1. Megoldások hozzáadása a munkaterülethez
2. Mentett keresések létrehozása
3. Számítógépcsoport létrehozása
4. Telepített Windows-ügynökkel rendelkező számítógépek IIS-naplók gyűjtésének engedélyezéséhez
5. Logikai lemez teljesítményszámlálók gyűjtése Linux rendszerű számítógépek (% Inode-OK; Szabad hely MB-ban; Foglalt hely; % Lemez átvitel/mp-ben; Lemezolvasások/mp; Lemezírások/mp)
6. Syslog-események gyűjtésére a Linux rendszerű számítógépek
7. Az alkalmazások eseménynaplójában a Windows-számítógépek hiba és figyelmeztetés eseményeinek gyűjtése
8. Windows-számítógépekről memória rendelkezésre álló memória (MB) teljesítményszámláló gyűjtése.
9. Az Azure Diagnostics által a Storage-fiókba írt IIS-naplók és Windows-eseménynaplók összegyűjtése
10. Egyéni naplók gyűjtése a Windows rendszerű számítógépről

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "pricingTier": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: pergb2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "defaultValue": "[bool('false')]",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    },
    "customLogName": {
    "type": "string",
    "metadata": {
      "description": "The custom log name"
      }
     }
    },
    "variables": {
      "Updates": {
        "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "Updates"
      },
      "AntiMalware": {
        "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "AntiMalware"
      },
      "SQLAssessment": {
        "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "SQLAssessment"
      },
      "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "retentionInDays": "[parameters('dataRetention')]",
        "features": {
          "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
        },
        "sku": {
          "name": "[parameters('pricingTier')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), parameters('customLogName'))]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "[parameters('customLogName')]",
            "description": "this is a description",
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": [
                      {
                        "matchIndex": 0,
                        "numberdGroup": null,
                        "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                      }
                    ]
                  }
                },
                "extractionType": "DateTime"
              }
            ],
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "linuxFileTypeLogPaths": null,
                    "windowsFileTypeLogPaths": [
                      "[concat('c:\\Windows\\Logs\\',parameters('customLogName'))]"
                    ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "matchIndex": 0,
                    "numberdGroup": null,
                    "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                  }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {  
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}
```

### <a name="deploying-the-sample-template"></a>A minta sablon üzembe helyezése

A minta sablon üzembe helyezése:

1. Mentse a csatolt mintát egy fájlba, például `azuredeploy.json` 
2. Szerkessze a sablont a kívánt konfigurációval
3. A sablon üzembe helyezéséhez használja a PowerShellt vagy a parancssort

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Parancssor

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Példa Resource Manager-sablonokra

Az Azure rövid útmutató sablonjának katalógusa számos Log Analytics sablont tartalmaz, beleértve a következőket:

* [Windows rendszerű virtuális gép üzembe helyezése a Log Analytics virtuálisgép-bővítménnyel](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Linux rendszerű virtuális gép üzembe helyezése a Log Analytics VM bővítménnyel](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Azure Site Recovery figyelése meglévő Log Analytics munkaterület használatával](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Azure-Web Apps figyelése meglévő Log Analytics munkaterület használatával](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Meglévő Storage-fiók hozzáadása a Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Következő lépések

* [Windows-ügynök üzembe helyezése az Azure-beli virtuális gépeken Resource Manager-sablon használatával](../../virtual-machines/extensions/oms-windows.md).

* [Linux-ügynök üzembe helyezése az Azure-beli virtuális gépeken Resource Manager-sablon használatával](../../virtual-machines/extensions/oms-linux.md).
