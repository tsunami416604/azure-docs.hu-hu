---
title: Az Application Insightsban lévő események számlálói | Microsoft Docs
description: A rendszer és az egyéni .NET/.NET Core EventCounters figyelése Application Insightsban.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 1719c917ee2a4c0a11e4a79953a8b67e946d5931
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889124"
---
# <a name="eventcounters-introduction"></a>EventCounters bemutatása

a `EventCounter` a .NET/.NET Core mechanizmus a számlálók vagy statisztikák közzétételéhez és felhasználásához. [Ez](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) a dokumentum áttekintést nyújt `EventCounters`okról és példákról a közzétételére és felhasználására. A EventCounters minden operációsrendszer-platformon támogatott – Windows, Linux és macOS. Azt is megteheti, hogy a [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) platformfüggetlen, csak Windows rendszerekben támogatott.

Míg a felhasználók bármilyen egyéni `EventCounters` közzétehetnek az igényeinek megfelelően, a .NET Core 3,0 Runtime alapértelmezés szerint közzéteszi ezeket a számlálókat. A dokumentum végigvezeti az Azure Application Insights `EventCounters` (rendszer által definiált vagy felhasználó által definiált) összegyűjtéséhez és megtekintéséhez szükséges lépéseken.

## <a name="using-application-insights-to-collect-eventcounters"></a>A Application Insights használata a EventCounters gyűjtéséhez

A Application Insights támogatja a `EventCounters` begyűjtését annak `EventCounterCollectionModule`ével, amely a [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)újonnan kiadott nuget-csomag részét képezi. a [AspNetCore](asp-net-core.md) vagy a [WorkerService](worker-service.md)használatakor a rendszer automatikusan engedélyezi a `EventCounterCollectionModule`. a `EventCounterCollectionModule` a nem konfigurálható gyűjtemények 60 másodperces gyakoriságát gyűjti a számlálókat. A EventCounters gyűjtéséhez nincs szükség különleges engedélyekre.

## <a name="default-counters-collected"></a>Összegyűjtött alapértelmezett számlálók

A .NET Core 3,0-ben futó alkalmazások esetében az SDK automatikusan összegyűjti a következő számlálókat. A számlálók neve "Category |" formában jelenik meg. Számláló ".

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
> A Microsoft. AspNetCore. hosting kategóriájú számlálók csak a Asp.Net Core-alkalmazásokban lesznek hozzáadva.

## <a name="customizing-counters-to-be-collected"></a>A gyűjteni kívánt számlálók testreszabása

Az alábbi példa bemutatja, hogyan adhat hozzá vagy távolíthat el számlálókat. Ezt a testreszabást az alkalmazás `ConfigureServices` metódusa fogja elvégezni, miután Application Insights telemetria-gyűjtemény engedélyezve lett `AddApplicationInsightsTelemetry()` vagy `AddApplicationInsightsWorkerService()`használatával. A következő példa egy ASP.NET Core alkalmazásból származó kódot mutat be. Más típusú alkalmazások esetében tekintse meg [ezt](worker-service.md#configuring-or-removing-default-telemetrymodules) a dokumentumot.

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

## <a name="event-counters-in-metric-explorer"></a>Az események számlálói a metrika Explorerben

Az EventCounter mérőszámok megjelenítéséhez a [metrika-kezelőben](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)válassza ki Application Insights erőforrást, majd a log-alapú metrikákat metrikai névtérként. Ezután a EventCounter-metrikák a PerformanceCounter kategóriában jelennek meg.

> [!div class="mx-imgBorder"]
> ![Application Insights](./media/event-counters/metrics-explorer-counter-list.png)ban jelentett számlálók

## <a name="event-counters-in-analytics"></a>Az elemzési események számlálói

Az **performanceCounters** táblában is kereshet és megjeleníthet Event Counter-jelentéseket az [Analyticsben](../../azure-monitor/app/analytics.md).

Például a következő lekérdezés futtatásával tekintheti meg, hogy a rendszer milyen számlálókat gyűjt, és hogyan érhető el a lekérdezéshez:

```Kusto
performanceCounters | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights](./media/event-counters/analytics-event-counters.png)ban jelentett számlálók

Egy adott számláló diagramjának lekéréséhez (például: `ThreadPool Completed Work Item Count`) a legutóbbi időszak alatt futtassa a következő lekérdezést.

```Kusto
performanceCounters 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> egyetlen számláló ![Csevegése Application Insights](./media/event-counters/analytics-completeditems-counters.png)

A többi telemetria hasonlóan a **performanceCounters** is tartalmaz egy olyan `cloud_RoleInstance` oszlopot, amely a gazdagép azon példányának identitását jelzi, amelyen az alkalmazás fut. A fenti lekérdezés a számláló értékét jeleníti meg, és felhasználható a különböző kiszolgálói példányok teljesítményének összehasonlítására.

## <a name="alerts"></a>Értesítések
Más mérőszámokhoz hasonlóan [riasztást is beállíthat](../../azure-monitor/app/alerts.md) , amely figyelmezteti, ha egy esemény számlálója a megadott korláton kívül esik. Nyissa meg a riasztások ablaktáblát, és kattintson a riasztás hozzáadása lehetőségre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Láthatom a EventCounters az élő Mérőszámokban?

Az élő metrikák nem jelenítik meg a EventCounters a mai naptól. A telemetria megtekintéséhez használja a metrika Explorert vagy az elemzést.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Mely platformok láthatják a .NET Core 3,0-számlálók alapértelmezett listáját?

A EventCounter nem igényel speciális engedélyeket, és a .NET Core 3,0 összes platformján támogatott. Az érintett műveletek közé tartoznak az alábbiak:

* **Operációs rendszer**: Windows, Linux vagy MacOS.
* **Üzemeltetési módszer**: folyamatban vagy folyamatban.
* **Üzembe helyezési módszer**: keretrendszer függő vagy önálló.
* **Webkiszolgáló**: IIS (Internet Information Server) vagy vércse.
* **Üzemeltetési platform**: az Azure app Service, az Azure VM, a Docker, az Azure Kubernetes Service (ak) Web Apps funkciója stb.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Engedélyeztem Application Insights az Azure Web App Portalon. De nem látom a EventCounters.?

 ASP.NET Core [Application Insights bővítmény](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) még nem támogatja ezt a funkciót. Ez a dokumentum akkor frissül, ha ez a funkció támogatott.

## <a name="next"></a>Következő lépések

* [Függőségek nyomon követése](../../azure-monitor/app/asp-net-dependencies.md)