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
ms.openlocfilehash: 0ec64a5ae412fb4a1900021fefcb7d9112b1b019
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255328"
---
# <a name="system-performance-counters-in-application-insights"></a>Az Application Insights rendszerteljesítmény-számlálók

A Windows [teljesítményszámlálók](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) széles választékát biztosítja, amelyek például a processzor foglaltságáról, vagy a memória-, merevlemez- és hálózathasználatról biztosítanak adatokat. A saját teljesítményszámlálókat is meghatározhat. Teljesítménygyűjtés számlálók mindaddig, amíg egy helyi gazdagépen vagy virtuális gép, amely rendszergazdai hozzáféréssel rendelkezik az alkalmazás fut az IIS alatt támogatott. Azonban, Azure Web Apps nincs közvetlen hozzáférése teljesítményszámlálók futó alkalmazások, elérhető számlálók egy részét gyűjti össze az Application Insights.

## <a name="view-counters"></a>Nézet számlálók

A metrikák panelen látható teljesítményszámlálókat alapértelmezés szerint.

![Az Application Insights által jelentett teljesítményszámlálókat](./media/performance-counters/performance-counters.png)

A jelenlegi alapértelmezett számlálók gyűjtendő ASP.NET/ASP.NET Core-webalkalmazások számára konfigurált a következők:

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

## <a name="add-counters"></a>Számlálók hozzáadása

Ha a kívánt teljesítményszámláló nem szerepel a listán, metrikák, adhat hozzá.

