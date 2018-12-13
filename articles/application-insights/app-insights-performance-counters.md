---
title: Teljesítményszámlálók az Application Insightsban |} A Microsoft Docs
description: Rendszer- és egyéni az Application Insights .NET-teljesítményszámlálók figyelése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: bcfb3a52793ba0daca980564d5d2248629b5caf4
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323012"
---
# <a name="system-performance-counters-in-application-insights"></a>Az Application Insights rendszerteljesítmény-számlálók
Windows széles körének biztosít [teljesítményszámlálók](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) például a Processzor foglaltsága, memória, lemez és hálózat használatának. Emellett megadhatja a saját. [Az Application Insights](app-insights-overview.md) megjelenítheti a teljesítményszámlálók az alkalmazás futásának IIS alatt egy helyi gazdagépen vagy virtuális gép, amely rendszergazdai hozzáféréssel rendelkezik. A diagramok jelzik az élő alkalmazás számára elérhető erőforrásokat, és segíthet azonosítani kiegyensúlyozatlan terhelés server-példányok között.

Teljesítményszámlálók a kiszolgálók panel, amely tartalmaz egy táblát a szegmensek kiszolgálópéldány jelennek meg.

![Az Application Insights által jelentett teljesítményszámlálókat](./media/app-insights-performance-counters/counters-by-server-instance.png)

(A teljesítményszámlálók nem érhetők el az Azure Web Appshez. De [Azure Diagnostics küldése az Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).)

## <a name="view-counters"></a>Nézet számlálók
A kiszolgálók panel teljesítményszámlálók alapértelmezett halmazát tartalmazza. 

Egyéb számlálók megtekintéséhez vagy szerkesztéséhez a kiszolgálók panel diagramjai, vagy nyisson meg egy új [Metrikaböngésző](app-insights-metrics-explorer.md) panelen, és adja hozzá az új diagramokat. 

Amikor a diagram szerkesztése metrikák elérhető számlálók szerepelnek.

![Az Application Insights által jelentett teljesítményszámlálókat](./media/app-insights-performance-counters/choose-performance-counters.png)

A legfontosabb diagramok egy helyen megtekintéséhez hozzon létre egy [irányítópult](app-insights-dashboards.md) és rögzíti őket hozzá.

## <a name="add-counters"></a>Számlálók hozzáadása
Ha azt szeretné, a teljesítményszámláló mérőszámok listája nem látható, ez, mert a webkiszolgálót az Application Insights SDK nem gyűjt. Konfigurálhatja a azt ennek a végrehajtására.

1. Ismerje meg, milyen számlálók érhetők el a kiszolgálót a kiszolgálón a PowerShell-parancs segítségével:
   
    `Get-Counter -ListSet *`
   
    (Lásd: [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Nyissa meg az applicationinsights.config fájlban.
   
   * Ha a fejlesztés során az alkalmazáshoz hozzáadott Application Insights, szerkessze az applicationinsights.config fájlt a projektben, és majd újból telepítenie kell azt a kiszolgálót.
   * Ha futásidőben webalkalmazás szoftverfejlesztők használt állapotfigyelő, ApplicationInsights.config található az IIS-ben az alkalmazás gyökérkönyvtárában. Minden kiszolgálópéldány frissítik vele van.
3. A teljesítmény gyűjtő irányelv szerkesztése:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Mind standard számlálókat, és azokat saját kezűleg valósította rögzítheti. `\Objects\Processes` standard számlálót, például az összes Windows rendszeren érhető el. `\Sales(photo)\# Items Sold` van egy egyéni számlálót, előfordulhat, hogy a webszolgáltatás meg lehessen valósítani egy példát. 

A formátum `\Category(instance)\Counter"`, vagy a példányok nem rendelkező kategóriák, egyszerűen `\Category\Counter`.

`ReportAs` számláló neve, amely nem egyezik a szükséges `[a-zA-Z()/-_ \.]+` -, azok karaktereket tartalmaz, amelyek nem szerepelnek a következő eljárások: betűk, kerek zárójeleket, perjellel, kötőjelet, aláhúzásjelet, területet, pont.

Ha megad egy példányt, akkor a jelentett mérőszám "CounterInstanceName" dimenziónak gyűjtendő.

### <a name="collecting-performance-counters-in-code"></a>A kódban teljesítményszámlálók gyűjtése
Rendszerteljesítmény-számlálók gyűjteni, és küldje el azokat az Application Insights, az alábbi kódrészlet tesztkörnyezetéhez igazíthatja:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Vagy megteheti ugyanezt teszi az létrehozott egyéni metrikák:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Teljesítményszámlálók az Analyticsben
Itt megkeresheti, és a teljesítmény számláló jelentések megjelenítéséhez [Analytics](app-insights-analytics.md).

A **performanceCounters** sémát tesz elérhetővé a `category`, `counter` nevét, és `instance` minden teljesítményszámláló neve.  A telemetria minden alkalmazáshoz csak az adott alkalmazáshoz számlálókat megjelenik. Például hogy milyen számlálók érhetők el: 

![Az Application Insights Analytics teljesítményszámlálók](./media/app-insights-performance-counters/analytics-performance-counters.png)

("Példányok" Itt hivatkozik a teljesítményszámláló-példány, nem a szerepkör- vagy virtuálisgép-példányt. A teljesítményszámláló példány neve általában szegmensek számlálók, például a processzor kihasználtsága a folyamat vagy alkalmazás nevével kiegészítve.)

A rendelkezésre álló memória diagram lekérése az elmúlt időszakban: 

![Az Application Insights Analytics memória idődiagramját](./media/app-insights-performance-counters/analytics-available-memory.png)

Egyéb telemetriai adatokat, például **performanceCounters** is rendelkezik egy olyan oszlop `cloud_RoleInstance` , amely azt jelzi, hogy a gazdagép server-példány, amelyen fut az alkalmazás identitását. Ha például a különböző gépeken az alkalmazás teljesítményének összehasonlítására: 

![Az Application Insights Analytics szerepkörpéldány alapján szegmentált teljesítmény](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Az ASP.NET és az Application Insights eseményszámok
*Mi a különbség a kivételek sebessége és a kivételek metrikák?*

* *Kivételek sebessége* rendszer teljesítményszámláló van. A CLR-beli összes a kezelt és nem kezelt kivétel lépett fel vannak, és a mintavételi időközben az összes elosztja az időköz hossza számít. Az Application Insights SDK ezt az eredményt gyűjt, és továbbítja őket a portálon.
* *Kivételek* száma a mintavételi időközben a diagram a portál által fogadott TrackException jelentéseket. Csak a kezelt kivételek ahol hozott TrackException meghívja a kódban, és nem tartalmazza az összes benne [nem kezelt kivételeket](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Teljesítményszámlálók az Asp.Net Core-alkalmazások
Teljesítményszámlálók használata támogatott, csak akkor, ha az alkalmazás a teljes .NET-keretrendszer van-e állítva. Nincs lehetőség a teljesítményszámlálók adatainak összegyűjtése a .net Core alkalmazásokat.

## <a name="alerts"></a>Riasztások
Például más metrikák is [riasztások beállítása](app-insights-alerts.md) figyelmezteti, ha egy teljesítményszámláló kerül kívül megadott korlátot. A riasztások panel megnyitásához, és kattintson a riasztás hozzáadása.

## <a name="next"></a>Következő lépések
* [Függőségi nyomkövetés](app-insights-asp-net-dependencies.md)
* [Kivétel követése](app-insights-asp-net-exceptions.md)

