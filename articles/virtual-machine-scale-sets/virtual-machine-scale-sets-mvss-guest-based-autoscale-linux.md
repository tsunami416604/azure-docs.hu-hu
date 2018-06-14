---
title: Az Azure automatikus skálázási használata Linux méretezési készlet sablonban Vendég metrikák |} Microsoft Docs
description: Megtudhatja, hogyan automatikus skálázás Vendég mérőszámok használatát a Linux virtuális gépek méretezési csoportján sablon
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781428"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Sablon Vendég mérőszámok használatát a Linux méretezési automatikus skálázás beállítása

Az Azure-ban mérőszámok, amelyek a virtuális gépek használata során, a méretezés beállítása két típusa van: néhány származik a virtuális gazdagép, illetve a mások származik a Vendég virtuális Gépen. Magas szinten Ha szabványos CPU, a lemez és a hálózati metrikáinak használ majd gazdagép metrikák valószínűleg remekül beválik. Ha azonban a metrikák nagyobb kijelölt kell, majd Vendég metrikák valószínűleg jobb méretezése. Vessen egy pillantást a kettő közötti különbségek:

Gazdagép-metrikák egyszerűbb és megbízhatóbb. Nem igényel további beállítást azok gyűjti össze az állomást a virtuális Gépet, mert mivel Vendég metrikák megkövetelik, hogy telepítse a [Windows Azure diagnosztikai bővítmény](../virtual-machines/windows/extensions-diagnostics-template.md) vagy a [Linux Azure Diagnostics bővítmény](../virtual-machines/linux/diagnostic-extension.md)a Vendég virtuális Gépen. Egy gazdagép-metrikák helyett Vendég metrikák használandó szükség, hogy a Vendég metrikák metrikák gazdagép metrikák-nál nagyobb kijelölt biztosítson. Ilyen például a memória-felhasználás metrikákat, amelyek csak vendég metrikák keresztül elérhető. A támogatott gazdagép-metrikák felsorolt [Itt](../monitoring-and-diagnostics/monitoring-supported-metrics.md), és a gyakran használt Vendég metrikák felsorolt [Itt](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Ez a cikk bemutatja, hogyan lehet módosítani a [minimális életképes méretezési sablon](./virtual-machine-scale-sets-mvss-start.md) Vendég metrikáinak Linux méretezési csoportok alapján automatikus skálázási szabályok használata.

## <a name="change-the-template-definition"></a>Módosítsa a sablon-definíciót

A minimális életképes méretezési sablon beállítása látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), és a sablon üzembe helyezése a Linux skála beállítani a Vendég-alapú automatikus skálázás látható [Itt](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Ez a sablon létrehozásához használt különbözeti vizsgáljuk meg (`git diff minimum-viable-scale-set existing-vnet`) adat által adat:

Első lépésként adja meg a paraméterek `storageAccountName` és `storageAccountSasToken`. A diagnosztikai ügynök tárolja a metrikaadatokat egy [tábla](../cosmos-db/table-storage-how-to-use-dotnet.md) az ezt a tárfiókot. A Linux diagnosztikai ügynök 3.0-s verzió frissítésétől a tárelérési kulcs használata már nem támogatott. Ehelyett használja a [SAS-Token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Ezt követően módosíthatja a méretezési `extensionProfile` a diagnosztika bővítményt tartalmazza. Ebben a konfigurációban adja meg az erőforrás-azonosítója a skála, amely összegyűjti, metrikák, valamint a tárfiók és a SAS-jogkivonat használni a metrikák tárolására. Adja meg, milyen gyakran (ebben az esetben az percenként) a metrikák összesítése és mely mérni kívánt nyomon (az eset, a készültségi használt memória). További információk a ezt a konfigurációt, és a használt memória százalékos eltérő metrikákat, lásd: [ebben a dokumentációban](../virtual-machines/linux/diagnostic-extension.md).

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

Végül adja hozzá egy `autoscaleSettings` erőforrás automatikus skálázás konfigurálása a metrikák alapján. Ehhez az erőforráshoz a `dependsOn` győződjön meg arról, hogy létezik-e a méretezési mielőtt megpróbálná automatikus skálázásra értéke a skála hivatkozó záradék. Ha automatikus skálázásra különböző metrika a, használja a `counterSpecifier` a diagnosztika bővítmény konfiguráció szerint a `metricName` az automatikus skálázás konfigurációban. Az automatikus skálázás konfigurációs további információkért lásd: a [automatikus skálázás gyakorlati tanácsok](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) és a [Azure figyelő REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/dn931928.aspx).

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
