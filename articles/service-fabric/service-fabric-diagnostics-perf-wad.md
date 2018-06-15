---
title: Az Azure Service Fabric - teljesítményfigyelés a Windows Azure diagnosztikai kiterjesztésű |} Microsoft Docs
description: Az Azure Service Fabric-fürtök a teljesítményszámlálók adatainak összegyűjtése a Windows Azure diagnosztikai.
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
ms.date: 03/26/2018
ms.author: srrengar
ms.openlocfilehash: 5c8c1f107e9e70e72c48ea93ef211b2a760ffb5c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213231"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>A Windows Azure diagnosztikai kiterjesztésű-figyelő

Ez a dokumentum a Windows-fürtök esetén a Windows Azure diagnosztikai (ÜVEGVATTA) bővítmény keresztül teljesítményszámlálók gyűjteményét beállításához szükséges lépéseket ismerteti. Linux-fürtök esetén állítsa be a [OMS-ügynököt](service-fabric-diagnostics-oms-agent.md) a csomópontok a teljesítményszámlálók adatainak összegyűjtése. 

 > [!NOTE]
> A megfelelő Önnek az alábbi lépéseket a fürt a ÜVEGVATTA bővítmény kell telepíteni. Ha nincs beállítva, látogasson el [esemény összesítésére és az adatgyűjtést, a Windows Azure diagnosztikai](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

## <a name="collect-performance-counters-via-the-wadcfg"></a>A WadCfg keresztül a teljesítményszámlálók adatainak összegyűjtése

Teljesítményszámlálók adatainak összegyűjtése ÜVEGVATTA keresztül, módosítania kell a konfiguráció megfelelő a fürt Resource Manager-sablon. A lépések végrehajtásával adja hozzá a teljesítményszámláló gyűjtése a sablont, és egy erőforrás-kezelő erőforrás frissítése.

1. A fürt sablonban található ÜVEGVATTA konfiguráció - található `WadCfg`. Fel szeretné venni a gyűjtendő kérelemteljesítmény-számlálókat a `DiagnosticMonitorConfiguration`.

2. A konfiguráció beállítása a teljesítményszámlálók adatainak összegyűjtése a következő szakaszt hozzáadásával a `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A `scheduledTransferPeriod` határozza meg, hogyan gyűjtött számláló átviszi a program az Azure storage táblában, és bármely frquently konfigurálva fogadó. 

3. Adjon hozzá teljesítményszámlálókat szeretné összegyűjtéséhez a `PerformanceCounterConfiguration` , amely az előző lépésben lett deklarálva. Az egyes számlálók szeretne gyűjteni a van meghatározva egy `counterSpecifier`, `sampleRate`, `unit`, `annotation`, és minden kapcsolódó `sinks`.

Íme egy példa konfiguráció a számláló a *teljes processzoridő* (mennyi ideig feldolgozó műveletekhez használatban volt a CPU) és *Service Fabric szereplő metódus meghívásáhozmásodpercenként*, a Service Fabric egyéni teljesítményszámlálói közül. Tekintse meg [megbízható szereplő teljesítményszámlálók](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) és [megbízható szolgáltatás teljesítményszámlálók](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) Service Fabric egyéni Teljesítményfigyelő számlálók teljes listáját.

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

 A mintavételi gyakoriság, a számláló az igényeinek megfelelően módosíthatja. A formátum az `PT<time><unit>`, így ha másodpercenként gyűjtött számláló, majd célszerű a `"sampleRate": "PT15S"`.

 >[!NOTE]
 >Bár használhatja `*` hasonlóképpen nevű teljesítményszámláló megadásához küldése a számlálót a fogadó keresztül (Application Insights) megköveteli, hogy külön-külön deklarálásuk. 

4. Miután hozzáadta a megfelelő teljesítményszámlálók gyűjtendő igénylő, frissítenie kell a fürt erőforrás, hogy ezek a módosítások megjelennek a működő fürthöz. Mentse a módosított `template.json` és PowerShell nyissa meg. Frissítheti a fürt használt `New-AzureRmResourceGroupDeployment`. A hívás szükséges az erőforráscsoportot, a frissített sablon fájl és a paraméterek fájl nevét, és erőforrás-kezelő megfelelő módosításokat szeretne az erőforrásokat, amelyeket azért frissített kéri. Miután bejelentkezett a fiókját, és jobb az előfizetéshez, használja a következő parancsot a frissítés futtatásához:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. A frissítés befejeződése után terítésével (közötti 15-45 percet vesz igénybe), ÜVEGVATTA kell kell gyűjtése a teljesítményszámlálók és a fürthöz tartozó elküldi őket a tárfiókban lévő WADPerformanceCountersTable a táblában. Tekintse meg az Application Insights által a teljesítményszámlálók [a AI fogadó ad hozzá a Resource Manager-sablon](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>További lépések
* A fürt további teljesítményszámlálók adatainak összegyűjtése. Lásd: [teljesítménymutatók](service-fabric-diagnostics-event-generation-perf.md) kell gyűjtött teljesítményszámlálók listája.
* [Használjon figyelési és diagnosztika a Windows virtuális gép és az Azure Resource Manager-sablonok](../virtual-machines/windows/extensions-diagnostics-template.md) módosításokat további a `WadCfg`, beleértve a további tárhely konfigurálásával és diagnosztikai adatok küldése.
