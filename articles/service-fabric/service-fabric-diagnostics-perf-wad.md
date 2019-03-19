---
title: Az Azure Service Fabric - teljesítmény figyelése a Windows Azure Diagnostics bővítmény |} A Microsoft Docs
description: Windows Azure Diagnostics használata az Azure Service Fabric-fürtök a teljesítményszámlálók adatainak összegyűjtése.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0ab14d41c149ec6e0ce76d24afb0e88a6af53935
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884557"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>A Windows Azure Diagnostics bővítményt az alkalmazásteljesítmény-figyelés

Ez a dokumentum ismerteti a rendszerteljesítmény-számlálók a Windows-fürtök a Windows Azure Diagnostics (WAD) bővítményével gyűjteményét beállításához szükséges lépéseket. Linux-fürtöket, állítsa be a [Log Analytics-ügynököket](service-fabric-diagnostics-oms-agent.md) a csomópontok a teljesítményszámlálók adatainak összegyűjtése. 

 > [!NOTE]
> Az alábbi lépéseket az Ön számára a fürtön a WAD-bővítményt kell telepíteni. Ha nem állította be, látogasson el [esemény összesítésére és a Windows Azure Diagnostics segítségével gyűjteményt](service-fabric-diagnostics-event-aggregation-wad.md).  

## <a name="collect-performance-counters-via-the-wadcfg"></a>A WadCfg keresztül teljesítményszámlálók gyűjtése

Teljesítményszámlálók adatainak összegyűjtése a WAD-n keresztül, akkor kell módosítania a konfiguráció megfelelő a fürt Resource Manager-sablon. Hajtsa végre a következő lépésekkel adhatja hozzá egy teljesítményszámláló gyűjtése a sablonhoz, és a egy Resource Manager-erőforrás frissítése.

1. Keresse meg a WAD-konfigurációt a fürt sablonban – keresés `WadCfg`. Fog rendeléseket teljesítményszámlálókat ad a `DiagnosticMonitorConfiguration`.

2. A konfiguráció beállításához adja hozzá a következő szakaszban, a teljesítményszámlálók adatainak összegyűjtése a `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A `scheduledTransferPeriod` határozza meg, hogy milyen gyakran gyűjtött a számlálók értékeit átkerülnek az Azure storage-táblához, és minden konfigurált fogadó. 

3. A teljesítményszámlálók, amelyet szeretne gyűjteni a felvétele a `PerformanceCounterConfiguration` , amely az előző lépésben lett deklarálva. Minden egyes gyűjteni kívánt teljesítményszámláló van definiálva egy `counterSpecifier`, `sampleRate`, `unit`, `annotation`, és minden kapcsolódó `sinks`.

Íme egy példa a számláló a konfigurációval a *teljes processzoridő* (mennyi ideig feldolgozó műveletekhez használatban volt a CPU) és *Service Fabric Actors megpróbálkoznimásodpercenként*, egy a Service Fabric egyéni teljesítményszámlálók. Tekintse meg [Reliable Actor-teljesítményszámlálók](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) és [Reliable Service teljesítményszámlálók](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) Service Fabric – teljesítményszámlálók egyéni teljes listáját.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 A számláló a mintavételi gyakoriság igényeknek megfelelően módosíthatók. A formátum akkor `PT<time><unit>`, ha azt szeretné, hogy másodpercenként gyűjtött számláló, majd kell beállítania a `"sampleRate": "PT15S"`.

 Teljesítményszámlálók, amelyek többféle kivitelűek lehetnek praktikus során gyűjtött teljesítményszámlálók folyamatonként tömbjét gyűjtése az ARM-sablon változókat is használhat. Az az alábbi a példában a processzor és szemétgyűjtő idő folyamatonként dátumok gyűjtése, és ezután 2 teljesítményszámlálók a maguk a csomópontok minden használatával változókat. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

 >[!NOTE]
 >Bár használhatja `*` teljesítményszámlálókat hasonlóképpen nevesített csoportok megadásához számlálókat küldése egy fogadó keresztül (az Application Insightsba) szükséges, hogy azok külön-külön deklarált. 

1. Miután hozzáadta a megfelelő teljesítményszámlálókat kell gyűjteni, a fürt erőforrásai frissíteni, hogy ezek a módosítások megjelennek a futó fürt szeretne. Mentse a módosított `template.json` , és nyissa meg a powershellt. A fürt használatával frissítheti `New-AzureRmResourceGroupDeployment`. A hívás szükség van az az erőforráscsoport, a frissített sablon fájlt, és a paramétereket tartalmazó fájlt, és kérni fogja, hogy a megfelelő módosításokat frissített erőforrások Resource Manager. Miután bejelentkezett a fiókjába, és a megfelelő előfizetéshez tartozik, használja a következő parancsot a frissítés futtatásához:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. A frissítés befejeződése után jelennek meg (attól függően, hogy-e az első üzembe helyezés és az erőforráscsoport mérete 15-45 perc között vesz igénybe), WAD kell a teljesítményszámlálók gyűjtése és elküldi azokat a tábla neve A fürthöz társított tárfiókban WADPerformanceCountersTable. Tekintse meg az Application Insights által a teljesítményszámlálók [a Resource Manager-sablon hozzáadása a mesterséges Intelligencia fogadó](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>További lépések
* A fürt több teljesítményszámlálót gyűjt. Lásd: [teljesítmény-mérőszámok](service-fabric-diagnostics-event-generation-perf.md) listája számlálókat kell gyűjteni.
* [Használat monitorozása és diagnosztizálása egy Windows virtuális gép és az Azure Resource Manager-sablonokkal](../virtual-machines/windows/extensions-diagnostics-template.md) módosításokat továbbá az `WadCfg`, beleértve a diagnosztikai adatok küldése további tárfiókok konfigurálásáról.
* Látogasson el a [WadCfg builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) hozhat létre egy teljesen új sablont, és ellenőrizze, hogy a szintaxisa helyes.