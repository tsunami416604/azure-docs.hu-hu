---
title: Diagnosztika a Durable Functions-ben – Azure
description: Ismerje meg, hogyan diagnosztizálhatja a problémákat a Azure Functions Durable Functions bővítménnyel.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278193"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnosztika Durable Functions az Azure-ban

Több lehetőség is van a [Durable Functionsával](durable-functions-overview.md)kapcsolatos problémák diagnosztizálására. Ezek némelyike megegyezik a hagyományos függvényekre vonatkozó módszerekkel, mások pedig csak a Durable Functions függvényeihez használhatók.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) a diagnosztika és a figyelés ajánlott módszere Azure Functionsokban. Ugyanez vonatkozik a Durable Functionsra is. A Application Insightsnek a Function alkalmazásban való kihasználása áttekintését lásd: [Azure functions figyelése](../functions-monitoring.md).

A Azure Functions tartós bővítmény olyan *követési eseményeket* is kibocsát, amelyek segítségével nyomon követheti a folyamat végpontok közötti végrehajtását. Ezek a követési események a Azure Portal [Application Insights Analytics](../../azure-monitor/app/analytics.md) eszközének használatával találhatók meg és kérhetők le.

### <a name="tracking-data"></a>Adatkövetés

Egy összehangoló példány minden életciklus-eseménye egy követési eseményt ír a Application Insights **nyomkövetési** gyűjteményéből. Ez az esemény több mezőből álló **customDimensions** -adattartalmat tartalmaz.  A mezőnevek minden `prop__`előtagértéke.

* **hubName**: annak a feladatnak a neve, amelyben a rendszer fut.
* **appName**: a Function alkalmazás neve. Ez a mező akkor hasznos, ha több Function-alkalmazással is rendelkezik, amelyek ugyanazt a Application Insights példányt osztják meg.
* **slotName**: az [üzembe helyezési](../functions-deployment-slots.md) pont, amelyben az aktuális Function alkalmazás fut. Ez a mező akkor lehet hasznos, ha az üzembe helyezési pontokat kihasználva használja fel a folyamatokat.
* **függvénynév**: a Orchestrator vagy a tevékenység függvény neve.
* **functionType**: a függvény típusa, például **Orchestrator** vagy **tevékenység**.
* **instanceId**: a koordináló példány egyedi azonosítója.
* **állapot**: a példány életciklus-végrehajtási állapota. Érvényes értékek a következők:
  * **Ütemezve**: a függvény végrehajtásra lett ütemezve, de még nem indult el.
  * **Elindítva**: a függvény futása megkezdődött, de még nem várt vagy nem fejeződött be.
  * **Várt**: a Orchestrator ütemezett némi munkát, és arra vár, hogy befejeződjön.
  * **Figyelés**: a Orchestrator egy külső eseményről szóló értesítést figyel.
  * **Befejezett**: a függvény sikeresen befejeződött.
  * **Sikertelen**: a függvény hibával meghiúsult.
* **OK**: a nyomkövetési eseményhez kapcsolódó további információk. Ha például egy példány egy külső eseményről szóló értesítésre vár, akkor ez a mező a várt esemény nevét jelzi. Ha egy függvény meghiúsult, akkor ez a mező a hiba részleteit tartalmazza.
* **isReplay**: logikai érték, amely azt jelzi, hogy a követési esemény az újrajátszott végrehajtáshoz van-e.
* **extensionVersion**: a tartós feladat kiterjesztésének verziója. A verzió adatai különösen fontos adatokat jelentenek a bővítmény lehetséges hibáinak jelentésekor. A hosszan futó példányok több verziót is jelenthetnek, ha a futás közben frissítés történik.
* **sorszám**: az esemény végrehajtási sorszáma. Az időbélyeggel kombinálva a végrehajtás ideje alapján rendezheti az eseményeket. *Vegye figyelembe, hogy ez a szám nulla értékre áll vissza, ha a gazdagép újraindul, miközben a példány fut, ezért fontos, hogy először az időbélyegző alapján rendezze az értéket, majd sorszám.*

A Application Insightsra kibocsátott adatok részletességét az `host.json` fájl `logger` (functions 1. x) vagy `logging` (functions 2,0) szakasza is konfigurálhatja.

