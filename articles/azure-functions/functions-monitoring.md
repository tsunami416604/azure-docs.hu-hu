---
title: Az Azure Functions monitorozása
description: Megtudhatja, hogyan használhatja az Azure Application Insights ot az Azure Functions funkcióival a függvények végrehajtásának figyeléséhez.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257853"
---
# <a name="monitor-azure-functions"></a>Az Azure Functions monitorozása

[Az Azure Functions](functions-overview.md) beépített integrációt kínál az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) szolgáltatással a függvények figyeléséhez. Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Functions rendszer által létrehozott naplófájlokat az Application Insights.

Az Application Insights használatát javasoljuk, mert napló-, teljesítmény- és hibaadatokat gyűjt. Automatikusan észleli a teljesítményanomáliákat, és hatékony elemzési eszközöket tartalmaz a problémák diagnosztizálásához és a funkciók használatának megértéséhez. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Az Application Insights ot is használhatja a helyi függvényalkalmazás-projektfejlesztés során. További információ: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

Mivel a szükséges Application Insights-instrumentation be van építve az Azure Functionsbe, mindössze egy érvényes instrumentation kulcsra van szüksége a függvényalkalmazás egy Application Insights-erőforráshoz való csatlakoztatásához. A instrumentation kulcsot hozzá kell adni az alkalmazás beállításait, amikor a függvényalkalmazás-erőforrás jön létre az Azure-ban. Ha a függvényalkalmazás még nem rendelkezik ezzel a kulccsal, [manuálisan is beállíthatja.](#enable-application-insights-integration)  

## <a name="application-insights-pricing-and-limits"></a>Az Application Insights díjszabása és korlátai

Kipróbálhatja az Application Insights-integrációt a Függvényalkalmazásokkal ingyenesen. Napi korlát van arra vonatkozóan, hogy mennyi adatot lehet ingyenesen feldolgozni. Előfordulhat, hogy ezt a korlátot a tesztelés során eléri. Az Azure portál- és e-mail-értesítéseket biztosít, amikor közeledik a napi korláthoz. Ha elmulasztja ezeket a riasztásokat, és eléri a korlátot, az új naplók nem jelennek meg az Application Insights-lekérdezésekben. Legyen tisztában a korláttal a szükségtelen hibaelhárítási idő elkerülése érdekében. További információt az [Árképzés és az adatmennyiség kezelése az Application Insightsban című témakörben](../azure-monitor/app/pricing.md)talál.

A függvényalkalmazás számára elérhető Application Insights-funkciók teljes listáját az [Application Insights for Azure Functions által támogatott funkciók](../azure-monitor/app/azure-functions-supported-features.md)részletezik.

## <a name="view-telemetry-in-monitor-tab"></a>Telemetria megtekintése a Figyelő lapon

Ha [az Application Insights-integráció engedélyezve van,](#enable-application-insights-integration)megtekintheti a telemetriai adatokat a **Figyelő** lapon.

1. A függvényalkalmazás lapján válasszon ki egy függvényt, amely legalább egyszer futott az Application Insights konfigurálása után. Ezután válassza a **Refresh** **Monitor** lapot.

   ![Behívási lista](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > A lista megjelenése akár öt percet is igénybe vehet, amíg a telemetriai ügyfél adatokat kötegel a kiszolgálóra történő továbbításhoz. A késleltetés nem vonatkozik az [élő mérőszámok streamre.](../azure-monitor/app/live-stream.md) Ez a szolgáltatás a lap betöltésekén csatlakozik a Functions állomáshoz, így a naplók közvetlenül az oldalra kerülnek.

1. Egy adott függvény meghívásának naplóit a **Dátum (UTC)** oszlophivatkozásra kattintva megtekintheti. Az adott meghívás naplózási kimenete egy új lapon jelenik meg.

   ![Meghívás részletei](media/functions-monitoring/invocation-details-ai.png)

1. Válassza a **Futtatás az Application Insightsban** hivatkozást a lekérdezés forrásának megtekintéséhez, amely lekéri az Azure Monitor naplóadatait az Azure-naplóban Ha ez az első alkalom, hogy az Azure Log Analytics szolgáltatást használja az előfizetésében, engedélyeznie kell.

1. Ha úgy dönt, hogy a linket, és úgy dönt, hogy engedélyezze Log Analytic. megjelenik a következő lekérdezés. Láthatja, hogy a lekérdezés eredménye az elmúlt`where timestamp > ago(30d)`30 napra korlátozódik ( ). Ezenkívül az eredmények legfeljebb 20`take 20`sort mutatnak ( ). Ezzel szemben a függvény meghívási részleteinek listája az elmúlt 30 napra szól, korlátozás nélkül.

   ![Az Application Insights Analytics meghívási listája](media/functions-monitoring/ai-analytics-invocation-list.png)

További információ: [A telemetriai adatok lekérdezése](#query-telemetry-data) a cikk későbbi részében.

## <a name="view-telemetry-in-application-insights"></a>Telemetriai adatok megtekintése az Application Insightsban

Ha meg szeretné nyitni az Application Insights egy függvényalkalmazásból az Azure Portalon, nyissa meg a függvényalkalmazás **áttekintése** lapot. A **Konfigurált szolgáltatások csoportban**válassza az **Application Insights**lehetőséget.

![Az Application Insights megnyitása a függvényalkalmazás áttekintése lapjáról](media/functions-monitoring/ai-link.png)

Az Application Insights használatáról az [Application Insights dokumentációjában](https://docs.microsoft.com/azure/application-insights/)olvashat. Ez a szakasz néhány példát mutat be az Application Insights ban az adatok megtekintésére. Ha már ismeri az Application Insights, közvetlenül a [telemetriai adatok konfigurálásáról és testreszabásáról szóló szakaszokat.](#configure-categories-and-log-levels)

![Az Application Insights – áttekintés lap](media/functions-monitoring/metrics-explorer.png)

Az Application Insights alábbi területei hasznosak lehetnek a függvények viselkedésének, teljesítményének és hibáinak kiértékelésekor:

| Vizsgálat | Leírás |
| ---- | ----------- |
| **[Hibák](../azure-monitor/app/asp-net-exceptions.md)** |  Diagramokat és riasztásokat hozhat létre függvényhibák és kiszolgálói kivételek alapján. A **művelet neve** a függvény neve. A függőségek hibái csak akkor jelennek meg, ha egyéni telemetriai adatokat valósít meg a függőségekhez. |
| **[Teljesítmény](../azure-monitor/app/performance-counters.md)** | A teljesítményproblémák at az erőforrás-kihasználtság és az átviteli teljesítmény **felhőbeli szerepkörpéldányonkénti**megtekintésével elemezheti. Ezek az adatok olyan forgatókönyvek hibakereséséhez lehetnek hasznosak, ahol a függvények lebontják az alapul szolgáló erőforrásokat. |
| **[Mutatókat](../azure-monitor/app/metrics-explorer.md)** | A mérőszámokon alapuló diagramokat és riasztásokat hozhat létre. A metrikák tartalmazzák a függvénymeghívások számát, a végrehajtási időt és a sikerességi arányokat. |
| **[Élő mérőszámok](../azure-monitor/app/live-stream.md)** | A metrikák adatait közel valós időben való létrehozása korlátjaként tekintheti meg. |

## <a name="query-telemetry-data"></a>Telemetriai adatok lekérdezése

[Az Application Insights Analytics](../azure-monitor/app/analytics.md) hozzáférést biztosít az összes telemetriai adathoz táblák formájában egy adatbázisban. Az Analytics lekérdezési nyelvet biztosít az adatok kinyeréséhez, kezeléséhez és megjelenítéséhez. 

A **naplózott** események feltárásához vagy lekérdezéséhez válassza a Naplók lehetőséget.

![Példa elemzési](media/functions-monitoring/analytics-traces.png)

Íme egy példa egy lekérdezésre, amely a kérelmek feldolgozónkénti eloszlását mutatja az elmúlt 30 percben.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Az elérhető táblák a bal oldali **Séma** lapon láthatók. A függvénymeghívások által létrehozott adatokat az alábbi táblázatokban találja meg:

| Tábla | Leírás |
| ----- | ----------- |
| **Nyomok** | A futásidejű és a függvénykód által létrehozott naplók. |
| **kérések** | Minden függvénymeghíváshoz egy kérelem tartozik. |
| **Kivételek** | A futásidejű ek kivételei. |
| **customMetrics** | A sikeres és sikertelen hívások száma, a sikerességi arány és az időtartam. |
| **Customevents** | A futásidejű események, például: függvényt kiváltó HTTP-kérelmek. |
| **performanceCounters** | Információ azoknak a kiszolgálóknak a teljesítményéről, amelyeken a funkciók futnak. |

A többi tábla a rendelkezésre állási tesztek, valamint az ügyfél és a böngésző telemetriai adatok. Egyéni telemetriai adatok hozzáadásával egyéni telemetriai adatokat adhat hozzá.

Az egyes táblákon belül a függvényspecifikus `customDimensions` adatok egy része egy mezőben található.  A következő lekérdezés például lekéri a naplószintű `Error`összes nyomkövetést.

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

A futásidejű `customDimensions.LogLevel` biztosítja `customDimensions.Category` a és a mezőket. A függvénykódba írt naplókban további mezőket is megadhat. Lásd: [Strukturált naplózás](#structured-logging) később ebben a cikkben.

## <a name="configure-categories-and-log-levels"></a>Kategóriák és naplószintek konfigurálása

Az Application Insights egyéni konfiguráció nélkül is használható. Az alapértelmezett konfiguráció nagy mennyiségű adatot eredményezhet. Ha Visual Studio Azure-előfizetést használ, előfordulhat, hogy eléri az Application Insights adatplafonját. A cikk későbbi részében megtudhatja, hogyan konfigurálhatja és szabhatja testre a függvények által az Application Insightsnak küldött adatokat. Függvényalkalmazás esetén a naplózás a [host.json] fájlban van konfigurálva.

### <a name="categories"></a>Kategóriák

Az Azure Functions naplózó minden naplóhoz tartalmaz egy *kategóriát.* A kategória azt jelzi, hogy a futásidejű kód vagy a függvénykód melyik része írta a naplót. Az alábbi diagram a futásidejű által létrehozott naplók fő kategóriáit ismerteti. 

| Kategória | Leírás |
| ----- | ----- | 
| Host.Results | Ezek a naplók az Application Insights ban **kérésként** jelennek meg. Egy függvény sikerét vagy sikertelenségét jelzik. Mindezek a naplók vannak `Information` írva szinten. Ha a `Warning` fenti vagy annál magasabb szűrést nem látja ezek az adatok. |
| Host.Aggregator | Ezek a naplók a függvénymeghívások számát és átlagát biztosítják egy [konfigurálható](#configure-the-aggregator) időtartamalatt. Az alapértelmezett időszak 30 másodperc vagy 1000 eredmény, amelyik előbb következik be. A naplók érhetők el a **customMetrics** tábla Application Insights. Ilyenek például a futtatások száma, a sikerességi arány és az időtartam. Mindezek a naplók vannak `Information` írva szinten. Ha a `Warning` fenti vagy annál magasabb szűrést nem látja ezek az adatok. |

Ezeken kívüli kategóriák összes naplója elérhető az Application Insights **traces-i** táblájában.

A függvények futásidejű által `Host` írt kategóriákkal rendelkező összes naplót a Függvények naplója írja. A **funkció elindult,** és a `Host.Executor`funkció **befejezett** naplók kategóriában . A sikeres futtatások esetén `Information` ezek a naplók szint. A kivételek naplózása szinten `Error` van. A futásidejű `Warning` is létrehoz szint naplók, például: várólista üzeneteket küldött a méreg várólistába.

A Functions futásidejű naplókat hoz létre egy "Host" kategóriával. Az 1.x `function started`verzióban `function executed`a `function completed` , és `Host.Executor`a naplók kategóriában. Kezdve a 2.x verzió, ezek `Function.<YOUR_FUNCTION_NAME>`a naplók a kategória .

Ha naplót ír a függvénykódban, `Function.<YOUR_FUNCTION_NAME>.User` a kategória bármilyen naplószint, és bármilyen naplószint lehet. A Functions futásidejű 1.x-es verziójában a kategória `Function`a .

### <a name="log-levels"></a>Naplózási szintek

Az Azure Functions naplózó is tartalmaz egy *naplószintet* minden naplóhoz. [A LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) egy felsorolás, és az egész kód relatív fontosságot jelez:

|Naplózási szint    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

A `None` naplószint magyarázata a következő szakaszban található. 

### <a name="log-configuration-in-hostjson"></a>Naplózási konfiguráció a host.json fájlban

A [host.json] fájl konfigurálja, hogy mennyi naplózási egy függvényalkalmazás küld az Application Insights. Minden kategóriához meg kell adni a minimális anamforát. Két példa van: az első példa a Functions runtime [2.x-es és újabb verzióját](functions-versions.md#version-2x) célozza meg (a .NET Core-nal), a második példa pedig az 1.x-es verziójú futásidejű.

### <a name="version-2x-and-higher"></a>2.x-es vagy újabb verzió

A Functions futásidejű verzióverzióa i.2.x és újabb verziói a [.NET Core naplózási szűrőhierarchiát](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)használják. 

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

### <a name="version-1x"></a>1.x verzió

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

* A vagyoni `Host.Results` `Function`kategóriába tartozó `Error` naplók esetén csak az Application Insights nak küldjön csak szint felett. A szint `Warning` és az alatti naplók figyelmen kívül kerülnek.
* A kategóriákkal `Host.Aggregator`rendelkező naplók esetén küldje el az összes naplót az Application Insightsnak. A `Trace` napló szintje megegyezik azzal, `Verbose`amit egyes `Trace` favágók hívnak , de a [host.json] fájlban használják.
* Az összes többi naplók, `Information` csak szint felett, és az Application Insights.

A [host.json] kategóriaértéke az azonos értékkel kezdődő összes kategória naplózását szabályozza. `Host`a [host.json] a `Host.General` `Host.Executor`, `Host.Results`, és így tovább naplózást szabályozza.

Ha [a host.json] több kategóriát is tartalmaz, amelyek ugyanazzal a karakterlánccal kezdődnek, a hosszabbak kerülnek egyeztetésre. Tegyük fel, hogy mindent `Host.Aggregator` szeretne `Error` a futásidejűtől `Host.Aggregator` kezdve, `Information` kivéve a naplózást a szinten, de a szinten szeretne naplózni:

### <a name="version-2x-and-later"></a>2.x-es és újabb verzió

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

### <a name="version-1x"></a>1.x verzió 

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

Egy kategória összes naplójának letiltásához `None`használja a naplószintet. Az adott kategóriában nincsenek naplók, és nincs felette naplószint.

## <a name="configure-the-aggregator"></a>A gyűjtő konfigurálása

Az előző szakaszban megjegyeztük, a futásidejű összesíti a függvényvégrehajtások adatait egy adott időszakban. Az alapértelmezett időszak 30 másodperc vagy 1000 futtatás, amelyik előbb következik be. Ezt a beállítást a [host.json] fájlban állíthatja be.  Például:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Mintavétel konfigurálása

Az Application Insights rendelkezik egy [mintavételi](../azure-monitor/app/sampling.md) szolgáltatás, amely megvédi a túl sok telemetriai adatok at befejezett végrehajtások csúcsterhelés idején. Ha a bejövő végrehajtások aránya meghaladja a megadott küszöbértéket, az Application Insights elkezdi véletlenszerűen figyelmen kívül hagyni a bejövő végrehajtásik egy részét. A másodpercenkénti végrehajtások maximális száma alapértelmezett beállítása 20 (az 1.x verzióban öt). A mintavételezéskonfigurálható a [host.json].  Például:

### <a name="version-2x-and-later"></a>2.x-es és újabb verzió

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

### <a name="version-1x"></a>1.x verzió 

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
> [A mintavételezés](../azure-monitor/app/sampling.md) alapértelmezés szerint engedélyezve van. Ha úgy tűnik, hogy hiányoznak az adatok, előfordulhat, hogy módosítania kell a mintavételi beállításokat, hogy illeszkedjen az adott figyelési forgatókönyvhez.

## <a name="write-logs-in-c-functions"></a>Naplók írása C#-függvényekben

Naplót írhat a függvénykódban, amelyek nyomkövetésként jelennek meg az Application Insightsban.

### <a name="ilogger"></a>ILogger

Paraméter helyett [iLogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) paramétert `TraceWriter` használjon a függvényekben. A használatával `TraceWriter` létrehozott naplók az Application `ILogger` Insights ra kerül, de lehetővé teszi a [strukturált naplózást.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Egy `ILogger` objektum, akkor `Log<level>` hívja [kiterjesztés módszerek ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) létrehozni naplók. A következő kód `Information` a "Function.<YOUR_FUNCTION_NAME> kategóriával rendelkező naplókat ír. Felhasználó."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturált naplózás

A helyőrzők sorrendje, nem pedig a nevük határozza meg, hogy a naplóüzenetben mely paramétereket használja a rendszer. Tegyük fel, hogy a következő kóddal rendelkezik:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Ha megtartja ugyanazt az üzenetkarakterláncot, és megfordítja a paraméterek sorrendjét, az eredményül kapott üzenetszöveg nem a megfelelő helyen fogja tartani az értékeket.

A helyőrzők kezelése így történik, így strukturált naplózást végezhet. Az Application Insights tárolja a paraméter név-érték pár és az üzenet karakterlánc. Az eredmény az, hogy az üzenet argumentumai olyan mezőkké válnak, amelyekről lekérdezhető.

Ha a naplózó metódushívása az előző példához `customDimensions.prop__rowKey`hasonlónak tűnik, lekérdezheti a mezőt . Az `prop__` előtag hozzáadódik annak érdekében, hogy ne legyenek ütközések a futásidejű által hozzáadott mezők és a függvénykód által hozzáadott mezők között.

Az eredeti üzenetkarakterláncra a mezőre hivatkozva `customDimensions.prop__{OriginalFormat}`is lekérdezhetünk.  

Itt egy minta JSON `customDimensions` ábrázolása az adatok:

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

### <a name="custom-metrics-logging"></a>Egyéni mérőszámok naplózása

A C# parancsfájl-függvények, `LogMetric` a `ILogger` bővítmény metódus használatával egyéni metrikák at Application Insights. Itt egy minta módszer hív:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ez a kód a `TrackMetric` .NET Application Insights API használatával történő hívás alternatívája.

## <a name="write-logs-in-javascript-functions"></a>Naplók írása JavaScript-függvényekben

A Node.js függvényekben naplók írására használható. `context.log` A strukturált naplózás nincs engedélyezve.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Egyéni mérőszámok naplózása

Ha a Functions futtatóidő [1.x-es verzióján](functions-versions.md#creating-1x-apps) fut, a Node.js függvények a módszer segítségével egyéni metrikákat hozhatnak létre az `context.log.metric` Application Insightsban. Ezt a módszert jelenleg nem támogatja a 2.x-es és újabb verzió. Itt egy minta módszer hív:

```javascript
context.log.metric("TestMetric", 1234);
```

Ez a kód a `trackMetric` Node.js SDK application insights használatával történő hívás alternatívája.

## <a name="log-custom-telemetry-in-c-functions"></a>Egyéni telemetria naplózása C#-függvényekben

Az Application Insights SDK-nak van egy Függvényspecifikus verziója, amelynek segítségével egyéni telemetriai adatokat küldhet a függvényekből az Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights)rendszerbe. A csomag telepítéséhez használja a parancssor következő parancsát:

# <a name="command"></a>[Parancs](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Ebben a parancsban cserélje le `<VERSION>` a csomag egy olyan verzióját, amely támogatja a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)telepített verzióját. 

A következő C# példák az [egyéni telemetriai API-t](../azure-monitor/app/api-custom-events-metrics.md)használja. A példa egy .NET osztálytár, de az Application Insights-kód megegyezik a C# parancsfájl.

### <a name="version-2x-and-later"></a>2.x-es és újabb verzió

A futásidejű 2.x-es és újabb verziói az Application Insights újabb funkcióit használják a telemetriai adatok és az aktuális művelet automatikus összeegyeztéséhez. Nincs szükség a művelet `Id`, a `ParentId`mező `Name` vagy a mezők manuális beállítására.

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

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) a jelenleg ajánlott API-t egy metrika létrehozásához.

### <a name="version-1x"></a>1.x verzió

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

Ne `TrackRequest` hívjon, `StartOperation<RequestTelemetry>` vagy mert ismétlődő függvénymeghívási kérelmeket fog látni.  A Függvények futásidejű automatikusan nyomon követi a kérelmeket.

Ne állítsa `telemetryClient.Context.Operation.Id`be . Ez a globális beállítás helytelen korrelációt okoz, ha sok függvény fut egyszerre. Ehelyett hozzon létre egy új`DependencyTelemetry` `EventTelemetry`telemetriai `Context` példányt ( , ) és módosítsa annak tulajdonságát. Ezután adja át a telemetriai `TelemetryClient` `TrackDependency()`példányt a megfelelő `Track` metódusnak a ( , `TrackEvent()`, `TrackMetric()`. Ez a módszer biztosítja, hogy a telemetriai adatok megfelelő korrelációs részleteket az aktuális függvény meghívása.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Egyéni telemetria naplózása JavaScript-függvényekben

Az alábbiakban egy mintakódrészletet talál, amely egyéni telemetriát küld az [Application Insights Node.js SDK-val:](https://github.com/microsoft/applicationinsights-node.js)

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

A `tagOverrides` paraméter `operation_Id` beállítja a függvény meghívási azonosítóját. Ez a beállítás lehetővé teszi, hogy egy adott függvény meghívásához az összes automatikusan generált és egyéni telemetriai adatok korreláljon.

## <a name="dependencies"></a>Függőségek

A v2 függvények automatikusan gyűjtik a HTTP-kérelmek, a ServiceBus, az EventHub és az SQL függőségeit.

A függőségek megjelenítéséhez egyéni kódot írhat. Példákat a [C# egyéni telemetriai szakaszban](#log-custom-telemetry-in-c-functions)található mintakódban talál. A mintakód egy *alkalmazásleképezést* eredményez az Application Insightsban, amely az alábbi hozadékhoz hasonlóan jelenik meg:

![Alkalmazástérkép](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Application Insights-integráció engedélyezése

Egy függvényalkalmazás adatokat küldeni az Application Insights, ismernie kell az Application Insights-erőforrás instrumentation kulcsát. A kulcsnak **a APPINSIGHTS_INSTRUMENTATIONKEY**nevű alkalmazásbeállításban kell lennie.

Amikor létrehozza a függvényalkalmazást [az Azure Portalon,](functions-create-first-azure-function.md)a parancssorból az [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md)használatával, vagy a Visual [Studio-kód](functions-create-first-function-vs-code.md)használatával, az Application Insights-integráció alapértelmezés szerint engedélyezve van. Az Application Insights-erőforrás neve megegyezik a függvényalkalmazás nevével, és ugyanabban a régióban vagy a legközelebbi régióban jön létre.

### <a name="new-function-app-in-the-portal"></a>Új függvényalkalmazás a portálon

A létrehozandó Application Insights erőforrás áttekintéséhez jelölje ki az **Application Insights** ablak kibontásához. Módosíthatja az **új erőforrás nevét,** vagy válasszon egy másik **helyet** egy [Azure-földrajzi területen,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol az adatokat szeretné tárolni.

![Az Application Insights engedélyezése függvényalkalmazás létrehozása közben](media/functions-monitoring/enable-ai-new-function-app.png)

Ha a Létrehozás lehetőséget **választja,** létrejön egy Application `APPINSIGHTS_INSTRUMENTATIONKEY` Insights-erőforrás a függvényalkalmazással, amely az alkalmazásbeállításokban van beállítva. Minden készen áll.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Hozzáadás meglévő függvényalkalmazáshoz 

Amikor függvényalkalmazást hoz létre a [Visual Studio](functions-create-your-first-function-visual-studio.md)használatával, létre kell hoznia az Application Insights erőforrást. Ezután hozzáadhatja a műszerezési kulcsot az adott erőforrásból alkalmazásbeállításként a függvényalkalmazásban.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

A Functions korai verziói beépített figyelést használtak, ami már nem ajánlott. Az Application Insights-integráció engedélyezésekor egy ilyen függvényalkalmazáshoz le kell [tiltania a beépített naplózást](#disable-built-in-logging)is.  

## <a name="report-issues"></a>Problémák bejelentése

Ha jelentést szeretne tenni egy probléma az Application Insights-integrációval a Függvényekben, vagy javaslatot vagy kérést szeretne [tenni, hozzon létre egy problémát a GitHubon.](https://github.com/Azure/Azure-Functions/issues/new)

## <a name="streaming-logs"></a>Streamelési naplók

Egy alkalmazás fejlesztése közben gyakran szeretné látni, hogy mi van írva a naplók közel valós időben, amikor az Azure-ban fut.

A függvényvégrehajtások által létrehozott naplófájlok adatfolyamának megtekintésére kétféleképpen tekintheti meg.

* **Beépített naplóstreamelés:** az App Service platform lehetővé teszi az alkalmazásnaplófájlok adatfolyamának megtekintését. Ez megegyezik a helyi [fejlesztés](functions-develop-local.md) során a függvények hibakeresése során és a portál **Teszt** lapján látható kimenettel. Minden naplóalapú információ megjelenik. További információt a Stream naplók című [témakörben talál.](../app-service/troubleshoot-diagnostic-logs.md#stream-logs) Ez a streamelési módszer csak egyetlen példányt támogat, és nem használható linuxos linuxos alkalmazással a felhasználási csomagban.

* **Élő metrikák stream:** ha a függvényalkalmazás csatlakozik az [Application Insights,](#enable-application-insights-integration)megtekintheti a naplóadatok és egyéb metrikák közel valós időben az Azure Portalon a [Live Metrics Stream](../azure-monitor/app/live-stream.md). Ezt a módszert akkor használja, ha több példányon vagy Linuxon futó függvények figyelése egy használati tervben. Ez a módszer [mintavételezett adatokat](#configure-sampling)használ.

A naplófolyamok a portálon és a legtöbb helyi fejlesztési környezetben is megtekinthetők. 

### <a name="portal"></a>Portál

A portálon mindkét típusú naplóadatfolyam ot megtekintheti.

#### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

A portálon lévő streamelési naplók megtekintéséhez válassza a **Platform funkciók** lapját a függvényalkalmazásban. Ezután a **Figyelés**csoportban válassza **a Naplóstreamelés lehetőséget.**

![Streamelési naplók engedélyezése a portálon](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ez összekapcsolja az alkalmazást a naplóstreamelési szolgáltatással, és az alkalmazásnaplók megjelennek az ablakban. Az **alkalmazásnaplók** és a **webkiszolgálói naplók**között válthat.  

![Streamelési naplók megtekintése a portálon](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Élő metrikastream

Az alkalmazás élő mérőszám-streamjének megtekintéséhez válassza a függvényalkalmazás **Áttekintés lapját.** Ha az Application Insights lehetővé teszi, megjelenik egy **Application Insights** hivatkozás **a Konfigurált szolgáltatások**területen. Ez a hivatkozás az alkalmazás alkalmazáselemzési lapjára mutat.

Az Application Insights ban válassza az **Élő metrikák stream**. [A mintanapló-bejegyzések](#configure-sampling) a **Mintatelemetria**területen jelennek meg.

![Élő mérőszámok stream megtekintése a portálon](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Alapvető eszközök

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

A streamelési naplókat az [Azure CLI](/cli/azure/install-azure-cli)használatával engedélyezheti. A következő parancsokkal jelentkezhet be, választhatja ki az előfizetést, és streamelheti a naplófájlokat:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

A streamelési naplókat az [Azure PowerShell](/powershell/azure/overview)használatával engedélyezheti. A PowerShell esetében a következő parancsokkal adja hozzá Azure-fiókját, válassza ki az előfizetést, és streamelje a naplófájlokat:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Beépített naplózás letiltása

Ha engedélyezi az Application Insights, tiltsa le a beépített naplózás, amely az Azure Storage-ot használja. A beépített naplózás hasznos a könnyű számítási feladatok teszteléséhez, de nem nagy terhelésű éles használatra készült. Éles figyeléséhez azt javasoljuk, Application Insights. Ha a beépített naplózás éles környezetben van használva, a naplózási rekord lehet, hogy hiányos, mert az Azure Storage-szabályozás.

A beépített naplózás letiltásához `AzureWebJobsDashboard` törölje az alkalmazásbeállítást. Az Azure Portalon lévő alkalmazásbeállítások törléséről a [Függvényalkalmazás kezelése](functions-how-to-use-azure-function-app-settings.md#settings)című **alkalmazásbeállítások** című szakaszban olvashat. Az alkalmazásbeállítás törlése előtt győződjön meg arról, hogy ugyanabban a függvényalkalmazásban egyetlen meglévő függvény sem használja az Azure Storage-eseményindítók vagy -kötések beállítását.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core naplózás](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
