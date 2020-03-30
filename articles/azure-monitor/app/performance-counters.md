---
title: Teljesítményszámlálók az Application Insightsban | Microsoft dokumentumok
description: Figyelje a rendszert és az egyéni .NET teljesítményszámlálókat az Application Insightsban.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669879"
---
# <a name="system-performance-counters-in-application-insights"></a>Rendszerteljesítmény-számlálók az Application Insightsban

A Windows [teljesítményszámlálók](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) széles választékát biztosítja, amelyek például a processzor foglaltságáról, vagy a memória-, merevlemez- és hálózathasználatról biztosítanak adatokat. Saját teljesítményszámlálókat is definiálhat. A teljesítményszámlálók gyűjtése mindaddig támogatott, amíg az alkalmazás az IIS szerint fut egy helyszíni gazdagépen vagy olyan virtuális gépen, amelyhez rendszergazdai hozzáféréssel rendelkezik. Bár az Azure Web Apps-ként futó alkalmazások nem rendelkeznek közvetlen hozzáféréssel a teljesítményszámlálókhoz, az Application Insights gyűjti a rendelkezésre álló számlálók egy részét.

## <a name="view-counters"></a>Számlálók megtekintése

A Metrikák ablaktábla a teljesítményszámlálók alapértelmezett készletét jeleníti meg.

![Az Application Insightsban jelentett teljesítményszámlálók](./media/performance-counters/performance-counters.png)

A ASP.NET/ASP.NET Core webalkalmazásokhoz begyűjtendő alapértelmezett számlálók a következők:
- %process\\processor time
- A\\processzor processzoridejének %-os normalizálása
- Rendelkezésre álló memóriabájt\\
- ASP.NET kérések/mp
- .NET CLR-kivételek másodpercenként
- ASP.NET AlkalmazásokKérelem végrehajtási ideje
- Magánbájtok feldolgozása\\
- Io-adatbájtok/mp feldolgozása\\
- ASP.NET\\alkalmazáskérelmek az alkalmazásvárólistában
- Processzor(_Total)\\processzoridő %-a

## <a name="add-counters"></a>Számlálók hozzáadása

Ha a kívánt teljesítményszámláló nem szerepel a mérőszámok listájában, hozzáadhatja azt.

1. A helyi kiszolgálón található PowerShell-paranccsal megtudhatja, hogy milyen számlálók érhetők el a kiszolgálón:

    `Get-Counter -ListSet *`

    (Lásd:) [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)
2. Nyissa meg az ApplicationInsights.config alkalmazást.

   * Ha a fejlesztés során felvette az Application Insights alkalmazást az alkalmazáshoz, szerkesztheti az ApplicationInsights.config programot a projektben, majd telepítse újra a kiszolgálókra.
3. A teljesítménygyűjtőről szóló irányelv szerkesztése:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core alkalmazások `ApplicationInsights.config`nem rendelkeznek , és így a fenti módszer nem érvényes ASP.NET Core Applications.

A szabványos és a saját maga által megvalósított számlálókat is rögzítheti. `\Objects\Processes`egy példa egy szabványos számlálóra, amely minden Windows rendszeren elérhető. `\Sales(photo)\# Items Sold`egy példa egy egyéni számlálóra, amely egy webszolgáltatásban valósítható meg.

A formátum, `\Category(instance)\Counter"`vagy a kategóriák, amelyek nem rendelkeznek példányokkal, csak `\Category\Counter`.

`ReportAs`olyan számlálónevekhez szükséges, `[a-zA-Z()/-_ \.]+` amelyek nem egyeznek - azaz olyan karaktereket tartalmaznak, amelyek nem szerepelnek a következő készletekben: betűk, kerek zárójelek, perjel, kötőjel, aláhúzás, szóköz, pont.

Ha megad egy példányt, a jelentési metrika "CounterInstanceName" dimenziójaként gyűjti a programot.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Teljesítményszámlálók gyűjtése ASP.NET webalkalmazások hoz vagy a .NET/.CORE konzolalkalmazásokhoz
A rendszer teljesítményszámlálóinak összegyűjtéséhez és az Application Insightsba való elküldéséhez módosíthatja az alábbi kódrészletet:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Vagy ugyanezt megteheti az ön által létrehozott egyéni mérőszámokkal is:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Teljesítményszámlálók gyűjtése kódban ASP.NET core webalkalmazásokhoz

Módosítsa `ConfigureServices` a `Startup.cs` metódust az osztályban az alábbiak szerint.

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

