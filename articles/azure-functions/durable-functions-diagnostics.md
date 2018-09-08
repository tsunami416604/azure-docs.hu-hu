---
title: Durable Functions - Azure-diagnosztika
description: Ismerje meg, hogyan diagnosztizálhatja a problémákat a Durable Functions bővítmény az Azure Functions szolgáltatáshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: e1211241ec3a2b32647260d1a5c7dc561019cfdf
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092241"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions)-diagnosztika

Több lehetőség a problémák diagnosztizálásához [Durable Functions](durable-functions-overview.md). Ezek a beállítások némelyike normál funkciók esetében azonos, és ezek némelyike Durable Functions egyedi.

## <a name="application-insights"></a>Application Insights

[Az Application Insights](../application-insights/app-insights-overview.md) diagnosztika és monitorozás az Azure Functions ajánlott módja. Durable Functions Ugyanez vonatkozik. Hogyan használható az Application Insights a függvényalkalmazásban áttekintését lásd: [figyelése az Azure Functions](functions-monitoring.md).

Az Azure Functions-Durable Extension is bocsát ki *követési események* , amelyek engedélyezik a vezénylések végrehajtásának teljes körű nyomon követését. Ezek található, és megkérdezi a használatával a [Application Insights-elemzési](../application-insights/app-insights-analytics.md) eszköz az Azure Portalon.

### <a name="tracking-data"></a>Követési adatok

Egy vezénylési példány minden életciklus esemény hatására való írásra egy követési eseményének a **nyomkövetések** gyűjtése az Application Insightsban. Ez az esemény tartalmaz egy **customDimensions** több mező adattartalom.  Mezőnevek vannak az összes előtaggal kiegészített `prop__`.

* **hubName**: a feladat központ, amelyben a vezénylések futnak nevére.
* **Alkalmazásnév**: a függvényalkalmazás nevére. Ez akkor hasznos, ha több függvényalkalmazás ugyanazt az Application Insights-példány megosztása.
* **slotName**: A [üzembe helyezési pont](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) az aktuális függvényalkalmazás futó. Ez akkor hasznos, ha az üzembe helyezési pontok verzióra a vezénylések használhatja.
* **Függvénynév**: az orchestrator vagy tevékenység függvény neve.
* **functionType**: a függvény típusú például **Orchestrator** vagy **tevékenység**.
* **instanceId**: az orchestration-példány egyedi azonosítója.
* **állapot**: a példány életciklusa végrehajtási állapotát. Érvényes értékek a következők:
    * **Ütemezett**: A függvény végrehajtási lett ütemezve, de még futó nem indul el.
    * **Lépések**: A függvény futtatása megkezdődött, de nem rendelkezik még várni a vagy befejeződött.
    * **Várni a**: az orchestrator néhány dolgot van ütemezve, és várakozik, amíg az befejeződik.
    * **Figyelő**: egy külső eseményértesítés az orchestrator figyel.
    * **Befejezett**: A függvény sikeresen befejeződött.
    * **Nem sikerült**: A függvény egy hiba miatt nem sikerült.
* **OK**: a követési esemény társított további adatokat. Például egy példányt vár egy külső eseményre értesítést, ha ez a mező vár az esemény nevét jelöli. Ha egy függvény sikertelen volt, ez a hiba részletes adatait tartalmazza.
* **isReplay**: logikai érték, amely jelzi, hogy-e a követési esemény a játssza vissza a végrehajtási.
* **extensionVersion**: a tartós feladat bővítmény verzióját. Ez akkor különösen fontos adatokat, ha a jelentéskészítési a bővítményt a lehetséges hibák. Hosszan futó példányok előfordulhat, hogy több verzió jelentést, ha frissítés történik futás közben. 
* **sequenceNumber**: végrehajtási sorszáma eseményre. Az időbélyeg segít az események sorrendjének végrehajtási idő szerint együtt. *Vegye figyelembe, hogy ez a szám alaphelyzetbe állítása nulla, ha a fogadó újraindul a példány futása közben, ezért fontos mindig szerinti rendezéshez-tárhelyek időbélyegző szerint, majd sequenceNumber lesz-e.*

