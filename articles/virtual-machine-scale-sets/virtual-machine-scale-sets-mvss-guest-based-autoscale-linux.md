---
title: Azure automatikus skálázás használata vendégmérőszámokkal egy Linux-méretezési csoport sablonjában
description: Ismerje meg, hogyan lehet automatikus skálázást használni vendég metrikák egy Linux virtuálisgép-méretezési készlet sablon
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271952"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatikus méretezés vendégmérőszámok használatával linuxos méretezési csoportsablonban

Az Azure-ban két tág típusú metrikák, amelyek a virtuális gépek és a méretezési csoportok: gazdagép metrikák és vendég metrikák. Magas szinten, ha szabványos processzort, lemezt és hálózati metrikákat szeretne használni, akkor a gazdagép metrikák megfelelőek. Ha azonban nagyobb számú metrikák kiválasztására van szüksége, akkor a vendégmutatókat meg kell vizsgálni.

A gazdametrikák nem igényelnek további telepítést, mert azokat a gazdagép virtuális gépgyűjti, míg a vendég metrikák [megkövetelik](../virtual-machines/windows/extensions-diagnostics-template.md) a Windows Azure Diagnosztikai bővítmény vagy a [Linux Azure Diagnosztika bővítmény](../virtual-machines/linux/diagnostic-extension.md) telepítését a vendég virtuális gépben. A vendégmetrikák használatának egyik gyakori oka az, hogy a vendégmetrikák nagyobb választékot biztosítanak a metrikákból, mint a gazdamutatók. Az egyik ilyen példa a memória-felhasználási metrikák, amelyek csak vendég metrikákon keresztül érhetők el. A támogatott gazdagép-metrikák [itt](../azure-monitor/platform/metrics-supported.md)találhatók , a gyakran használt vendégmetrikák pedig [itt.](../azure-monitor/platform/autoscale-common-metrics.md) Ez a cikk bemutatja, hogyan módosíthatja az [alapvető életképes méretezési csoport sablont](virtual-machine-scale-sets-mvss-start.md) a vendég metrikák on Linux méretezési csoportok alapuló automatikus skálázási szabályok használatával.

## <a name="change-the-template-definition"></a>A sablondefiníció módosítása

Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) létrehoztunk egy egyszerű méretezési csoport sablont. Most antól ezt a korábbi sablont fogjuk használni, és módosítjuk azt egy olyan sablon létrehozásához, amely egy Linux-méretezési csoportot telepít vendégmetrika alapú automatikus skálázattal.

Először adja hozzá `storageAccountName` `storageAccountSasToken`a paramétereket a és a hoz. A diagnosztikai ügynök tárolja metrika adatokat egy [táblában](../cosmos-db/table-storage-how-to-use-dotnet.md) ebben a tárfiókban. A Linux Diagnostics Agent 3.0-s verziójától a storage access kulcs használata már nem támogatott. Ehelyett használjon [SAS-jogkivonatot.](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

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

Ezután módosítsa a `extensionProfile` méretezési készletet a diagnosztikai bővítményhez. Ebben a konfigurációban adja meg a metrikák gyűjtésére szolgáló méretezési csoport erőforrás-azonosítóját, valamint a tárfiókot és a metrikák tárolására használt SAS-jogkivonatot. Adja meg, hogy milyen gyakran a metrikák összesítése (ebben az esetben percenként), és mely metrikákat kell nyomon követni (ebben az esetben a felhasznált memória százaléka). A konfigurációval és a felhasznált memóriától eltérő mérőszámokkal kapcsolatos részletesebb információkért tekintse meg [ezt a dokumentációt.](../virtual-machines/linux/diagnostic-extension.md)

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

Végül adjon `autoscaleSettings` hozzá egy erőforrást az automatikus skálázás konfigurálásához ezek a metrikák alapján. Ez az `dependsOn` erőforrás rendelkezik egy záradékkal, amely hivatkozik a méretezési csoportra annak érdekében, hogy a méretezési csoport létezik, mielőtt automatikusskálázást kísérelne meg. Ha úgy dönt, egy másik metrika `counterSpecifier` automatikus skálázás, akkor `metricName` használja a diagnosztikai bővítmény konfigurációját, mint az automatikus skálázási konfiguráció. Az automatikus skálázási konfigurációról az [automatikus skálázás ajánlott eljárása és](../azure-monitor/platform/autoscale-best-practices.md) az [Azure Monitor REST API referenciadokumentációjában olvashat bővebben.](/rest/api/monitor/autoscalesettings)

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
