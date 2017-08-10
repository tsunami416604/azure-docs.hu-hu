---
title: "A Node.js szolgáltatások figyelése az Azure Application Insights segítségével | Microsoft Docs"
description: "Teljesítmény figyelése és problémák diagnosztizálása a Node.js szolgáltatásokban az Application Insights segítségével."
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: hu-hu
ms.lasthandoff: 05/02/2017

---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>A Node.js szolgáltatások és appok figyelése az Application Insights segítségével

Miután üzembe helyezte a háttérszolgáltatásokat, az [Azure Application Insights](app-insights-overview.md) felügyeli azokat, hogy segítsen [felderíteni és gyorsan diagnosztizálni a teljesítménnyel kapcsolatos és más jellegű problémákat](app-insights-detect-triage-diagnose.md). Bármilyen Node.js szolgáltatáshoz használható, azok üzemeltetési helyétől: az adatközpontban, Azure-beli virtuális gépen vagy Web Apps-on, vagy akár nyilvános felhőkön.

A figyelési adatok fogadásához, tárolásához és vizsgálatához az alábbi utasítások segítségével építsen be egy ügynököt a kódba, és állítson be egy megfelelő Application Insights-erőforrást az Azure-ban. A további elemzés és vizsgálat érdekében az ügynök adatot küld ennek az erőforrásnak.

A Node.js ügynök automatikusan képes figyelni a bejövő és kimenő HTTP-kéréseket, számos rendszermérőszámot és a kivételeket. A 0.20 verziótó kezdve néhány gyakori külső csomag figyelésére is képes, mint például a `mongodb`, a `mysql` és a `redis`. Az egyes bejövő HTTP-kérésekhez kapcsolódó összes eseményt összekapcsolja a gyorsabb hibaelhárítás érdekében.

Az appot és a rendszert több szempontból is figyelheti, ha kézileg állítja be őket a későbbiekben bemutatott API ügynök használatával.

