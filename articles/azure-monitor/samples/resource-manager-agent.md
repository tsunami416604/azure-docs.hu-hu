---
title: Resource Manager-sablonok – minták az ügynökökhöz
description: Példa Azure Resource Manager sablonokat a virtuálisgép-ügynökök üzembe helyezéséhez és konfigurálásához a Azure Monitorban.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 00d6635b7bb322d28f0fe3df509ce0cb03e19f3d
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95308664"
---
# <a name="resource-manager-template-samples-for-agents-in-azure-monitor"></a>Resource Manager-sablonok – minták a Azure Monitor lévő ügynökökhöz
Ez a cikk minta [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/template-syntax.md) tartalmaz a [log Analytics-ügynök](../platform/log-analytics-agent.md) és a [diagnosztikai bővítmény](../platform/diagnostics-extension-overview.md) üzembe helyezéséhez és konfigurálásához a Azure monitor-ben. Mindegyik minta tartalmaz egy sablonfájlt és egy, a sablonhoz adni kívánt minta értékeket tartalmazó paramétereket tartalmazó fájlt.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="azure-monitor-agent-preview"></a>Azure Monitor-ügynök (előzetes verzió)
Az ebben a szakaszban szereplő minták a Windows-és Linux-ügynökök Azure Monitor ügynökének (előzetes verzió). Ide tartozik az ügynök telepítése az Azure-beli virtuális gépeken, valamint az Azure arc-kompatibilis kiszolgálók is. 

### <a name="windows-azure-virtual-machine"></a>Windows Azure virtuális gép
A következő minta telepíti a Azure Monitor ügynököt egy Windows Azure-beli virtuális gépre.

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string"
      },
      "location": {
          "type": "string"
      }
  },
  "resources": [
      {
          "name": "[concat(parameters('vmName'),'/AzureMonitorWindowsAgent')]",
          "type": "Microsoft.Compute/virtualMachines/extensions",
          "location": "[parameters('location')]",
          "apiVersion": "2020-06-01",
          "properties": {
              "publisher": "Microsoft.Azure.Monitor",
              "type": "AzureMonitorWindowsAgent",
              "typeHandlerVersion": "1.0",
              "autoUpgradeMinorVersion": true
          }
      }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

### <a name="linux-azure-virtual-machine"></a>Linux Azure-beli virtuális gép
Az alábbi példa telepíti a Azure Monitor Agent ügynököt egy Linux Azure-beli virtuális gépre.

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string"
      },
      "location": {
          "type": "string"
      }
  },
  "resources": [
      {
          "name": "[concat(parameters('vmName'),'/AzureMonitorLinuxAgent')]",
          "type": "Microsoft.Compute/virtualMachines/extensions",
          "location": "[parameters('location')]",
          "apiVersion": "2020-06-01",
          "properties": {
              "publisher": "Microsoft.Azure.Monitor",
              "type": "AzureMonitorLinuxAgent",
              "typeHandlerVersion": "1.5",
              "autoUpgradeMinorVersion": true
          }
      }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-linux-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

### <a name="windows-azure-arc-enabled-server"></a>Windows Azure arc-kompatibilis kiszolgáló
A következő minta telepíti a Azure Monitor ügynököt egy Windows Azure arc-kompatibilis kiszolgálóra.

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string"
      },
      "location": {
          "type": "string"
      }
  },
  "resources": [
      {
          "name": "[concat(parameters('vmName'),'/AzureMonitorWindowsAgent')]",
          "type": "Microsoft.HybridCompute/machines/extensions",
          "location": "[parameters('location')]",
          "apiVersion": "2019-08-02-preview",
          "properties": {
              "publisher": "Microsoft.Azure.Monitor",
              "type": "AzureMonitorWindowsAgent",
              "autoUpgradeMinorVersion": true
          }
      }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

### <a name="linux-azure-arc-enabled-server"></a>Linux Azure arc-kompatibilis kiszolgáló
A következő minta telepíti a Azure Monitor ügynököt egy Linux Azure arc-kompatibilis kiszolgálóra.

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string"
      },
      "location": {
          "type": "string"
      }
  },
  "resources": [
      {
          "name": "[concat(parameters('vmName'),'/AzureMonitorLinuxAgent')]",
          "type": "Microsoft.HybridCompute/machines/extensions",
          "location": "[parameters('location')]",
          "apiVersion": "2019-08-02-preview",
          "properties": {
              "publisher": "Microsoft.Azure.Monitor",
              "type": "AzureMonitorLinuxAgent",
              "autoUpgradeMinorVersion": true
          }
      }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-linux-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="log-analytics-agent"></a>Log Analytics-ügynök
Az ebben a szakaszban szereplő minták telepítik a Log Analytics ügynököt az Azure-beli Windows-és Linux-alapú virtuális gépeken, és összekapcsolják azt egy Log Analytics munkaterülettel.

###  <a name="windows"></a>Windows
A következő minta telepíti a Log Analytics ügynököt egy Windows Azure-beli virtuális gépre. Ez a [Windows log Analytics virtuálisgép-bővítményének](../../virtual-machines/extensions/oms-windows.md)engedélyezésével végezhető el.

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location of the virtual machine"
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Id of the workspace."
      }
    },
    "workspaceKey": {
      "type": "string",
      "metadata": {
        "description": "Primary or secondary workspace key."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2018-10-01",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmName'), '/Microsoft.Insights.LogAnalyticsAgent')]",
            "apiVersion": "2015-06-15",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
      ]
    }
  ]
}

