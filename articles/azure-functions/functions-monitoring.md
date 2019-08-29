---
title: Az Azure Functions monitorozása
description: Ismerje meg, hogyan használható az Azure Application Insights és a Azure Functions a függvények végrehajtásának figyelésére.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 7220c72625697490d9c42dab7e18ca4bf64d3f43
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085611"
---
# <a name="monitor-azure-functions"></a>Az Azure Functions monitorozása

A [Azure functions](functions-overview.md) az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) beépített integrációját kínálja a függvények figyelésére. Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Functions a rendszer által létrehozott naplófájlok Application Insightsba való küldéséhez.

Javasoljuk, hogy használja a Application Insights, mert a napló-, a teljesítmény-és a hiba-adatokat gyűjti. A szolgáltatás automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a függvények használatának megismerésében. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Application Insights is használhatja a helyi funkció alkalmazás-projekt fejlesztése során. További információ: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

Mivel a szükséges Application Insights rendszerállapot-Azure Functions be van építve, mindössze egy érvényes kialakítási kulcs szükséges ahhoz, hogy a Function alkalmazást egy Application Insights erőforráshoz lehessen kapcsolni.

## <a name="application-insights-pricing-and-limits"></a>Díjszabás és korlátozások Application Insights

Kipróbálhatja Application Insights integrációját a Function apps ingyenes használatával. Napi korláttal rendelkezhet, hogy mennyi mennyiségű adatfeldolgozásra van lehetőség ingyenesen. Ezt a korlátot a tesztelés során is elérheti. Az Azure portál-és e-mail-értesítéseket biztosít, ha közeledik a napi korláthoz. Ha kihagyja ezeket a riasztásokat, és lenyomja a korlátot, az új naplók nem jelennek meg Application Insights lekérdezésekben. Ügyeljen arra, hogy a szükségtelen hibaelhárítási idő elkerülhető legyen. További információ: [a díjszabás és az adatmennyiség kezelése Application Insightsban](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Application Insights integráció engedélyezése

Ahhoz, hogy egy Function alkalmazás adatküldést Application Insights, ismernie kell egy Application Insights erőforrás rendszerállapot-kulcsát. A kulcsnak egy **APPINSIGHTS_INSTRUMENTATIONKEY**nevű alkalmazás-beállításban kell lennie.

### <a name="new-function-app-in-the-portal"></a>Új Function-alkalmazás a portálon

Ha [a Azure Portal létrehozza a Function alkalmazást](functions-create-first-azure-function.md), Application Insights integráció alapértelmezés szerint engedélyezve van. A Application Insights erőforrás neve megegyezik a Function alkalmazás nevével, és az ugyanabban a régióban vagy a legközelebbi régióban jön létre.

A létrehozandó Application Insights-erőforrás áttekintéséhez válassza ki azt a **Application Insights** ablak kibontásához. Módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) -földrajzban, ahol az adatait tárolni szeretné.

![Application Insights engedélyezése a Function app létrehozásakor](media/functions-monitoring/enable-ai-new-function-app.png)

Ha a **Létrehozás**lehetőséget választja, a rendszer létrehoz egy Application Insights erőforrást a Function alkalmazással, `APPINSIGHTS_INSTRUMENTATIONKEY` amely a set in Application settings (alkalmazás beállításai) beállítással rendelkezik. Minden készen áll.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Hozzáadás meglévő Function-alkalmazáshoz 

Amikor az [Azure CLI](functions-create-first-azure-function-azure-cli.md), a [Visual Studio](functions-create-your-first-function-visual-studio.md)vagy a [Visual Studio Code](functions-create-first-function-vs-code.md)használatával hoz létre egy Function alkalmazást, létre kell hoznia a Application Insights erőforrást. Ezután hozzáadhatja a kialakítási kulcsot az adott erőforrásból a Function alkalmazásban.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

