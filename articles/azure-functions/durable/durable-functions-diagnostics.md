---
title: Diagnosztika a tartós függvényekben - Azure
description: Ismerje meg, hogyan diagnosztizálhatja az Azure Functions tartós függvények bővítményével kapcsolatos problémákat.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278193"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>A Durable Functions diagnosztikája az Azure-ban

Számos lehetőség van a tartós funkciókkal kapcsolatos problémák [diagnosztizálására.](durable-functions-overview.md) Ezek némelyike megegyezik a hagyományos függvényekre vonatkozó módszerekkel, mások pedig csak a Durable Functions függvényeihez használhatók.

## <a name="application-insights"></a>Application Insights

[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) az Azure Functions diagnosztikai és figyelési műveletek ajánlott módja. Ugyanez vonatkozik a tartós funkciókra is. Az Application Insights funkcióalkalmazásában való kihasználásáról az [Azure-függvények figyelése című témakörben olvashat.](../functions-monitoring.md)

Az Azure Functions Durable Extension is kibocsát *követési eseményeket,* amelyek lehetővé teszik, hogy nyomon kövesse a vezénylési teljes körű végrehajtását. Ezek a nyomon követési események megtalálhatók és lekérdezhetők az [Azure Insights Analytics](../../azure-monitor/app/analytics.md) eszköz használatával az Azure Portalon.

### <a name="tracking-data"></a>Adatok nyomon követése

Egy vezénylési példány minden életciklus-esemény emiatt egy követési eseményt kell írni a **traces** gyűjtemény az Application Insightsban. Ez az esemény egy több mezőt tartalmazó **egyéniDimenziók** hasznos tartalmat tartalmaz.  A mezőnevek et `prop__`a program előkészíti.

* **hubName**: Annak a feladatközpontnak a neve, amelyben a vezénylések futnak.
* **appName**: A függvényalkalmazás neve. Ez a mező akkor hasznos, ha több függvényalkalmazás ugyanazt az Application Insights-példányt osztja meg.
* **slotName**: Az a [központi telepítési hely,](../functions-deployment-slots.md) amelyben az aktuális függvényalkalmazás fut. Ez a mező akkor hasznos, ha a központi telepítési bővítőhelyeket a vezénylési verziókhoz használja.
* **functionName**: Az orchestrator vagy a tevékenységfüggvény neve.
* **functionType**: A függvény típusa, például **Orchestrator** vagy **Activity**.
* **instanceId**: A vezénylési példány egyedi azonosítója.
* **állapot**: A példány életciklus-végrehajtási állapota. Az érvényes értékek a következők:
  * **Ütemezett:** A függvény végrehajtásra volt ütemezve, de még nem indult el.
  * **Elindítva**: A függvény futása megkezdődött, de még nem várt vagy nem fejeződött be.
  * **Várt**: Az orchestrator tervezett néhány munkát, és várja, hogy teljes legyen.
  * **Figyelés:** Az orchestrator figyel egy külső esemény értesítést.
  * **Befejeződött**: A függvény sikeresen befejeződött.
  * **Nem sikerült**: A függvény hiba miatt nem sikerült.
* **ok**: A nyomon követési eseményhez kapcsolódó további adatok. Ha például egy példány egy külső eseményértesítésre vár, ez a mező annak az eseménynek a nevét jelzi, amelyre vár. Ha egy függvény meghibásodott, ez a mező a hiba részleteit fogja tartalmazni.
* **isPlay**: Logikai érték, amely jelzi, hogy a követési esemény visszajátszott végrehajtásra van-e.
* **extensionVersion**: A Tartós feladat bővítmény verziója. A verzióinformáció különösen fontos adat, amikor a bővítmény lehetséges hibáit jelenti. A hosszú ideig futó példányok több verziót is jelenthetnek, ha a frissítés futás közben történik.
* **sequenceNumber**: Egy esemény végrehajtási sorozatszáma. Az időbélyegzővel kombinálva segít az események végrehajtási idő szerint történő megrendelésében. *Vegye figyelembe, hogy ez a szám nullára lesz állítva, ha az állomás újraindul, miközben a példány fut, ezért fontos, hogy mindig időbélyeg szerint rendezve, majd sequenceNumber.*

Az Application Insights számára kibocsátott nyomon követési adatok részletessége konfigurálható a `logger` `logging` `host.json` fájl (Functions 1.x) vagy (Functions 2.0) szakaszában.

#### <a name="functions-10"></a>1.0 függvények

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

