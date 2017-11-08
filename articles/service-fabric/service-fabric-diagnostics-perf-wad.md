---
title: "Az Azure Service Fabric - teljesítményfigyelés a Windows Azure diagnosztikai kiterjesztésű |} Microsoft Docs"
description: "Az Azure Service Fabric-fürtök a teljesítményszámlálók adatainak összegyűjtése a Windows Azure diagnosztikai."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Windows Azure diagnosztikai kiterjesztésű-figyelő

Ez a dokumentum a Windows-fürtök esetén a Windows Azure diagnosztikai (ÜVEGVATTA) bővítmény keresztül teljesítményszámlálók gyűjteményét beállításához szükséges lépéseket ismerteti. Linux-fürtök esetén állítsa be a [OMS-ügynököt](service-fabric-diagnostics-oms-agent.md) a csomópontok a teljesítményszámlálók adatainak összegyűjtése. 

 > [!NOTE]
> A megfelelő Önnek az alábbi lépéseket a fürt a ÜVEGVATTA bővítmény kell telepíteni. Ha nincs beállítva, látogasson el [esemény összesítésére és az adatgyűjtést, a Windows Azure diagnosztikai](service-fabric-diagnostics-event-aggregation-wad.md) és kövesse a *a diagnosztika bővítmény telepítése*.

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

3. Adjon hozzá teljesítményszámlálókat szeretné összegyűjtéséhez a `PerformanceCounterConfiguration` , amely az előző lépésben lett deklarálva. Az egyes számlálók szeretne gyűjteni a van meghatározva egy `counterSpecifier`, `sampleRate`, `unit`, `annotation`, és minden kapcsolódó `sinks`. Íme egy példa egy teljesítményszámlálója számára a *teljes processzoridő* (mennyi ideig feldolgozó műveletekhez használatban volt a CPU):

    ```json
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
            }
        ]
    }
    ```

    A mintavételi gyakoriság, a számláló az igényeinek megfelelően módosíthatja. A formátum az `PT<time><unit>`, így ha másodpercenként gyűjtött számláló, majd célszerű a `"sampleRate": "PT15S"`.

    Hasonló módon hozhatja létre számos más teljesítményszámlálók hozzáadását a listát a `PerformanceCounterConfiguration`. Bár használhatja `*` hasonlóképpen nevű teljesítményszámláló megadásához küldése a számlálót a fogadó keresztül (Application Insights) megköveteli, hogy külön-külön deklarálásuk. 

4. A megfelelő teljesítményszámlálók gyűjtendő igénylő kellett hozzáadott, frissítenie kell a fürt erőforrás, hogy ezek a módosítások megjelennek a működő fürthöz. Mentse a módosított `template.json` és PowerShell nyissa meg. Frissítheti a fürt használt `New-AzureRmResourceGroupDeployment`. A hívás szükséges az erőforráscsoportot, a frissített sablon fájl és a paraméterek fájl nevét, és erőforrás-kezelő megfelelő módosításokat szeretne az erőforrásokat, amelyeket azért frissített kéri. Miután bejelentkezett a fiókját, és jobb az előfizetéshez, használja a következő parancsot a frissítés futtatásához:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. A frissítés befejeződése után terítésével (közötti 15-45 percet vesz igénybe), ÜVEGVATTA kell kell gyűjtése a teljesítményszámlálók és elküldi őket a táblázat a tárfiókban lévő deklarálva a `WadCfg`.

## <a name="next-steps"></a>Következő lépések
* Lásd: a teljesítményszámlálók, az Application Insights az Application Insights hozzáadásával a gyűjtése a `WadCfg`. Olvassa el a *a AI fogadó hozzáadása a Resource Manager-sablon* szakasz [esemény elemzése és az Application insights szolgáltatással a képi megjelenítés ](service-fabric-diagnostics-event-analysis-appinsights.md) az Application Insights fogadó konfigurálásához.
* A fürt további teljesítményszámlálók adatainak összegyűjtése. Lásd: [teljesítménymutatók](service-fabric-diagnostics-event-generation-perf.md) kell gyűjtött teljesítményszámlálók listája.
* [Használjon figyelési és diagnosztika a Windows virtuális gép és az Azure Resource Manager-sablonok](../virtual-machines/windows/extensions-diagnostics-template.md) módosításokat további a `WadCfg`, beleértve a további tárhely konfigurálásával és diagnosztikai adatok küldése.