1. Ismerje meg, milyen számlálók érhetők el a kiszolgáló a következő PowerShell paranccsal a helyi kiszolgálón:

    `Get-Counter -ListSet *`

    (Lásd: [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Open ApplicationInsights.config.

   * Ha a fejlesztés során az alkalmazáshoz hozzáadott Application Insights, szerkessze az applicationinsights.config fájlt a projektben, és ezt követően telepítse újra a kiszolgálókat.
3. A teljesítmény gyűjtő irányelv szerkesztése:

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> Az ASP.NET Core-alkalmazások nem rendelkeznek `ApplicationInsights.config`, és ezért a fenti módszerrel nem érvényes az ASP.NET Core-alkalmazások.

Mind standard számlálókat, és azokat, hogy megvalósította a saját maga is rögzítheti. `\Objects\Processes` van például egy standard számlálót, amely az összes Windows rendszeren érhető el. `\Sales(photo)\# Items Sold` van egy egyéni számlálót, előfordulhat, hogy a webszolgáltatás meg lehessen valósítani egy példát.

A formátum `\Category(instance)\Counter"`, vagy a példányok nem rendelkező kategóriák, egyszerűen `\Category\Counter`.

`ReportAs` számláló neve, amely nem egyezik a szükséges `[a-zA-Z()/-_ \.]+` -, azok karaktereket tartalmaz, amelyek nem szerepelnek a következő eljárások: betűk, kerek zárójeleket, perjellel, kötőjelet, aláhúzásjelet, területet, pont.

Ha megad egy példányt, akkor a jelentett mérőszám "CounterInstanceName" dimenziónak gyűjtendő.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>ASP.NET-webalkalmazások vagy.NET/.NET Core-Konzolalkalmazást a kódban teljesítményszámlálók gyűjtése
Rendszerteljesítmény-számlálók gyűjteni, és küldje el azokat az Application Insights, az alábbi kódrészlet tesztkörnyezetéhez igazíthatja:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Vagy megteheti ugyanezt teszi az létrehozott egyéni metrikák:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Az ASP.NET Core webes alkalmazásokhoz kódban teljesítményszámlálók gyűjtése

Módosítsa `ConfigureServices` metódus az a `Startup.cs` , az alábbi osztály.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Teljesítményszámlálók az Analyticsben
Itt megkeresheti, és a teljesítmény számláló jelentések megjelenítéséhez [Analytics](../../azure-monitor/app/analytics.md).

A **performanceCounters** sémát tesz elérhetővé a `category`, `counter` nevét, és `instance` minden teljesítményszámláló neve.  A telemetria minden alkalmazáshoz csak az adott alkalmazáshoz számlálókat megjelenik. Például hogy milyen számlálók érhetők el: 

![Az Application Insights Analytics teljesítményszámlálók](./media/performance-counters/analytics-performance-counters.png)

("Példány" Itt a teljesítményszámláló-példány nem a szerepkör vagy a kiszolgáló virtuálisgép-példányt is hivatkozik. A teljesítményszámláló példány neve általában szegmensek számlálók, például a processzor kihasználtsága a folyamat vagy alkalmazás nevével kiegészítve.)

A rendelkezésre álló memória diagram lekérése az elmúlt időszakban: 

![Az Application Insights Analytics memória idődiagramját](./media/performance-counters/analytics-available-memory.png)

Egyéb telemetriai adatokat, például **performanceCounters** is rendelkezik egy olyan oszlop `cloud_RoleInstance` , amely azt jelzi, hogy a gazdagép server-példány, amelyen fut az alkalmazás identitását. Ha például a különböző gépeken az alkalmazás teljesítményének összehasonlítására: 

![Az Application Insights Analytics szerepkörpéldány alapján szegmentált teljesítmény](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Az ASP.NET és az Application Insights eseményszámok

*Mi a különbség a kivételek sebessége és a kivételek metrikák?*

* *Kivételek sebessége* rendszer teljesítményszámláló van. A CLR-beli összes a kezelt és nem kezelt kivétel lépett fel vannak, és a mintavételi időközben az összes elosztja az időköz hossza számít. Az Application Insights SDK ezt az eredményt gyűjt, és továbbítja őket a portálon.

* *Kivételek* száma a mintavételi időközben a diagram a portál által fogadott TrackException jelentéseket. Csak a kezelt kivételek ahol hozott TrackException meghívja a kódban, és nem tartalmazza az összes benne [nem kezelt kivételeket](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Azure Web Apps-ban futó alkalmazások számlálói

Üzembe helyezett Azure Web Apps az ASP.NET és az ASP.NET Core-alkalmazások egy speciális védőfal környezetben futnak. Ebben a környezetben nem engedélyezi a közvetlen hozzáférést a rendszerteljesítmény-számlálók. Azonban egy korlátozott részhalmaza számlálók ki vannak téve környezeti változókként leírtak szerint [Itt](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Az ASP.NET és az ASP.NET Core készült Application Insights SDK az Azure Web Apps teljesítményszámlálók gyűjti ezeket a speciális környezeti változókat. Csak egy részhalmazát számlálók érhetők el ebben a környezetben, és a teljes lista is található [itt.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Teljesítményszámlálók az ASP.NET Core-alkalmazások

* [Az ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verzió 2.4.1 és teljesítményszámlálók fent gyűjt, ha az alkalmazás fut, az Azure Web App (Windows)

* SDK verziója 2.7.0-beta3 és teljesítményszámlálók fent gyűjt, ha az alkalmazás a Windows rendszert futtató, és célcsoport-kezelési `NETSTANDARD2.0` vagy újabb verziója.
* Az alkalmazások .NET-keretrendszerre teljesítményszámlálók SDK összes verziói támogatottak.
* Ez a cikk a teljesítmény számláló támogatása a nem Windows hozzáadásakor frissül.

## <a name="alerts"></a>Riasztások
Például más metrikák is [riasztások beállítása](../../azure-monitor/app/alerts.md) figyelmezteti, ha egy teljesítményszámláló kerül kívül megadott korlátot. Nyissa meg a riasztások panelen, és kattintson a riasztás hozzáadása.

## <a name="next"></a>Következő lépések

* [Függőségi nyomkövetés](../../azure-monitor/app/asp-net-dependencies.md)
* [Kivétel követése](../../azure-monitor/app/asp-net-exceptions.md)

