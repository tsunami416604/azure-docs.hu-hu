---
title: Függőségek nyomon követése az Azure Application Insightsban | Microsoft dokumentumok
description: Az Application Insights segítségével figyelheti a helyszíni vagy a Microsoft Azure webalkalmazásból érkező függőségi hívásokat.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1d4e8d1a0482257c92f47a00bd440e786c09c7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292126"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Függőségek nyomon követése az Azure Application Insightsban 

A *függőség* egy külső összetevő, amelyet az alkalmazás hív meg. Ez általában egy HTTP,, adatbázis vagy fájlrendszer használatával hívott szolgáltatás. [Az Application Insights](../../azure-monitor/app/app-insights-overview.md) méri a függőségi hívások időtartamát, függetlenül attól, hogy sikertelen-e vagy sem, valamint további információkat, például a függőség nevét és így tovább. Megvizsgálhat bizonyos függőségi hívásokat, és összefüggésbe hozhatja őket a kérésekkel és kivételekkel.

## <a name="automatically-tracked-dependencies"></a>Automatikusan nyomon követett függőségek

Application Insights SDK-k .NET és `DependencyTrackingTelemetryModule` .NET Core hajók, amely egy telemetriai modul, amely automatikusan gyűjti a függőségeket. Ez a függőségi gyűjtemény automatikusan engedélyezve van [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) alkalmazások, ha a csatolt hivatalos dokumentumok szerint konfigurálva. `DependencyTrackingTelemetryModule` [nuget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) csomagként szállítják, és automatikusan megjelenik, ha a `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore`NuGet csomagok vagy a .

 `DependencyTrackingTelemetryModule`jelenleg automatikusan követi a következő függőségeket:

