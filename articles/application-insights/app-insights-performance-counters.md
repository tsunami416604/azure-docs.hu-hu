---
title: "Az Application Insights teljesítményszámlálók |} Microsoft Docs"
description: "A figyelő rendszer és az Application Insights egyéni .NET teljesítményszámlálók."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: bwren
ms.openlocfilehash: 038d6e051be8112b9264e7efa6485965d11e32c8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Az Application Insightsban rendszerteljesítmény-számlálók
A Windows számos biztosít [teljesítményszámlálók](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) például a Processzor Foglaltság, memória, lemez és hálózat használatának. Is definiálhat a saját. [Az Application Insights](app-insights-overview.md) megjelenítheti a teljesítményszámlálók Ha az alkalmazás fut az IIS a helyi gazdagép vagy virtuális gépet, amely rendszergazdai hozzáféréssel rendelkezik. A diagramok jelzi az élő alkalmazás számára elérhető erőforrások, és segít a kiszolgálópéldányok közötti egyenetlen terhelés azonosításához.

Teljesítményszámlálók jelennek meg a kiszolgálók panel, amelyen a kiszolgálópéldány az adott szegmens egy táblázatot tartalmaz.

![Az Application Insightsban jelentett teljesítményszámlálók](./media/app-insights-performance-counters/counters-by-server-instance.png)

(A teljesítményszámlálók nem érhetők el az Azure Web Apps. Azonban úgy is [Azure Diagnostics küldése az Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Nézet számlálók
A kiszolgálók panel teljesítményszámlálók alapértelmezett készletét jeleníti meg. 

Más számlálók megtekintéséhez vagy a kiszolgálók panel diagramokat, vagy nyisson meg egy új [Metrikaböngésző](app-insights-metrics-explorer.md) panelt, és adja hozzá az új diagramokat. 

Amikor a diagram szerkesztése az elérhető számlálók metrikák szerepelnek.

![Az Application Insightsban jelentett teljesítményszámlálók](./media/app-insights-performance-counters/choose-performance-counters.png)

Tekintse meg a leghasznosabb diagramokat az egyik helyen, hozzon létre egy [irányítópult](app-insights-dashboards.md) és rögzítheti őket hozzá.

## <a name="add-counters"></a>Számlálók hozzáadása
Ha a kívánt teljesítményszámláló metrikák listája nem látható, ennek oka a webkiszolgálón az Application Insights SDK nem gyűjt. Erre konfigurálhatja.

1. Megtudhatja, milyen számlálók érhetők el a kiszolgálón a kiszolgáló a PowerShell-parancs használatával:
   
    `Get-Counter -ListSet *`
   
    (Lásd: [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Nyissa meg az ApplicationInsights.config.
   
   * Az Application Insights az alkalmazáshoz, a fejlesztés során ApplicationInsights.config szerkesztése a projektben, és majd helyezze újra üzembe, hogy a kiszolgálókhoz.
   * Ha a állapotfigyelő állíthatnak be futtatás közben egy webalkalmazást, ApplicationInsights.config található az IIS-ben az alkalmazás gyökérkönyvtárában. Frissíteni nincs összes server-példány.
3. A teljesítmény adatgyűjtő irányelv szerkesztése:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Rögzítheti a szabványos számlálók, mind azok saját kezűleg megvalósítását. `\Objects\Processes`Példa egy szabványos számláló az összes Windows rendszereken érhető el. `\Sales(photo)\# Items Sold`Íme egy egyéni számláló, előfordulhat, hogy egy webszolgáltatás-bővítmény kell végrehajtani. 

A formátum `\Category(instance)\Counter"`, vagy nem rendelkezik a példányok kategóriákat, csak `\Category\Counter`.

`ReportAs`a számláló neve, amely nem egyezik a szükséges `[a-zA-Z()/-_ \.]+` -Ez azt jelenti, hogy azok karakterek, amelyek nem szerepelnek a következő készletek: betűkből kerek zárójeleket, törtvonallal, kötőjelet, aláhúzásjelet, terület, pont.

Ha megad egy példányát, akkor a jelentésben szereplő metrikája "CounterInstanceName" dimenziónak gyűjtenek.

### <a name="collecting-performance-counters-in-code"></a>A kódban teljesítményszámlálók gyűjtése
A rendszer a teljesítményszámlálók adatainak összegyűjtése, és küldje el az Application Insights, az alábbi részlet módosíthatja:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Vagy is elvégezheti a létrehozott egyéni metrikákat az ugyanaz:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Az elemzés teljesítményszámlálók
Kereshet és a teljesítmény számláló jelentések megjelenítéséhez [Analytics](app-insights-analytics.md).

A **performanceCounters** séma elérhetővé teszi a `category`, `counter` nevét, és `instance` minden teljesítményszámláló nevét.  A telemetriai minden alkalmazáshoz csak a számláló az alkalmazás látható. Például hogy milyen számlálók érhetők el: 

![Az Application Insights analytics teljesítményszámlálók](./media/app-insights-performance-counters/analytics-performance-counters.png)

(Itt "Példány" hivatkozik a teljesítményszámláló-példány, nem a szerepkör- vagy virtuálisgép-példányt. A teljesítményszámlálójának példánynevét általában szegmensek számlálók például a processzor kihasználtsága a folyamat vagy az alkalmazás nevével.)

A rendelkezésre álló memória diagram beolvasása a legutóbbi időszakban: 

![Az Application Insights Analytics memória timechart](./media/app-insights-performance-counters/analytics-available-memory.png)

Egyéb telemetriai adatokat, például **performanceCounters** egy olyan oszlop is van `cloud_RoleInstance` azt jelzi, hogy a futtató kiszolgálópéldány, amelyen fut az alkalmazás identitását. Ha például a az alkalmazás teljesítményével kapcsolatos különböző gépeken összehasonlítandó: 

![Az Application Insights Analytics szerepkör példánya szegmentált teljesítmény](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Az ASP.NET és az Application Insights száma
*Mi az a különbség a kivétel arányról és kivételek között?*

* *Kivétel arány* rendszer teljesítményszámláló van. A közös nyelvi futtató környezet összes a kezelt és kezeletlen kivételt okozott, és a mintavételi időközben a teljes elosztja a időközt hosszának száma. Az Application Insights SDK ennek gyűjt, és elküldi a portálon.
* *Kivételek* a mintavételi időközben a diagram a portál által fogadott TrackException jelentések száma. Ez magában foglalja a csak a kezelt kivételek ahol TrackException hívja be a kódját, és nem tartalmazza az összes írt [nem kezelt kivételek](app-insights-asp-net-exceptions.md). 

## <a name="alerts"></a>Riasztások
Például a más metrikákkal is [riasztás beállításához](app-insights-alerts.md) figyelmezteti, ha a teljesítményszámláló megadott maximális kívül kerül. Nyissa meg a riasztások panelen, majd kattintson a riasztás hozzáadása.

## <a name="next"></a>Következő lépések
* [A függőségi nyomon követése](app-insights-asp-net-dependencies.md)
* [Kivétel követése](app-insights-asp-net-exceptions.md)

