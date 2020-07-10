---
title: Az Azure Functions monitorozása
description: Ismerje meg, hogyan használható az Azure Application Insights és a Azure Functions a függvények végrehajtásának figyelésére.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5560d24601b8aef0d8a4058cc2c04e27e9c86362
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170411"
---
# <a name="monitor-azure-functions"></a>Az Azure Functions monitorozása

A [Azure functions](functions-overview.md) az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) beépített integrációját kínálja a függvények figyelésére. Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Functions a rendszer által létrehozott naplófájlok Application Insightsba való küldéséhez.

Javasoljuk, hogy használja a Application Insights, mert a napló-, a teljesítmény-és a hiba-adatokat gyűjti. A szolgáltatás automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a függvények használatának megismerésében. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Application Insights is használhatja a helyi funkció alkalmazás-projekt fejlesztése során. További információ: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

Mivel a szükséges Application Insights rendszerállapot-Azure Functions be van építve, mindössze egy érvényes kialakítási kulcs szükséges ahhoz, hogy a Function alkalmazást egy Application Insights erőforráshoz lehessen kapcsolni. Ha a Function app-erőforrást az Azure-ban hozza létre, a kialakítási kulcsot hozzá kell adni az alkalmazás beállításaihoz. Ha a Function alkalmazás még nem rendelkezik ezzel a kulccsal, [manuálisan is beállíthatja](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Díjszabás és korlátozások Application Insights

Kipróbálhatja Application Insights integrációját Azure Functions ingyen. Napi korláttal rendelkezhet, hogy mennyi mennyiségű adatfeldolgozásra van lehetőség ingyenesen. Ezt a korlátot a tesztelés során is elérheti. Az Azure portál-és e-mail-értesítéseket biztosít, ha közeledik a napi korláthoz. Ha kihagyja ezeket a riasztásokat, és lenyomja a korlátot, az új naplók nem jelennek meg Application Insights lekérdezésekben. Ügyeljen arra, hogy a szükségtelen hibaelhárítási idő elkerülhető legyen. További információ: [a díjszabás és az adatmennyiség kezelése Application Insightsban](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights tartalmaz egy [mintavételi](../azure-monitor/app/sampling.md) funkciót, amely képes arra, hogy túl sok telemetria-adatmennyiséget állítson elő a befejezett végrehajtásokon a maximális terhelés idején. A mintavétel alapértelmezés szerint engedélyezve van. Ha úgy tűnik, hogy hiányoznak az adatok, előfordulhat, hogy módosítania kell a mintavételi beállításokat, hogy az megfeleljen az adott figyelési forgatókönyvnek. További információ: a [mintavételezés konfigurálása](#configure-sampling).

A Function app számára elérhető Application Insights szolgáltatások teljes listáját a [Azure functions által támogatott funkciók Application Insightsjában](../azure-monitor/app/azure-functions-supported-features.md)részletesen ismertetjük.

## <a name="view-telemetry-in-monitor-tab"></a>Telemetria megtekintése a figyelés lapon

Ha [engedélyezve van Application Insights integráció](#enable-application-insights-integration), megtekintheti a telemetria a **figyelés** lapon.

1. A Function app (függvény alkalmazása) lapon válasszon ki egy olyan függvényt, amely legalább egyszer fut Application Insights konfigurálása után. Ezután válassza a **figyelő** lehetőséget a bal oldali ablaktáblán. Válassza a rendszeres **frissítés** lehetőséget, amíg meg nem jelenik a függvény meghívások listája.

   ![Meghívások listája](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Akár öt percet is igénybe vehet, amíg a lista megjelenik, miközben a telemetria-ügyfél a kiszolgálónak továbbított adatforgalmat. A késleltetés nem vonatkozik a [élő metrikastreamra](../azure-monitor/app/live-stream.md). A szolgáltatás a lap betöltésekor csatlakozik a functions-gazdagéphez, így a naplók közvetlenül az oldalra lesznek továbbítva.

1. Egy adott függvény naplóinak megtekintéséhez válassza ki az adott hívás **dátum (UTC)** oszlopának hivatkozását. Az adott hívás naplózási kimenete új lapon jelenik meg.

   ![Meghívás részletei](media/functions-monitoring/invocation-details-ai.png)

1. A Azure Monitor lekérdezés forrásának megtekintéséhez válassza a **Futtatás a Application Insightsban** lehetőséget. Ha első alkalommal használja az Azure-Log Analytics az előfizetésében, a rendszer kéri, hogy engedélyezze.

1. A Log Analytics engedélyezése után a következő lekérdezés jelenik meg. Láthatja, hogy a lekérdezés eredményei az elmúlt 30 napra korlátozódnak ( `where timestamp > ago(30d)` ). Emellett az eredmények legfeljebb 20 sort mutatnak ( `take 20` ). Ezzel szemben a függvény Meghívási részleteinek listája az elmúlt 30 napra, korlátozás nélkül.

   ![Application Insights Analytics-meghívások listája](media/functions-monitoring/ai-analytics-invocation-list.png)

További információ: telemetria- [adatok lekérdezése](#query-telemetry-data) a cikk későbbi részében.

## <a name="view-telemetry-in-application-insights"></a>Telemetria megtekintése Application Insights

Ha Application Insights szeretne megnyitni egy Function alkalmazásból a Azure Portalban, válassza a **Application Insights** lehetőséget a bal oldalon található **Beállítások** területen. Ha első alkalommal használja Application Insights az előfizetését, a rendszer felszólítja, hogy engedélyezze azt: válassza a **Bekapcsolás Application Insights**lehetőséget, majd válassza az **alkalmaz** lehetőséget a következő oldalon.

![Application Insights megnyitása a Function app – áttekintés oldalon](media/functions-monitoring/ai-link.png)

A Application Insights használatáról az [Application Insights dokumentációjában](https://docs.microsoft.com/azure/application-insights/)olvashat bővebben. Ez a szakasz néhány példát mutat be a Application Insights lévő adatmegjelenítésre. Ha már ismeri a Application Insightst, közvetlenül [a telemetria-adatainak konfigurálásával és testreszabásával foglalkozó fejezetekre](#configure-categories-and-log-levels)léphet.

![Application Insights Áttekintés lap](media/functions-monitoring/metrics-explorer.png)

Az Application Insights következő területei hasznosak lehetnek a függvények viselkedésének, teljesítményének és hibáinak kiértékelése során:

| Vizsgálat | Leírás |
| ---- | ----------- |
| **[Hibák](../azure-monitor/app/asp-net-exceptions.md)** |  Diagramok és riasztások létrehozása a függvények hibái és a kiszolgálói kivételek alapján. A **művelet** neve a függvény neve. A függőségek meghibásodása csak akkor jelenik meg, ha egyéni telemetria valósít meg a függőségekhez. |
| **[Teljesítmény](../azure-monitor/app/performance-counters.md)** | A teljesítménnyel kapcsolatos problémák elemzéséhez tekintse meg az erőforrás-kihasználtságot és az átviteli sebességet a **felhőalapú szerepkör példányain**. Ez az adat hasznos lehet olyan forgatókönyvek hibakereséséhez, ahol a függvények a mögöttes erőforrások leállását végzik. |
| **[Metrikák](../azure-monitor/app/metrics-explorer.md)** | Metrikák alapján létrehozhat diagramokat és riasztásokat. A metrikák közé tartozik a Function meghívások száma, a végrehajtási idő és a sikerességi arány. |
| **[Élő metrikák](../azure-monitor/app/live-stream.md)** | A metrikák adatait a közel valós időben létrehozva tekintheti meg. |

## <a name="query-telemetry-data"></a>Telemetria-adatbázis lekérdezése

[Application Insights Analytics](../azure-monitor/app/analytics.md) hozzáférést biztosít minden telemetria-adatbázishoz egy adatbázisban lévő táblák formájában. Az elemzés lekérdezési nyelvet biztosít az adatok kinyeréséhez, módosításához és megjelenítéséhez. 

Válassza a **naplók** lehetőséget a naplózott események felderítéséhez vagy lekérdezéséhez.

![Elemzési példa](media/functions-monitoring/analytics-traces.png)

Az alábbi példa egy lekérdezési példát mutat be, amely az elmúlt 30 percben megjeleníti az egyes feldolgozó kérelmek eloszlását.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

A rendelkezésre álló táblák a bal oldali **séma** lapon jelennek meg. Az alábbi táblázatokban megtalálhatja a függvények által generált adathívásokat:

| Táblázat | Leírás |
| ----- | ----------- |
| **nyomok** | A futtatókörnyezet és a függvény kódja által létrehozott naplók. |
| **kérések** | Egy kérelem az egyes függvények meghívásához. |
| **kivételek** | A futtatókörnyezet által kiváltott kivételek. |
| **customMetrics** | A sikeres és sikertelen meghívások száma, a sikerességi arány és az időtartam. |
| **customEvents** | A futtatókörnyezet által követett események, például: a függvényt kiváltó HTTP-kérelmek. |
| **performanceCounters** | Információk azon kiszolgálók teljesítményéről, amelyeken a függvények futnak. |

A többi tábla a rendelkezésre állási tesztekhez, valamint az ügyfél és a böngésző telemetria. Egyéni telemetria is létrehozhat, amelyekkel adathozzáadást adhat hozzájuk.

A függvények egyes adatai egy adott `customDimensions` mezőben találhatók.  A következő lekérdezés például lekéri az összes naplózási szintű nyomkövetést `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

A futtatókörnyezet biztosítja a `customDimensions.LogLevel` és a `customDimensions.Category` mezőket. További mezőket is megadhat a függvény kódjába írt naplókban. Lásd a jelen cikk későbbi, [strukturált naplózás](#structured-logging) című részében.

## <a name="configure-categories-and-log-levels"></a>Kategóriák és naplózási szintek konfigurálása

A Application Insightst egyéni konfiguráció nélkül is használhatja. Az alapértelmezett konfiguráció nagy mennyiségű adattal járhat. Ha Visual Studio Azure-előfizetést használ, előfordulhat, hogy a Application Insightshoz tartozó adatkorlátot is elérheti. A cikk későbbi részében megtudhatja, hogyan konfigurálhatja és testre szabhatja a függvények által Application Insightsba küldött adatok konfigurálását. Egy Function alkalmazás esetében a naplózás a fájl [host.js] van konfigurálva.

### <a name="categories"></a>Kategóriák

A Azure Functions Logger minden naplóhoz tartalmaz *kategóriát* . A kategória azt jelzi, hogy a futásidejű kód mely része vagy a függvény kódja írta a naplót. A következő diagram a futtatókörnyezet által létrehozott naplók fő kategóriáit ismerteti. 

| Kategória | Leírás |
| ----- | ----- | 
| Host.Results | Ezek a naplók Application Insights **kérelmekként** jelennek meg. A függvények sikerességét vagy hibáját jelzik. Ezen naplók mindegyike szinten van írva `Information` . Ha a `Warning` -t vagy a fentit szűri, akkor nem jelenik meg ezek az információk. |
| Host. aggregator | Ezek a naplók a függvények számát és átlagát biztosítják egy [konfigurálható](#configure-the-aggregator) időszakra vonatkozóan. Az alapértelmezett időtartam 30 másodperc vagy 1 000 eredmény, attól függően, hogy melyik következik be először. A naplók a Application Insights **customMetrics** táblájában érhetők el. Ilyenek például a futtatások száma, a sikerességi arány és az időtartam. Ezen naplók mindegyike szinten van írva `Information` . Ha a `Warning` -t vagy a fentit szűri, akkor nem jelenik meg ezek az információk. |

Az ezektől eltérő kategóriákhoz tartozó összes napló a Application Insights **nyomkövetési** táblázatában érhető el.

Az-nal kezdődő kategóriákat tartalmazó naplókat `Host` a functions futtatókörnyezet írja. Az **elindított függvény** és a **befejezett naplók funkció** kategóriája `Host.Executor` . A sikeres futtatáshoz ezek a naplók `Information` szint. A kivételek naplózása `Error` szinten történik. A futásidejű `Warning` naplókat is létrehoz, például: üzenetsor-üzenetek küldése a méreg-várólistába.

A functions futtatókörnyezet olyan naplókat hoz létre, amelyek a "host" kezdetű kategóriába tartoznak. Az 1. x verzióban a, a, a `function started` `function executed` és `function completed` a naplók kategóriája `Host.Executor` . A 2. x verziótól kezdődően ezek a naplók rendelkeznek a kategóriával `Function.<YOUR_FUNCTION_NAME>` .

Ha a függvény kódjában írja be a naplókat, a kategória `Function.<YOUR_FUNCTION_NAME>.User` és a naplózási szint is lehet. A functions futtatókörnyezet 1. x verziójában a kategória a `Function` .

### <a name="log-levels"></a>Naplózási szintek

A Azure Functions naplózó *naplózási szintet* is tartalmaz minden naplóval. A [naplózási szint](/dotnet/api/microsoft.extensions.logging.loglevel) egy enumerálás, és az egész szám kód relatív fontosságot jelez:

|Naplózási szint    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Tájékoztatás | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|Nincsenek        | 6 |

A naplózási szintet `None` a következő szakaszban ismertetjük. 

### <a name="log-configuration-in-hostjson"></a>Naplózási konfiguráció host.json

A [host.jsa] fájlban azt konfigurálja, hogy a Function app hogyan küldje el a Application Insights. Minden kategória esetében meg kell határozni a küldéshez minimálisan szükséges naplózási szintet. Két példa létezik: az első példa a functions Runtime [2. x vagy újabb verzióját](functions-versions.md#version-2x) célozza meg (a .net Core használatával), a második példa pedig az 1. x futtatókörnyezet.

### <a name="version-2x-and-higher"></a>2. x vagy újabb verzió

A functions futtatókörnyezet v2. x és újabb verziói a [.net Core naplózási szűrő-hierarchiát](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)használják. 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>1. x verzió

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Ez a példa a következő szabályokat állítja be:

* A kategóriával vagy a-vel rendelkező naplók esetében a `Host.Results` `Function` csak `Error` a szint és a Application Insights küldését kell elküldenie. A `Warning` szintű és az alábbi naplók figyelmen kívül lesznek hagyva.
* A kategória típusú naplók esetében az `Host.Aggregator` összes naplót küldje Application Insights. A `Trace` naplózási szint ugyanaz, mint a naplózók meghívása `Verbose` , de a `Trace` fájl [host.js] használja.
* Az összes többi napló esetében csak `Information` a szint és a Application Insights küldése szükséges.

A (z) [host.jsa] (z) kategória értéke az összes olyan kategória esetében, amely azonos értékkel kezdődik. `Host`a [host.json] (z),, `Host.General` `Host.Executor` `Host.Results` stb. vezérlők naplózásahost.js.

Ha [host.jsbekapcsolása] több olyan kategóriát tartalmaz, amelyek ugyanazzal a karakterlánccal kezdődnek, akkor a többit is megegyeznek. Tegyük fel, hogy a futtatókörnyezetből mindent szeretne, kivéve, ha a `Host.Aggregator` naplózási szintet szeretné használni `Error` , de `Host.Aggregator` a következő szintre szeretne bejelentkezni `Information` :

### <a name="version-2x-and-later"></a>2. x vagy újabb verzió

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>1. x verzió 

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

Egy kategória összes naplójának letiltásához használhatja a naplózási szintet `None` . A rendszer nem ír naplókat az adott kategóriával, és nincs felettük naplózási szint.

## <a name="configure-the-aggregator"></a>A gyűjtő konfigurálása

Ahogy az előző szakaszban is látható, a futtatókörnyezet összesíti a függvények végrehajtásával kapcsolatos adatokat egy adott időszakban. Az alapértelmezett időtartam 30 másodperc vagy 1 000 fut, amelyik előbb eléri a értéket. Ezt a beállítást a fájl [host.js] is konfigurálhatja.  Például:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Mintavételezés konfigurálása

Application Insights tartalmaz egy [mintavételi](../azure-monitor/app/sampling.md) funkciót, amely képes arra, hogy túl sok telemetria-adatmennyiséget állítson elő a befejezett végrehajtásokon a maximális terhelés idején. Ha a bejövő végrehajtások aránya meghaladja a megadott küszöbértéket, Application Insights véletlenszerűen figyelmen kívül hagyja a bejövő végrehajtások némelyikét. A másodpercenkénti végrehajtások maximális számának alapértelmezett beállítása 20 (öt az 1. x verzióban). A mintavételezésthost.js- [on](https://docs.microsoft.com/azure/azure-functions/functions-host-json#applicationinsights)is konfigurálhatja.  Például:

### <a name="version-2x-and-later"></a>2. x vagy újabb verzió

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request"
      }
    }
  }
}
```

A 2. x verzióban a mintavételezésből bizonyos típusú telemetria kizárhat. A fenti példában a típusú adatok `Request` ki vannak zárva a mintavételezésből. Ez biztosítja az *összes* függvény végrehajtásának (kérésének) naplózását, míg más típusú telemetria továbbra is mintavételezés alá esnek.

### <a name="version-1x"></a>1. x verzió 

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

## <a name="write-logs-in-c-functions"></a>Naplók írása C#-függvényekben

A függvény kódjában olyan naplókat írhat, amelyek nyomkövetésként jelennek meg Application Insightsban.

### <a name="ilogger"></a>ILogger

Paraméter helyett használja a functions [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) paraméterét `TraceWriter` . A "Go to Application Insights" használatával létrehozott naplók `TraceWriter` , de `ILogger` lehetővé teszi a [strukturált naplózást](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Ha egy `ILogger` objektummal rendelkezik, a `Log<level>` [ILogger bővítményi metódusokat hívhat meg a](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) naplók létrehozásához. A következő kód a `Information` "Function. <YOUR_FUNCTION_NAME> kategóriába írja a naplókat. Felhasználó. "

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturált naplózás

A helyőrzők sorrendje, a nevük nem, meghatározza, hogy a rendszer mely paramétereket használja a naplófájlban. Tegyük fel, hogy a következő kóddal rendelkezik:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Ha megtartja ugyanazt az üzenetet, és megfordítja a paraméterek sorrendjét, az eredményül kapott üzenet szövege nem megfelelő helyen lenne.

A helyőrzők kezelése így történik, így strukturált naplózást végezhet. Application Insights a paraméter név-érték párokat és az üzenet karakterláncát tárolja. Ennek az az oka, hogy az üzenet argumentumai olyan mezők lesznek, amelyeken lekérdezéseket végezhet.

Ha a naplózó metódus hívása az előző példához hasonlóan néz ki, akkor lekérdezheti a mezőt `customDimensions.prop__rowKey` . A `prop__` rendszer hozzáadja az előtagot annak biztosításához, hogy ne legyenek ütközések a futtatókörnyezet által hozzáadott és mezőket tartalmazó mezők között.

A mezőre hivatkozva az eredeti üzenet sztringjét is lekérdezheti `customDimensions.prop__{OriginalFormat}` .  

Az alábbi példa az adat JSON-ábrázolását mutatja be `customDimensions` :

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Egyéni metrikák naplózása

A C# parancsfájl-függvények esetében a `LogMetric` bővítmény metódusával `ILogger` hozhat létre egyéni metrikákat Application Insightsban. Íme egy példa metódus hívása:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ez a kód egy alternatív megoldás `TrackMetric` a .net-hez készült Application INSIGHTS API-val való hívásra.

## <a name="write-logs-in-javascript-functions"></a>Naplók írása JavaScript-függvényekben

Node.js függvényekben a paranccsal `context.log` írhat naplókat. A strukturált naplózás nincs engedélyezve.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Egyéni metrikák naplózása

Ha a functions futtatókörnyezet [1. x verziójában](functions-versions.md#creating-1x-apps) fut, Node.js függvények a `context.log.metric` metódus használatával hozhatnak létre egyéni metrikákat a Application Insights. Ez a metódus jelenleg nem támogatott a 2. x és újabb verziókban. Íme egy példa metódus hívása:

```javascript
context.log.metric("TestMetric", 1234);
```

Ez a kód egy alternatív megoldás `trackMetric` a Application Insights Node.js SDK használatával történő hívásra.

## <a name="log-custom-telemetry-in-c-functions"></a>Egyéni telemetria naplózása C#-függvényekben

A Application Insights SDK egy függvény-specifikus verziója, amellyel egyéni telemetria-adatok küldhetők a függvényekből Application Insights: [Microsoft. Azure. webjobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). A következő paranccsal telepítheti a csomagot a parancssorból:

# <a name="command"></a>[Parancs](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Ebben a parancsban cserélje le a parancsot a `<VERSION>` csomag egy olyan verziójára, amely támogatja a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)telepített verzióját. 

A következő C#-példák az [Egyéni TELEMETRIA API](../azure-monitor/app/api-custom-events-metrics.md)-t használják. A példa egy .NET-osztályhoz tartozó könyvtárra mutat, de a Application Insights kód a C#-parancsfájl esetében azonos.

### <a name="version-2x-and-later"></a>2. x vagy újabb verzió

A futtatókörnyezet 2. x vagy újabb verziói a Application Insights újabb funkcióit használják a telemetria automatikus összekapcsolásához a jelenlegi művelettel. Nem szükséges manuálisan beállítani a műveletet `Id` , `ParentId` vagy a `Name` mezőket.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

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
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

A [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) a jelenleg ajánlott API a metrika létrehozásához.

### <a name="version-1x"></a>1. x verzió

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

            // Parse query parameter
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
        }
        
        // Correlate all telemetry with the current Function invocation
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

Ne telefonáljon, `TrackRequest` vagy `StartOperation<RequestTelemetry>` mert a függvény meghívásakor duplikált kérelmeket fog látni.  A függvények futtatókörnyezete automatikusan nyomon követi a kérelmeket.

Nincs beállítva `telemetryClient.Context.Operation.Id` . Ez a globális beállítás helytelen korrelációt okoz, ha sok függvény egyidejűleg fut. Ehelyett hozzon létre egy új telemetria-példányt ( `DependencyTelemetry` , `EventTelemetry` ), és módosítsa a `Context` tulajdonságát. Ezután továbbítsa a telemetria-példányt a megfelelő `Track` metódusba `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Ez a módszer biztosítja, hogy a telemetria megfelelő korrelációs adatokat biztosítson az aktuális függvény meghívásához.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Egyéni telemetria naplózása JavaScript-függvényekben

Az alábbi kódrészletek egyéni telemetria küldenek a [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js)-val:

### <a name="version-2x-and-later"></a>2. x vagy újabb verzió

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride);
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

### <a name="version-1x"></a>1. x verzió

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride);
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

A `tagOverrides` paraméter beállítja a `operation_Id` függvény Meghívási azonosítóját. Ez a beállítás lehetővé teszi egy adott függvény összes automatikusan generált és egyéni telemetria korrelációját.

## <a name="dependencies"></a>Függőségek

A functions v2 automatikusan gyűjti a függőségeket a HTTP-kérelmek, a ServiceBus, a EventHub és az SQL számára.

A függőségek megjelenítéséhez egyéni kódot is írhat. Példákat a [C# egyéni telemetria szakaszban](#log-custom-telemetry-in-c-functions)talál. A mintakód olyan Application Insights alkalmazás- *hozzárendelést* eredményez, amely a következő képhez hasonlóan néz ki:

![Alkalmazástérkép](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Application Insights-integráció engedélyezése

Ahhoz, hogy egy Function alkalmazás adatküldést Application Insights, ismernie kell egy Application Insights erőforrás rendszerállapot-kulcsát. A kulcsnak egy **APPINSIGHTS_INSTRUMENTATIONKEY**nevű alkalmazás-beállításban kell lennie.

Ha a [Azure Portal](functions-create-first-azure-function.md)hozza létre a Function alkalmazást, a parancssorból [Azure functions Core Tools](functions-create-first-azure-function-azure-cli.md)vagy [Visual Studio Code](functions-create-first-function-vs-code.md)használatával, Application Insights az integráció alapértelmezés szerint engedélyezve van. A Application Insights erőforrás neve megegyezik a Function alkalmazás nevével, és az ugyanabban a régióban vagy a legközelebbi régióban jön létre.

### <a name="new-function-app-in-the-portal"></a>Új Function-alkalmazás a portálon

A létrehozandó Application Insights-erőforrás áttekintéséhez válassza ki azt a **Application Insights** ablak kibontásához. Módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné.

![Application Insights engedélyezése a Function app létrehozásakor](media/functions-monitoring/enable-ai-new-function-app.png)

Ha a **Létrehozás**lehetőséget választja, a rendszer létrehoz egy Application Insights erőforrást a Function alkalmazással, amely a `APPINSIGHTS_INSTRUMENTATIONKEY` set in Application settings (alkalmazás beállításai) beállítással rendelkezik. Minden készen áll.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Hozzáadás meglévő Function-alkalmazáshoz 

Ha a [Visual Studióval](functions-create-your-first-function-visual-studio.md)hoz létre Function alkalmazást, létre kell hoznia a Application Insights erőforrást. Ezután hozzáadhatja a kialakítási kulcsot az adott erőforrásból a Function [alkalmazásban](functions-how-to-use-azure-function-app-settings.md#settings) .

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

A függvények korábbi verziói beépített figyelést használnak, ami már nem ajánlott. Ha egy ilyen Function alkalmazáshoz Application Insights integrációt engedélyez, le kell [tiltania a beépített naplózást](#disable-built-in-logging)is.  

## <a name="report-issues"></a>Problémák bejelentése

Ha Application Insights-integrációval kapcsolatos problémát szeretne jelenteni a functions szolgáltatásban, vagy javaslatot vagy kérést szeretne készíteni, [hozzon létre egy problémát a githubban](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Folyamatos átviteli naplók

Egy alkalmazás fejlesztése során gyakran érdemes megtekinteni, hogy mi történik a naplókba közel valós időben, amikor az Azure-ban fut.

Kétféle módon lehet megtekinteni a függvények végrehajtásával létrehozott naplófájlok streamjét.

* **Beépített log streaming**: a app Service platformon megtekintheti az alkalmazás naplófájljainak streamjét. Ez egyenértékű azzal a kimenettel, amelyet a függvények a [helyi fejlesztés](functions-develop-local.md) során végzett hibakereséskor, illetve a portálon a **teszt** lap használatakor észlelt. Megjelenik az összes napló alapú információ. További információ: stream- [naplók](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ez a folyamatos átviteli módszer csak egyetlen példányt támogat, és nem használható a Linux rendszeren futó alkalmazással egy használati tervben.

* **Élő metrikastream**: Ha a function alkalmazás [Application Insightshoz csatlakozik](#enable-application-insights-integration), a Azure Portal a [élő metrikastream](../azure-monitor/app/live-stream.md)használatával megtekintheti a naplózási adatokat és az egyéb mérőszámokat közel valós időben. Ezt a módszert akkor használja, ha több példányon vagy Linuxon futó figyelési funkciót használ a használati tervben. Ez a metódus [mintavételes adathalmazt](#configure-sampling)használ.

A naplózási streamek a Portálon és a legtöbb helyi fejlesztési környezetben is megtekinthetők. 

### <a name="portal"></a>Portal

A portálon mindkét típusú naplózási adatfolyamot megtekintheti.

#### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

Ha a portálon szeretné megtekinteni a folyamatos átviteli naplókat, válassza a **platform szolgáltatások** fület a Function alkalmazásban. Ezután a **figyelés**területen válassza a **naplózási adatfolyam**lehetőséget.

![Folyamatos átviteli naplók engedélyezése a portálon](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ezzel összekapcsolja az alkalmazást a log streaming szolgáltatáshoz, és az alkalmazás naplói megjelennek az ablakban. Válthat az **alkalmazási naplók** és a **webkiszolgálói naplók**között.  

![Folyamatos átviteli naplók megtekintése a portálon](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Élő metrikastream

Az alkalmazás Élő metrikastream megtekintéséhez válassza a Function app **Áttekintés** lapját. Application Insights engedélyezése esetén a **konfigurált szolgáltatások**területen megjelenik egy **Application Insights** -hivatkozás. Ez a hivatkozás az alkalmazás Application Insights lapjára lép.

A Application Insights területen válassza a **élő metrikastream**lehetőséget. A [mintavételes naplóbejegyzések](#configure-sampling) a **minta telemetria**alatt jelennek meg.

![Élő metrikastream megtekintése a portálon](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Alapvető eszközök

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI](/cli/azure/install-azure-cli)használatával engedélyezheti a folyamatos átviteli naplókat. A következő parancsokkal jelentkezzen be, válassza ki az előfizetését és a stream naplófájljait:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/overview)használatával engedélyezheti a folyamatos átviteli naplókat. A PowerShell esetében használja az alábbi parancsokat az Azure-fiók hozzáadásához, és válassza ki az előfizetését és a stream naplófájljait:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="scale-controller-logs-preview"></a>Kontroller-naplók méretezése (előzetes verzió)

Ez a funkció előzetes verzióban érhető el. 

A [Azure functions skálázási vezérlő](./functions-scale.md#runtime-scaling) figyeli a Azure functions gazdagép azon példányait, amelyeken az alkalmazás fut. Ez a vezérlő döntéseket hoz a példányok hozzáadásához vagy eltávolításához az aktuális teljesítmény alapján. A méretezési vezérlő kibocsátja a naplókat Application Insights vagy a blob Storage-ba, hogy jobban megértse a méretezési vezérlő által a Function alkalmazásra vonatkozó döntéseket.

A szolgáltatás engedélyezéséhez adjon hozzá egy nevű új Alkalmazásbeállítás-beállítást `SCALE_CONTROLLER_LOGGING_ENABLED` . Ennek a beállításnak a formátuma a `<DESTINATION>:<VERBOSITY>` következők alapján kell, hogy legyen:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Például a következő Azure CLI-parancs bekapcsolja a méretezési vezérlő részletes naplózását a Application Insightsra:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Ebben a példában cserélje le a `<FUNCTION_APP_NAME>` és a `<RESOURCE_GROUP_NAME>` nevet a Function alkalmazás nevére, illetve az erőforráscsoport nevét. 

A következő Azure CLI-parancs letiltja a naplózást a részletesség beállításával `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

A naplózást a `SCALE_CONTROLLER_LOGGING_ENABLED` következő Azure CLI-parancs használatával is letilthatja a beállítás eltávolításával:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="disable-built-in-logging"></a>Beépített naplózás letiltása

A Application Insights engedélyezésekor tiltsa le az Azure Storage-t használó beépített naplózást. A beépített naplózás hasznos a könnyű számítási feladatokkal történő teszteléshez, de nem a nagy terhelésű éles használathoz. Éles monitorozáshoz ajánlott Application Insights. Ha a beépített naplózást éles környezetben használja, előfordulhat, hogy a naplózási rekord nem fejeződött be az Azure Storage-ban való szabályozás miatt.

A beépített naplózás letiltásához törölje az `AzureWebJobsDashboard` alkalmazás beállítását. Az Alkalmazásbeállítások a Azure Portalban való törlésével kapcsolatos további információkért tekintse meg a [functions-alkalmazás kezelésével](functions-how-to-use-azure-function-app-settings.md#settings)foglalkozó témakör **Alkalmazásbeállítások** szakaszát. Az Alkalmazásbeállítások törlése előtt győződjön meg arról, hogy az azonos Function alkalmazásban lévő meglévő függvények nem használják az Azure Storage-eseményindítók és-kötések beállítását.

## <a name="next-steps"></a>További lépések

További információkat találhat az alábbi forrásokban:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core naplózás](/aspnet/core/fundamentals/logging/)

[host.jsbekapcsolva]: functions-host-json.md