A követési adatokat az Application insights szolgáltatásba a kibocsátott részletességét konfigurálható a `logger` szakaszában a `host.json` fájlt.

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

Alapértelmezés szerint a rendszer rendelkezésre újrajátszás-nem követési események. Az adatok mennyisége csökkenthető beállításával `Host.Triggers.DurableTask` való `"Warning"` vagy `"Error"` ebben az esetben követési események fogja csak bocsátja ki kivételes esetekben.

A részletes vezénylési visszajátszását események kibocsátó engedélyezése a `LogReplayEvents` állítható `true` a a `host.json` fájlt `durableTask` látható módon:

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

> [!NOTE]
> Alapértelmezés szerint az Application Insights telemetriát az Azure Functions futtatókörnyezete elkerülése érdekében adatok túl gyakran kibocsátó mintát venni. Ez azt eredményezheti, nyomon követési adatok elveszhetnek, egy rövid idő alatt sok életciklus-események előfordulásakor. A [a cikk az Azure Functions Monitorozási](functions-monitoring.md#configure-sampling) ezt a viselkedést konfigurálását ismerteti.

### <a name="single-instance-query"></a>Egypéldányos lekérdezés

A következő lekérdezés egyetlen példánya korábbi követési adatait jeleníti meg a [Hello feladatütemezési](durable-functions-sequence.md) vezénylési működik. Használatával írt a [Application Insights lekérdezési nyelv (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Kiszűri a visszajátszás végrehajtási, hogy csak a *logikai* végrehajtási elérési út jelenik meg. Események is rendezve szerinti rendezés `timestamp` és `sequenceNumber` , ahogyan az alábbi lekérdezést: 

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

Az eredmény az listája követési események, amelyek a vezénylési, többek között a végrehajtás ideje, növekvő sorrendben rendezve függvényeinek tevékenység végrehajtási elérési útját jeleníti meg.

![Application Insights-lekérdezés](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Példány összegzése lekérdezés

A következő lekérdezést a megadott időtartományban futtató összes orchestration-példányok állapotát jeleníti meg.

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
Példány azonosítóinak listáját és azok állapotának futásidejű jön létre.

![Application Insights-lekérdezés](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Naplózás

Fontos szem előtt tartani az orchestrator visszajátszását viselkedés közvetlenül egy orchestrator-függvényt a naplók írásakor. Vegyük példaként a következő orchestrator függvényt:

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivityAsync("F1");
    context.log("Calling F2.");
    yield context.df.callActivityAsync("F2");
    context.log("Calling F3.");
    yield context.df.callActivityAsync("F3");
    context.log("Done!");
});
```

Az eredményül kapott naplóadatokat fogja keresse meg a következőhöz hasonló:

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
> Ne feledje, hogy a naplók hívni, ajánlott az F1, F2 és F3 jogcímet, míg ezek a függvények csak *ténylegesen* nevű először munka közben. Ismétlés során későbbi hívások kimarad, és a kimenetek az orchestrator logika játssza vissza a rendszer.

Ha azt szeretné, csak bejelentkezésre nem visszajátszását végrehajtási, írhat a napló csak akkor, ha egy feltételes kifejezéses `IsReplaying` van `false`. Fontolja meg a fenti példában, de ezúttal a visszajátszás ellenőrzi.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Ezzel a kimenet a következőképpen történik:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> A `IsReplaying` tulajdonság még nem áll rendelkezésre a JavaScript.

## <a name="custom-status"></a>Egyéni állapot

Egyéni vezénylési állapot lehetővé teszi az orchestrator függvény egyéni állapot értékének beállítása. A HTTP-állapot lekérdezési API-n keresztül biztosított Ez az állapot vagy a `DurableOrchestrationClient.GetStatusAsync` API-t. Az egyéni vezénylési állapot lehetővé teszi, hogy az orchestrator funkciók gazdagabb figyelése. Az orchestrator függvénykód lehetnek például `DurableOrchestrationContext.SetCustomStatus` hívások frissíteni a hosszú ideig futó művelet folyamatban van. Egy ügyfél, például egy weblap vagy más külső rendszerre, majd sikerült rendszeresen lekérdezi gazdagabb végrehajtási adatok HTTP-állapot lekérdezési API-k. A minta használatával `DurableOrchestrationContext.SetCustomStatus` lejjebb:

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

A vezénylési futása közben a külső ügyfelek lehet beolvasni az egyéni állapot:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Az ügyfelek a következő választ fog kapni: 

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
>  Az egyéni hasznos adat, képesnek kell lennie ahhoz, hogy elférjen az Azure Table Storage oszlop azért legfeljebb 16 KB-os UTF-16 JSON-szövegben. Külső tárterület is használhatja, ha nagyobb hasznos adat van szüksége.

## <a name="debugging"></a>Hibakeresés

Hibakeresés közvetlenül a függvénykódot az Azure Functions támogatja, és ugyanazon támogató hajtja előre Durable Functions, az Azure-ban fut-e, vagy helyileg. Vannak azonban kell ügyelnie, ha a hibakeresés néhány viselkedést:

* **Visszajátszani**: Orchestrator funkciók rendszeresen játszani, ha új bemenetek érkezik. Ez azt jelenti, hogy egyetlen *logikai* egy orchestrator-függvény végrehajtása eredményezhet az azonos töréspontot lenyomásával többször, különösen akkor, ha a beállítás korai szakaszában a függvénykódot.
* **Await**: minden alkalommal, amikor egy `await` van észlelt, azt poskytne vezérlő térjen vissza a tartós feladat keretrendszer dispatcher. Ha első alkalommal egy adott `await` lett észlelt, a kapcsolódó feladat nincs *soha nem* folytatódik. Soha nem folytatja a feladatot, mert ke krokování *keresztül* a await (a Visual Studióban F10) már nem ténylegesen lehetséges. Csak ke krokování keresztül működik, ha egy feladat játssza vissza van folyamatban.
* **Üzenetküldési időtúllépések**: Durable Functions belső, mind az orchestrator és függvények tevékenység meghajtó végrehajtásának üzenetsorbeli üzenetek használja. Több virtuális gépes környezetben és a hibakeresés a hosszabb ideig támadó csomópontmetrikák ismétlődő végrehajtási eredményez, az üzenet egy másik virtuális Géphez. Ez a viselkedés rendszeres üzenetsor-eseményindító függvényeket is létezik, de fontos, hogy felhívják ebben a környezetben, mivel az üzenetsorok egy implementálási részlete.

> [!TIP]
> Beállításakor töréspontok keresése, ha csak nem visszajátszását végrehajtási megáll, beállíthat egy feltételes töréspontot, hogy szüneteket csak ha `IsReplaying` van `false`.

## <a name="storage"></a>Storage

Alapértelmezés szerint a Durable Functions állapota tárolja az Azure Storage-ban. Ez azt jelenti, hogy vizsgálhatja meg a vezénylések használni például az eszközök állapotának [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Képernyőkép az Azure Storage Explorerrel](media/durable-functions-diagnostics/storage-explorer.png)

Ez akkor hasznos, láthatja, hogy pontosan milyen állapota egy vezénylési lehet, mert a hibakereséshez. A várólistában lévő üzenetek is vizsgálni, amelyből megtudhatja, milyen munkahelyi függőben lévő (vagy bizonyos esetekben elakadt).

> [!WARNING]
> Bár a table storage-ban végrehajtási előzményeinek megtekintéséhez kényelmes, elkerülése érdekében minden olyan függőséget véve a táblánál. Mivel a Durable Functions bővítmény haladásával is megváltoztathatja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartós időzítők használata](durable-functions-timers.md)
