---
title: Diagnosztika a Durable Functions-ben – Azure
description: Ismerje meg, hogyan diagnosztizálhatja a problémákat a Azure Functions Durable Functions bővítménnyel.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2cc60ee2c73aa6858f68d6b13a895a0188bb5735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098132"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnosztika Durable Functions az Azure-ban

Több lehetőség is van a Durable Functionsával kapcsolatos problémák [](durable-functions-overview.md)diagnosztizálására. Ezek némelyike megegyezik a hagyományos függvényekre vonatkozó módszerekkel, mások pedig csak a Durable Functions függvényeihez használhatók.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) a diagnosztika és a figyelés ajánlott módszere Azure Functionsokban. Ugyanez vonatkozik a Durable Functionsra is. A Application Insightsnek a Function alkalmazásban való kihasználása áttekintését lásd: [Azure functions figyelése](../functions-monitoring.md).

A Azure Functions tartós bővítmény olyan *követési eseményeket* is kibocsát, amelyek segítségével nyomon követheti a folyamat végpontok közötti végrehajtását. Ezek a Azure Portal [Application Insights Analytics](../../azure-monitor/app/analytics.md) eszközének használatával találhatók meg és kérhetők le.

### <a name="tracking-data"></a>Adatkövetés

Egy összehangoló példány minden életciklus-eseménye egy követési eseményt ír a Application Insights **nyomkövetési** gyűjteményéből. Ez az esemény több mezőből álló **customDimensions** -adattartalmat tartalmaz.  A `prop__`mezők nevei minden előtagértéke.

* **hubName**: Annak a feladatnak a neve, amelyben a rendszer fut.
* **appName**: A Function alkalmazás neve. Ez akkor hasznos, ha több Function-alkalmazással is rendelkezik, amelyek ugyanazt a Application Insights példányt osztják meg.
* **slotName**: Az [üzembe helyezési](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) pont, amelyben az aktuális Function alkalmazás fut. Ez akkor lehet hasznos, ha az üzembe helyezési pontokat a felépítésük verziójára használja.
* **függvénynév**: A Orchestrator vagy a tevékenység függvény neve.
* **functionType**: A függvény típusa, például **Orchestrator** vagy **tevékenység**.
* **instanceId**: A koordináló példány egyedi azonosítója.
* **állapot**: A példány életciklus-végrehajtási állapota. Érvényes értékek a következők:
  * **Ütemezett**: A függvény ütemezése végrehajtásra lett ütemezve, de még nem indult el.
  * Elindítva: A függvény futása megkezdődött, de még nem várt vagy nem fejeződött be.
  * **Várt**: A Orchestrator ütemezett némi munkát, és arra vár, hogy befejeződjön.
  * **Figyelés**: A Orchestrator egy külső eseményről szóló értesítést figyel.
  * **Befejezve**: A függvény sikeresen befejeződött.
  * **Sikertelen**: A függvény hibával meghiúsult.
* **OK**: A nyomkövetési eseményhez kapcsolódó további információk. Ha például egy példány egy külső eseményről szóló értesítésre vár, akkor ez a mező a várt esemény nevét jelzi. Ha egy függvény meghiúsult, akkor a hiba részleteit fogja tartalmazni.
* **isReplay**: Logikai érték, amely azt jelzi, hogy a követési esemény az újrajátszott végrehajtásra van-e kiválasztva.
* **extensionVersion**: A tartós feladathoz tartozó bővítmény verziója. Ez különösen fontos, ha a bővítmény lehetséges hibáit jelenti. A hosszan futó példányok több verziót is jelenthetnek, ha a futás közben frissítés történik.
* **sorszám**: Esemény végrehajtási sorszáma. Az időbélyeggel kombinálva a végrehajtás ideje alapján rendezheti az eseményeket. *Vegye figyelembe, hogy ez a szám nulla értékre áll vissza, ha a gazdagép újraindul, miközben a példány fut, ezért fontos, hogy először az időbélyegző alapján rendezze az értéket, majd sorszám.*

A Application Insightsra kibocsátott adatok részletességét a `logger` `host.json` fájl (functions 1. x) vagy `logging` (functions 2. x) szakaszában lehet konfigurálni.

#### <a name="functions-1x"></a>Functions 1.x

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

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Alapértelmezés szerint az összes nem újrajátszható követési esemény ki van bocsátva. Az adatmennyiség csökkenthető a beállítással `Host.Triggers.DurableTask` , `"Warning"` illetve `"Error"` a esetében, amikor a követési események csak kivételes helyzetekben lesznek kibocsátva.

Ha engedélyezni szeretné a részletes előkészítési események kiosztását, a `LogReplayEvents` a `host.json` fájlban `durableTask` az alábbi `true` módon állítható be:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

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

