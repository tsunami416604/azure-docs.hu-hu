---
title: Függőségi nyomkövetés az Azure Application Insights |} A Microsoft Docs
description: Függőségi hívások figyelése a helyszíni vagy a Microsoft Azure webes alkalmazás az Application insights segítségével.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565373"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Függőségi nyomkövetés az Azure Application insights szolgáltatásban 

A *függőségi* egy külső összetevő, amely hívja meg az alkalmazást. Fontos általában nevű HTTP-n vagy egy adatbázist vagy egy fájlrendszert használó szolgáltatás. [Az Application Insights](../../azure-monitor/app/app-insights-overview.md) függőségi hívások időtartama e méri a sikertelen-e, illetve olyan kiegészítő információkkal, például a név függőség, és így tovább. Vizsgálja meg az adott függőségi hívások, és a kérések és kivételek összefüggésbe hozva azokat.

## <a name="automatically-tracked-dependencies"></a>Automatikusan követi függőségek

Application Insights SDK-k a .NET és .NET Core keretrendszerhez tartozik `DependencyTrackingTelemetryModule` által automatikusan gyűjtött függőségek Telemetriai modulból. Ez a függőségi gyűjtemény automatikusan engedélyezve van a [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) alkalmazásokat, ha a társított hivatalos docs megfelelően konfigurálva. `DependencyTrackingTelemetryModule` tartalmazza a szükséges [ez](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-csomagot, és automatikusan állapotúra vagy a NuGet-csomagok használata esetén `Microsoft.ApplicationInsights.Web` vagy `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` jelenleg követi automatikusan a következő függőségeket:

|Függőségek |Részletek|
|---------------|-------|
|Http/Https | A helyi vagy távoli http/https-hívások |
|WCF-hívások| Csak automatikusan rögzíti a Http-alapú kötések használata.|
|SQL | A hívások `SqlClient`. Lásd: [ez](##advanced-sql-tracking-to-get-full-sql-query) lekérdezése SQL rögzítéséhez.  |
|[Az Azure storage (Blob, tábla, üzenetsor)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-kliens indított hívások. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0-s verzió vagy újabb verzió. |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0-s verziójával vagy újabb verzió. |
|Azure Cosmos DB | Csak automatikusan rögzíti a HTTP/HTTPS használata esetén. Az Application Insights nem rögzíti a TCP-módot. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Automatikus függőségi Konzolalkalmazással a nyomkövetés beállítása

Automatikusan nyomon függőségek.NET/.NET Core-konzolalkalmazások, telepítse a Nuget-csomag `Microsoft.ApplicationInsights.DependencyCollector`, és inicializálnia `DependencyTrackingTelemetryModule` módon:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Automatikus függőség figyelése működik?

A következő módszerek egyikét használva automatikusan gyűjtött függőségek:

* Válassza módszerek köré bájt kód instrumentation használatával. (InstrumentationEngine StatusMonitor, vagy az Azure webalkalmazás-bővítmény)
* EventSource visszahívások
* DiagnosticSource visszahívások (a legújabb.NET/.NET Core SDK-k)

## <a name="manually-tracking-dependencies"></a>Manuálisan követési függőségek

Az alábbiakban néhány példa a függőségeket, amely nem automatikusan gyűjteni, és ezért a manuális követési megkövetelése.

* Az Azure Cosmos DB automatikusan rögzíti csak akkor, ha [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) szolgál. Az Application Insights nem rögzíti a TCP-módot.
* Redis

Az SDK által automatikusan összegyűjtött ezeket a függőségeket, ezeket manuálisan segítségével követheti a [TrackDependency API](api-custom-events-metrics.md#trackdependency) a standard szintű automatikus adatgyűjtő modulok által használt.

Ha a kódot készíthet, amelyeket Ön nem írt szerelvény, például sikerült, minden hívás ismerje meg, milyen közreműködői adatbeolvasási a válaszidők az időt. Ahhoz, hogy ezek az adatok az Application Insights függőségi diagramjain jelennek meg, küldje el használatával `TrackDependency`.

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

Azt is megteheti `TelemetryClient` bővítő metódusokat biztosít `StartOperation` és `StopOperation` amely segítségével manuálisan nyomon követheti a függőségeket, ahogy [Itt](custom-operations-tracking.md#outgoing-dependencies-tracking)

Ha azt szeretné, hogy kikapcsolja a standard szintű függőségi követési modul, távolítsa el a hivatkozást a DependencyTrackingTelemetryModule [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) az ASP.NET-alkalmazások. ASP.NET Core-alkalmazások, kövesse az utasításokat [Itt](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Weboldalakról AJAX-hívások nyomon követése

Weblapok, az Application Insights JavaScript SDK automatikusan gyűjti AJAX-hívások függőségekként leírtak szerint [Itt](javascript.md#ajax-performance). Ez a dokumentum a kiszolgáló-összetevők függőségei összpontosít.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Az SQL változáskövetési beolvasni a teljes SQL-lekérdezést speciális

Az SQL-hívás, a kiszolgáló és az adatbázis neve van mindig összegyűjtött és tárolt, az összegyűjtött neveként `DependencyTelemetry`. Nincs "adatok", amely a teljes SQL-lekérdezés szövege nevű további mezőket.

Az ASP.NET Core-alkalmazások esetében nem nincsenek további lépés szükséges a teljes SQL-lekérdezést.

Az ASP.NET-alkalmazások teljes SQL-lekérdezést igényel a rendszerállapot-motor bájt kód instrumentation segítségével gyűjti. Egyes platformokra vonatkozó további lépéseket, az alábbiakban leírtak szükség.

| Platform | Teljes SQL-lekérdezést szükség lépés(ek) |
| --- | --- |
| Azure Web App |A WebApp Vezérlőpultját, a [nyissa meg az Application Insights paneljén](../../azure-monitor/app/azure-web-apps.md) , és engedélyezze a .NET SQL-parancsok |
| IIS-kiszolgáló (az Azure virtuális gép, helyszíni stb.) | [Az alkalmazást futtató kiszolgálón telepítse az Állapotfigyelőt](../../azure-monitor/app/monitor-performance-live-website-now.md) , és indítsa újra az IIS.
| Azure Cloud Service | Adjon hozzá [indítási feladat StatusMonitor telepítése](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Az alkalmazás lehet applicationinsights – SDK-ra felkészített időben NuGet-csomag telepítésével [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) vagy [ASP.NET Core-alkalmazások](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| Az IIS Express | Nem támogatott

A fenti esetekben a megfelelő módszer annak ellenőrzése, hogy a rendszerállapot-motor megfelelően telepítve van ellenőrzi, hogy az SDK-verziója, az összegyűjtött `DependencyTelemetry` "rddp" van. "rdddsd" vagy "rddf" jelzi a függőségek DiagnosticSource vagy EventSource visszahívások keresztül gyűjteni, és ezért a teljes SQL-lekérdezést nem lehetett rögzíteni.

## <a name="where-to-find-dependency-data"></a>Hol található a függőségi adatokat

* [Alkalmazás-hozzárendelés](app-map.md) megjeleníti az alkalmazás és a szomszédos összetevők közötti függőségek.
* [Tranzakció diagnosztikája](transaction-diagnostics.md) jeleníti meg az egységes, korrelált server-adatok.
* [Böngészők lap](javascript.md#ajax-performance) jeleníti meg a felhasználók böngészőinek AJAX-hívások.
* Kattintson végig ellenőrizze azok függőségi hívásaihoz lassú vagy hibás kérésekből származó.
* [Analytics](#logs-analytics) használható függőségi adatokat lekérdezni.

## <a name="diagnosis"></a> Lassú kérelmek diagnosztizálása

Minden egyes kérés esemény társítva a függőségi hívások, kivételeket és eseményeket, amelyek a rendszer nyomon követi, amíg az alkalmazás a kérés feldolgozása folyamatban van. Tehát ha az egyes kérések rosszul végeznek, annak meg, hogy van-e lassú válaszai a függőség miatt.

### <a name="tracing-from-requests-to-dependencies"></a>A kérelmek, a függőségek nyomon követése

Nyissa meg a **teljesítmény** lapra, és keresse meg a **függőségek** műveletek mellett a felső fülön.

Kattintson a egy **függőség neve** teljes alatt. Miután kiválasztotta a függőség, időtartamok megoszlása a függőségi grafikon a jobb oldalon jelennek meg.

![A függőségi lapon, majd egy függőség neve a diagram tetején kattintson fülre a teljesítmény](./media/asp-net-dependencies/2-perf-dependencies.png)

Kattintson a kék a **minták** gombra a jobb alsó, majd a minta a végpontok közötti tranzakció részleteinek megtekintéséhez.

![Kattintson ide a részletek a végpontok közötti tranzakció minta](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Az élő webhelyet profil

Nem tudja, ahol az idő halad? A [Application Insights profiler](../../azure-monitor/app/profiler.md) nyomkövetések HTTP meghívja a élő webhelyre, és megjeleníti a függvények a kódban, amely a leghosszabb időt vett igénybe.

## <a name="failed-requests"></a>Sikertelen kérelmek

Sikertelen kérelmek is lehet társítva, a függőségek hívásainak sikertelen.

Megnyithatja azt a **hibák** a bal oldali lapon, majd kattintson a a **függőségek** a felső fülön.

![Kattintson a sikertelen kérelmeit tartalmazó diagram](./media/asp-net-dependencies/4-fail.png)

Itt fogja látni a sikertelen függőségek száma. Egy kísérlet az alsó táblázaton a függőségi nevére kattintva sikertelen előfordulás kapcsolatos további részletekért. A kék kattintva **függőségek** a végpontok közötti tranzakció részleteinek jobb alsó gombra.

## <a name="logs-analytics"></a>Naplók (Analytics)

Nyomon követheti a függőségeket a [Kusto-lekérdezés nyelvi](/azure/kusto/query/). Néhány példa:

* Keresse meg az összes sikertelen függőségi hívások:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Keresse meg az AJAX-hívások:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Függőségi hívások kérelmekhez keresése:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Keresés AJAX-hívások lapmegtekintés társított:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hogyan biztosítja az automatikus függőségi gyűjtő jelentést nem sikerült, a függőségek hívásainak?*

* Sikertelen függőségi hívások lesz a "sikeres" mező "false" értékűre. `DependencyTrackingTelemetryModule` nem készít jelentést `ExceptionTelemetry`. A teljes adatmodellt Pro závislost leírt [Itt](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Open-source SDK
Minden Application Insights SDK-t, például függőségi gyűjtő moduljának akkor is nyílt forráskódú. Olvassa el és járulnak hozzá a kódot, vagy a problémák jelentése [a hivatalos GitHub-adattárat](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>További lépések

* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md)
* [Felhasználók és Lapadatok](../../azure-monitor/app/javascript.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
