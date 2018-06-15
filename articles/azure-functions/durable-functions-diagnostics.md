---
title: Diagnosztika a tartós funkciók – Azure
description: Útmutató az Azure Functions a tartós funkciók kiterjesztésű problémák diagnosztizálásához.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 4829ea88e0b6507159c192c111acf8ec7e5088e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764015"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnosztika a tartós funkciók (az Azure Functions)

Több lehetőség a problémák diagnosztizálásával [tartós funkciók](durable-functions-overview.md). A beállítások egy része esetén azonosak rendszeres funkciók, és némelyikük egyediek tartós funkciók.

## <a name="application-insights"></a>Application Insights

[Az Application Insights](../application-insights/app-insights-overview.md) ennek a diagnosztikai és a felügyelet az Azure Functions ajánlott módja. Ugyanez érvényes a tartós funkciók. Megtudhatja, hogyan használhatók ki az Application Insights az függvény alkalmazásban, [figyelése az Azure Functions](functions-monitoring.md).

Az Azure Functions tartós bővítmény is bocsát ki *nyomon követés* , amelyek lehetővé teszik az orchestration-végpontok közötti végrehajtása nyomon követését. Ezek található, és megkérdezi a használatával a [Application Insights Analytics](../application-insights/app-insights-analytics.md) eszköz az Azure portálon.

### <a name="tracking-data"></a>Nyomon követési adatok

Vezénylési példány életciklus eseményeket hatására egy nyomkövetési esemény lehet írni a **nyomkövetések** Application Insights-gyűjteményt érinti. Ez az esemény tartalmaz egy **customDimensions** adattartalom több mezőt.  Mezőnevek vannak az összes $a rendelkező `prop__`.

* **hubName**: a feladat hub, amelyben a álló üzenettípusok összehangolását futnak nevét.
* **Alkalmazásnév**: a függvény alkalmazás nevét. Ez akkor hasznos, ha több függvény alkalmazások ugyanabban az Application Insights példány megosztása.
* **slotName**: A [üzembe helyezési pont](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) az aktuális függvény alkalmazást futó. Ez akkor hasznos, ha kihasználja üzembe helyezési verzióra a álló üzenettípusok összehangolását.
* **Függvénynév**: az orchestrator vagy tevékenység függvény neve.
* **functionType**: a függvény típusú, mint **Orchestrator** vagy **tevékenység**.
* **instanceId**: az orchestration-példány egyedi azonosítója.
* **állapot**: a példány életciklus végrehajtási állapotát. Érvényes értékek a következők:
    * **Ütemezett**: A függvény végrehajtása lett ütemezve, de még fut még nem indult el.
    * **Lépések**: A függvény elindult, de nem rendelkezik még várakozni vagy befejeződött.
    * **Várakozni**: az orchestrator ütemezett munka egy része, és arra vár, amíg befejeződik.
    * **Figyelő**: az orchestrator a külső eseményértesítés figyeli.
    * **Befejeződött**: A függvény sikeresen befejeződött.
    * **Nem sikerült**: A függvény hiba miatt sikertelen.
* **OK**: a nyomkövetési esemény társított további adatokat. Például ha egy példányát arra vár, hogy egy külső eseményértesítés, ez a mező vár az esemény nevét jelöli. Ha a függvény nem sikerült, ez a hiba részletes adatait tartalmazza.
* **isReplay**: a rendszer logikai érték azt jelzi, hogy e a nyomkövetési esemény játssza végrehajtása.
* **extensionVersion**: a tartós feladatkiterjesztés verzióját. Ez akkor különösen fontos adatokat, ha a bővítménybeli lehetséges hibát. Hosszan futó példányok előfordulhat, hogy több verzió jelentést, egy frissítés futtatása esetén. 
* **sequenceNumber**: az esemény végrehajtása sorszámát. Az időbélyeg segít az események rendezése végrehajtási idő szerint együtt. *Vegye figyelembe, hogy ez az érték nulla, ha a gazdagép újraindul a példány futása közben, ezért fontos, hogy mindig rendezéshez először időbélyeg alaphelyzetbe állítása, majd sequenceNumber.*

Az Application Insights részére kibocsátott adatokról nyilvántartásával részletességi konfigurálható a `logger` szakasza a `host.json` fájlt.

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

Alapértelmezés szerint minden nyomkövetési események kibocsátott. Az adatok mennyisége csökkenthető úgy, hogy `Host.Triggers.DurableTask` való `"Warning"` vagy `"Error"` ebben az esetben nyomon követés fog csak kell kibocsátott kivételes helyzetekben.

