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
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: feb2e2f9f36ab20c0b96fab9432df41faf4f9569
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407932"
---
# <a name="system-performance-counters-in-application-insights"></a>Az Application Insights rendszerteljesítmény-számlálók

Windows széles körének biztosít [teljesítményszámlálók](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) például a Processzor foglaltsága, memória, lemez és hálózat használatának. A saját teljesítményszámlálókat is meghatározhat. Mindaddig, amíg az alkalmazás fut az IIS alatt egy helyi gazdagépen vagy virtuális gép, amely rendszergazdai hozzáféréssel rendelkezik.

## <a name="view-counters"></a>Nézet számlálók

A metrikák panelen látható teljesítményszámlálókat alapértelmezés szerint.

![Az Application Insights által jelentett teljesítményszámlálókat](./media/app-insights-performance-counters/performance-counters.png)

A .NET-webalkalmazásokra vonatkozó gyűjtött aktuális alapértelmezett számlálókat a következők:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

A legfontosabb diagramok egy helyen megtekintéséhez hozzon létre egy [irányítópult](app-insights-dashboards.md) és rögzíti őket hozzá.

## <a name="add-counters"></a>Számlálók hozzáadása

Ha a kívánt teljesítményszámláló nem szerepel a listán, metrikák, adhat hozzá.

1. Ismerje meg, milyen számlálók érhetők el a kiszolgáló a következő PowerShell paranccsal a helyi kiszolgálón:
   
    `Get-Counter -ListSet *`
   
    (Lásd: [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Nyissa meg az applicationinsights.config fájlban.
   
   * Ha a fejlesztés során az alkalmazáshoz hozzáadott Application Insights, szerkessze az applicationinsights.config fájlt a projektben, és ezt követően telepítse újra a kiszolgálókat.
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

Mind standard számlálókat, és azokat, hogy megvalósította a saját maga is rögzítheti. `\Objects\Processes` van például egy standard számlálót, amely az összes Windows rendszeren érhető el. `\Sales(photo)\# Items Sold` van egy egyéni számlálót, előfordulhat, hogy a webszolgáltatás meg lehessen valósítani egy példát. 

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

("Példány" Itt a teljesítményszámláló-példány nem a szerepkör vagy a kiszolgáló virtuálisgép-példányt is hivatkozik. A teljesítményszámláló példány neve általában szegmensek számlálók, például a processzor kihasználtsága a folyamat vagy alkalmazás nevével kiegészítve.)

A rendelkezésre álló memória diagram lekérése az elmúlt időszakban: 

![Az Application Insights Analytics memória idődiagramját](./media/app-insights-performance-counters/analytics-available-memory.png)

Egyéb telemetriai adatokat, például **performanceCounters** is rendelkezik egy olyan oszlop `cloud_RoleInstance` , amely azt jelzi, hogy a gazdagép server-példány, amelyen fut az alkalmazás identitását. Ha például a különböző gépeken az alkalmazás teljesítményének összehasonlítására: 

![Az Application Insights Analytics szerepkörpéldány alapján szegmentált teljesítmény](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Az ASP.NET és az Application Insights eseményszámok
*Mi a különbség a kivételek sebessége és a kivételek metrikák?*

* *Kivételek sebessége* rendszer teljesítményszámláló van. A CLR-beli összes a kezelt és nem kezelt kivétel lépett fel vannak, és a mintavételi időközben az összes elosztja az időköz hossza számít. Az Application Insights SDK ezt az eredményt gyűjt, és továbbítja őket a portálon.

* *Kivételek* száma a mintavételi időközben a diagram a portál által fogadott TrackException jelentéseket. Csak a kezelt kivételek ahol hozott TrackException meghívja a kódban, és nem tartalmazza az összes benne [nem kezelt kivételeket](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Teljesítményszámlálók az ASP.Net Core-alkalmazások
Teljesítményszámlálók használata támogatott, csak akkor, ha az alkalmazás a teljes .NET-keretrendszer van-e állítva. Nincs lehetőség a teljesítményszámlálók adatainak összegyűjtése a .net Core alkalmazásokat.

## <a name="alerts"></a>Riasztások
Például más metrikák is [riasztások beállítása](app-insights-alerts.md) figyelmezteti, ha egy teljesítményszámláló kerül kívül megadott korlátot. Nyissa meg a riasztások panelen, és kattintson a riasztás hozzáadása.

## <a name="next"></a>Következő lépések
* [Függőségi nyomkövetés](app-insights-asp-net-dependencies.md)
* [Kivétel követése](app-insights-asp-net-exceptions.md)

