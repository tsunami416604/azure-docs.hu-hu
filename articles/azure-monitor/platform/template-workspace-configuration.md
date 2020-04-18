---
title: Azure Resource Manager-sablon a Log Analytics-munkaterülethez
description: Az Azure Resource Manager-sablonok segítségével hozhat létre és konfigurálhat Log Analytics-munkaterületeket.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 81e46f53c0afc69c927918daa0488c4835d60805
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605019"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>A Log Analytics-munkaterület kezelése az Azure Resource Manager-sablonokkal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Az Azure Resource Manager-sablonok](../../azure-resource-manager/templates/template-syntax.md) segítségével hozhat létre és konfigurálhat Log Analytics-munkaterületeket az Azure Monitorban. Példák a sablonokkal végrehajtható feladatokra:

* Munkaterület létrehozása a tarifacsomag és a kapacitásfoglalás beállításával
* Megoldás hozzáadása
* Mentett keresések létrehozása
* Számítógépcsoport létrehozása
* IIS-naplók gyűjtésének engedélyezése olyan számítógépekről, amelyeken telepítve van a Windows-ügynök
* Teljesítményszámlálók gyűjtése Linux- és Windows-számítógépekről
* Események gyűjtése a syslogból Linux rendszerű számítógépeken 
* Események gyűjtése a Windows eseménynaplóiból
* Egyéni naplók gyűjtése a Windows rendszerből
* A logelemző ügynök hozzáadása egy Azure virtuális géphez
* Naplóelemzés konfigurálása az Azure-diagnosztika használatával gyűjtött adatok indexelésére

Ez a cikk sablonmintákat tartalmaz, amelyek bemutatják a sablonokkal végrehajtható konfigurációk némelyikét.

## <a name="api-versions"></a>API-verziók

Az alábbi táblázat a példában használt erőforrások API-verzióját sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| Munkaterület   | munkaterületek    | 2017-03-15-előzetes |
| Keresés      | mentett Keresések | 2015-03-20 |
| Adatforrás | Datasources   | 2015-11-01-előzetes |
| Megoldás    | megoldások     | 2015-11-01-előzetes |

## <a name="create-a-log-analytics-workspace"></a>A Log Analytics-munkaterület létrehozása