|Függőségek |Részletek|
|---------------|-------|
|http/https | Helyi vagy távoli http/https hívások |
|WCF-hívások| Csak http-alapú kötések használata esetén követi automatikusan a nyomon követést.|
|SQL | A segítségével `SqlClient`kezdeményezett hívások Tekintse meg [ezt](#advanced-sql-tracking-to-get-full-sql-query) az SQL-lekérdezés rögzítéséhez.  |
|[Azure-tárhely (Blob, Tábla, Várólista)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-ügyféllel kezdeményezett hívások. |
|[Eventhub ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0-s vagy újabb verzió. |
|[ServiceBus ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3.0.0-s vagy újabb verzió. |
|Azure Cosmos DB | Csak http/HTTPS protokoll használatával automatikusan követi nyomon. A TCP-módot az Application Insights nem rögzíti. |

Ha hiányzik egy függőség, vagy egy másik SDK-t használ, győződjön meg arról, hogy az [automatikusan gyűjtött függőségek](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)listájában szerepel. Ha a függőség nem automatikusan gyűjtött, akkor is nyomon követheti manuálisan a [pálya függőségi hívás.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Automatikus függőségkövetés beállítása a konzolalkalmazásokban

A .NET konzolalkalmazások függőségeiautomatikus nyomon követéséhez telepítse a Nuget csomagot `Microsoft.ApplicationInsights.DependencyCollector`, és inicializálja `DependencyTrackingTelemetryModule` az alábbiak szerint:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

A .NET Core konzolalkalmazások telemetryConfiguration.Active elavult. Tekintse meg a [munkavállalói szolgáltatás dokumentációjában](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) és a [ASP.NET Core monitoring dokumentációban](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) található útmutatást

### <a name="how-automatic-dependency-monitoring-works"></a>Hogyan működik az automatikus függőségfigyelés?

A függőségek automatikusan gyűjtésre kerülnek az alábbi technikák egyikével:

* Byte-kód instrumentation használata a kiválasztott módszerek körül. (InstrumentationEngine vagy statusmonitorról vagy Az Azure Web App Extension-ből)
* EventSource visszahívások
* DiagnosticSource visszahívások (a legújabb .NET/.NET core SDK-kban)

## <a name="manually-tracking-dependencies"></a>Függőségek manuális nyomon követése

Az alábbiakban néhány példa a függőségek, amelyek nem automatikusan gyűjtik, és így manuális nyomon követést igényel.

* Az Azure Cosmos DB automatikusan csak akkor követi nyomon, ha [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) van használva. A TCP-módot az Application Insights nem rögzíti.
* Redis

Az SDK által automatikusan nem gyűjtött függőségek esetében manuálisan nyomon követheti őket a szabványos automatikus gyűjtési modulok által használt [TrackDependency API használatával.](api-custom-events-metrics.md#trackdependency)

Ha például olyan kódösszeállítással hozza létre a kódot, amelyet nem ön írt, az összes hívás átfutásával megtudhatja, hogy milyen mértékben járul hozzá a válaszidőkhöz. Ha azt szeretné, hogy ezek az adatok megjelenjenek `TrackDependency`az Application Insights függőségi diagramjaiban, küldje el őket a használatával.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Másik lehetőségként `TelemetryClient` bővítménymódszereket `StartOperation` is biztosít, amelyek `StopOperation` a függőségek manuális nyomon követésére használhatók, amint az [itt](custom-operations-tracking.md#outgoing-dependencies-tracking) látható

Ha ki szeretné kapcsolni a szabványos függőség-követési modult, távolítsa el a függőségitkövetőtelemetelemet az [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ASP.NET alkalmazáshoz. Az ASP.NET alapalkalmazásokhoz kövesse [az itt](asp-net-core.md#configuring-or-removing-default-telemetrymodules)található utasításokat.

## <a name="tracking-ajax-calls-from-web-pages"></a>Ajax hívások nyomon követése weboldalakról

Weboldalak esetén az Application Insights JavaScript SDK automatikusan összegyűjti az AJAX-hívásokat függőségként.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Speciális SQL-követés a teljes SQL-lekérdezés lehívásához

SQL-hívások esetén a kiszolgáló és az adatbázis nevét mindig a `DependencyTelemetry`rendszer az összegyűjtött névként gyűjti és tárolja. Van egy "data" nevű további mező, amely a teljes SQL-lekérdezés szövegét is tartalmazhatja.

A ASP.NET Core alkalmazások, nincs további lépés szükséges a teljes SQL-lekérdezés beszerezni.

A ASP.NET alkalmazások esetében a teljes SQL-lekérdezés bájtkód-műszerezés segítségével történik, amely műszerezési motort igényel. További platformspecifikus lépésekre van szükség az alábbiakban leírtak szerint.

| Platform | A teljes SQL-lekérdezés bekerüléséhez szükséges lépés(ek) |
| --- | --- |
| Azure-webalkalmazás |A webalkalmazás vezérlőpultján [nyissa meg az Application Insights panelt,](../../azure-monitor/app/azure-web-apps.md) és engedélyezze az SQL-parancsokat a .NET |
| IIS-kiszolgáló (Azure Virtuális gép, on-prem és így tovább.) | Az Állapotfigyelő PowerShell-modul segítségével [telepítse a Műszerezési motort,](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) és indítsa újra az IIS-t. |
| Azure-felhőszolgáltatás | Indítási feladat hozzáadása [a StatusMonitor telepítéséhez](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Az alkalmazást buildeléskor kell beépíteni az ApplicationInsights SDK-ba a [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) vagy [ASP.NET Core alkalmazások](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) NuGet-csomagjainak telepítésével |
| IIS Expressz | Nem támogatott

A fenti esetekben a műszerezési motor helyes beszerelésének helyes módja annak ellenőrzése, hogy `DependencyTelemetry` az összegyűjtött SDK-változat "rddp"-es. Az "rdddsd" vagy az "rddf" azt jelzi, hogy a függőségek gyűjtése a DiagnosticSource vagy az EventSource visszahívásokon keresztül történik, így a teljes SQL-lekérdezés nem lesz rögzítve.

## <a name="where-to-find-dependency-data"></a>Hol találhatók a függőségi adatok?

* [Az Alkalmazástérkép](app-map.md) megjeleníti az alkalmazás és a szomszédos összetevők közötti függőségeket.
* [A Tranzakciódiagnosztika](transaction-diagnostics.md) egységes, korrelált kiszolgálóadatokat jelenít meg.
* [A böngészők lapon](javascript.md) az AJAX-hívások láthatók a felhasználók böngészőiből.
* Kattintson át a lassú vagy sikertelen kérelmeket, hogy ellenőrizze a függőségi hívásokat.
* [Az Analytics](#logs-analytics) a függőségi adatok lekérdezésére használható.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Lassú kérések diagnosztizálása

Minden kérelemesemény a függőségi hívásokhoz, kivételekhez és egyéb eseményekhez van társítva, amelyeket az alkalmazás a kérelem feldolgozása során nyomon követ. Így ha egyes kérelmek rosszul teljesítenek, megtudhatja, hogy ez a függőségből származó lassú válaszok miatt van-e.

### <a name="tracing-from-requests-to-dependencies"></a>Nyomkövetés a kérelmektől a függőségekig

Nyissa meg a **Teljesítmény** lapot, és keresse meg a Műveletek melletti Legfelső lap **Függőségek** lapját.

Kattintson a **függőség igéző neve** alatt. Miután kiválasztotta a függőséget, a függőség időtartamának eloszlása grafikonjelenik meg a jobb oldalon.

![A teljesítmény lapon kattintson a függőség fülre a diagram tetején, majd egy függőségi névre a diagramon.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kattintson a kék **minták** gombra a jobb alsó sarokban, majd egy mintán, hogy láthassa a tranzakció végpontok utáni részleteit.

![Kattintson egy mintára a tranzakció végpontok között](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Az élő webhely profilozása

Nem tudod, hová megy az idő? Az [Application Insights profilozó](../../azure-monitor/app/profiler.md) nyomon követi a HTTP-hívásokat az élő webhelyre, és megmutatja a függvényeket a kódban, amely a leghosszabb időt vett igénybe.

## <a name="failed-requests"></a>Sikertelen kérelmek

A sikertelen kérelmek függőségek sikertelen hívásaival is társíthatók.

A bal oldalon a **Hibák** lapra léphetünk, majd a felső sarokban található **függőségek** fülre kattinthatunk.

![Kattintson a sikertelen kérelmek diagramjára](./media/asp-net-dependencies/4-fail.png)

Itt láthatja a sikertelen függőségek számát. Ha további részleteket szeretne megtudni egy sikertelen előfordulásról, amely egy függőségi névre kattint az alsó táblázatban. A jobb alsó sarokban található kék **Függőségek** gombra kattintva megkaphatja a teljes tranzakció részleteit.

## <a name="logs-analytics"></a>Naplók (Analytics)

A függőségeket a [Kusto lekérdezési nyelvén](/azure/kusto/query/)követheti nyomon. Néhány példa:

* Sikertelen függőségi hívások keresése:

``` Kusto

    dependencies | where success != "True" | take 10
```

* AJAX-hívások keresése:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Kérelmekhez társított függőségi hívások keresése:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Az oldalmegtekintésekhez társított AJAX-hívások keresése:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hogyan történik az automatikus függőséggyűjtő jelentése a függőségek hívásaiban?*

* A sikertelen függőségi hívások "sikeres" mezőjének értéke Hamis lesz. `DependencyTrackingTelemetryModule`nem jelent `ExceptionTelemetry`jelentést . A függőség teljes adatmodelljét [itt](data-model-dependency-telemetry.md)ismerteti.

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK
Mint minden Application Insights SDK, függőségi gyűjtemény modul is nyílt forráskódú. Olvassa el és járuljon hozzá a kódhoz, vagy jelentse a problémákat [a hivatalos GitHub-tárban.](https://github.com/Microsoft/ApplicationInsights-dotnet-server)

## <a name="next-steps"></a>További lépések

* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md)
* [A felhasználói & oldaladatokat](../../azure-monitor/app/javascript.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
