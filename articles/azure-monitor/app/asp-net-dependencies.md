---
title: Függőségi nyomkövetés az Azure Application Insights |} A Microsoft Docs
description: A helyszíni vagy Microsoft Azure webalkalmazástól származó függőségi hívások figyelése Application Insightsokkal.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 8fb1550a3f1d4b3336384139b049b60e23e648d7
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666241"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Függőségek nyomon követése az Azure Application Insights 

A *függőség* egy külső összetevő, amelyet az alkalmazás meghív. Fontos általában nevű HTTP-n vagy egy adatbázist vagy egy fájlrendszert használó szolgáltatás. [Application Insights](../../azure-monitor/app/app-insights-overview.md) méri a függőségi hívások időtartamát, függetlenül attól, hogy a meghibásodása vagy sem, valamint további információk, például a függőség neve és így tovább. Megvizsgálhat bizonyos függőségi hívásokat, és összekapcsolhatja őket a kérelmekkel és a kivételekkel.

## <a name="automatically-tracked-dependencies"></a>Automatikusan követett függőségek

Application Insights SDK-kat a .NET-és .NET Core-hajókhoz `DependencyTrackingTelemetryModule` amely egy telemetria modul, amely automatikusan gyűjti a függőségeket. Ez a függőségi gyűjtemény automatikusan engedélyezve van a [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és a [ASP.net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) alkalmazásokhoz, ha a társított hivatalos dokumentumokhoz van konfigurálva. `DependencyTrackingTelemetryModule` a rendszer [ezt](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) a NuGet-csomagot használja, és automatikusan, a NuGet-csomagok egyikének használatakor `Microsoft.ApplicationInsights.Web` vagy `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` jelenleg automatikusan nyomon követi a következő függőségeket:

|Függőségek |Részletek|
|---------------|-------|
|HTTP/HTTPS | Helyi vagy távoli HTTP/HTTPS-hívások |
|WCF-hívások| Csak automatikusan nyomon követhető, ha HTTP-alapú kötések vannak használatban.|
|SQL | `SqlClient`ekkel végzett hívások. [Tekintse meg az SQL](#advanced-sql-tracking-to-get-full-sql-query) -lekérdezés rögzítését ismertető témakört.  |
|[Azure Storage (blob, tábla, üzenetsor)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-ügyféllel kezdeményezett hívások. |
|[EventHub ügyfél-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | A 1.1.0 vagy újabb verzió. |
|[ServiceBus ügyfél-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 vagy újabb verzió. |
|Azure Cosmos DB | Csak a HTTP/HTTPS használata esetén automatikusan nyomon követhető. Az Application Insights nem rögzíti a TCP-módot. |

Ha hiányzik egy függőség, vagy egy másik SDK-t használ, győződjön meg róla, hogy az [automatikusan összegyűjtött függőségek](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)listáján szerepel. Ha a függőség nincs automatikusan begyűjtve, nyomon követheti manuálisan a [függőségi hívással](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Automatikus függőség-követés beállítása a konzolos alkalmazásokban

A .NET/.NET Core Console-alkalmazások függőségeinek automatikus nyomon követéséhez telepítse a Nuget csomagot `Microsoft.ApplicationInsights.DependencyCollector`, és inicializálja `DependencyTrackingTelemetryModule` a következőképpen:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Hogyan működik az automatikus függőség-figyelés?

A függőségeket a rendszer a következő módszerek egyikének használatával automatikusan gyűjti:

* Byte Code Instrumentation használata a Select metódusok köré. (InstrumentationEngine a StatusMonitor vagy az Azure Web App Extension-ből)
* EventSource visszahívások
* DiagnosticSource-visszahívások (a legújabb .NET/.NET Core SDK-k)

## <a name="manually-tracking-dependencies"></a>Függőségek manuális követése

Az alábbiakban néhány példát láthat a függőségekről, amelyeket a rendszer nem gyűjt automatikusan, és így manuális követést igényel.

* A Azure Cosmos DB automatikusan nyomon követhető, ha a rendszer [HTTP/HTTPS protokollt](../../cosmos-db/performance-tips.md#networking) használ. Az Application Insights nem rögzíti a TCP-módot.
* Redis

Az SDK által automatikusan összegyűjtött függőségek esetében manuálisan nyomon követheti azokat a [TRACKDEPENDENCY API](api-custom-events-metrics.md#trackdependency) -val, amelyet a standard automatikus gyűjtemény moduljai használnak.

Ha a kódot készíthet, amelyeket Ön nem írt szerelvény, például sikerült, minden hívás ismerje meg, milyen közreműködői adatbeolvasási a válaszidők az időt. Ha meg szeretné jeleníteni ezeket az adataikat a Application Insights függőségi diagramokban, küldje el a `TrackDependency`használatával.

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

Azt is megteheti, `TelemetryClient` olyan bővítményi metódusokat is biztosít `StartOperation` és `StopOperation` amelyek használatával manuálisan követheti a függőségeket, ahogy az [itt](custom-operations-tracking.md#outgoing-dependencies-tracking) látható

Ha ki szeretné kapcsolni a normál függőség-követési modult, távolítsa el a ASP.NET-alkalmazások [ApplicationInsights. config fájljában](../../azure-monitor/app/configuration-with-applicationinsights-config.md) található DependencyTrackingTelemetryModule mutató hivatkozást. ASP.NET Core alkalmazásokhoz kövesse az alábbi [utasításokat.](asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="tracking-ajax-calls-from-web-pages"></a>AJAX-hívások követése weblapokról

Weblapok esetében Application Insights JavaScript SDK automatikusan függőségként gyűjti az AJAX-hívásokat.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Részletes SQL-követés a teljes SQL-lekérdezés beszerzéséhez

SQL-hívások esetén a kiszolgáló és az adatbázis neve mindig az összegyűjtött `DependencyTelemetry`neve szerint lesz összegyűjtve és tárolva. Van egy "adat" nevű további mező, amely a teljes SQL-lekérdezési szöveget tartalmazhatja.

ASP.NET Core alkalmazások esetében nincs szükség további lépésekre a teljes SQL-lekérdezés beszerzéséhez.

A ASP.NET-alkalmazások esetében a teljes SQL-lekérdezést a rendszer a byte Code Instrumentation segítségével gyűjti össze, amelyhez rendszerállapot-kezelő motor szükséges. Az alább leírtak szerint további platform-specifikus lépések szükségesek.

| Platform | A teljes SQL-lekérdezés beolvasásához szükséges lépés (ek) |
| --- | --- |
| Azure-webalkalmazás |A webalkalmazás-Vezérlőpulton [nyissa meg a Application Insights](../../azure-monitor/app/azure-web-apps.md) panelt, és engedélyezze az SQL-parancsokat a .net alatt. |
| IIS-kiszolgáló (Azure-beli virtuális gép, helyszíni stb.) | A Állapotmonitor PowerShell-modullal [telepítse a Instrumentation-motort](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) , és indítsa újra az IIS-t. |
| Azure-felhőszolgáltatás | [Indítási feladat hozzáadása a StatusMonitor telepítéséhez](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Az alkalmazást a NuGet-csomagok [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) vagy [ASP.net Core alkalmazások](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) számára történő telepítésével kell előkészíteni a ApplicationInsights SDK-ra. |
| IIS Express | Nem támogatott

A fenti esetekben a rendszerállapot-kezelő motor megfelelő ellenőrzésének helyes módszere az, ha ellenőrzi, hogy az összegyűjtött `DependencyTelemetry` SDK-verziója "rddp". a "rdddsd" vagy a "rddf" érték azt jelzi, hogy a függőségek gyűjtése DiagnosticSource vagy EventSource visszahívásokon keresztül történik, így a teljes SQL-lekérdezés nem lesz rögzítve.

## <a name="where-to-find-dependency-data"></a>Hol található a függőségi adatokat

* Az [alkalmazás-Térkép](app-map.md) megjeleníti az alkalmazás és a szomszédos összetevők közötti függőségeket.
* A [tranzakciós diagnosztika](transaction-diagnostics.md) az egyesített, korrelált kiszolgáló adatait jeleníti meg.
* A [böngészők lapon](javascript.md) Ajax-hívásokat láthat a felhasználói böngészőkből.
* A függőségi hívások vizsgálatához kattintson a lassú vagy sikertelen kérelmekre.
* Az [elemzési szolgáltatás](#logs-analytics) a függőségi adatlekérdezéshez használható.

## <a name="diagnosis"></a>Lassú kérelmek diagnosztizálása

Minden kérési esemény társítva van a függőségi hívások, a kivételek és az alkalmazás által a kérelem feldolgozásakor követett egyéb eseményekhez. Ha azonban bizonyos kérések helytelenek, megtudhatja, hogy a függőség miatt lassú válasz-e.

### <a name="tracing-from-requests-to-dependencies"></a>A kérelmek, a függőségek nyomon követése

Nyissa meg a **teljesítmény** lapot, és navigáljon a műveletek lap tetején található **függőségek** lapra.

Kattintson a **függőség nevére** a teljes területen. Miután kiválasztotta a függőség eloszlásának diagramját, a jobb oldalon jelennek meg.

![A teljesítmény lapon kattintson a felső függőség fülre, majd a diagramon a függőség neve elemre.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kattintson a jobb alsó sarokban található kék **minták** gombra, majd egy mintán a végpontok közötti tranzakció részleteinek megjelenítéséhez.

![Kattintson a mintára a végpontok közötti tranzakció részleteinek megtekintéséhez](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Az élő webhelyet profil

Nem tudja, ahol az idő halad? A [Application Insights PROFILER](../../azure-monitor/app/profiler.md) http-hívásokat végez az élő webhelyhez, és megjeleníti a kód azon funkcióit, amelyek a leghosszabb időt vettek igénybe.

## <a name="failed-requests"></a>Sikertelen kérelmek

Sikertelen kérelmek is lehet társítva, a függőségek hívásainak sikertelen.

Lépjen a **hibák** lapra a bal oldalon, majd kattintson a felül található **függőségek** fülre.

![Kattintson a sikertelen kérelmeit tartalmazó diagram](./media/asp-net-dependencies/4-fail.png)

Itt láthatja a sikertelen függőségek darabszámát. Ha további részleteket szeretne megtudni egy sikertelen előfordulásról, az alsó táblázatban található függőségi névre kattintva próbálkozhat. Kattintson a jobb alsó sarokban található kék **függőségek** gombra a végpontok közötti tranzakció részleteinek beszerzéséhez.

## <a name="logs-analytics"></a>Naplók (Analitika)

A függőségeket a [Kusto lekérdezési nyelvén](/azure/kusto/query/)követheti nyomon. Néhány példa:

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hogyan nem sikerült az automatikus függőségi gyűjtő jelentése a függőségek meghívására?*

* Sikertelen függőségi hívások esetén a "sikeres" mező értéke false (hamis) lesz. a `DependencyTrackingTelemetryModule` nem jelent `ExceptionTelemetry`. A függőség teljes adatmodelljéről [itt](data-model-dependency-telemetry.md)olvashat.

## <a name="open-source-sdk"></a>Open-source SDK
Mint minden Application Insights SDK, a függőség-gyűjtési modul is nyílt forráskódú. Olvassa el és járuljon hozzá a kóddal, vagy jelentse [a hibákat a hivatalos GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server)-tárházban.

## <a name="next-steps"></a>Következő lépések

* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md)
* [Felhasználói & Lap adatvédelme](../../azure-monitor/app/javascript.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