A következő példa létrehoz egy munkaterületet a helyi számítógépsablon használatával. A JSON-sablon úgy van beállítva, hogy csak az új munkaterület nevét és helyét követelje meg. Más munkaterületi paraméterekhez megadott értékeket használ, például [a hozzáférés-vezérlési módot,](design-logs-deployment.md#access-control-mode)a tarifacsomagot, a megőrzési és a kapacitásfoglalási szintet.

> [!WARNING]
> A következő sablon létrehoz egy Log Analytics-munkaterületet, és konfigurálja az adatgyűjtést. Ez megváltoztathatja a számlázási beállításokat. A Log Analytics-munkaterületen gyűjtött adatok számlázásának megismerése az [Azure-környezetben](manage-cost-storage.md) való alkalmazás előtt tekintse át a Használat és a költségek kezelése című részt.

A kapacitásfoglaláshoz az adatok betöltéséhez kijelölt kapacitásfoglalást határoz `CapacityReservation` meg a termékváltozat `capacityReservationLevel`és a tulajdonság GB-ban megadott értékének megadásával. Az alábbi lista részletezi a támogatott értékeket és a viselkedést a konfigurálásakor.

- Miután beállította a foglalási korlátot, 31 napon belül nem válthat másik termékváltozatra.

- Miután beállította a foglalási értéket, csak 31 napon belül növelheti azt.

- Az értéket csak 100 többszörösére `capacityReservationLevel` állíthatja be, legfeljebb 50000 értékkel.

- Ha növeli a foglalási szintet, az időzítő alaphelyzetbe áll, és a frissítéstől további 31 napig nem módosíthatja azt.  

- Ha módosítja a munkaterület bármely más tulajdonságát, de a foglalási korlátot ugyanarra a szintre tartja, az időzítő nem áll alaphelyzetbe. 

### <a name="create-and-deploy-template"></a>Sablon létrehozása és üzembe helyezése

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
      "sku": {
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
                    "name": "[parameters('sku')]"
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

   >[!NOTE]
   >A kapacitásfoglalási beállításokhoz használja ezeket a tulajdonságokat a "termékváltozat" alatt:
   >* "név": "CapacityReservation",
   >* "capacityReservationLevel": 100

2. A sablont az igényeinek megfelelően szerkesztheti. Fontolja meg egy [Erőforrás-kezelő paraméterfájl létrehozását](../../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy a paramétereket szövegközi értékként adná át. Tekintse át a [Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) sablonhivatkozást, és ismerje meg, hogy mely tulajdonságok és értékek támogatottak. 

3. Mentse a fájlt **deploylaworkspacetemplate.json fájlként** egy helyi mappába.

4. Készen áll a sablon üzembe helyezésére. A munkaterület létrehozásához vagy a parancssorból hozhatja létre a munkaterületet, és megadhatja a munkaterület nevét és helyét a parancs részeként. A munkaterület nevének globálisan egyedinek kell lennie az összes Azure-előfizetésben.

   * A PowerShell számára használja a következő parancsokat a sablont tartalmazó mappából:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * A parancssorhoz használja a sablont tartalmazó mappából származó alábbi parancsokat:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:<br><br> ![Példa eredmény, ha a telepítés befejeződött](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Log Analytics-munkaterület konfigurálása

A következő sablonminta bemutatja, hogyan kell:

1. Megoldások hozzáadása a munkaterülethez
2. Mentett keresések létrehozása. Annak érdekében, hogy a központi telepítések ne bírálják felül véletlenül a mentett kereséseket, egy eTag tulajdonságot kell hozzáadni a "savedSearches" erőforráshoz a mentett keresések idempotenciájának felülbírálásához és fenntartásához.
3. Számítógépcsoport létrehozása
4. IIS-naplók gyűjtésének engedélyezése olyan számítógépekről, amelyeken telepítve van a Windows-ügynök
5. Logikai lemezperf számlálók gyűjtése Linux rendszerű számítógépekről (% használt inodák; Ingyenes Megabájt; % Használt terület; Lemezátvitel/mp; Lemezolvasás/mp; Lemezírás/mp)
6. Syslog-események gyűjtése Linux-számítógépekről
7. Hiba- és figyelmeztetési események gyűjtése az alkalmazás eseménynaplójából Windows rendszerű számítógépekről
8. A rendelkezésre álló memória mbyte-teljesítményszámlálójának összegyűjtése Windows rendszerű számítógépekről
9. Az Azure-diagnosztika által egy tárfiókba írt IIS-naplók és Windows-eseménynaplók gyűjtése
10. Egyéni naplók gyűjtése a Windows rendszerből

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
    "sku": {
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
          "name": "[parameters('sku')]"
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
            "category": "VMSS",
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
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
    "sku": {
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

### <a name="deploying-the-sample-template"></a>A mintasablon telepítése

A mintasablon telepítése:

1. A csatolt minta mentése fájlba, például`azuredeploy.json` 
2. A sablon szerkesztése a kívánt konfigurációval
3. A sablon központi telepítése a PowerShell vagy a parancssor használatával

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Parancssor

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Példa Erőforrás-kezelő sablonokra

Az Azure gyorsútmutató sablongyűjteménye számos sablont tartalmaz a Log Analytics szolgáltatáshoz, többek között a következőket:

* [Windows rendszert futtató virtuális gép telepítése a Log Analytics virtuálisgép-bővítményével](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Linuxot futtató virtuális gép üzembe helyezése a Log Analytics virtuálisgép-bővítménylel](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Az Azure Site Recovery figyelése meglévő Log Analytics-munkaterület használatával](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Az Azure-webalkalmazások figyelése meglévő Log Analytics-munkaterület használatával](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Meglévő tárfiók hozzáadása a Log Analytics szolgáltatáshoz](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>További lépések

* [Telepítse a Windows-ügynököt az Azure virtuális gépekre az Erőforrás-kezelő sablon használatával.](../../virtual-machines/extensions/oms-windows.md)

* [Linux-ügynök telepítése az Azure virtuális gépeken a Resource Manager sablon használatával.](../../virtual-machines/extensions/oms-linux.md)