A függvények korábbi verziói beépített figyelést használnak, ami már nem ajánlott. Ha egy ilyen Function alkalmazáshoz Application Insights integrációt engedélyez, le kell [tiltania a beépített naplózást](#disable-built-in-logging)is.  

## <a name="view-telemetry-in-monitor-tab"></a>Telemetria megtekintése a figyelés lapon

Ha [engedélyezve van Application Insights integráció](#enable-application-insights-integration), megtekintheti a telemetria a **figyelés** lapon.

1. A Function app (függvény alkalmazása) lapon válasszon ki egy olyan függvényt, amely legalább egyszer fut Application Insights konfigurálása után. Ezután válassza a **figyelés** fület.

   ![Figyelő lap kiválasztása](media/functions-monitoring/monitor-tab.png)

1. Válassza a rendszeres **frissítés** lehetőséget, amíg meg nem jelenik a függvény meghívások listája.

   Akár öt percet is igénybe vehet, amíg a lista megjelenik, miközben a telemetria-ügyfél a kiszolgálónak továbbított adatforgalmat. (A késleltetés nem vonatkozik a [élő metrikastreamra](../azure-monitor/app/live-stream.md). A szolgáltatás a lap betöltésekor csatlakozik a functions-gazdagéphez, így a naplók közvetlenül az oldalra kerülnek.)

   ![Meghívások listája](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Egy adott függvény naplóinak megtekintéséhez válassza ki az adott hívás **dátum** oszlopának hivatkozását.

   ![Meghívási részletek hivatkozása](media/functions-monitoring/invocation-details-link-ai.png)

   Az adott hívás naplózási kimenete új lapon jelenik meg.

   ![Meghívás részletei](media/functions-monitoring/invocation-details-ai.png)

Láthatja, hogy mindkét oldalon **fut Application Insights** hivatkozás az Application Insights Analytics-lekérdezésre, amely beolvassa az információt.

![Futtatás az Application Insightsban](media/functions-monitoring/run-in-ai.png)

A következő lekérdezés jelenik meg. Láthatja, hogy a Meghívási lista az elmúlt 30 napra korlátozódik. A lista legfeljebb 20 sort`where timestamp > ago(30d) | take 20`mutat be. A Meghívási részletek listája az elmúlt 30 napra korlátozza a korlátot.

![Application Insights Analytics-meghívások listája](media/functions-monitoring/ai-analytics-invocation-list.png)

További információ: telemetria- [adatok lekérdezése](#query-telemetry-data) a cikk későbbi részében.

## <a name="view-telemetry-in-application-insights"></a>Telemetria megtekintése Application Insights

Ha Application Insights szeretne megnyitni a Azure Portalban lévő Function alkalmazásból, lépjen a Function alkalmazás **Áttekintés** lapjára. A **konfigurált szolgáltatások**területen válassza a **Application Insights**lehetőséget.

![Application Insights megnyitása a Function app – áttekintés oldalon](media/functions-monitoring/ai-link.png)

A Application Insights használatáról az [Application Insights dokumentációjában](https://docs.microsoft.com/azure/application-insights/)olvashat bővebben. Ez a szakasz néhány példát mutat be a Application Insights lévő adatmegjelenítésre. Ha már ismeri a Application Insightst, közvetlenül [a telemetria-adatainak konfigurálásával és testreszabásával](#configure-categories-and-log-levels)foglalkozó fejezetekre léphet.

![Application Insights Áttekintés lap](media/functions-monitoring/metrics-explorer.png)

Az Application Insights következő területei hasznosak lehetnek a függvények viselkedésének, teljesítményének és hibáinak kiértékelése során:

| Tab | Leírás |
| ---- | ----------- |
| **[Hibák](../azure-monitor/app/asp-net-exceptions.md)** |  Diagramok és riasztások létrehozása a függvények hibái és a kiszolgálói kivételek alapján. A **művelet** neve a függvény neve. A függőségek meghibásodása csak akkor jelenik meg, ha egyéni telemetria valósít meg a függőségekhez. |
| **[Teljesítmény](../azure-monitor/app/performance-counters.md)** | Teljesítménnyel kapcsolatos problémák elemzése. |
| **Kiszolgálók** | Megtekintheti a kiszolgálón az erőforrás-kihasználtságot és az átviteli sebességet. Ez az adat hasznos lehet olyan forgatókönyvek hibakereséséhez, ahol a függvények a mögöttes erőforrások leállását végzik. A kiszolgálókat Felhőbeli **szerepkör**-példányoknak nevezzük. |
| **[Mutatókat](../azure-monitor/app/metrics-explorer.md)** | Metrikák alapján létrehozhat diagramokat és riasztásokat. A metrikák közé tartozik a Function meghívások száma, a végrehajtási idő és a sikerességi arány. |
| **[Élő metrikastream](../azure-monitor/app/live-stream.md)** | A metrikák adatait tekintheti meg valós időben létrehozva. |

## <a name="query-telemetry-data"></a>Telemetria-adatbázis lekérdezése

[Application Insights Analytics](../azure-monitor/app/analytics.md) hozzáférést biztosít minden telemetria-adatbázishoz egy adatbázisban lévő táblák formájában. Az elemzés lekérdezési nyelvet biztosít az adatok kinyeréséhez, módosításához és megjelenítéséhez.

![Elemzés kiválasztása](media/functions-monitoring/select-analytics.png)

![Elemzési példa](media/functions-monitoring/analytics-traces.png)

Az alábbi példa egy lekérdezési példát mutat be, amely az elmúlt 30 percben megjeleníti az egyes feldolgozó kérelmek eloszlását.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

A rendelkezésre álló táblák a bal oldali **séma** lapon jelennek meg. Az alábbi táblázatokban megtalálhatja a függvények által generált adathívásokat:

| Tábla | Leírás |
| ----- | ----------- |
| **nyomok** | A futtatókörnyezet és a függvény kódja által létrehozott naplók. |
| **kérelmek** | Egy kérelem az egyes függvények meghívásához. |
| **kivételek** | A futtatókörnyezet által kiváltott kivételek. |
| **customMetrics** | A sikeres és sikertelen meghívások száma, a sikerességi arány és az időtartam. |
| **customEvents** | A futtatókörnyezet által követett események, például: Függvényt kiváltó HTTP-kérelmek. |
| **performanceCounters** | Információk azon kiszolgálók teljesítményéről, amelyeken a függvények futnak. |

A többi tábla a rendelkezésre állási tesztekhez, valamint az ügyfél és a böngésző telemetria. Egyéni telemetria is létrehozhat, amelyekkel adathozzáadást adhat hozzájuk.

A függvények egyes adatai egy `customDimensions` adott mezőben találhatók.  A következő lekérdezés például lekéri az összes naplózási szintű `Error`nyomkövetést.

```
traces 
| where customDimensions.LogLevel == "Error"
```

A futtatókörnyezet biztosítja a `customDimensions.LogLevel` és `customDimensions.Category` a mezőket. További mezőket is megadhat a függvény kódjába írt naplókban. Lásd a jelen cikk későbbi, [strukturált naplózás](#structured-logging) című részében.

## <a name="configure-categories-and-log-levels"></a>Kategóriák és naplózási szintek konfigurálása

A Application Insightst egyéni konfiguráció nélkül is használhatja. Az alapértelmezett konfiguráció nagy mennyiségű adattal járhat. Ha Visual Studio Azure-előfizetést használ, előfordulhat, hogy a Application Insightshoz tartozó adatkorlátot is elérheti. A cikk későbbi részében megtudhatja, hogyan konfigurálhatja és testre szabhatja a függvények által Application Insightsba küldött adatok konfigurálását. Egy Function alkalmazás esetében a naplózás a [Host. JSON] fájlban van konfigurálva.

### <a name="categories"></a>Categories

A Azure Functions Logger minden naplóhoz tartalmaz *kategóriát* . A kategória azt jelzi, hogy a futásidejű kód mely része vagy a függvény kódja írta a naplót. 

A functions futtatókörnyezet olyan naplókat hoz létre, amelyek a "host" kezdetű kategóriába tartoznak. Az 1. x verzióban a `function started`, `function executed`a, `function completed` a és a naplók `Host.Executor`kategóriája. A 2. x verziótól kezdődően ezek a naplók rendelkeznek `Function.<YOUR_FUNCTION_NAME>`a kategóriával.

Ha naplókat ír a függvény kódjába, a kategória a `Function` functions futtatókörnyezet 1. x verziójában található. A 2. x verzióban a kategória `Function.<YOUR_FUNCTION_NAME>.User`a.

### <a name="log-levels"></a>Naplózási szintek

A Azure Functions naplózó naplózási *szintet* is tartalmaz minden naplóval. A [naplózási szint](/dotnet/api/microsoft.extensions.logging.loglevel) egy enumerálás, és az egész szám kód relatív fontosságot jelez:

|LogLevel    |Kód|
|------------|---|
|Híváslánc       | 0 |
|Hibakeresés       | 1 |
|Information | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|Nincsenek        | 6 |

A naplózási szintet `None` a következő szakaszban ismertetjük. 

### <a name="log-configuration-in-hostjson"></a>Konfiguráció naplózása a Host. JSON fájlban

A [Host. JSON] fájl azt konfigurálja, hogy a Function app hogyan küldi el a Application Insights. Minden kategória esetében meg kell határozni a küldéshez minimálisan szükséges naplózási szintet. Két példa létezik: az első példa a functions [2. x verziójának](functions-versions.md#version-2x) (.net Core) verzióját célozza meg, a második példa pedig az 1. x futtatókörnyezet.

### <a name="version-2x"></a>2-es verzió. x

A v2. x futtatókörnyezet a [.net Core naplózási szűrő hierarchiáját](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)használja. 

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

### <a name="version-1x"></a>1\. x verzió

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

* A kategóriával `Host.Results` vagy `Function`a-vel rendelkező `Error` naplók esetében a csak a szint és a Application Insights küldését kell elküldenie. A szintű `Warning` és az alábbi naplók figyelmen kívül lesznek hagyva.
* A kategória `Host.Aggregator`típusú naplók esetében az összes naplót küldje Application Insights. A `Trace` naplózási szint ugyanaz, mint a naplózók meghívása `Verbose`, de `Trace` a [Host. JSON] fájlban használható.
* Az összes többi napló esetében csak `Information` a szint és a Application Insights küldése szükséges.

A [Host. JSON] kategória értéke az összes olyan kategória naplózását szabályozza, amely azonos értékkel kezdődik. `Host`a [Host. JSON] vezérlők a, `Host.General`, `Host.Executor`stb `Host.Results`. naplózását vezérlik.

Ha a [Host. JSON] több olyan kategóriát tartalmaz, amelyek ugyanazzal a karakterlánccal kezdődnek, akkor a többit is megegyeznek. Tegyük fel, hogy a futtatókörnyezetből `Host.Aggregator` mindent szeretne, `Error` kivéve, ha a naplózási szintet szeretné használni `Information` , de a következő szintre szeretne `Host.Aggregator` bejelentkezni:

### <a name="version-2x"></a>2-es verzió. x 

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

### <a name="version-1x"></a>1\. x verzió 

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

Egy kategória összes naplójának letiltásához használhatja a naplózási szintet `None`. A rendszer nem ír naplókat az adott kategóriával, és nincs felettük naplózási szint.

A következő szakaszok a futtatókörnyezet által létrehozott naplók fő kategóriáit ismertetik. 

### <a name="category-hostresults"></a>Kategória Host. Results

Ezek a naplók Application Insights kérelmekként jelennek meg. A függvények sikerességét vagy hibáját jelzik.

![Kérelmek diagramja](media/functions-monitoring/requests-chart.png)

Ezen naplók mindegyike `Information` szinten van írva. Ha a-t `Warning` vagy a fentit szűri, akkor nem jelenik meg ezek az információk.

### <a name="category-hostaggregator"></a>Kategória Host. aggregator

Ezek a naplók a függvények számát és átlagát biztosítják egy [konfigurálható](#configure-the-aggregator) időszakra vonatkozóan. Az alapértelmezett időtartam 30 másodperc vagy 1 000 eredmény, attól függően, hogy melyik következik be először. 

A naplók a Application Insights **customMetrics** táblájában érhetők el. Ilyenek például a futtatások száma, a sikerességi arány és az időtartam.

![customMetrics-lekérdezés](media/functions-monitoring/custom-metrics-query.png)

Ezen naplók mindegyike `Information` szinten van írva. Ha a-t `Warning` vagy a fentit szűri, akkor nem jelenik meg ezek az információk.

### <a name="other-categories"></a>Egyéb kategóriák

A már felsorolt kategóriákhoz tartozó összes napló a Application Insights nyomkövetési táblájában érhető el.

![nyomkövetési lekérdezés](media/functions-monitoring/analytics-traces.png)

Az-nal kezdődő `Host` kategóriákat tartalmazó naplókat a functions futtatókörnyezet írja. A "függvény elindítva" és a "Function Completed `Host.Executor`" naplók kategóriával rendelkeznek. A sikeres futtatáshoz ezek a naplók `Information` szint. A kivételek naplózása `Error` szinten történik. A futásidejű naplókat `Warning` is létrehoz, például: üzenetsor-üzenetek küldése a méreg-várólistába.

A függvény kódjában írt naplók kategóriája `Function` , és bármilyen naplózási szint lehet.

## <a name="configure-the-aggregator"></a>A gyűjtő konfigurálása

Ahogy az előző szakaszban is látható, a futtatókörnyezet összesíti a függvények végrehajtásával kapcsolatos adatokat egy adott időszakban. Az alapértelmezett időtartam 30 másodperc vagy 1 000 fut, amelyik előbb eléri a értéket. Ezt a beállítást a [Host. JSON] fájlban állíthatja be.  Például:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Mintavételezés konfigurálása

Application Insights tartalmaz egy [mintavételi](../azure-monitor/app/sampling.md) funkciót, amely képes arra, hogy túl sok telemetria-adatmennyiséget állítson elő a befejezett végrehajtásokon a maximális terhelés idején. Ha a bejövő végrehajtások aránya meghaladja a megadott küszöbértéket, Application Insights véletlenszerűen figyelmen kívül hagyja a bejövő végrehajtások némelyikét. A másodpercenkénti végrehajtások maximális számának alapértelmezett beállítása 20 (öt az 1. x verzióban). A mintavételt a [Host. JSON]fájlban állíthatja be.  Például:

### <a name="version-2x"></a>2-es verzió. x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>1\. x verzió 

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

> [!NOTE]
> A [mintavétel](../azure-monitor/app/sampling.md) alapértelmezés szerint engedélyezve van. Ha úgy tűnik, hogy hiányoznak az adatok, előfordulhat, hogy módosítania kell a mintavételi beállításokat, hogy az megfeleljen az adott figyelési forgatókönyvnek.

## <a name="write-logs-in-c-functions"></a>Naplók írása a C# functions szolgáltatásban

A függvény kódjában olyan naplókat írhat, amelyek nyomkövetésként jelennek meg Application Insightsban.

### <a name="ilogger"></a>ILogger

`TraceWriter` Paraméter helyett használja a functions [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) paraméterét. A "Go to `TraceWriter` Application Insights" használatával létrehozott naplók `ILogger` , de lehetővé [](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)teszi a strukturált naplózást.

Ha egy `ILogger` objektummal rendelkezik, `Log<level>` a ILogger bővítményi metódusokat hívhat [meg a](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) naplók létrehozásához. A következő kód a `Information` "Function" kategóriába írja a naplókat.

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

Ha a naplózó metódus hívása az előző példához hasonlóan néz ki, akkor `customDimensions.prop__rowKey`lekérdezheti a mezőt. A `prop__` rendszer hozzáadja az előtagot annak biztosításához, hogy ne legyenek ütközések a futtatókörnyezet által hozzáadott és mezőket tartalmazó mezők között.

A mezőre `customDimensions.prop__{OriginalFormat}`hivatkozva az eredeti üzenet sztringjét is lekérdezheti.  

Az alábbi példa az `customDimensions` adat JSON-ábrázolását mutatja be:

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

### <a name="custom-metrics-logging"></a>Egyéni metrikák naplózása

A C# script functions szolgáltatásban a `LogMetric` bővítmény metódusával `ILogger` egyéni metrikákat hozhat létre Application Insightsban. Íme egy példa metódus hívása:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ez a kód egy alternatív megoldás a `TrackMetric` .net-hez készült Application Insights API-val való hívásra.

## <a name="write-logs-in-javascript-functions"></a>Naplók írása JavaScript-függvényekben

A Node. js függvények esetében a `context.log` paranccsal írhat naplókat. A strukturált naplózás nincs engedélyezve.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Egyéni metrikák naplózása

Ha a functions futtatókörnyezet [1. x verziójában](functions-versions.md#creating-1x-apps) fut, a Node. js függvények a `context.log.metric` metódus használatával egyéni metrikákat hozhatnak létre Application Insights. Ez a metódus jelenleg nem támogatott a 2. x verzióban. Íme egy példa metódus hívása:

```javascript
context.log.metric("TestMetric", 1234);
```

Ez a kód a Application Insights Node. `trackMetric` js SDK-val való hívásának alternatívája.

## <a name="log-custom-telemetry-in-c-functions"></a>Egyéni telemetria naplózása C# a functions szolgáltatásban

A [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet-csomag segítségével egyéni telemetria-fájlokat küldhet a Application Insightsba. Az alábbi C# példa az [Egyéni telemetria API](../azure-monitor/app/api-custom-events-metrics.md)-t használja. A példa egy .NET-osztályhoz tartozó könyvtárra mutat, de a Application Insights kódja megegyezik C# a parancsfájlhoz.

### <a name="version-2x"></a>2-es verzió. x

A 2. x verziójú futtatókörnyezet a Application Insights újabb funkcióit használja, hogy automatikusan korrelálja a telemetria az aktuális művelettel. Nem szükséges manuálisan beállítani a műveletet `Id`, `ParentId`vagy `Name` a mezőket.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

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

### <a name="version-1x"></a>1\. x verzió

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

Ne telefonáljon `StartOperation<RequestTelemetry>` , vagy mert a függvény meghívásakor `TrackRequest` duplikált kérelmeket fog látni.  A függvények futtatókörnyezete automatikusan nyomon követi a kérelmeket.

Nincs beállítva `telemetryClient.Context.Operation.Id`. Ez a globális beállítás helytelen korrelációt okoz, ha sok függvény egyidejűleg fut. Ehelyett hozzon létre egy új telemetria-`DependencyTelemetry`példányt `EventTelemetry`(,), `Context` és módosítsa a tulajdonságát. Ezután továbbítsa a telemetria-példányt a megfelelő `Track` `TelemetryClient` metódusba (`TrackDependency()`, `TrackEvent()`). Ez a módszer biztosítja, hogy a telemetria megfelelő korrelációs adatokat biztosítson az aktuális függvény meghívásához.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Egyéni telemetria naplózása a JavaScript-függvényekben

Íme egy kódrészlet, amely egyéni telemetria küld az [Application Insights Node. js SDK](https://github.com/microsoft/applicationinsights-node.js)-val:

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

    context.done();
};
```

A `tagOverrides` paraméter`operation_Id` beállítja a függvény Meghívási azonosítóját. Ez a beállítás lehetővé teszi egy adott függvény összes automatikusan generált és egyéni telemetria korrelációját.

## <a name="dependencies"></a>Függőségek

A functions v2 automatikusan összegyűjti a HTTP-kérelmekre, a ServiceBus és az SQL-re vonatkozó függőségeket.

A függőségek megjelenítéséhez egyéni kódot is írhat. Példákért tekintse meg a mintakód szakaszt az [ C# egyéni telemetria szakaszban](#log-custom-telemetry-in-c-functions). A mintakód olyan Application Insights alkalmazás- *hozzárendelést* eredményez, amely a következő képhez hasonlóan néz ki:

![Alkalmazástérkép](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Problémák jelentése

Ha Application Insights-integrációval kapcsolatos problémát szeretne jelenteni a functions szolgáltatásban, vagy javaslatot vagy kérést szeretne készíteni, [hozzon létre egy problémát a githubban](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Streamelési naplók

Az alkalmazások fejlesztése során gyakran azt szeretné, hogy a naplók az Azure-ban való futtatásakor közel valós időben legyenek beírva.

Kétféle módon lehet megtekinteni a függvények végrehajtásával létrehozott naplófájlok streamjét.

* **Beépített log streaming**: a app Service platformon megtekintheti az alkalmazás naplófájljainak streamjét. Ez egyenértékű azzal a kimenettel, amelyet a függvények a [helyi fejlesztés](functions-develop-local.md) során végzett hibakereséskor, illetve a portálon a **teszt** lap használatakor észlelt. Megjelenik az összes napló alapú információ. További információ: [How to stream logs](../app-service/troubleshoot-diagnostic-logs.md#streamlogs). Ez a folyamatos átviteli módszer csak egyetlen példányt támogat, és nem használható a Linux rendszeren futó alkalmazással egy használati tervben.

* **Élő metrikastream**: Ha a Function alkalmazás [csatlakoztatva](#enable-application-insights-integration)van a Application Insightshoz, akkor a Azure Portal a [élő metrikastream](../azure-monitor/app/live-stream.md)segítségével közel valós időben megtekintheti a naplózási adatokat és az egyéb metrikákat. Ezt a módszert akkor használja, ha több példányon vagy Linuxon futó figyelési funkciót használ a használati tervben. Ez a metódus [mintavételes](#configure-sampling)adathalmazt használ.

A naplózási streamek a Portálon és a legtöbb helyi fejlesztési környezetben is megtekinthetők. 

### <a name="portal"></a>Portál

A portálon mindkét típusú naplózási adatfolyamot megtekintheti.

#### <a name="built-in-log-streaming"></a>Beépített log streaming

Ha a portálon szeretné megtekinteni a folyamatos átviteli naplókat, válassza a **platform szolgáltatások** fület a Function alkalmazásban. Ezután a **figyelés**területen válassza a **naplózási adatfolyam**lehetőséget.

![Folyamatos átviteli naplók engedélyezése a portálon](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ezzel összekapcsolja az alkalmazást a log streaming szolgáltatáshoz, és az alkalmazás naplói megjelennek az ablakban. Válthat az alkalmazási **naplók** és a **webkiszolgálói naplók**között.  

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

## <a name="disable-built-in-logging"></a>Beépített naplózás letiltása

A Application Insights engedélyezésekor tiltsa le az Azure Storage-t használó beépített naplózást. A beépített naplózás hasznos a könnyű számítási feladatokkal történő teszteléshez, de nem a nagy terhelésű éles használathoz. Éles monitorozáshoz ajánlott Application Insights. Ha a beépített naplózást éles környezetben használja, előfordulhat, hogy a naplózási rekord nem fejeződött be az Azure Storage-ban való szabályozás miatt.

A beépített naplózás letiltásához törölje az `AzureWebJobsDashboard` alkalmazás beállítását. Az Alkalmazásbeállítások a Azure Portalban való törlésével kapcsolatos további információkért tekintse meg a functions- [](functions-how-to-use-azure-function-app-settings.md#settings)alkalmazás kezelésével foglalkozó témakör **Alkalmazásbeállítások** szakaszát. Az Alkalmazásbeállítások törlése előtt győződjön meg arról, hogy az azonos Function alkalmazásban lévő meglévő függvények nem használják az Azure Storage-eseményindítók és-kötések beállítását.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core naplózás](/aspnet/core/fundamentals/logging/)

[Host. JSON]: functions-host-json.md
