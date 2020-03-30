---
title: Eseményszámlálók az Application Insightsban | Microsoft dokumentumok
description: Figyelje a rendszert és az egyéni .NET/.NET Core EventCounters-t az Application Insightsban.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663589"
---
# <a name="eventcounters-introduction"></a>EventCounters bevezetés

`EventCounter`a .NET/.NET Core mechanizmus számlálók vagy statisztikák közzétételére és felhasználására. [Ez](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) a `EventCounters` dokumentum áttekintést és példákat ad a közzétételükhöz és felhasználásukhoz. Az EventCounters minden operációs rendszer platformján támogatott – Windows, Linux és macOS. Úgy is tekinthető, mint egy platformfüggetlen megfelelője a [PerformanceCounters,](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) hogy csak akkor támogatott, a Windows rendszerek.

Bár a felhasználók `EventCounters` az igényeiknek megfelelően bármilyen egyéni beállítást közzétehetnek, a .NET Core 3.0 futásidejű alapértelmezés szerint közzéteszi ezeknek a számlálóknak a készletét. A dokumentum végigvezeti az Azure Application `EventCounters` Insights ban az összegyűjtéshez és a (rendszer definiált vagy felhasználó által definiált) megtekintéséhez szükséges lépéseket.

## <a name="using-application-insights-to-collect-eventcounters"></a>Az Application Insights használata eseményszámlálók gyűjtésére

Az Application Insights `EventCounters` támogatja `EventCounterCollectionModule`a gyűjtését a [microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)újonnan kiadott nuget csomaggal. `EventCounterCollectionModule`automatikusan engedélyezve van az [AspNetCore](asp-net-core.md) vagy a [WorkerService használatakor.](worker-service.md) `EventCounterCollectionModule`60 másodperces, nem konfigurálható gyűjtési gyakorisággal gyűjti a számlálókat. Nincsszükség speciális engedély az EventCounters gyűjtésére.

## <a name="default-counters-collected"></a>Összegyűjtött alapértelmezett számlálók

A .NET Core 3.0-ban futó alkalmazások esetében az SDK automatikusan gyűjti a következő számlálókat. A számlálók neve "Kategória| Számláló".

|Kategória | Számláló|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> A Microsoft.AspNetCore.Hosting kategória számlálói csak ASP.NET Core Applications alkalmazásban kerülnek hozzáadásra.

## <a name="customizing-counters-to-be-collected"></a>Az összegyűjtendő számlálók testreszabása

A következő példa bemutatja, hogyan lehet számlálókat hozzáadni/eltávolítani. Ez a testreszabás az `ConfigureServices` alkalmazás metódusában történik, miután az Application `AddApplicationInsightsTelemetry()` `AddApplicationInsightsWorkerService()`Insights telemetriai gyűjteménye engedélyezve van a vagy a használatával. Az alábbiakban egy ASP.NET Core alkalmazás példakódja látható. Más típusú alkalmazások esetén olvassa el [ezt](worker-service.md#configuring-or-removing-default-telemetrymodules) a dokumentumot.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Eseményszámlálók a Metrikakezelőben

Az EventCounter metrikák megtekintéséhez [a Metrika-kezelőben](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)válassza az Application Insights erőforrást, és válassza a Naplóalapú metrikákat metrikanévtérként. Ezután az EventCounter mérőszámok megjelennek az Egyéni kategória alatt.

> [!div class="mx-imgBorder"]
> ![Az Application Insightsban jelentett eseményszámlálók](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Eseményszámlálók az Analytics szolgáltatásban

Az eseményszámláló-jelentéseket az [Analytics](../../azure-monitor/app/analytics.md)alkalmazásban is keresheti, és megjelenítheti a **customMetrics** táblában.

Futtassa például a következő lekérdezést, és tekintse meg, hogy milyen számlálókat gyűjt a rendszer, és milyen lekérdezésre is elérhetők:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Az Application Insightsban jelentett eseményszámlálók](./media/event-counters/analytics-event-counters.png)

Ha egy adott számláló (például: `ThreadPool Completed Work Item Count`) diagramját szeretné lekérni az elmúlt időszakban, futtassa a következő lekérdezést.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Egyetlen számláló csevegése az Application Insightsban](./media/event-counters/analytics-completeditems-counters.png)

Más telemetriai adatokhoz hasonlóan a `cloud_RoleInstance` **customMetrics** is rendelkezik egy oszlopmal, amely jelzi annak a gazdakiszolgáló-példánynak az identitását, amelyen az alkalmazás fut. A fenti lekérdezés példányonkénti számlálóértéket jelenít meg, és a különböző kiszolgálópéldányok teljesítményének összehasonlítására használható.

## <a name="alerts"></a>Riasztások
Más mutatókhoz hasonlóan [beállíthat egy figyelmeztetést,](../../azure-monitor/app/alerts.md) amely figyelmezteti, ha egy eseményszámláló kívül esik a megadott korláton. Nyissa meg a Riasztások ablaktáblát, és kattintson a Riasztás hozzáadása gombra.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Láthatom az EventCounters-t az élő mérőszámokban?

Az élő mérőszámok a mai napig nem jelenítik meg az EventCounters-t. A Metrikakezelő vagy az Analytics segítségével megtekintheti a telemetriai adatokat.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Mely platformokon láthatom a .NET Core 3.0 számlálók alapértelmezett listáját?

Az EventCounter nem igényel különleges engedélyeket, és minden platformon támogatott .NET Core 3.0 támogatott. Az érintett műveletek közé tartoznak az alábbiak:

* **Operációs rendszer**: Windows, Linux vagy macOS.
* **Hosting módszer**: Folyamatban vagy folyamaton kívüli.
* **Telepítési módszer**: Keretrendszerfüggő vagy önálló.
* **Webkiszolgáló**: IIS (Internet Information Server) vagy Kestrel.
* **Üzemeltetési platform:** Az Azure App Service, az Azure VM, a Docker, az Azure Kubernetes Service (AKS) webalkalmazások szolgáltatása és így tovább.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Engedélyeztem az Application Insights szolgáltatást az Azure Web App Portalról. De nem látom EventCounters.?

 [Application Insights-bővítmény](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) ASP.NET Core még nem támogatja ezt a funkciót. Ez a dokumentum a szolgáltatás támogatása esetén frissül.

## <a name="next-steps"></a><a name="next"></a>További lépések

* [Függőségek nyomon követése](../../azure-monitor/app/asp-net-dependencies.md)
