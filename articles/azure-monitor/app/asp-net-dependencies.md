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
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299281"
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
| Azure Cloud Service |[Használat indítási feladat](../../azure-monitor/app/cloudservices.md) való [állapotfigyelő telepítése](monitor-performance-live-website-now.md#download) |
| Az IIS Express | Nem támogatott

A fenti esetekben a megfelelő módszer annak ellenőrzése, hogy a rendszerállapot-motor megfelelően telepítve van ellenőrzi, hogy az SDK-verziója, az összegyűjtött `DependencyTelemetry` "rddp" van. "rdddsd" vagy "rddf" jelzi a függőségek DiagnosticSource vagy EventSource visszahívások keresztül gyűjteni, és ezért a teljes SQL-lekérdezést nem lehetett rögzíteni.

## <a name="where-to-find-dependency-data"></a>Hol található a függőségi adatokat

* [Alkalmazás-hozzárendelés](app-map.md) megjeleníti az alkalmazás és a szomszédos összetevők közötti függőségek.
* [Tranzakció diagnosztikája](transaction-diagnostics.md) jeleníti meg az egységes, korrelált server-adatok.
* [Böngészők panelen](javascript.md#ajax-performance) jeleníti meg a felhasználók böngészőinek AJAX-hívások.
* Kattintson végig ellenőrizze azok függőségi hívásaihoz lassú vagy hibás kérésekből származó.
* [Analytics](#analytics) használható függőségi adatokat lekérdezni.

## <a name="diagnosis"></a> Lassú kérelmek diagnosztizálása

Minden egyes kérés esemény társítva a függőségi hívások, kivételeket és eseményeket, amelyek a rendszer nyomon követi, amíg az alkalmazás a kérés feldolgozása folyamatban van. Tehát ha az egyes kérések rosszul végeznek, annak meg, hogy van-e lassú válaszai a függőség miatt.

Nézzük meg, amely egy példát.

### <a name="tracing-from-requests-to-dependencies"></a>A kérelmek, a függőségek nyomon követése

A teljesítmény panel megnyitásához, és tekintse meg a rács kérések:

![Kéréseit átlagok és száma](./media/asp-net-dependencies/02-reqs.png)

A felső egy hosszú időt vesz igénybe. Nézzük meg, ha azt is megtudhatja, hol a telik el.

Kattintson az egyéni lekérési események megtekintéséhez a sorhoz:

![A kérelem események listája](./media/asp-net-dependencies/03-instances.png)

Kattintson a tovább vizsgálja meg, és görgessen le a távoli függőségi hívás ehhez a kérelemhez kapcsolódó bármely hosszú ideig futó példány:

![Távoli függőségekhez intézett hívások keresse meg, azonosíthatja a szokatlan időtartama](./media/asp-net-dependencies/04-dependencies.png)

A kérelem egy hívás egy helyi service telt karbantartási idő a legtöbb tűnik.

További információért a sorhoz kiválasztása:

![Kattintson végig a távoli függőség nem sokkal azonosításához](./media/asp-net-dependencies/05-detail.png)

Úgy tűnik, a függőség, ahol a probléma van. Mi a probléma már pinpointed, tehát most sínen szeretné tudni, miért Ez a meghívás tart sokáig.

### <a name="request-timeline"></a>Kérelem ütemterv

Más esetben nem nincs függőségi hívás, amely túl hosszú. De között idősor nézetének, láthatjuk, ahol a késés a belső feldolgozási történt:

![Távoli függőségekhez intézett hívások keresse meg, azonosíthatja a szokatlan időtartama](./media/asp-net-dependencies/04-1.png)

Úgy tűnik, hogy nagy eseményáramlási kimaradást követően az első függőség hívása, így a kódban, hogy miért ez megnézzük kell.

### <a name="profile-your-live-site"></a>Az élő webhelyet profil

Nem tudja, ahol az idő halad? A [Application Insights profiler](../../azure-monitor/app/profiler.md) nyomkövetések HTTP meghívja a élő webhelyre, és megjeleníti a függvények a kódban, amely a leghosszabb időt vett igénybe.

## <a name="failed-requests"></a>Sikertelen kérelmek

Sikertelen kérelmek is lehet társítva, a függőségek hívásainak sikertelen. Ismét hogy végigkattinthat nyomon követheti a problémát.

![Kattintson a sikertelen kérelmeit tartalmazó diagram](./media/asp-net-dependencies/06-fail.png)

Kattintson végig a sikertelen kérelmek előfordulása, és tekintse meg a kapcsolódó eseményeket.

![Kattintson egy kérés típusa, a másik nézet ugyanazon-példány beolvasásához, kattintson rá a kivétel részletes-példány.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Elemzés

Nyomon követheti a függőségeket a [Kusto-lekérdezés nyelvi](/azure/kusto/query/). Néhány példa:

* Keresse meg az összes sikertelen függőségi hívások:

```

    dependencies | where success != "True" | take 10
```

* Keresse meg az AJAX-hívások:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Függőségi hívások kérelmekhez keresése:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Keresés AJAX-hívások lapmegtekintés társított:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hogyan biztosítja az automatikus függőségi gyűjtő jelentést nem sikerült, a függőségek hívásainak?*

* Sikertelen függőségi hívások lesz a "sikeres" mező "false" értékűre. `DependencyTrackingTelemetryModule` nem készít jelentést `ExceptionTelemetry`. A teljes adatmodellt Pro závislost leírt [Itt](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Open-source SDK
Minden Application Insights SDK-t, például függőségi gyűjtő moduljának akkor is nyílt forráskódú. Olvassa el és járulnak hozzá a kódot, vagy a problémák jelentése [a hivatalos GitHub-adattárat](https://github.com/Microsoft/ApplicationInsights-dotnet-server).


## <a name="next-steps"></a>További lépések

* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md)
* [Felhasználók és Lapadatok](../../azure-monitor/app/javascript.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