> [!NOTE]
> Alapértelmezés szerint az Application Insights telemetria által az Azure Functions futtatókörnyezettel adatok túl gyakran kibocsátó elkerülése érdekében mintát venni. Ennek hatására a nyomon követési adatok elveszhetnek, rövid idő alatt sok életciklus-események előfordulásakor. A [Azure Functions figyelési cikk](functions-monitoring.md#configure-sampling) Ez a viselkedés konfigurálását ismerteti.

### <a name="single-instance-query"></a>Egypéldányos lekérdezés

A következő lekérdezés korábbi nyomon követési adatok egyetlen példányának jeleníti meg a [Hello feladatütemezési](durable-functions-sequence.md) vezénylési működik. Az oktatóprogram használatával a [Application Insights Query Language (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Azt, hogy csak a visszajátszás-végrehajtási kiszűri a *logikai* végrehajtási elérési látható. Események is rendezve szerint rendezve `timestamp` és `sequenceNumber` ahogy az az alábbi lekérdezést: 

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
| where isReplay == false
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Eredménye nyomon követés lista tartalmazza a vezénylési, többek között a végrehajtás ideje, növekvő sorrendben rendezve függvényeket tevékenység végrehajtási elérési útját jeleníti meg.

![Application Insights-lekérdezés](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Példányok összefoglaló lekérdezés

A következő lekérdezést a megadott időtartomány futtató összes orchestration-példányok állapotát jeleníti meg.

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
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
A példány azonosítók listáját és azok futási állapotának eredménye.

![Application Insights-lekérdezés](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Naplózás

Fontos, az orchestrator ismétlési viselkedés szem előtt tartani naplók közvetlenül az orchestrator függvényből írásakor. Vegyük példaként a következő orchestrator-funkció:

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak funkciók v2)

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

Az eredményül kapott naplóadatokat kinéznie a következőhöz hasonló lesz:

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
> Ne feledje, hogy amíg a naplók jogcím F1 F2 és F3 hív, ezek a funkciók csak *ténylegesen* nevű először munka közben. Későbbi hívások során ismétlési kimarad, és a kimenetek újból az orchestrator logikai vannak.

Ha azt szeretné, nem ismétlési végrehajtási csak bejelentkezni, írhat a napló csak akkor, ha egy feltételes kifejezéses `IsReplaying` van `false`. Fontolja meg a fenti példában, de ezúttal a visszajátszás-ellenőrzéseket.

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
A módosítás a kimenet a következőképpen történik:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> A `IsReplaying` tulajdonság még nem érhető el a JavaScript.

## <a name="custom-status"></a>Egyéni állapota

Egyéni vezénylési állapot lehetővé teszi az orchestrator-függvény egyéni állapot értékének beállítása. A HTTP-állapot lekérdezés API biztosítja az állapot vagy a `DurableOrchestrationClient.GetStatusAsync` API. Egyéni vezénylési állapota lehetővé teszi, hogy az orchestrator funkciók gazdagabb figyelését. Például az orchestrator függvény kódot tartalmazhatnak `DurableOrchestrationContext.SetCustomStatus` hívások frissítése hosszú futású művelet előrehaladását. Egy ügyfél, például egy weblap vagy más külső rendszer, majd lekérdezhet rendszeresen gazdagabb végrehajtási adatok HTTP-állapot lekérdezés API-k. A minta használata `DurableOrchestrationContext.SetCustomStatus` lejjebb tekinthetők meg:

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

A vezénylési futása közben, a külső ügyfelek lehet beolvasni az egyéni állapotát:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Az ügyfelek a következő válasz jelenik meg: 

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
>  Az egyéni adattartalom korlátozódik a 16 KB-os UTF-16 JSON-szöveg, mert fel kell tudni az Azure Table Storage oszlop elfér. Külső tárolót is használhatja, ha nagyobb payload van szüksége.

## <a name="debugging"></a>Hibakeresés

Függvény kód hibakereséséhez közvetlenül az Azure Functions támogatja, és azonos támogató hordoz magában, ha előre tartós funkciók, hogy az Azure-beli vagy helyi. Van azonban néhány konfigurációk határozhatják meg kell ügyelnie, ha hibakeresési:

* **Visszajátszásos**: Orchestrator funkciók rendszeresen visszajátszásos új bemenetek fogadásakor. Ez azt jelenti, hogy egyetlen *logikai* egy orchestrator függvény végrehajtása eredményezhet elérte-e az azonos töréspont többször, különösen akkor, ha a funkciókódot korai szakaszában van beállítva.
* **Await**: amikor egy `await` van történt, akkor adja eredményül vezérlő vissza a tartós feladat keretrendszer kézbesítő a. Ha egy különös tekintettel az első alkalommal `await` lett észlelt, a társított tevékenység van *soha nem* folytatódik. A feladat sosem tér vissza, mert léptetési *keresztül* a await (a Visual Studio F10) nincs ténylegesen lehetőség. Keresztül léptetési csak akkor működik feladat alatt a rendszer játssza vissza.
* **Üzenetküldési időtúllépések**: tartós funkciók belső módon használja a várólista-üzenetek meghajtó mind az orchestrator és függvények tevékenység végrehajtását. Több virtuális Gépre kiterjedő környezetben a hibakeresés huzamosabb ideig ossza okozhat az üzenetet, ismétlődő végrehajtási eredményezve átvételéhez másik virtuális gép. Ez a viselkedés rendszeres várólista-eseményindító funkciók is létezik, de fontos, hogy felhívják ebben a környezetben, mivel a várólisták egy megvalósítási részletei.

> [!TIP]
> Beállításakor töréspontok, ha azt szeretné, csak hibájához nem ismétlési végrehajtásakor, beállíthat feltételes töréspont adott oldaltörések csak akkor, ha `IsReplaying` van `false`.

## <a name="storage"></a>Storage

Alapértelmezés szerint a tartós funkciók tárolja az Azure Storage állapotát. Ez azt jelenti, hogy a álló eszközökkel, például a üzenettípusok összehangolását állapotának vizsgálhatja [Microsoft Azure Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Az Azure Tártallózó képernyőkép](media/durable-functions-diagnostics/storage-explorer.png)

Ez akkor hasznos, láthatja, hogy pontosan milyen állapotban az orchestration lehet, mert a hibakereséshez. A várólisták üzenetek is megvizsgálhatók, amelyből megtudhatja, milyen munkahelyi függőben lévő (illetve egyes esetekben akadt).

> [!WARNING]
> Célszerű a table storage-ban végrehajtási előzményei között találja, amíg elkerülése érdekében ebben a táblázatban minden függőségi véve. A tartós funkciók bővítmény fejlődésének meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartós időzítők használata](durable-functions-timers.md)