## <a name="performance-counters-in-analytics"></a>Teljesítményszámlálók az Analytics szolgáltatásban
A teljesítményszámláló-jelentéseket az [Analytics](../../azure-monitor/app/analytics.md)szolgáltatásban keresheti meg és jelenítheti meg.

A **performanceCounters** séma `category`felfedi `counter` az `instance` egyes teljesítményszámlálók nevét és nevét.  Az egyes alkalmazások telemetriai adataiban csak az adott alkalmazás számlálói jelennek meg. Például, hogy milyen számlálók állnak rendelkezésre: 

![Teljesítményszámlálók az Application Insights-elemzésben](./media/performance-counters/analytics-performance-counters.png)

("Példány" itt a teljesítményszámláló-példányra vonatkozik, nem a szerepkörre vagy a kiszolgálógép-példányra. A teljesítményszámláló-példány neve általában a folyamat vagy az alkalmazás neve szerint szegmentálja a számlálókat, például a processzoridőt.)

Az elmúlt időszakban rendelkezésre álló memória diagramjának beszerzése: 

![Memóriaidő-diagram az Application Insights-elemzésben](./media/performance-counters/analytics-available-memory.png)

Más telemetriai adatokhoz hasonlóan a `cloud_RoleInstance` **performanceCounters** is rendelkezik egy oszlopmal, amely jelzi annak a gazdakiszolgáló-példánynak az identitását, amelyen az alkalmazás fut. Például az alkalmazás teljesítményének összehasonlítása a különböző gépeken: 

![Teljesítmény szegmentált szerepkörpéldány az Application Insights analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET és az Application Insights számít

*Mi a különbség a kivételi arány és a kivételek metrikák között?*

* *A kivételi sebesség* a rendszer teljesítményszámlálója. A CLR megszámolja az összes leadott és nem kezelt kivételt, és elosztja az összeget egy mintavételi időközben az intervallum hosszával. Az Application Insights SDK összegyűjti ezt az eredményt, és elküldi azt a portálra.

* *Kivételek* a portál által a diagram mintavételi időközében kapott TrackException-jelentések száma. Csak azokat a kezelt kivételeket tartalmazza, amelyekhez TrackException hívásokat írt a kódba, és nem tartalmazza az összes [kezeletlen kivételt.](../../azure-monitor/app/asp-net-exceptions.md) 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Teljesítményszámlálók az Azure Web Apps alkalmazásokban futó alkalmazásokhoz

Az Azure Web Apps alkalmazásokba telepített ASP.NET és ASP.NET Core alkalmazások egy speciális sandbox környezetben futnak. Ez a környezet nem teszi lehetővé a rendszer teljesítményszámlálóihoz való közvetlen hozzáférést. A számlálók egy korlátozott részhalmaza azonban [az itt](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)leírt környezeti változókként jelenik meg. Az Application Insights SDK ASP.NET és ASP.NET Core számára teljesítményszámlálókat gyűjt az Azure Web Apps-ből ezekből a speciális környezeti változókból. Ebben a környezetben csak a számlálók egy része érhető el, és a teljes lista [itt található.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Teljesítményszámlálók ASP.NET alapalkalmazásokban

A core-ASP.NET teljesítményszámlálók támogatása korlátozott:

* [Az SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1-es és újabb verziói teljesítményszámlálókat gyűjtenek, ha az alkalmazás az Azure Web Apps (Windows) alkalmazásban fut.
* Az SDK 2.7.1-es és újabb verziói teljesítményszámlálókat gyűjtenek, ha az alkalmazás Windows rendszerben fut, és a célokat vagy újabb verziókat célozza `NETSTANDARD2.0` meg.
* A .
* Az SDK 2.8.0-s és újabb verziói támogatják a cpu/memória számlálót Linux alatt. A Linux nem támogat más számlálót. A rendszerszámlálók Linux (és más nem Windows-környezetekben) beszereznie ajánlott módja az [EventCounters](eventcounters.md) használata

## <a name="alerts"></a>Riasztások
Más mutatókhoz hasonlóan [beállíthat egy riasztást,](../../azure-monitor/app/alerts.md) amely figyelmezteti, ha egy teljesítményszámláló kívül esik a megadott korláton. Nyissa meg a Riasztások ablaktáblát, és kattintson a Riasztás hozzáadása gombra.

## <a name="next-steps"></a><a name="next"></a>További lépések

* [Függőségek nyomon követése](../../azure-monitor/app/asp-net-dependencies.md)
* [Kivételkövetés](../../azure-monitor/app/asp-net-exceptions.md)

