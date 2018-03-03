---
title: "Az Azure Functions monitorozása"
description: "Útmutató az Azure Functions Azure Application Insights használhatók függvény végrehajtása."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: d2a61f5f51e3c4a1de6baa79493cb2c7380c76b6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="monitor-azure-functions"></a>Az Azure Functions monitorozása

## <a name="overview"></a>Áttekintés 

[Az Azure Functions](functions-overview.md) beépített integrálható a [Azure Application Insights](../application-insights/app-insights-overview.md) a monitorozási funkciók. Ez a cikk bemutatja, hogyan telemetriai adatokat küldhet az Application Insights funkciók konfigurálásához.

![Application Insights Metrikaböngésző](media/functions-monitoring/metrics-explorer.png)

Funkciók is rendelkezik beépített figyelést, amely nem használja az Application insights szolgáltatással. Az Application Insights azt javasoljuk, mert további adatok és az adatok elemzésére jobb mód kínál. A beépített figyelésével kapcsolatos további információkért lásd: a [utolsó szakaszban ismertetett](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Az Application Insights-integráció engedélyezése

Egy függvény alkalmazás adatokat küldhet az Application Insights kell ismernie a instrumentation kulcsot az Application Insights-példány. Két módon, hogy a kapcsolat a [Azure-portálon](https://portal.azure.com):

* [Hozzon létre egy csatlakoztatott Application Insights-példányt, a függvény alkalmazás létrehozásakor](#new-function-app).
* [Az Application Insights-példány csatlakoztatása egy meglévő függvény alkalmazást](#existing-function-app).

### <a name="new-function-app"></a>Új függvényalkalmazás

Az Application Insights az függvény alkalmazás engedélyezése **létrehozása** lap:

1. Állítsa be a **Application Insights** kapcsoló **a**.

2. Válasszon egy **Application Insights hely**.

   ![Egy függvény alkalmazás létrehozásakor az Application Insights engedélyezése](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Meglévő függvény alkalmazás

A rendszerállapot-kulcs beszerzése, és mentse azt egy függvény alkalmazásban:

1. Az Application Insights-példány létrehozása. Alkalmazás típusa **általános**.

   ![Hozzon létre egy Application Insights-példányt, írja be az általános](media/functions-monitoring/ai-general.png)

2. Másolja az instrumentation-kulcsot a **Essentials** lap az Application Insights-példány. A megjelenített kulcsérték beolvasandó végén rámutat egy **másolásához kattintson a** gombra.

   ![Az Application Insights instrumentation kulcs másolása](media/functions-monitoring/copy-ai-key.png)

1. A függvény alkalmazás **Alkalmazásbeállítások** lapon [egy Alkalmazásbeállítás hozzáadása](functions-how-to-use-azure-function-app-settings.md#settings) kattintva **új beállítás hozzáadása**. Az új beállítás APPINSIGHTS_INSTRUMENTATIONKEY nevet, és illessze be a másolt instrumentation kulcs.

   ![Alkalmazásbeállítások instrumentation kulcs hozzáadása](media/functions-monitoring/add-ai-key.png)

1. Kattintson a **Save** (Mentés) gombra.

## <a name="disable-built-in-logging"></a>Beépített naplózás letiltása

Ha engedélyezi az Application Insights, azt javasoljuk, hogy tiltsa le a [beépített naplózást, amely használja az Azure storage](#logging-to-storage). A beépített naplózást hasznos könnyű munkaterhelések szolgáltatással való tesztelés, de nem nagyléptékű üzemi használatra készült. Az éles figyelés az Application Insights ajánlott. Ha beépített naplózási éles környezetben használja, a naplózás rekord az Azure Storage szabályozás miatt hiányosak lehetnek.

Beépített naplózási letiltásához törölje a `AzureWebJobsDashboard` Alkalmazásbeállítás. Az Azure portálon Alkalmazásbeállítások törlésével kapcsolatos információkért lásd: a **Alkalmazásbeállítások** szakasza [egy függvény alkalmazás kezelése](functions-how-to-use-azure-function-app-settings.md#settings).

Az Application Insights és beépített naplózás letiltása, engedélyezése esetén a **figyelő** az Azure portálon függvény viszi Application Insights lapján.

## <a name="view-telemetry-data"></a>Telemetriai adatok megtekintése

Keresse meg a kapcsolódó Application Insights-példány egy függvény alkalmazásból a portálon, válassza ki a **Application Insights** a függvény app hivatkozásra kattintva **áttekintése** lap.

Az Application Insights használatával kapcsolatos információkért lásd: a [Application Insights dokumentáció](https://docs.microsoft.com/azure/application-insights/). Ez a szakasz néhány olyan adatok megtekintése az Application Insightsban jeleníti meg. Ha már ismeri az Application insights szolgáltatással, lépjen közvetlenül [konfigurálásáról és testreszabásáról a telemetriai adatok szakaszok](#configure-categories-and-log-levels).

A [Metrikaböngésző](../application-insights/app-insights-metrics-explorer.md), diagramokat hozhat létre, és kérnek, például a mérőszámok alapján riasztások számának függvény meghívásához, végrehajtási ideje és sikerességi arányát.

![Metrikaböngésző](media/functions-monitoring/metrics-explorer.png)

Az a [hibák](../application-insights/app-insights-asp-net-exceptions.md) lapon hozhat létre diagramokat és függvény hibák és a kiszolgáló kivételek alapján riasztásokat. A **műveletnév** függvény neve. Hibák a függőségek nem láthatók, kivéve, ha megvalósítása [egyéni telemetria](#custom-telemetry-in-c-functions) függőségek.

![Meghibásodások](media/functions-monitoring/failures.png)

Az a [teljesítmény](../application-insights/app-insights-performance-counters.md) lapon elemezheti a teljesítménnyel kapcsolatos problémákat.

![Teljesítmény](media/functions-monitoring/performance.png)

A **kiszolgálók** lapon láthatók, erőforrás-kihasználást és átviteli kiszolgálónként. Ezek az adatok hol vannak bogging funkciók le az alapul szolgáló erőforrások forgatókönyvek hibakereséshez hasznos lehet. Kiszolgálók nevezzük **szerepkörpéldányokat felhőalapú**.

![Kiszolgálók](media/functions-monitoring/servers.png)

A [metrikák adatfolyamot](../application-insights/app-insights-live-stream.md) lapon láthatók metrikai adatok valós idejű létrehozását.

![Élő stream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Telemetriai adatok lekérdezése

[Application Insights Analytics](../application-insights/app-insights-analytics.md) biztosítja az összes olyan adatbázis tábláit formájában a telemetriai adatok elérését. Elemzés lekérdezésnyelvet kibontása, ahol elvégezhető a módosításuk és megjeleníteni az adatokat biztosít.

![Válassza ki az Analytics](media/functions-monitoring/select-analytics.png)

![Elemzés – példa](media/functions-monitoring/analytics-traces.png)

Íme egy példa. Az elmúlt 30 perc kérések száma worker eloszlását mutatja a.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

A táblák rendelkezésre álló megjelennek-e a **séma** lapján a bal oldali ablaktáblán. Az alábbi táblázatban függvény meghívásához által létrehozott adatok található:

* **nyomkövetések** -naplók funkciókódot és a futtatókörnyezet által létrehozott.
* **kérelmek** -egyet mindegyik függvény hívása.
* **kivételek** – bármely a futtatókörnyezet által okozott kivételeket.
* **customMetrics** -száma sikeres és sikertelen meghívásához, sikerességi arányát, időtartam.
* **customEvents** -események nyomon követheti a futtatókörnyezet, például: egy funkció HTTP-kérelmekre.
* **performanceCounters** -információt a funkciók futó a kiszolgáló teljesítményét.

A más táblák rendelkezésreállás figyelésére szolgáló tesztek és ügyfélböngészőnek/telemetriai vonatkoznak. Egyéni telemetriai adatok hozzáadása is létrehozható.

Minden táblában funkciók-specifikus adatok egy részét van egy `customDimensions` mező.  Például az alábbi lekérdezés lekéri a naplózási szint rendelkező összes adat `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

A futtatókörnyezet biztosít `customDimensions.LogLevel` és `customDimensions.Category`. Megadhatja, hogy a naplókat, a függvény kódban írt további mezők. Lásd: [naplózási strukturált](#structured-logging) című cikkben.

## <a name="configure-categories-and-log-levels"></a>Konfigurálja a kategóriák és szintek jelentkezzen

Az Application Insights az egyéni konfiguráció nélkül is használhat, de az alapértelmezett konfigurációt eredményezhet nagy mennyiségű adatot. A Visual Studio Azure-előfizetés használata, az Application Insights az adatok maximális előfordulhat, hogy kattint. Ez a cikk fennmaradó bemutatja, hogyan konfigurálhatja és testre az adatokat, amelyek a funkciók az Application Insights küldenek.

### <a name="categories"></a>Kategóriák

Az Azure Functions naplózó tartalmaz egy *kategória* minden naplóhoz. A kategória azt jelzi, hogy melyik része a runtime vagy a függvény kódban megírt a naplót. 

A Functions futtatókörnyezete hoz létre, amelyek rendelkeznek egy "Állomás" kategória kezdetű naplókat. Például a "elindult,""függvény végre a következő függvény" és "függvény végre lett hajtva" naplók kategóriába tartoznak "Host.Executor". 

A függvény kódban ír naplókat, ha a kategória az "Függvény".

### <a name="log-levels"></a>Naplózási szintek

Az az Azure functions naplózó is magában foglalja a *naplózási szintjének* rendelkező minden napló. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) felsorolásoknak, és az egész kód relatív fontosságát jelzi:

|LogLevel    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

Naplózási szintjének `None` esetén, tekintse meg a következő szakaszban. 

### <a name="configure-logging-in-hostjson"></a>Naplózás konfigurálása az host.json

A *host.json* fájl konfigurálja az Application Insights küld egy függvény alkalmazás milyen mértékű naplózása. Kategórián adja meg a minimális naplózási szint küldeni. Például:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Ez a példa állít be a következő szabályokat:

1. A naplók és a kategória "Host.Results" vagy "Function", csak küldése `Error` szint vagy újabb verzió az Application Insights részére. A naplók `Warning` szinten és az alábbiakban figyelmen kívül lesznek hagyva.
2. A naplók és a gazdagép kategóriát. Gyűjtő küldés csak `Information` szint és a fenti az Application Insights részére. A naplók `Debug` szinten és az alábbiakban figyelmen kívül lesznek hagyva.
3. Minden más naplók küldése csak `Information` szint és a fenti az Application Insights részére.

A kategória értéke *host.json* ugyanazt az értéket kezdődő minden kategória naplózását szabályozza. Például "működteti" *host.json* "Host.General", "Host.Executor", "Host.Results" és így tovább naplózását szabályozza.

Ha *host.json* több kategóriájával ugyanazt a karakterláncot, kezdődő hosszabb megfelelően első teljesül. Tegyük fel például, azt szeretné, hogy a futtatókörnyezet "Host.Aggregator" jelentkezhet kivéve mindent `Error` szint, miközben "Host.Aggregator" naplózásra kerül `Information` szintje:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

A kategória összes napló letiltásához, használhatja a naplózási szint `None`. Nincsenek naplók adott kategória vannak megírva, és nem naplózási szint fölött van.

A következő szakaszok ismertetik a futtatókörnyezet által létrehozott naplók fő kategóriába. 

### <a name="category-hostresults"></a>Kategória Host.Results

Ezek a naplók "kérelmek", az Application Insights megjelenítése. Azt jelzik, hogy sikeres vagy sikertelen volt egy függvény.

![Kérelmek diagram](media/functions-monitoring/requests-chart.png)

Ezek a naplók íródtak `Information` szinten, ezért ha szűrheti a `Warning` vagy újabb verziók esetén nem látja az adatok.

### <a name="category-hostaggregator"></a>Category Host.Aggregator

Ezek a naplók biztosít számát, valamint a függvény meghívásához átlagok képest egy [konfigurálható](#configure-the-aggregator) időszakának idő. Az alapértelmezett időtartam 30 másodperc vagy 1000 eredményeket, amelyik előbb következik be. 

A naplók érhetők el a **customMetrics** Application Insights táblájában. Többek között az számú fut, sikerességi arányról és időtartama.

![customMetrics lekérdezés](media/functions-monitoring/custom-metrics-query.png)

Ezek a naplók íródtak `Information` szinten, ezért ha szűrheti a `Warning` vagy újabb verziók esetén nem látja az adatok.

### <a name="other-categories"></a>Más kategóriák

Minden kategóriákban eltérő már szerepel a listában érhetők el naplók a a **nyomkövetések** Application Insights táblájában.

![nyomok lekérdezés](media/functions-monitoring/analytics-traces.png)

A Functions futtatókörnyezete összes naplók és a "Állomás" karakterrel kezdődő kategóriák írja. A "Függvény lépések" és "Függvény kész" naplók kategóriába tartoznak "Host.Executor". Sikeres futás esetén ezek a naplók vannak `Information` szint; kivételek vannak naplózása `Error` szintjét. A futtatókörnyezet is létrehoz `Warning` szinten a naplókat, például: az elhalt várólistára küldött üzenetek várólistára.

A funkciókódot által írt naplók "Függvény" kategóriába tartoznak, és lehet, hogy bármely naplózási szintet.

## <a name="configure-the-aggregator"></a>A gyűjtő konfigurálása

Az előző szakaszban leírtaknak megfelelően a futtatókörnyezet függvény végrehajtások adatait egy meghatározott időtartamra vonatkozóan összesíti. Az alapértelmezett időtartam 30 másodperc, vagy 1000 fut, amelyik előbb eléri. Ez a beállítás konfigurálása a *host.json* fájlt.  Például:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>A mintavétel konfigurálása

Az Application Insights rendelkezik egy [mintavételi](../application-insights/app-insights-sampling.md) funkciója, amely védeni az előállító néha a csúcsterhelés túl sok telemetriai adatokat. A telemetriai adatok elemek száma meghaladja a megadott mértékben, az Application Insights hozzákezd véletlenszerűen figyelmen kívül hagyja a bejövő elemek egy része. Beállíthatja, hogy a mintavételi *host.json*.  Például:

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>A C# funkciók naplók írása

Az Application Insights nyomkövetési frissítésként jelenik meg a függvény kódban írhat naplókat.

### <a name="ilogger"></a>ILogger

Használjon egy [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) a funkciók ahelyett, hogy a paraméter egy `TraceWriter` paraméter. Naplók segítségével létrehozott `TraceWriter` nyissa meg az Application Insights részére, de `ILogger` lehetővé teszi [naplózási strukturált](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Az egy `ILogger` objektum meghívja a `Log<level>` [kiterjesztésmetódusok a ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) naplózhatók. Például a következő kódot ír `Information` naplók és a "Függvény" kategóriát.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturált naplózás

Helyőrzők nevük nem sorrendje határozza meg, mely paraméterek szerepelnek a fenti üzenet jelenik. Tegyük fel például, a következő kódot:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Ha a ugyanazon üzenet karakterlánc tároljuk, és a paraméterek az sorrendjét, az eredményül kapott üzenet kellene értékek rossz helyen.

Helyőrzők ily módon kezeli, így mindent strukturált naplózás. Az Application Insights tárolja a paraméter név-érték párok mellett az üzenet-karakterlánc. Az eredménye, hogy az üzenet argumentumok válik lekérheti a mezőket.

Például ha a tranzakciónaplókat tartalmazó metódus hívása az előző példához hasonlóan néz ki, akkor lekérdezhet mező `customDimensions.prop__rowKey`. A `prop__` előtag kerül, győződjön meg arról, hogy nincsenek-e mező a futtatókörnyezet ad hozzá, és a funkciókódot mezők között nincsenek ütközések ad hozzá.

Is lekérheti az eredeti üzenet karakterlánc mező Vezérlőpultjának `customDimensions.prop__{OriginalFormat}`.  

Íme egy minta JSON-ábrázolását `customDimensions` adatokat:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Egyéni metrikák naplózása  

A C# parancsfájl funkciók, használhatja a `LogMetric` bővítmény metódusa `ILogger` egyéni metrikákat az Application Insightsban létrehozásához. Íme egy minta metódus hívása:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Ez a kód hívása helyett `TrackMetric` használatával [az Application Insights API a .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Naplók írását a JavaScript-funkcióként

A Node.js funkciók használata `context.log` naplók írni. Strukturált naplózás nincs engedélyezve.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Egyéni metrikák naplózása  

A Node.js funkciók, használhatja a `context.log.metric` metódus egyéni metrikákat az Application Insightsban létrehozásához. Íme egy minta metódus hívása:

```javascript
context.log.metric("TestMetric", 1234); 
```

Ez a kód hívása helyett `trackMetric` használatával [a Node.js Application Insights SDK](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>A C# funkciók egyéni telemetria

Használhatja a [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) egyéni telemetriai adatokat küldhet az Application Insights NuGet-csomagot.

Íme egy példa a C#-kódban, amely használja a [egyéni telemetriai API](../application-insights/app-insights-api-custom-events-metrics.md). A példa egy .NET-osztály könyvtárhoz, de az Application Insights kód megegyezik a C# a parancsfájlt.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Ne hívja `TrackRequest` vagy `StartOperation<RequestTelemetry>`, mivel látni fogja, a függvény meghívása ismétlődő kérelmek.  A Functions futtatókörnyezete automatikusan nyomon követi a kérelmeket.

Nincs beállítva `telemetryClient.Context.Operation.Id`. Ez egy globális beállítás, és helytelen correllation okoz, ha sok funkciók fut egyszerre. Ehelyett hozzon létre egy új telemetriai példányt (`DependencyTelemetry`, `EventTelemetry`), és módosítsa a `Context` tulajdonság. Akkor továbbítja a telemetria-példány a megfelelő `Track` metódusa `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Ez biztosítja, hogy rendelkezik-e a megfelelő correllation részletes aktuális függvény hívásához a telemetriai adatokat.

## <a name="custom-telemetry-in-javascript-functions"></a>A JavaScript-funkcióként egyéni telemetria

A [Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) jelenleg bétaverziójú. Íme néhány mintakód, amely egyéni telemetriai adatokat küld az Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

A `tagOverrides` paraméterkészletei `operation_Id` a függvény meghívási azonosító Ez a beállítás lehetővé teszi, hogy az automatikusan generált és egyéni telemetriai adatokat egy adott funkció meghíváshoz mindegyikét összefüggéseket.

## <a name="known-issues"></a>Ismert problémák

### <a name="dependencies"></a>Függőségek

Függőségek, amelyre a függvény más szolgáltatások nem jelennek meg automatikusan, de a Függőségek megjelenítése egyéni kód írhat. A mintakódot a [C# egyéni telemetria szakasz](#custom-telemetry-in-c-functions) bemutatja, hogyan. A mintakód eredményezi egy *alkalmazás-hozzárendelés* az Application Insightsban láthatóhoz hasonló ezt:

![Alkalmazástérkép](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>A jelentés problémák

Az Application Insights-integráció funkciók a probléma, vagy egy javaslat vagy a kérelem, [létrehoz egy problémát a Githubon](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Az Application Insights nélkül figyelése

Az Application Insights monitorozási funkciók, ez ugyanis további adatok és az adatok elemzésére jobb mód javasolt. De is találhatók naplók és telemetriai adatokat az Azure portál lapjai függvény alkalmazások.

### <a name="logging-to-storage"></a>A naplózás tárolási

Beépített naplózást a kapcsolati karakterlánc által meghatározott tárolási fiókját használja a `AzureWebJobsDashboard` Alkalmazásbeállítás. Ha az adott Alkalmazásbeállítás van konfigurálva, megtekintheti a naplózási adatokat az Azure portálon. Függvény app lapon, a függvény, és válassza ki a **figyelő** fülre, és függvény végrehajtások listájának lekérése. Válassza ki a függvény végrehajtása az időtartam, a bemeneti adatok, a hibák és a kapcsolódó naplófájlok áttekintéséhez.

Ha az Application Insights használ, és rendelkezik [beépített naplózás le van tiltva](#disable-built-in-logging), a **figyelő** lapon viszi Application insights szolgáltatással.

### <a name="real-time-monitoring"></a>Valós idejű figyelése

Is adatfolyam formájában a naplófájlokat, hogy egy parancssori munkamenetet egy helyi munkaállomás használatával a [Azure parancssori felület (CLI) 2.0-s](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/overview).  

Az Azure CLI 2.0 a következő parancsokkal jelentkezzen be, és válassza ki az előfizetését, és adatfolyam-naplófájlokat:

```
az login
az account list
az account set <subscriptionNameOrId>
az appservice web log tail --resource-group <resource group name> --name <function app name>
```

Azure PowerShell a következő parancsokkal az Azure-fiókja hozzáadásához válassza ki az előfizetését, és adatfolyam-naplófájlokat:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

További információkért lásd: [hogyan adatfolyam-naplók](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Helyileg naplófájlok megtekintése

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Application Insights](/azure/application-insights/)
* [Az ASP.NET Core naplózása](/aspnet/core/fundamentals/logging/)
