<properties
    pageTitle="Az Application Insights SDK hozzáadása a Node.js alkalmazás figyelésére | Microsoft Azure"
    description="Az Application Insights segítségével elemezheti a használati adatokat, a rendelkezésre állást és a teljesítményt a helyszíni vagy Microsoft Azure webalkalmazásán."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="awills"/>



# Az Application Insights SDK hozzáadása a Node.js alkalmazás figyelésére

*Az Application Insights jelenleg még előzetes verziójú kiadásban érhető el.*

A [Visual Studio Application Insights](app-insights-overview.md) élő alkalmazásfigyeléssel segíti a [teljesítménybeli problémák és kivételek észlelését és diagnosztizálását](app-insights-detect-triage-diagnose.md), valamint az [alkalmazáshasználat felderítését](app-insights-overview-usage.md). Egyrészt olyan alkalmazásokkal működik, amelyeket a saját helyszíni IIS-kiszolgálóin vagy Azure VM-eken futtat, másrészt Azure webalkalmazásokkal.



Az SDK biztosítja a bejövő HTTP-kérelemarányok és válaszok, a teljesítményszámlálók (CPU, memória, RPS) és a nem kezelt kivételek automatikus gyűjtését. Emellett hozzá lehet adni egyéni hívásokat a függőségek, mérőszámok vagy egyéb események nyomon követéséhez.

![Példa teljesítményfigyelő diagramokra](./media/app-insights-windows-services/10-perf.png)


#### Előkészületek

A következők szükségesek:

* Visual Studio 2013 vagy újabb. Az újabb jobb.
* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével.

## <a name="add"></a>Application Insights-erőforrás létrehozása

Jelentkezzen be az [Azure portálra][portal], és hozzon létre egy új Application Insights-erőforrást. Az Azure-ban egy [erőforrás][roles] lényegében egy szolgáltatáspéldány. Az alkalmazás telemetriájának elemzése és bemutatása az erőforrásban történik.

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-windows-services/01-new-asp.png)

Az alkalmazás típusaként válassza az Egyéb lehetőséget. A kiválasztott alkalmazástípus adja meg az erőforráspanelek alapértelmezett tartalmát, valamint a [Metrikaböngészőben][metrics] látható tulajdonságokat.

#### A kialakítási kulcs másolása

A kulcs azonosítja az erőforrást, és hamarosan telepíteni fogja azt az SDK-ba, hogy az adatokat az erőforrásba irányíthassa.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/app-insights-windows-services/02-props-asp.png)


## <a name="sdk"></a> Az SDK telepítése az alkalmazásban

```
npm install applicationinsights --save
```

## Használat

Ezzel engedélyezi a kérelmek figyelését, a nem kezelt kivételek nyomon követését és a rendszerteljesítmény figyelését (CPU/memória/RPS).

```javascript

var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

A kialakítási kulcsot is be lehet állítani az APPINSIGHTS_INSTRUMENTATIONKEY környezeti változóban. Ha ezt teszi, nincs szükség argumentumra az `appInsights.setup()` vagy az `appInsights.getClient()` meghívásakor.

Kipróbálhatja az SDK-t telemetria küldése nélkül: állítsa át a kialakítási kulcsot olyan karakterláncra, amely nem üres.


## <a name="run"></a> A projekt futtatása

Futtassa az alkalmazást, és próbálja ki: nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.


## <a name="monitor"></a> A telemetria megtekintése

Térjen vissza az [Azure Portalra](https://portal.azure.com), és keresse meg az Application Insights-erőforrást.


Tekintse meg az adatokat az Áttekintés lapon. Először csak egy vagy két pontot lát. Példa:

![Kattintson végig rajtuk a további adatokért](./media/app-insights-windows-services/12-first-perf.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra. [További információk a metrikákról.][perf]

#### Nincs adat?

* Az alkalmazás segítségével nyisson meg különböző oldalakat, hogy létrejöjjön némi telemetria.
* Az egyes események megtekintéséhez nyissa meg a [Keresés](app-insights-diagnostic-search.md)csempét. Események esetében kicsit tovább is eltarthat a mérőszámok folyamatain való végighaladás.
* Várjon néhány másodpercet, és kattintson a **Frissítés** lehetőségre. A diagramok rendszeres időközönként frissülnek, de manuálisan is frissítheti őket, ha várja valamilyen adatok megjelenését.
* Lásd: [Hibaelhárítás][qna].

## Az alkalmazás közzététele

Most telepítse az alkalmazását az IIS-be vagy az Azure-be, és figyelje meg, hogyan gyűlnek az adatok.


#### Nem lát adatokat a kiszolgálón való közzététel után?

Nyissa meg ezeket a portokat a kimenő forgalom számára a kiszolgáló tűzfalán:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Probléma adódott a lemezképfájl-kiszolgálóján?

Tekintse meg [ezt a Hibaelhárítási cikket](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).



## Testreszabott használat 

### Az automatikus gyűjtés letiltása

```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### Egyéni figyelés

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[További információk a telemetriai API-ról](app-insights-api-custom-events-metrics.md)

### Több kialakítási kulcs használata

```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## Példák

### Függőségek nyomon követése

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### A „GET” kérelmek manuális kéréseinek nyomon követése

```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```

## Következő lépések

* [A telemetria figyelése a portálon](app-insights-dashboards.md)
* [Analytics-lekérdezések írása a telemetrián](app-insights-analytics-tour.md)



<!--Link references-->

[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md



<!--HONumber=Sep16_HO4-->