A következő lekérdezés a [Hello Sequence](durable-functions-sequence.md) függvény összehangolása egyetlen példányának korábbi követési adatait jeleníti meg. A [Application Insights lekérdezési nyelv (AIQL)](https://aka.ms/LogAnalyticsLanguageReference)használatával van írva. Kiszűri az ismétlések végrehajtását, így csak a *logikai* végrehajtási útvonal látható. Az események rendezési `timestamp` `sequenceNumber` sorrendje az alábbi lekérdezés szerint rendezhető:

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

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

Az eredményül kapott naplók a következőhöz hasonló módon fognak kinézni:

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
> Ne feledje, hogy míg a naplók az F1, az F2 és az F3 meghívását kérik , ezeket a függvényeket csak az első alkalommal nevezik. A rendszer kihagyja az újrajátszás során megjelenő további hívásokat, és a kimeneteket visszajátssza a Orchestrator logikába.

Ha csak a nem újrajátszható végrehajtást szeretné bejelentkezni, írhat egy feltételes kifejezést úgy, hogy csak akkor `IsReplaying` jelentkezzen be, ha a. `false` Vegye figyelembe a fenti példát, de ezúttal újrajátszás-ellenőrzésekkel.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

Ezzel a módosítással a napló kimenete a következő:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Egyéni állapot

Az egyéni előkészítési állapot lehetővé teszi egyéni állapot értékének megadását a Orchestrator függvényhez. Ezt az állapotot a http-állapot lekérdezési API- `DurableOrchestrationClient.GetStatusAsync` jával vagy az API-n keresztül biztosítjuk. Az egyéni előkészítési állapot lehetővé teszi a Orchestrator függvények szélesebb körű figyelését. A Orchestrator függvény kódja például tartalmazhat `DurableOrchestrationContext.SetCustomStatus` hívásokat a hosszan futó művelet előrehaladásának frissítéséhez. Az ügyfél, például egy weblap vagy más külső rendszer, rendszeres időközönként lekérdezheti a HTTP-állapot lekérdezési API-jait a részletes végrehajtási információkhoz. Az alábbi minta `DurableOrchestrationContext.SetCustomStatus` használatával kell megadnia a mintát:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

* **Visszajátszás**: A Orchestrator függvények rendszeresen újrajátszják az új bemenetek fogadását. Ez azt jelenti, hogy egy Orchestrator-függvény egyetlen *logikai* végrehajtásával több alkalommal is megtalálhatja ugyanazt a töréspontot, különösen akkor, ha a függvény kódja korán van beállítva.
* **Várakozás**: Amikor a `await` rendszer megtalálta a műveletet, visszaadja a vezérlést a tartós feladatokra szolgáló Framework-diszpécsernek. Ha az első alkalommal `await` fordul elő, a kapcsolódó feladat *soha nem* folytatódik. Mivel a feladat soha nem folytatódik, a várakozás (az F10 a Visual Studióban) nem lehetséges. Az átlépés csak akkor működik, ha egy feladat újra van játszva.
* **Üzenetkezelési időtúllépések**: A belső Durable Functions üzenetsor-üzeneteket használ a Orchestrator függvények és a tevékenységek funkcióinak végrehajtásához. A több virtuális gépre kiterjedő környezetekben a hosszabb ideig tartó hibakeresés miatt egy másik virtuális gép is felveheti az üzenetet, ami ismétlődő végrehajtást eredményezhet. Ez a viselkedés a rendszeres üzenetsor-trigger függvények esetében is létezik, de fontos, hogy ebben a környezetben is kimutasson, mivel a várólisták megvalósítási részletességgel rendelkeznek.

> [!TIP]
> A töréspontok beállításakor, ha csak a nem újrajátszható végrehajtást szeretné megszakítani, beállíthat egy feltételes töréspontot, amely `IsReplaying` csak `false`akkor szakad meg, ha a.

## <a name="storage"></a>Storage

Alapértelmezés szerint a Durable Functions az Azure Storage-ban tárolja az állapotot. Ez azt jelenti, hogy a munkafolyamatok állapotát a [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)eszközzel ellenőrizheti.

![Képernyőkép Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Ez a hibakereséshez hasznos, mert pontosan azt látja, hogy milyen állapotban lehet a rendszer. A várólistákban lévő üzenetek megtekinthetők a függőben lévő (vagy bizonyos esetekben beragadott) munkák megismerésére is.

> [!WARNING]
> Habár érdemes megtekinteni a táblázatos tárolóban a végrehajtási előzményeket, ne vegyen fel függőségeket ezen a táblán. Előfordulhat, hogy a Durable Functions bővítmény fejlődése megváltozhat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használhatja a tartós időzítőket](durable-functions-timers.md)
