---
title: Az Azure Functions monitorozása
description: Megismerkedhet az Azure Application Insights az Azure Functions figyelése függvény végrehajtása.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 0e4c308e745cbf2ffbc18f64101043aff3ddde35
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495685"
---
# <a name="monitor-azure-functions"></a>Az Azure Functions monitorozása

[Az Azure Functions](functions-overview.md) biztosít beépített integráció [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) functions figyelése. Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Functions szolgáltatást a rendszer által létrehozott naplófájlok küldeni az Application Insights.

Azt javasoljuk, hogy az Application Insights használatát, mert a napló, a teljesítmény és a hiba az adatok gyűjtésével. Automatikusan felismeri a teljesítményanomáliákat és segítséget nyújt a problémák diagnosztizálása és megérteni, hogyan használhatók a függvényeit hatékony elemzőeszközöket tartalmaz. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Az Application Insights helyi függvény projekt alkalmazásfejlesztés során is használható. További információkért lásd: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

A szükséges az Application Insights-kialakítási Azure Functions be van építve, mert szüksége egy érvényes kialakítási kulcsot a függvényalkalmazás csatlakozni az Application Insights-erőforrás.

## <a name="application-insights-pricing-and-limits"></a>Az Application Insights díjszabása és korlátozásai

Kipróbálhatja az Application Insights-integráció a Függvényalkalmazások ingyenes. Nincs napi korlát mennyi adatot ingyenes dolgozhassák fel. Előfordulhat, hogy eléri ezt a korlátot a tesztelés során. Az Azure portal és az e-mailes értesítéseket biztosít, amikor a, Ön hamarosan eléri a napi korlátot. Ha hagyja ki ezeket a riasztásokat, és nyomja le a korlátot, nem az új naplók jelenik meg az Application Insights-lekérdezéseket. Vegye figyelembe a korlátot, a szükségtelen hibaelhárítási idő elkerülése érdekében. További információkért lásd: [az Application Insights árak és adatmennyiségek kezelése](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Az Application Insights-integráció engedélyezése

Egy függvényalkalmazás adatokat küldeni az Application Insights, tudnia kell, a rendszerállapotkulcsot az Application Insights-erőforrás. A kulcsnak kell lennie egy alkalmazásban nevű beállításhoz **állítani az APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Új függvényalkalmazáshoz a portálon

Ha Ön [a függvényalkalmazás létrehozása az Azure Portalon](functions-create-first-azure-function.md), Application Insights integrációja alapértelmezés szerint engedélyezve van. Application Insights-erőforrás neve megegyezik a függvényalkalmazás rendelkezik, és ugyanabban a régióban, vagy a legközelebbi régióban jön.

Tekintse át a létrehozott Application Insights-erőforrást, válassza azt, bontsa ki a **Application Insights** ablak. Módosíthatja a **új-erőforrásnév** , vagy válasszon egy másik **hely** a egy [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) ahol az adatokat tárolni szeretné.

![Függvényalkalmazás létrehozása során az Application Insights engedélyezése](media/functions-monitoring/enable-ai-new-function-app.png)

Ha úgy dönt **létrehozás**, Application Insights-erőforrás jön létre a függvényalkalmazást, amely rendelkezik a `APPINSIGHTS_INSTRUMENTATIONKEY` beállítása az alkalmazás beállításait. Minden készen áll.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Meglévő függvényalkalmazással hozzáadása 

Létrehozásakor egy függvény használatával a [Azure CLI-vel](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md), vagy [Visual Studio Code](functions-create-first-function-vs-code.md), létre kell hoznia az Application Insights-erőforrást. Ezután hozzáadhatja a kialakítási kulcsot adott erőforrásból, alkalmazás-beállítás a függvényalkalmazásban.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Függvények korai verziói használt beépített figyelés, amely a továbbiakban nem javasolt. Amikor engedélyezi az ilyen egy függvényalkalmazást az Application Insights-integráció, akkor is kell [tiltsa le a beépített naplózási](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>A figyelés lapon telemetria megtekintése

A [engedélyezve van az Application Insights-integráció](#enable-application-insights-integration), a telemetriai adatokat is megtekintheti a **figyelő** fülre.

1. Válassza ki a függvényalkalmazás oldaláról, a függvény, amely legalább egyszer lefutott, az Application Insights konfigurálása után. Válassza ki a **figyelő** fülre.

   ![Válassza ki a figyelés lap](media/functions-monitoring/monitor-tab.png)

1. Válassza ki **frissítése** rendszeres időközönként, amíg megjelenik a függvény meghívásához listája.

   A lista megjelenését, miközben a telemetriai ügyfél kötegeli az adatokat a kiszolgáló továbbítja az akár öt percet is igénybe vehet. (A késés nem vonatkozik a [élő metrikák Stream](../azure-monitor/app/live-stream.md). A szolgáltatás csatlakozik a Functions-gazdagép az oldal betöltésekor, így közvetlenül az oldalra a naplók átvitt.)

   ![Indítások listája](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Egy adott függvény meghívási számára a naplók megtekintéséhez válassza ki a **dátum** a meghívási oszlop hivatkozására.

   ![Hívás részletei hivatkozásra](media/functions-monitoring/invocation-details-link-ai.png)

   A meghívási naplózási kimenetét egy új lap jelenik meg.

   ![Hívás részletei](media/functions-monitoring/invocation-details-ai.png)

Láthatja, hogy rendelkezik-e a két lap egy **Futtatás az Application Insightsban** hivatkozás az Application Insights Analytics-lekérdezéshez, amely lekéri az adatokat.

![Futtatás az Application Insightsban](media/functions-monitoring/run-in-ai.png)

A következő lekérdezés akkor jelenik meg. Láthatja, hogy a meghívási lista korlátozva az utolsó 30 nap. A lista mutatja azokat a 20-nál több sort (`where timestamp > ago(30d) | take 20`). A meghívás részletei Tranzakciólista nincs korlát az elmúlt 30 napra vonatkozóan.

![Application Insights-elemzési meghívási listája](media/functions-monitoring/ai-analytics-invocation-list.png)

További információkért lásd: [telemetriai adatok lekérdezése](#query-telemetry-data) a cikk későbbi részében.

## <a name="view-telemetry-in-application-insights"></a>Az Application Insights telemetria megtekintése

Az Azure Portalon egy függvényalkalmazást az Application Insights megnyitásához nyissa meg a függvényalkalmazás **áttekintése** lapot. A **konfigurált szolgáltatások**válassza **Application Insights**.

![Nyissa meg az Application Insights – áttekintés oldalát](media/functions-monitoring/ai-link.png)

Az Application Insights használatával kapcsolatos információkért lásd: a [Application Insights dokumentáció](https://docs.microsoft.com/azure/application-insights/). Ez a szakasz bemutatja néhány példa a adatainak megtekintése az Application Insightsban. Ha már ismeri az Application insights szolgáltatással, akkor lépjen közvetlenül [konfigurálása és testreszabása a telemetriai adatokat a szakaszok](#configure-categories-and-log-levels).

![Az Application Insights áttekintő lapja](media/functions-monitoring/metrics-explorer.png)

A következő területeken az Application Insights hasznos lehet a viselkedést, teljesítmény és hibák az a funkciók kiértékelése során:

| Tab | Leírás |
| ---- | ----------- |
| **[Hibák](../azure-monitor/app/asp-net-exceptions.md)** |  Hozzon létre a diagramok és értesítések függvény hibák és kivételek alapján. A **műveletnév** függvény neve. Hibák a függőségek nem jelennek meg, kivéve, ha a függőségekhez egyéni telemetriát megvalósítása. |
| **[Teljesítmény](../azure-monitor/app/performance-counters.md)** | Teljesítménnyel kapcsolatos problémák elemzéséhez. |
| **Kiszolgálók** | Erőforrás-használat és a kiszolgáló sebességet megtekintése. Ezek az adatok hibakeresési forgatókönyvek, ahol funkciók vannak bogging le a mögöttes erőforrások hasznos lehet. Kiszolgálók nevezzük **Felhőbeli a szerepkörpéldányt**. |
| **[Mérőszámok](../azure-monitor/app/metrics-explorer.md)** | Hozzon létre diagramokat és metrikák alapuló riasztások. Mérőszámok közé tartozik a függvény meghívásához, a végrehajtási idő és a sikerességi arányokat számát. |
| **[Élő metrikastream](../azure-monitor/app/live-stream.md)** | A mérőszámadatok megtekintése, valós idejű létrehozás. |

## <a name="query-telemetry-data"></a>Telemetriai adatok lekérdezése

[Application Insights-elemzési](../azure-monitor/app/analytics.md) figyelmébe, amely az összes telemetriai adatok egy adatbázisban lévő táblák formájában. Analytics kibontása, ahol elvégezhető a módosításuk és az adatok megjelenítése egy lekérdezési nyelvet biztosít.

![Válassza ki az Analytics](media/functions-monitoring/select-analytics.png)

![Analytics-példa](media/functions-monitoring/analytics-traces.png)

Íme egy lekérdezési példa bemutatja a kérések száma worker eloszlása az elmúlt 30 percben.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

A rendelkezésre álló táblák jelennek meg a **séma** lapra a bal oldalon. Az alábbi táblázatokban függvény meghívásához által létrehozott adatok találhatja meg:

| Tábla | Leírás |
| ----- | ----------- |
| **nyomok** | Függvénykódot és a modul által létrehozott naplókat. |
| **kérés** | Minden függvény meghívási egy kérelem. |
| **kivétel** | A modul által okozott kivételek. |
| **customMetrics** | A sikeres és sikertelen meghívásához, a sikerességi arányról és az időtartam száma. |
| **customEvents** | Események nyomon követett futásidőben, például: Aktiválja a függvényt egy HTTP-kérelmekre. |
| **PerformanceCounters** | Információ arról, hogy a függvények futnak a kiszolgáló teljesítményét. |

A más táblák rendelkezésre állási tesztek, és az ügyfél és a böngésző telemetriai vonatkoznak. Egyéni telemetriai adatok hozzáadása valósítható meg.

Minden egyes táblában lévő egyes funkciók jellemző adatok szerepel egy `customDimensions` mező.  Ha például az alábbi lekérdezés lekéri a naplózási szint rendelkező összes nyomkövetési `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

A modul biztosítja a `customDimensions.LogLevel` és `customDimensions.Category` mezőket. Megadhat további mezőket a naplók a függvénykódot az írást. Lásd: [strukturált naplózást](#structured-logging) a cikk későbbi részében.

## <a name="configure-categories-and-log-levels"></a>Kategóriák konfigurálása és a szintek naplózása

Az Application Insights egyéni konfiguráció nélkül is használhatja. Az alapértelmezett konfigurációt eredményezhet nagy mennyiségű adat. Ha egy Visual Studio Azure-előfizetést használ, a adatkorlátjának előfordulhat, hogy eléri az Application Insights. Ez a cikk későbbi részében, megtudhatja, hogyan konfigurálja, és testre szabhatja az adatokat, amelyek a függvények küldenek az Application Insights. A függvényalkalmazás a naplózás úgy van konfigurálva az a [host.json] fájlt.

### <a name="categories"></a>Kategóriák

Az Azure Functions naplózó tartalmaz egy *kategória* minden napló. A kategória azt jelzi, hogy melyik része a futtatókörnyezet kód vagy a függvénykódot okkal készítette el a naplóban. 

A Functions futtatókörnyezete naplókat hoz létre a "Host." karakterrel kezdődő kategória A "függvény elindult," "függvény végre" és "Befejezve" függvénynaplókat "Host.Executor." kategóriába tartoznak. 

Naplók a függvénykódban ír, ha a kategória-e a "Függvény."

### <a name="log-levels"></a>Naplózási szintek

Az Azure Functions naplózó is tartalmaz egy *naplózási szintjének* minden napló a. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) enumeráció, és az egész kód azt jelzi, hogy relatív fontosságát:

|LogLevel    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

Naplózási szintjének `None` ismertetése a következő szakaszban. 

### <a name="log-configuration-in-hostjson"></a>Host.json a folyamatnapló-konfiguráció

A [host.json] fájl milyen mértékű naplózása egy függvényalkalmazást az Application Insights küld konfigurálja. Minden egyes kategóriánál adja meg a minimális naplózási szint küldése. Nincsenek a két példa: az első példa célok a [funkciók verzió 2.x verziójú futtatókörnyezet](functions-versions.md#version-2x) (.NET Core) és a második példa 1.x verzió futásidejű.

### <a name="version-2x"></a>Verzió 2.x

A 2.x-futtatókörnyezet-használja a [.NET Core naplózási szűrő hierarchia](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

### <a name="version-1x"></a>Verzió 1.x

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

Ebben a példában állítja be a következő szabályok:

* Kategória naplók `Host.Results` vagy `Function`, csak küldése `Error` szint és a fenti az Application Insightsba. A naplók `Warning` szintjét és az alábbiakban figyelmen kívül hagyja.
* A kategória-naplók `Host.Aggregator`, minden naplók küldése az Application Insightsba. A `Trace` naplózási szint: ugyanaz, mint néhány másolása hívás `Verbose`, de `Trace` a a [host.json] fájlt.
* Az összes többi naplók küldése csak `Information` szint és a fenti az Application Insightsba.

A kategória értéke [host.json] kezdődő ugyanazt az értéket az összes kategória naplózását szabályozza. `Host` a [host.json] naplózási vezérlőket `Host.General`, `Host.Executor`, `Host.Results`, és így tovább.

Ha [host.json] magában foglalja a több kategóriát, amely azonos karakterláncra indítása hosszabb azokat először definícióiból. Tegyük fel, hogy azt szeretné, hogy a futtatókörnyezet, kivéve a `Host.Aggregator` jelentkezhet `Error` szintjét, de azt szeretné `Host.Aggregator` jelentkezhet a `Information` szintje:

### <a name="version-2x"></a>Verzió 2.x 

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

### <a name="version-1x"></a>Verzió 1.x 

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

A kategória összes napló le, használhatja a naplózási szint `None`. Nincsenek naplók írt adott kategóriával rendelkezik, és nincs naplózási szint fölött van.

A következő szakaszok ismertetik a naplókat, amely létrehozza a futtatókörnyezet fő kategóriája. 

### <a name="category-hostresults"></a>Kategória Host.Results

Ezek a naplók megjelenítése "kérések", az Application insights szolgáltatásban. Azt jelzik, hogy sikeres vagy sikertelen függvény.

![Kérelmeit tartalmazó diagram](media/functions-monitoring/requests-chart.png)

Ezek a naplók mindegyikét írt `Information` szintjét. Ha szűrést `Warning` vagy újabb, nem jelenik meg az adatokat.

### <a name="category-hostaggregator"></a>Kategória Host.Aggregator

Ezek a naplók webszolgáltatásokon keresztül bonyolítják számát és a függvény meghívásához, átlagokat egy [konfigurálható](#configure-the-aggregator) időszakának idő. Az alapértelmezett időszak 30 másodperc, vagy 1000 eredmény, amelyiket hamarabb. 

A naplók érhetők el a **customMetrics** tábla az Application Insightsban. Például a fut, a sikerességi arányról és az időtartam száma.

![customMetrics lekérdezés](media/functions-monitoring/custom-metrics-query.png)

Ezek a naplók mindegyikét írt `Information` szintjét. Ha szűrést `Warning` vagy újabb, nem jelenik meg az adatokat.

### <a name="other-categories"></a>Más kategóriák

Kategóriák eltérő már az összes napló felsorolt érhetők el a **nyomkövetések** tábla az Application Insightsban.

![nyomok lekérdezés](media/functions-monitoring/analytics-traces.png)

A kategóriák kezdődő összes napló `Host` a Functions futtatókörnyezete készültek. A "Függvény lépések" és "Befejezve" függvénynaplókat kategóriába tartoznak `Host.Executor`. Sikeres futtatások vannak ezek a naplók `Information` szintjét. Kivétel jelentkezett `Error` szintjét. A futtatókörnyezet is létrehoz `Warning` . szintű naplók, például: az ártalmas üzenetsorba küldött üzenetek várólistára.

A függvénykód által írt naplók kategóriába tartoznak `Function` és bármely naplózási szint is lehetnek.

## <a name="configure-the-aggregator"></a>A gyűjtő konfigurálása

Az előző szakaszban feljegyzett futásidejű függvénykivételek adatait egy időszakra vonatkozóan összesíti. Az alapértelmezett időszak 30 másodperc, vagy 1000 fut, amelyik először bekövetkezik. Ez a beállítás konfigurálása a [host.json] fájlt.  Például:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Mintavétel konfigurálása

Az Application Insights rendelkezik egy [mintavételi](../azure-monitor/app/sampling.md) szolgáltatás, amely védheti meg a túl sok telemetriai adatokat előállító esetenként a csúcsterhelés között a végrehajtás befejeződött. A bejövő végrehajtások száma meghalad egy megadott küszöbértéket, az Application Insights véletlenszerűen figyelmen kívül a bejövő végrehajtások némelyike elindítja. Az alapértelmezett beállítás for végrehajtások másodpercenként maximális száma: 20 (öt verzióban 1.x). Beállíthatja, hogy a mintavétel [host.json].  Például:

### <a name="version-2x"></a>Verzió 2.x 

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

### <a name="version-1x"></a>Verzió 1.x 

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
> [Mintavételi](../azure-monitor/app/sampling.md) alapértelmezés szerint engedélyezve van. Ha jelennek meg adatok hiányoznak, szüksége lehet módosítani a mintavételezési beállításokat adott figyelési helyzethez.

## <a name="write-logs-in-c-functions"></a>Naplók írhat C#-függvények

Naplók írhat a függvénykódban az Application Insights nyomkövetésként jelennek meg.

### <a name="ilogger"></a>ILogger

Használja az [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) paraméter az a funkciók helyett egy `TraceWriter` paraméter. Használatával létrehozott naplók `TraceWriter` nyissa meg az Application Insightsba, de `ILogger` lehetővé teszi [strukturált naplózást](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Az egy `ILogger` objektumot, hívja `Log<level>` [ILogger a bővítő metódusokat](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) naplók létrehozása. A következő kód az írási műveletek `Information` naplók kategória "Függvény."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturált naplózást

Nem a nevüket, a helyőrzők sorrendje határozza meg, mely paraméterek szerepelnek, a napló üzenetben. Tegyük fel, a következő kódot:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Ha ugyanazt a üzenet karakterláncot tároljuk, és a paramétereket az sorrendjét, az eredményül kapott üzenet szövege kellene az értékeket a megfelelő helyeken.

Helyőrzők ily módon kezeli, így végezhet strukturált naplózást. Az Application Insights tárolja a paraméter név-érték párok, és az üzenet-karakterlánc. Az eredménye, hogy az üzenet argumentumok válik a lekérdezhető mezők.

Ha a naplózó metódus meghívása az előző példához hasonlóan néz ki, lekérdezheti, ha a mező `customDimensions.prop__rowKey`. A `prop__` előtag lesz hozzáadva, annak érdekében, hogy nincsenek a mezőket a modul ad hozzá, a mezők a függvénykódot között nincsenek ütközések hozzáadja.

Ön úgy is lekérdezheti az eredeti üzenet karakterláncot a mezőre hivatkozik `customDimensions.prop__{OriginalFormat}`.  

Íme egy példa JSON-reprezentációja `customDimensions` adatokat:

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

A parancsfájl C#-függvények, használhatja a `LogMetric` metódust a `ILogger` hozhat létre egyéni metrikákat az Application Insightsban. Íme egy példa a metódushívás:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ez a kód hívása helyett a `TrackMetric` a .NET-hez az Application Insights API használatával.

## <a name="write-logs-in-javascript-functions"></a>JavaScript-függvények naplóznak

A Node.js-függvények, használjon `context.log` naplók írni. Strukturált naplózást nincs engedélyezve.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Egyéni metrikák naplózása

Amikor futtatja [verzió 1.x](functions-versions.md#creating-1x-apps) Node.js-függvények használhatja a Functions-futtatókörnyezet, az a `context.log.metric` metódussal hoz létre egyéni metrikákat az Application Insights. Ez a módszer jelenleg nem támogatott verziója az 2.x. Íme egy példa a metódushívás:

```javascript
context.log.metric("TestMetric", 1234);
```

Ez a kód hívása helyett a `trackMetric` az Application Insights Node.js SDK használatával.

## <a name="log-custom-telemetry-in-c-functions"></a>Egyéni telemetriát bejelentkezés C# funkciók

Használhatja a [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) egyéni telemetriai adatokat küldeni az Application Insights NuGet-csomagot. A következő C# példában a [API egyéni telemetriához](../azure-monitor/app/api-custom-events-metrics.md). A példában az a .NET osztálytár, de az Application Insights-kódot a C#-szkript esetében megegyezik.

### <a name="version-2x"></a>Verzió 2.x

A verzió 2.x verziójú futtatókörnyezet újabb funkciókat használ az Application Insights automatikusan telemetria korrelációját, a jelenlegi műveletet. Nem kell manuálisan beállítani a művelet `Id`, `ParentId`, vagy `Name` mezőket.

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

### <a name="version-1x"></a>Verzió 1.x

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

Ne hívja a `TrackRequest` vagy `StartOperation<RequestTelemetry>` mivel látni fogja a függvény meghívási ismétlődő kérelmeket.  A Functions futtatókörnyezete automatikusan nyomon követi a kérelmeket.

Nem állít be `telemetryClient.Context.Operation.Id`. A globális beállítás hatására a helytelen korrelációs, ha sok funkciók fut egyidejűleg. Ehelyett hozzon létre egy új telemetria-példány (`DependencyTelemetry`, `EventTelemetry`) és módosíthatja annak `Context` tulajdonság. Ezután adja át a telemetria-példány a megfelelő `Track` metódust `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Ez a módszer biztosítja, hogy a telemetria az aktuális függvény meghívási megfelelő korrelációs részleteit.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Egyéni telemetriát napló a JavaScript-függvények

A [Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) jelenleg bétaverzióban van. Íme néhány mintakódját, amely egyéni telemetriát küld az Application Insights:

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

A `tagOverrides` paraméterkészlettel a `operation_Id` a függvény meghívási azonosító. Ez a beállítás lehetővé teszi, hogy korrelációját, az automatikusan létrehozott és egyéni telemetriai egy adott függvény meghívási mindegyikét.

## <a name="dependencies"></a>Függőségek

Függőségek, a függvény rendelkező más szolgáltatások nem jelennek meg automatikusan. A függőségek megjelenítéséhez egyéni kódot is írhat. Példák: szereplő mintakódban a [ C# egyéni telemetriát szakasz](#log-custom-telemetry-in-c-functions). A mintakód eredményez olyan *alkalmazástérkép* az Application insights szolgáltatásban az alábbi képen láthatóhoz hasonló:

![Alkalmazástérkép](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>A jelentés kapcsolatos problémák

Az Application Insights-integráció a függvények hibát, vagy egy javaslat vagy a kérelem, [probléma létrehozása a Githubról](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Streamelési naplók

Az alkalmazások fejlesztése során hasznos gyakran közel valós idejű naplóinformációkat tekinthet. A stream a függvények az Azure Portalon vagy a helyi számítógépen parancssori munkamenet által létrehozott naplófájlok megtekintéséhez.

Ez a kimenet látható során a függvények hibakeresése egyenértékű a [helyi fejlesztési](functions-develop-local.md). További információkért lásd: [naplók közvetítése](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Folyamatos átviteli naplók csak egyetlen példány az a funkciók gazdagép támogatja. A függvény átméretezi több példányra, ha más a példányok adatait nem láthatók a naplózási adatfolyam. A [élő metrikák Stream](../azure-monitor/app/live-stream.md) Application Insights több példányt támogat. Bár még a közel valós idejű Stream analytics is alapján [változásához](#configure-sampling).

### <a name="portal"></a>Portál

Folyamatos átviteli naplók megtekintése a portálon, válassza ki a **platformfunkciók** függvényalkalmazásban a lapján. Ezután a **figyelés**, válassza a **naplóstreamelés**.

![Engedélyezze a folyamatos átviteli naplók a portálon](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ez az alkalmazás csatlakozik a napló streamelési szolgáltatás, és az alkalmazásnaplókat az ablakban jelenik meg. Választhat **alkalmazásnaplókat** és **webalkalmazás-naplók**.  

![Folyamatos átviteli naplók megtekintése a portálon](./media/functions-monitoring/streaming-logs-window.png)

### <a name="azure-cli"></a>Azure CLI

Folyamatos átviteli naplók használatával engedélyezheti a [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli). Az Azure CLI használatával az alábbi parancsok jelentkezzen be, válassza ki az előfizetést és a stream naplófájlokat:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Folyamatos átviteli naplók használatával engedélyezheti [Azure PowerShell-lel](/powershell/azure/overview). PowerShell esetén használja a következő parancsok hozzáadása az Azure-fiókja, válassza ki az előfizetést és a stream naplófájlokat:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Beépített naplózás letiltása

Ha engedélyezi az Application Insights, tiltsa le a beépített naplózási által használt Azure Storage. A beépített naplózási hasznos tesztelni a kisebb számítási feladatokhoz, de nagy terhelésű éles környezetben való használatra nem alkalmas. Éles környezetben a figyeléshez, javasoljuk, hogy az Application Insights. Ha beépített naplózást éles környezetben használja, a naplózás rekord hiányosak lehetnek az Azure Storage-szabályozás miatt.

Beépített naplózási letiltásához törölje a `AzureWebJobsDashboard` alkalmazásbeállítást. Az Azure Portalon Alkalmazásbeállítások törlésével kapcsolatos információkért lásd: a **Alkalmazásbeállítások** szakaszában [függvényalkalmazás kezelése](functions-how-to-use-azure-function-app-settings.md#settings). Mielőtt törli az alkalmazásbeállítást, ellenőrizze, nem ugyanaz a függvényalkalmazás a meglévő funkciók használata a beállítás az Azure Storage-eseményindítók és kötések.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core-naplózás](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
