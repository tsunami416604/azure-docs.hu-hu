---
title: Az Azure automatikus méretezés használata a Linux rendszerű méretezési csoport sablonjában Vendég mérőszámok |} A Microsoft Docs
description: Ismerje meg, az automatikus skálázáshoz Vendég mérőszámok segítségével a Linux Virtual Machine Scale Set-sablonokban
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: manayar
ms.openlocfilehash: e30fdb684fbabbdcea334115e3f645e63dec6623
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322636"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Az automatikus méretezés egy Linux rendszerű méretezési csoport sablonjában Vendég mérőszámok segítségével

Metrikák az Azure-ban, amely gyűjti az adatokat a virtuális gépek és a méretezési csoportokat két típusa van: néhány virtuális gazdagép származnak, illetve mások származik a Vendég virtuális Gépen. Magas szinten Ha standard Processzor, lemez és a hálózati metrikák, használja majd a gazda mérőszámok valószínűleg jó megoldás. Ha azonban egy nagyobb kijelölt mérőszámok van szüksége, majd Vendég mérőszámok valószínűleg jobb megfelel. Vessünk egy pillantást a kettő közötti különbségeket:

Gazdagép metrikáinak egyszerűbb és megbízhatóbb. Nincs szükségük további telepítési, mert a gazdagép virtuális gép által gyűjtött, mivel a Vendég mérőszámok megkövetelése, hogy telepítse a [Windows Azure Diagnostics bővítmény](../virtual-machines/windows/extensions-diagnostics-template.md) vagy a [Linux Azure diagnosztikai bővítmény](../virtual-machines/linux/diagnostic-extension.md)a Vendég virtuális Gépen. Vendég mérőszámok használata helyett a gazda mérőszámok egyik gyakori oka az, hogy a Vendég mérőszámok biztosítja, mint a gazda mérőszámok mérőszámok nagyobb kijelölés. Ilyen például a memória-felhasználási mérőszámok, amely csak vendég mérőszámok keresztül érhető el. A támogatott gazdagépmetrikák felsorolt [Itt](../monitoring-and-diagnostics/monitoring-supported-metrics.md), és a gyakran használt Vendég mérőszámok felsorolt [Itt](../azure-monitor/platform/autoscale-common-metrics.md). Ez a cikk bemutatja, hogyan lehet módosítani a [minimális működőképes méretezési csoport sablon](./virtual-machine-scale-sets-mvss-start.md) használata Linux rendszerű méretezési csoportokhoz tartozó Vendég mérőszámok alapján automatikus skálázási szabályokat.

## <a name="change-the-template-definition"></a>A Sablondefiníció módosítása

A minimális működőképes méretezési csoport sablonjának látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), és a sablon üzembe helyezése a Linux rendszerű méretezési csoport beállítása a vendégalapú automatikus skálázással látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Most vizsgálja meg a sablon létrehozásához használt diff (`git diff minimum-viable-scale-set existing-vnet`) darab által darab:

Először adja hozzá a paraméterek `storageAccountName` és `storageAccountSasToken`. Diagnostics-ügynök tárolja a metrikaadatok egy [tábla](../cosmos-db/table-storage-how-to-use-dotnet.md) a tárfiók. A Linux diagnosztikai ügynök 3.0-s verziójával kezdődően a tárelérési kulcs használata már nem támogatott. Használjon inkább egy [SAS-Token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ezután módosítsa a méretezési `extensionProfile` a diagnosztikai bővítményt tartalmazza. Ebben a konfigurációban adja meg az erőforrás-Azonosítóját a méretezési csoport gyűjtése mérőszámainak, valamint a tárfiókot és SAS-jogkivonat használatával a mérőszámokat tárol. Adja meg, milyen gyakran a mérőszámok összesítése (ebben az esetben az percenként), és mely metrikákat (az az eset, a készültségi használt memória) nyomon követésére. További információkat ebben a konfigurációban, és a használt memória százalékos eltérő metrikák, lásd: [ebben a dokumentációban](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Végül adja hozzá egy `autoscaleSettings` erőforrás automatikus konfigurálása a metrikák alapján. Az erőforrás rendelkezik egy `dependsOn` záradékban hivatkozott a méretezési csoport, győződjön meg arról, hogy a méretezési létezik, mielőtt megpróbálná automatikus beállítása. Ha úgy dönt, az automatikus skálázást egy másik metrikát, akkor kell használnia a `counterSpecifier` , mint a diagnosztikai bővítmény konfigurációjának a `metricName` a az automatikus skálázási konfigurációját. Az automatikus skálázási konfigurációjának további információkért lásd: a [automatikus méretezés ajánlott eljárásairól](..//azure-monitor/platform/autoscale-best-practices.md) és a [Azure Monitor REST API forrásdokumentáció](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