```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "westus"
      },
      "workspaceId": {
        "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "workspaceKey": {
        "value": "Tse-gj9CemT6A80urYa2hwtjvA5axv1xobXgKR17kbVdtacU6cEf+SNo2TdHGVKTsZHZd1W9QKRXfh+$fVY9dA=="
      }
  }
}
```


### <a name="linux"></a>Linux
Az alábbi példa telepíti a Log Analytics Agent ügynököt egy Linux Azure-beli virtuális gépre. Ez a [Windows log Analytics virtuálisgép-bővítményének](../../virtual-machines/extensions/oms-linux.md)engedélyezésével végezhető el.

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location of the virtual machine"
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Id of the workspace."
      }
    },
    "workspaceKey": {
      "type": "string",
      "metadata": {
        "description": "Primary or secondary workspace key."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2018-10-01",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmName'), '/Microsoft.Insights.LogAnalyticsAgent')]",
            "apiVersion": "2015-06-15",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "typeHandlerVersion": "1.7",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
      ]
    }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-linux-vm"
      },
      "location": {
        "value": "westus"
      },
      "workspaceId": {
        "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "workspaceKey": {
        "value": "Tse-gj9CemT6A80urYa2hwtjvA5axv1xobXgKR17kbVdtacU6cEf+SNo2TdHGVKTsZHZd1W9QKRXfh+$fVY9dA=="
      }
  }
}
```



## <a name="diagnostic-extension"></a>Diagnosztikai bővítmény
Az ebben a szakaszban szereplő minták telepítik a diagnosztikai bővítményt az Azure-beli Windows-és Linux-alapú virtuális gépeken, és az adatgyűjtéshez konfigurálja azokat.

### <a name="windows"></a>Windows
A következő minta engedélyezi és konfigurálja a diagnosztikai bővítményt egy Windows Azure-beli virtuális gépen. A konfiguráció részleteit lásd: a [Windows Diagnostics bővítmény sémája](../platform/diagnostics-extension-schema-windows.md).

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the virtual machine."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account."
      }
    },
    "storageAccountId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the storage account."
      }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the workspace."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2018-10-01",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "resources": [
            {
                "type": "Microsoft.Compute/virtualMachines/extensions",
                "name": "[concat(parameters('vmName'), '/Microsoft.Insights.VMDiagnosticsSettings')]",
                "apiVersion": "2015-06-15",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
                ],
                "properties": {
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "IaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                                "overallQuotaInMB": 10000,
                                "DiagnosticInfrastructureLogs": {
                                    "scheduledTransferLogLevelFilter": "Error"
                                },
                                "PerformanceCounters": {
                                    "scheduledTransferPeriod": "PT1M",
                                    "sinks": "AzureMonitorSink",
                                    "PerformanceCounterConfiguration": [
                                        {
                                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                                            "sampleRate": "PT1M",
                                            "unit": "percent"
                                        }
                                    ]
                                },
                                "WindowsEventLog": {
                                    "scheduledTransferPeriod": "PT5M",
                                    "DataSource": [
                                        {
                                            "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                                        },
                                        {
                                            "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                                        },
                                        {
                                            "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                                        }
                                    ]
                                }
                            },
                            "SinksConfig": {
                                "Sink": [
                                    {
                                        "name": "AzureMonitorSink",
                                        "AzureMonitor":
                                        {
                                            "ResourceId": "[parameters('workspaceResourceId')]"
                                        }
                                    }
                                ]
                            }
                        },
                        "storageAccount": "[parameters('storageAccountName')]"
                    },
                    "protectedSettings": {
                        "storageAccountName": "[parameters('storageAccountName')]",
                        "storageAccountKey": "[listkeys(parameters('storageAccountId'), '2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net"            }
                }
            }
        ]
    },
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[parameters('location')]",
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "westus"
      },
      "storageAccountName": {
        "value": "mystorageaccount"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Storage/storageAccounts/my-windows-vm"
      },
      "workspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "workspaceKey": {
        "value": "Npl#3y4SmqG4R30ukKo3oxfixZ5axv1xocXgKR17kgVdtacU4cEf+SNr2TdHGVKTsZHZv3R8QKRXfh+ToVR9dA-="
      }
  }
}
```

### <a name="linux"></a>Linux
A következő minta lehetővé teszi a diagnosztikai bővítmény használatát egy Linux Azure-beli virtuális gépen. A konfiguráció részleteit lásd: a [Windows Diagnostics bővítmény sémája](../../virtual-machines/extensions/diagnostics-linux.md).

#### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "vmId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the virtual machine."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the virtual machine."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account."
      }
    },
    "storageSasToken": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the storage account."
      }
    },
    "eventHubUrl": {
      "type": "string",
      "metadata": {
        "description": "URL of the event hub."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2018-10-01",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "resources": [
          {
              "type": "Microsoft.Compute/virtualMachines/extensions",
              "name": "[concat(parameters('vmName'), '/Microsoft.Insights.VMDiagnosticsSettings')]",
              "apiVersion": "2015-06-15",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Azure.Diagnostics",
                  "type": "LinuxDiagnostic",
                  "typeHandlerVersion": "3.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "StorageAccount": "[parameters('storageAccountName')]",
                    "ladCfg": {
                      "sampleRateInSeconds": 15,
                      "diagnosticMonitorConfiguration": 
                      {
                        "performanceCounters": 
                        {
                          "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
                          "performanceCounterConfiguration": [
                            {
                              "unit": "Percent",
                              "type": "builtin",
                              "counter": "PercentProcessorTime",
                              "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
                              "annotation": [
                                {
                                  "locale": "en-us",
                                  "displayName": "Aggregate CPU %utilization"
                                }
                              ],
                              "condition": "IsAggregate=TRUE",
                              "class": "Processor"
                            },
                            {
                              "unit": "Bytes",
                              "type": "builtin",
                              "counter": "UsedSpace",
                              "counterSpecifier": "/builtin/FileSystem/UsedSpace",
                              "annotation": [
                                {
                                  "locale": "en-us",
                                  "displayName": "Used disk space on /"
                                }
                              ],
                              "condition": "Name=\"/\"",
                              "class": "Filesystem"
                            }
                          ]
                        },
                        "metrics": {
                          "metricAggregation": [
                            {
                              "scheduledTransferPeriod": "PT1H"
                            },
                            {
                              "scheduledTransferPeriod": "PT1M"
                            }
                          ],
                          "resourceId": "[parameters('vmId')]"
                        },
                        "eventVolume": "Large",
                        "syslogEvents": {
                          "sinks": "MySyslogJsonBlob,MyLoggingEventHub",
                          "syslogEventConfiguration": {
                            "LOG_USER": "LOG_INFO"
                          }
                        }
                      }
                    },
                    "perfCfg": [
                      {
                        "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
                        "table": "LinuxCpu",
                        "frequency": 60,
                        "sinks": "MyLinuxCpuJsonBlob,MyLinuxCpuEventHub"
                      }
                    ],
                    "fileLogs": [
                      {
                        "file": "/var/log/myladtestlog",
                        "table": "MyLadTestLog",
                        "sinks": "MyFilelogJsonBlob,MyLoggingEventHub"
                      }
                    ]
                  },
                  "protectedSettings": {
                    "storageAccountName": "yourdiagstgacct",
                    "storageAccountSasToken": "[parameters('storageSasToken')]",
                    "sinksConfig": {
                      "sink": [
                        {
                          "name": "MySyslogJsonBlob",
                          "type": "JsonBlob"
                        },
                        {
                          "name": "MyFilelogJsonBlob",
                          "type": "JsonBlob"
                        },
                        {
                          "name": "MyLinuxCpuJsonBlob",
                          "type": "JsonBlob"
                        },
                        {
                          "name": "MyJsonMetricsBlob",
                          "type": "JsonBlob"
                        },
                        {
                          "name": "MyLinuxCpuEventHub",
                          "type": "EventHub",
                          "sasURL": "[parameters('eventHubUrl')]"
                        },
                        {
                          "name": "MyMetricEventHub",
                          "type": "EventHub",
                          "sasURL": "[parameters('eventHubUrl')]"
                        },
                        {
                          "name": "MyLoggingEventHub",
                          "type": "EventHub",
                          "sasURL": "[parameters('eventHubUrl')]"
                        }
                      ]
                    }
                  }
              }
          }
        ]
    }
  ]
}
```

#### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-linux-vm"
      },
      "vmId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-linux-vm"
      },
      "location": {
        "value": "westus"
      },
      "storageAccountName": {
        "value": "mystorageaccount"
      },
      "storageSasToken": {
        "value": "?sv=2019-10-10&ss=bfqt&srt=sco&sp=rwdlacupx&se=2020-04-26T23:06:44Z&st=2020-04-26T15:06:44Z&spr=https&sig=1QpoTvrrEW6VN2taweUq1BsaGkhDMnFGTfWakucZl4%3D"
      },
      "eventHubUrl": {
        "value": "https://my-eventhub-namespace.servicebus.windows.net/my-eventhub?sr=my-eventhub-namespace.servicebus.windows.net%2fmy-eventhub&sig=4VEGPTg8jxUAbTcyeF2kwOr8XZdfgTdMWEQWnVaMSqw=&skn=manage"
      }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* [További Azure monitor-sablonok beszerzése](resource-manager-samples.md).
* [További információ a log Analytics-ügynökről](../platform/log-analytics-agent.md).
* [További információ a diagnosztikai bővítményről](../platform/diagnostics-extension-overview.md).