![Példa teljesítményfigyelő diagramokra](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>Első lépések

Tekintsük át, hogyan állíthatja be a felügyeletet egy apphoz vagy egy szolgáltatáshoz.

### <a name="resource"></a> Egy App Insights-erőforrás beállítása

**Mielőtt hozzákezd**, győződjön meg róla, hogy rendelkezik Azure-előfizetéssel vagy [igényeljen ingyenesen egy újat][azure-free-offer]. Ha szervezete már rendelkezik Azure-előfizetéssel, egy rendszergazda [az alábbi utasítások követésével][add-aad-user] Önt is hozzá tudja adni az előfizetéshez.

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

Lépjen az [Azure Portalra][portal], és hozzon létre egy Application Insights-erőforrást az alábbiakban leírtak szerint: kattintson az „Új” > „Fejlesztői eszközök” > „Application Insights” elemre. Az erőforrás tartalmaz egy végpontot a telemetriai adatok fogadására, valamint az érkező adatok, a mentett jelentések és irányítópultok, a szabály- és riasztási konfigurációk és továbbiak tárolására.

![App Insights-erőforrás létrehozása](./media/app-insights-nodejs/03-new_appinsights_resource.png)

Az erőforrás-létrehozási oldalon válassza a „Node.js alkalmazás” lehetőséget az alkalmazástípusok legördülő listájáról. Az alkalmazástípus határozza meg, milyen alapértelmezett irányítópult- és jelentéskészletet hoz önnek létre a rendszer. Aggodalomra azonban semmi ok, bármely App Insights-erőforrás képes bármilyen nyelvből és platformból adatot gyűjteni.

![Új App Insights-erőforrás űrlap](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a> A Node.js ügynök beállítása

Most építse be az ügynököt az appba, hogy az tudjon adatokat gyűjteni.
Első lépésként másolja ki az erőforrás rendszerállapotkulcsát (a továbbiakban: `ikey`) a portálról az alábbiak szerint. Az App Insights rendszer ezt a kulcsot használja, hogy az adatokat leképezze Azure-erőforrásaihoz, ezért környezeti változóban vagy az ügynök által használandó kódban kell megadnia azt.  

![Rendszerállapotkulcs másolása](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

A következő lépésben adja hozzá a Node.js ügynökkönyvtárat az app függőségeihez a package.json lapon. Futtassa az app gyökérkönyvtárából az alábbi parancsot:

```bash
npm install applicationinsights --save
```

Most explicit módon töltse be a könyvtárat a kódba. Mivel az ügynök a rendszerállapotot több más könyvtárba is beépíti, olyan hamar kell betölteni, amilyen hamar csak lehet, akár más `require` utasítások előtt. Első lépésként adja hozzá első .js fájl elejéhez a következőt:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

A `setup` metódus konfigurálja a rendszerállapotkulcsot (és így az Azure-erőforrást), hogy minden követett elemhez alapértelmezetten azt használja a rendszer. Hívja meg a `start` utasítást, miután a konfiguráció befejeződött, hogy elkezdődhessen a telemetriai adatok összegyűjtése és küldése.

A rendszerállapotkulcsot az APPINSIGHTS\_INSTRUMENTATIONKEY környezeti változón keresztül is megadhatja ahelyett, hogy kézileg adná azt át a `setup()` vagy a `getClient()` függvénynek. Ez az eljárás lehetővé teszi, hogy ne írja be az erőforráskulcsot a jóváhagyott forráskódba, és különböző erőforráskulcsot adjon meg a különböző környezeteknek.

A további konfigurációs lehetőségek az alábbi dokumentációban érhetőek el.

Kipróbálhatja az ügynököt telemetria küldése nélkül is, ha átállítja a rendszerállapotkulcsot olyan karakterláncra, amely nem üres.

### <a name="monitor"></a> Figyelje alkalmazását

Az ügynök automatikusan gyűjti telemetriaadatokat a Node.js futtatókörnyezetről és néhány gyakori külső modulról. Az alkalmazás használatával most hozzon létre néhányat ezekből az adatokból.

Ezután az [Azure Portalon][portal] tallózzon a korábbiakban létrehozott Application Insights-erőforráshoz, és tekintse meg az első néhány adatpontot az Áttekintő idővonalon, ahogyan az a következő képen is látható. Kattintson végig a diagramokon a további részletekért.

![Első adatpontok](./media/app-insights-nodejs/12-first-perf.png)

Kattintson az Alkalmazás-hozzárendelés gombra az apphoz felderített topológia megtekintéséhez, ahogyan az a következő képen is látható. Kattintson végig a diagramokon a további részletekért.

![Egyszerű apphozzárendelés](./media/app-insights-nodejs/06-appinsights_appmap.png)

Tudjon meg többet appról, és hárítsa el a problémákat a „Vizsgálat” szakasz további elérhető nézeteivel.

![Vizsgálat szakasz](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Nincs adat?

Mivel az ügynök kötegeli az adatokat a beküldéshez, az elemek késve jelenhetnek meg a portálon. Ha nem lát adatokat az erőforrásában, próbálja elvégezni valamelyik javítást az alábbiak közül:

* Használja továbbra is az alkalmazást, végezzen el több műveletet, hogy több telemetria jöjjön létre.
* Kattintson a **Frissítés** gombra a portál erőforrásnézetében. A diagramok rendszeresen automatikusan frissülnek, de az erőforrások frissítése ezt azonnal elvégzi.
* Ellenőrizze, hogy a [szükséges kimenő portok](app-insights-ip-addresses.md) nyitva vannak-e.
* Az egyes események áttekintéséhez nyissa meg a [Keresés](app-insights-diagnostic-search.md) csempét.
* Tekintse meg a [gyakori kérdésekkel foglalkozó részt][].


## <a name="agent-configuration"></a>Ügynökkonfiguráció

Az alábbiakban láthatóak az ügynök konfigurációs metódusai és alapértelmezett értékeik.

Ahhoz, hogy teljes körűen megállapíthassa egy szolgáltatás eseményeinek korrelációját, mindenképpen be kell állítania a `.setAutoDependencyCorrelation(true)` értéket. Ez lehetővé teszi, hogy a Node.js-ben az ügynök kövesse a kontextust az aszinkron visszahívások során.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>Ügynök API

<!-- TODO: Fully document agent API. -->

A .NET ügynök API-jának teljes leírását [itt](app-insights-api-custom-events-metrics.md) találja.

Az Application Insights Node.js-ügyfelének használatával bármilyen kérést, eseményt, mérőszámot vagy kivételt követhet. Az alábbi példa néhány elérhető API-t mutat be.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Függőségek követése

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>Egyéni tulajdonság hozzáadása eseményekhez

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET-kérések követése

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>Kiszolgáló indítási idejének követése

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>További erőforrások

* [A telemetria figyelése a portálon](app-insights-dashboards.md)
* [Analytics-lekérdezések írása a telemetriai adatokhoz](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[gyakori kérdésekkel foglalkozó részt]: app-insights-troubleshoot-faq.md