#### <a name="functions-20"></a>Funkciók 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Alapértelmezés szerint a rendszer minden nem visszajátszáskövetési eseményt bocsát ki. Az adatok mennyisége csökkenthető `Host.Triggers.DurableTask` beállítással, `"Warning"` vagy `"Error"` ebben az esetben a nyomon követési események csak kivételes helyzetekben kerülnek kibocsátásra.

A részletes vezénylési visszajátszási események `LogReplayEvents` kiírásának `true` engedélyezéséhez a fájlban az `host.json` `durableTask` alábbi módon állítható be:

#### <a name="functions-10"></a>1.0 függvények

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funkciók 2.0

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
> Alapértelmezés szerint az Application Insights telemetriai adatok at az Azure Functions futásidejű, hogy ne kelljen túl gyakran adatokat. Ez azt eredményezheti, hogy a követési adatok elvesznek, ha rövid idő alatt számos életciklus-esemény következik be. Az [Azure Functions Monitoring cikk](../functions-monitoring.md#configure-sampling) ismerteti, hogyan konfigurálhatja ezt a viselkedést.

### <a name="single-instance-query"></a>Egypéldányos lekérdezés

A következő lekérdezés a [Hello Sequence](durable-functions-sequence.md) függvény vezénylésének egyetlen példányának előzménykövetési adatait jeleníti meg. Az Application Insights [lekérdezési nyelvével (AIQL)](https://aka.ms/LogAnalyticsLanguageReference)íródott. Kiszűri a visszajátszás végrehajtását, így csak a *logikai* végrehajtási útvonal jelenik meg. Az események rendezéssel rendezhetők az alábbi lekérdezés szerint `timestamp` és `sequenceNumber` ahogy az látható:

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

Az eredmény a nyomon követési események listája, amely a vezénylés végrehajtási útvonalát mutatja, beleértve a végrehajtási idő által növekvő sorrendben rendezett tevékenységfüggvényeket is.

![Application Insights-lekérdezés](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Példány összegző lekérdezése

A következő lekérdezés megjeleníti az összes vezénylési példány állapotát, amely egy megadott időtartományban futott.

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

Az eredmény a példányazonosítók és az aktuális futásidejű állapotuk listája.

![Application Insights-lekérdezés](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Naplózás

Fontos, hogy tartsa szem előtt az orchestrator visszajátszási viselkedését, amikor közvetlenül egy orchestrator függvényből ír naplókat. Vegyük például a következő orchestrator függvényt:

### <a name="precompiled-c"></a>Előre lefordított C #

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

### <a name="c-script"></a>C# parancsfájl

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

### <a name="javascript-functions-20-only"></a>JavaScript (csak a 2.0-s függvények nél)

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

Az eredményül kapott naplóadatok a következő példakimenethez hasonlóan fognak kinézni:

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
> Ne feledje, hogy míg a naplók azt állítják, hogy hívja F1, F2 és F3, ezeket a függvényeket csak *ténylegesen* hívja az első alkalommal, amikor találkozott. A visszajátszás során bekövetkező további hívások at kihagyja a rendszer, és a kimeneteket visszajátssza az orchestrator logikája.

Ha csak a nem visszajátszásos végrehajtásra szeretne bejelentkezni, csak akkor `IsReplaying` `false`írhat feltételes kifejezést a naplóba, ha a . Vegye figyelembe a fenti példát, de ezúttal a visszajátszási ellenőrzésekkel.

#### <a name="precompiled-c"></a>Előre lefordított C #

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

#### <a name="javascript-functions-20-only"></a>JavaScript (csak a 2.0-s függvények nél)

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

A Durable Functions 2.0-s függvényektől kezdődően a `ILogger` .NET orchestrator függvények is létrehozhatnak egy olyan függvényt, amely automatikusan kiszűri a naplónaplókat a visszajátszás során. Ez az automatikus szűrés `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` az API használatával történik.

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

A korábban említett változtatások esetén a napló kimenete a következő:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

## <a name="custom-status"></a>Egyéni állapot

Egyéni vezénylési állapot lehetővé teszi, hogy egyéni állapotértéket az orchestrator függvény. Ez az állapot a HTTP-állapot `IDurableOrchestrationClient.GetStatusAsync` lekérdezési API-n vagy az API-n keresztül érhető el. Az egyéni vezénylési állapot lehetővé teszi, hogy gazdagabb figyelés vezénylési függvények. Az orchestrator függvénykódja például tartalmazhat `IDurableOrchestrationContext.SetCustomStatus` hívásokat egy hosszú ideig futó művelet folyamatának frissítéséhez. Egy ügyfél, például egy weblap vagy más külső rendszer, ezután rendszeresidőközönként lekérdezheti a HTTP-állapotlekérdezésapi-kat a gazdagabb folyamatinformációkért. A minta az `IDurableOrchestrationContext.SetCustomStatus` alábbiakban található:

### <a name="precompiled-c"></a>Előre lefordított C #

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
> Az előző C# példa a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak a 2.0-s függvények nél)

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

Avezénylés futása közben a külső ügyfelek lehívhatják ezt az egyéni állapotot:

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
> Az egyéni állapot hasznos tartalom korlátozódik 16 KB UTF-16 JSON szöveget, mert képesnek kell lennie arra, hogy elférjen egy Azure Table Storage oszlopban. Használhatja a külső tároló, ha nagyobb hasznos adatra van szüksége.

## <a name="debugging"></a>Hibakeresés

Az Azure Functions közvetlenül támogatja a hibakeresési függvénykódot, és ugyanez a támogatás a tartós függvények hez továbbít, akár az Azure-ban, akár helyileg futnak. A hibakeresés során azonban néhány viselkedést figyelembe kell venni:

* **Visszajátszás**: Az Orchestrator függvények rendszeresen [újrajátszása,](durable-functions-orchestrations.md#reliability) amikor új bemenetek érkeznek. Ez a viselkedés azt jelenti, hogy egy orchestrator függvény egyetlen *logikai* végrehajtása azt eredményezheti, hogy ugyanazt a töréspontot többször is megnyomja, különösen akkor, ha a függvénykód korai szakaszában van beállítva.
* **Várakozás**: Amikor `await` egy egy orchestrator függvényben találkozik, a tartós feladatkeret-diszpécser nek visszaadja a vezérlőt. Ha ez az első `await` alkalom, hogy egy adott tevékenység et észleltek, a társított tevékenység *soha nem* folytatódik. Mivel a feladat soha nem folytatódik, a várakozás *(A* Visual Studio F10) léptetése nem lehetséges. Az átlépés csak akkor működik, ha egy feladatot visszajátszik.
* **Üzenetküldési időmegadások:** A tartós függvények belső legbelül várólistaüzeneteket használ az orchestrator, a tevékenység és az entitásfüggvények végrehajtásának ösztönzésére. Több virtuális gép környezetben a hibakeresés hosszabb ideig történő betörése miatt egy másik virtuális gép is felveheti az üzenetet, ami ismétlődő végrehajtást eredményezhet. Ez a viselkedés a rendszeres várólista-eseményindító függvények esetében is létezik, de fontos rámutatni ebben a környezetben, mivel a várólisták megvalósítási részletek.
* **Leállítás és indítás:** A tartós függvényekben lévő üzenetek a hibakeresési munkamenetek között is megmaradnak. Ha leállítja a hibakeresést, és egy tartós függvény végrehajtása közben leállítja a helyi állomásfolyamatot, előfordulhat, hogy a függvény automatikusan újra végrehajtja a következő hibakeresési munkamenetben. Ez a viselkedés zavaró lehet, ha nem várható. A hibakeresési [munkamenetek közötti belső tárolóvárólistákból](durable-functions-perf-and-scale.md#internal-queue-triggers) származó összes üzenet törlése az egyik módszer a viselkedés elkerülésére.

> [!TIP]
> Ha töréspontokat állít be az orchestrator függvényekben, ha csak a visszajátszás nélküli végrehajtást szeretné `IsReplaying` `false`megtörni, beállíthat egy feltételes töréspontot, amely csak akkor tör iktatja meg, ha a .

## <a name="storage"></a>Storage

Alapértelmezés szerint a Durable Functions tárolja állapotát az Azure Storage-ban. Ez a viselkedés azt jelenti, hogy a Vezénylések állapotát olyan eszközökkel vizsgálhatja meg, mint például a [Microsoft Azure Storage Explorer.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)

![Az Azure Storage Explorer képernyőképe](./media/durable-functions-diagnostics/storage-explorer.png)

Ez akkor hasznos, hibakeresés, mert pontosan azt látja, hogy milyen állapotban lehet egy vezénylési lehet. A várólistákban lévő üzenetek et is meg lehet vizsgálni, hogy megtudja, milyen munka van függőben (vagy bizonyos esetekben beragadt).

> [!WARNING]
> Bár a végrehajtási előzmények megtekintéséhez a táblatárolóban, ne vegye a függőség et ebben a táblában. A Tartós függvények bővítmény fejlődésével változhat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure Functions figyeléséről](../functions-monitoring.md)
