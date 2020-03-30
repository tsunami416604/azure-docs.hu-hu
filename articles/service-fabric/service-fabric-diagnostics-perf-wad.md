---
title: Teljesítményfigyelés a Windows Azure Diagnosztikával
description: A Windows Azure Diagnosztika segítségével teljesítményszámlálókat gyűjthet az Azure Service Fabric-fürtökhöz.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609910"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Teljesítményfigyelés a Windows Azure Diagnosztika bővítményével

Ez a dokumentum ismerteti a teljesítményszámlálók gyűjteményének beállításához szükséges lépéseket a Windows Azure-diagnosztika (WAD) bővítményen keresztül a Windows-fürtökhöz. Linux-fürtök, állítsa be a [Log Analytics-ügynök](service-fabric-diagnostics-oms-agent.md) teljesítményszámlálók gyűjtése a csomópontok. 

 > [!NOTE]
> A WAD-bővítményt telepíteni kell a fürtön, hogy ezek a lépések működjenek az Ön számára. Ha nincs beállítva, menjen át [az Eseményösszesítés és -gyűjtés ablakhoz a Windows Azure Diagnosztika használatával.](service-fabric-diagnostics-event-aggregation-wad.md)  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Teljesítményszámlálók gyűjtése a WadCfg-en keresztül

Teljesítményszámlálók wad-on keresztüli gyűjtéséhez a fürt Erőforrás-kezelő sablonjában megfelelően módosítania kell a konfigurációt. Az alábbi lépésekkel hozzáadhat egy teljesítményszámlálót, amelyet össze szeretne gyűjteni a sablonhoz, és futtatni szeretné az Erőforrás-kezelő erőforrás-frissítését.

1. Keresse meg a WAD-konfigurációt a `WadCfg`fürt sablonjában - keresse meg a . A alatt összegyűjthető teljesítményszámlálókat fog hozzáadni. `DiagnosticMonitorConfiguration`

2. Állítsa be a konfigurációt a teljesítményszámlálók gyűjtésére úgy, hogy a következő szakaszt adja hozzá a hoz. `DiagnosticMonitorConfiguration` 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A `scheduledTransferPeriod` meghatározza, hogy milyen gyakran a számlálók, amelyek gyűjtött értékek át kerülnek az Azure storage-táblába és bármely konfigurált fogadó. 

3. Adja hozzá a teljesítményszámlálókat, `PerformanceCounterConfiguration` amelyeket be szeretne gyűjteni az előző lépésben deklarált teljesítményszámlálókhoz. Minden egyes számláló, amelyet össze `counterSpecifier`szeretne `sampleRate` `unit`gyűjteni, a , , , `annotation`és minden releváns `sinks`.

Íme egy példa egy konfigurációa a számláló a *teljes processzoridő* (az az idő, a processzor volt használatban a feldolgozási műveletek) és a *Service Fabric aktor metódus invocations másodpercenként,* az egyik a Service Fabric egyéni teljesítményszámlálók. Tekintse meg [a megbízható aktor teljesítményszámlálók](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) és [megbízható szolgáltatás teljesítményszámlálók](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) a Service Fabric egyéni perf számlálók teljes listáját.

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

 A számláló mintavételi aránya az ön igényeinek megfelelően módosítható. A formátum a `PT<time><unit>`, így ha azt szeretné, hogy a `"sampleRate": "PT15S"`számláló gyűjtött minden második, akkor be kell állítania a .

 Az ARM-sablonváltozóival teljesítményszámlálók tömbje is gyűjthet, ami hasznos lehet, ha folyamatonként teljesítményszámlálókat gyűjt. Az alábbi példában folyamatonként gyűjtjük a processzoridőt és a szemétgyűjtői időt, majd 2 teljesítményszámlálót a csomópontokon, amelyek változókat használnak. 

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

1. Miután hozzáadta a megfelelő teljesítményszámlálókat, amelyeket össze kell gyűjteni, frissítenie kell a fürterőforrást, hogy ezek a módosítások megjelenjenek a futó fürtben. Mentse a `template.json` módosított, és nyissa meg a PowerShell. A segítségével frissítheti `New-AzResourceGroupDeployment`a fürtöt. A híváshoz szükség van az erőforráscsoport, a frissített sablonfájl és a paraméterfájl nevére, és kéri az Erőforrás-kezelőt, hogy hajtsa végre a frissített erőforrások megfelelő módosításait. Miután bejelentkezett a fiókjába, és a megfelelő előfizetésben van, a következő paranccsal futtathatja a frissítést:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Miután a frissítés befejeződik gördülő ki (tart között 15-45 perc attól függően, hogy ez az első üzembe helyezés és az erőforráscsoport mérete), WAD kell gyűjteni a teljesítmény számlálók és elküldi őket a tábla neve WADPerformanceCountersTable a fürthöz társított tárfiókban. Tekintse meg a teljesítményszámlálókat az Application Insightsban [az AI-fogadó nak az Erőforrás-kezelő sablonhoz való hozzáadásával.](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)

## <a name="next-steps"></a>További lépések
* Gyűjtsön több teljesítményszámlálót a fürthöz. A [teljesítménymutatók](service-fabric-diagnostics-event-generation-perf.md) ban az összegyűjtött számlálók listáját.
* [A figyelés és a diagnosztika windowsos virtuális gép- és Azure Resource Manager-sablonokkal további](../virtual-machines/windows/extensions-diagnostics-template.md) módosításokat hajtson végre a rendszeren, `WadCfg`beleértve a diagnosztikai adatok diagnosztikai adatok küldéséhez szükséges további tárfiókok konfigurálását is.
* Látogasson el a [WadCfg építőjéhez,](https://azure.github.io/azure-diagnostics-tools/config-builder/) és hozzon létre egy sablont a semmiből, és győződjön meg arról, hogy a szintaxis helyes. (https://azure.github.io/azure-diagnostics-tools/config-builder/) építeni egy sablont a semmiből, és győződjön meg róla, a szintaxis helyes.