#### <a name="functions-10"></a>Függvények 1,0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Függvények 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Alapértelmezés szerint az összes nem újrajátszható követési esemény ki van bocsátva. Az adatmennyiség csökkenthető a `Host.Triggers.DurableTask` beállításával `"Warning"` vagy `"Error"`, amelyben az események követési eseményei csak kivételes helyzetekben lesznek kibocsátva.

Ha engedélyezni szeretné a részletes előkészítési események kiosztását, a `LogReplayEvents` beállítható úgy, hogy a `host.json` fájlban `true` a `durableTask` alatt látható módon:

#### <a name="functions-10"></a>Függvények 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Függvények 2,0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Alapértelmezés szerint a Azure Functions futtatókörnyezet Application Insights telemetria, hogy az adatkibocsátás túl gyakori legyen. Ez azt eredményezheti, hogy a nyomkövetési adatok elvesznek, ha rövid időn belül sok életciklus-esemény következik be. A [Azure functions-figyelési cikk](../functions-monitoring.md#configure-sampling) elmagyarázza, hogyan konfigurálhatja ezt a viselkedést.

### <a name="single-instance-query"></a>Egypéldányos lekérdezés

A következő lekérdezés a [Hello Sequence](durable-functions-sequence.md) függvény összehangolása egyetlen példányának korábbi követési adatait jeleníti meg. A [Application Insights lekérdezési nyelv (AIQL)](https://aka.ms/LogAnalyticsLanguageReference)használatával van írva. Kiszűri az ismétlések végrehajtását, így csak a *logikai* végrehajtási útvonal látható. Az események rendezése `timestamp` és `sequenceNumber` alapján rendezhető az alábbi lekérdezésben látható módon:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Az eredmény azon követési események listája, amelyek a koordinálás végrehajtási útvonalát mutatják, beleértve a tevékenységek függvényeit is, amelyek sorrendje a végrehajtás időpontja.

![Application Insights lekérdezés](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Példány összegző lekérdezése

A következő lekérdezés az adott időtartományban futó összes összehangoló példány állapotát megjeleníti.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Ennek eredménye a példány-azonosítók és az aktuális futtatókörnyezeti állapot listája.

![Application Insights lekérdezés](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Naplózás

Fontos, hogy a Orchestrator-újrajátszás viselkedését ne feledje, amikor közvetlenül egy Orchestrator-függvényből ír naplókat. Vegyük például a következő Orchestrator függvényt:

### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C#Parancsfájl

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Az eredményül kapott naplózási adatokat a következő példában szereplő kimenethez hasonlóan fogjuk kinézni:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Ne feledje, hogy míg a naplók az F1, az F2 és az F3 meghívását kérik, ezeket a függvényeket *csak az* első alkalommal nevezik. A rendszer kihagyja az újrajátszás során megjelenő további hívásokat, és a kimeneteket visszajátssza a Orchestrator logikába.

Ha csak a nem újrajátszható végrehajtást szeretné bejelentkezni, írhat egy feltételes kifejezést úgy, hogy csak akkor jelentkezzen be, ha `IsReplaying` `false`. Vegye figyelembe a fenti példát, de ezúttal újrajátszás-ellenőrzésekkel.

#### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

A Durable Functions 2,0-es verziótól kezdődően a .NET Orchestrator functions olyan `ILogger` létrehozására is lehetőséget biztosít, amely automatikusan kiszűri a napló utasításait a visszajátszás során. Ez az automatikus szűrés a `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` API használatával végezhető el.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

A korábban említett módosításokat követően a napló kimenete a következő:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Az előző C# példák a Durable functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

## <a name="custom-status"></a>Egyéni állapot

Az egyéni előkészítési állapot lehetővé teszi egyéni állapot értékének megadását a Orchestrator függvényhez. Ezt az állapotot a HTTP status Query API vagy a `IDurableOrchestrationClient.GetStatusAsync` API segítségével biztosítjuk. Az egyéni előkészítési állapot lehetővé teszi a Orchestrator függvények szélesebb körű figyelését. Például a Orchestrator függvény kódja tartalmazhat `IDurableOrchestrationContext.SetCustomStatus` hívásokat a hosszan futó művelet előrehaladásának frissítéséhez. Az ügyfél, például egy weblap vagy más külső rendszer, rendszeres időközönként lekérdezheti a HTTP-állapot lekérdezési API-jait a részletes végrehajtási információkhoz. A `IDurableOrchestrationContext.SetCustomStatus`t használó mintát az alábbi táblázat ismerteti:

### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Az előző C# példa a Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

A folyamat futása közben a külső ügyfelek behívhatják ezt az egyéni állapotot:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Az ügyfelek a következő választ kapják:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Az egyéni állapot adattartalma 16 KB-os UTF-16 JSON-szövegre van korlátozva, mert képesnek kell lennie arra, hogy illeszkedjen egy Azure Table Storage-oszlopba. Ha nagyobb adattartalomra van szüksége, külső tárterületet is használhat.

## <a name="debugging"></a>Hibakeresés

Azure Functions támogatja a hibakeresési funkció programkódjának közvetlen használatát, és ugyanezen támogatás a Durable Functions, akár az Azure-ban, akár helyileg fut. Előfordulhat azonban, hogy a hibakeresés során néhány viselkedést kell figyelembe vennie:

* **Visszajátszás**: a Orchestrator függvény rendszeresen [újrajátszható](durable-functions-orchestrations.md#reliability) , ha új bemenet érkezik. Ez a viselkedés azt jelenti, hogy egy Orchestrator függvény egyetlen *logikai* végrehajtásával több alkalommal is megtalálhatja ugyanazt a töréspontot, különösen akkor, ha a függvény kódja korán van beállítva.
* **Várakozás**: ha egy `await` egy Orchestrator-függvényben fordul elő, a rendszer visszairányítja a vezérlést a tartós feladatokhoz. Ha az első alkalommal, amikor egy adott `await` észlelhető, a kapcsolódó feladat *soha nem* folytatódik. Mivel a feladat soha nem folytatódik *, a várakozás (az F10* a Visual Studióban) nem lehetséges. Az átlépés csak akkor működik, ha egy feladat újra van játszva.
* **Üzenetkezelési időtúllépések**: a Durable functions belsőleg használ üzenetsor-üzeneteket a Orchestrator, a tevékenység és az entitás funkcióinak végrehajtásához. A több virtuális gépre kiterjedő környezetekben a hosszabb ideig tartó hibakeresés miatt egy másik virtuális gép is felveheti az üzenetet, ami ismétlődő végrehajtást eredményezhet. Ez a viselkedés a rendszeres üzenetsor-trigger függvények esetében is létezik, de fontos, hogy ebben a környezetben is kimutasson, mivel a várólisták megvalósítási részletességgel rendelkeznek.
* **Leállítás és indítás**: a tartós függvények üzenetei megmaradnak a hibakeresési munkamenetek között. Ha leállítja a hibakeresést, és leállítja a helyi gazdagép folyamatát egy tartós függvény végrehajtása közben, akkor a függvény automatikusan újrafuthat egy jövőbeli hibakeresési munkamenetben. Ez a viselkedés zavaró lehet, ha nem várt. A [belső tárolási várólistákról](durable-functions-perf-and-scale.md#internal-queue-triggers) érkező összes üzenet törlése a hibakeresési munkamenetek között egy olyan módszer, amellyel elkerülhető a működés.

> [!TIP]
> Ha töréspontokat állít be a Orchestrator függvényekben, ha csak a nem újrajátszható végrehajtást szeretné megszüntetni, beállíthat egy feltételes töréspontot, amely csak akkor szakad meg, ha a `IsReplaying` `false`.

## <a name="storage"></a>Storage

Alapértelmezés szerint a Durable Functions az Azure Storage-ban tárolja az állapotot. Ez azt jelenti, hogy a munkafolyamatok állapotát a [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)eszközzel ellenőrizheti.

![Képernyőkép Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Ez a hibakereséshez hasznos, mert pontosan azt látja, hogy milyen állapotban lehet a rendszer. A várólistákban lévő üzenetek megtekinthetők a függőben lévő (vagy bizonyos esetekben beragadott) munkák megismerésére is.

> [!WARNING]
> Habár érdemes megtekinteni a táblázatos tárolóban a végrehajtási előzményeket, ne vegyen fel függőségeket ezen a táblán. Előfordulhat, hogy a Durable Functions bővítmény fejlődése megváltozhat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Azure Functions figyeléséről](../functions-monitoring.md)
