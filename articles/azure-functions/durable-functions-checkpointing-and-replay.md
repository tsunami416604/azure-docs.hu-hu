---
title: Ellenőrzőpontok és a tartós funkciók - Azure ismétlés
description: Ismerje meg, hogyan ellenőrzőpontok létrehozása és a válasz működik a tartós funkciók bővítményt az Azure Functions.
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 39cdb9b2c6eae9a3176aedc64b8d187e298fdfdd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764570"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Ellenőrzőpontok és a tartós függvények (az Azure Functions) ismétlés

A legfőbb attribútumai a tartós funkciók egyik **megbízható végrehajtását**. Tevékenység és az orchestrator függvények adatközponton belüli különböző virtuális gépek előfordulhat, hogy futnak, és virtuális gépek vagy a mögöttes hálózati infrastruktúra nem megbízható 100 %.

Ennek ellenére tartós funkciók álló üzenettípusok összehangolását megbízható végrehajtását biztosítja. Igen, a meghajtó függvény meghívása tárüzenetsort és rendszeres időközönként ellenőrzőpontok futtatási előzményei révén tárolási táblákba (úgynevezett felhő tervezési minta használatával [esemény forrás](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Adott előzmények majd képes visszajátszani automatikusan újraépítheti a a memóriában orchestrator funkcióinak.

## <a name="orchestration-history"></a>Vezénylési előzmények

Tegyük fel, a következő orchestrator-funkció:

#### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak funkciók v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const output = [];
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "London"));

    return output;
});
```

Minden egyes `await` (C#) vagy `yield` (JavaScript) utasítás, a tartós feladat keretrendszer ellenőrzőpontokat a függvény a táblázat tároló végrehajtási állapotát. Ebben az állapotban, hogy mi a neve a *vezénylési előzmények*.

## <a name="history-table"></a>Előzménytábla

Általánosságban véve a tartós feladat keretrendszer hajtja végre a következő parancsot minden ellenőrzőpont:

1. Futtatási előzményei menti az Azure Storage-táblákat.
2. Függvények Enqueues üzeneteket az orchestrator szeretné meghívni.
3. Enqueues üzeneteket maga az orchestrator &mdash; például tartós időzítő üzeneteket.

Az ellenőrzőpont végrehajtása után az orchestrator függvény szabad eltávolítani a memóriából addig, amíg nincs rá több munkát.

> [!NOTE]
> Az Azure Storage nem biztosít bármely tranzakciós garancia-adatainak mentése az a table storage és a várólisták között. Kijavíthassa a hibákat, a tartós funkciók tárolási szolgáltatót használ *végleges konzisztencia* kombinációját. Ezek a minták győződjön meg arról, hogy adatok nem vesztek el egy összeomlási vagy a közepén ellenőrzőpont kapcsolat megszakadása esetén.

Létrehozása után a korábban bemutatott függvény előzményeinek jelenjenek meg a következőhöz Azure Table Storage (illusztrációs célokat szolgálnak rövidítése):

| PartitionKey (InstanceId)                     | EventType             | Időbélyeg               | Input (Bemenet) | Name (Név)             | Eredmény                                                    | status | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | NULL értékű  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tokió!" ""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Hello Seattle!" ""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello London!" ""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokió!" ",""Hello Seattle!" ",""Hello London!" "]" | Befejeződött           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Néhány megjegyzések oszlop értékeit:
* **PartitionKey**: tartalmazza a vezénylési Példányazonosítója.
* **Esemény típusa**: az esemény típusát jelöli. Előfordulhat, hogy a következő típusok egyike lehet:
    * **OrchestrationStarted**: az orchestrator függvény egy await újra, vagy az első alkalommal futtatja. A `Timestamp` oszlop determinisztikus értéke feltöltésére használt a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API.
    * **ExecutionStarted**: az orchestrator függvény lépések végrehajtása az első alkalommal. Ez az esemény is tartalmaz a függvény bemeneti a `Input` oszlop.
    * **TaskScheduled**: egy tevékenység függvény lett ütemezve. A tevékenység függvény neve bekerül az a `Name` oszlop.
    * **TaskCompleted**: egy tevékenység függvény végre lett hajtva. A függvény eredménye az a `Result` oszlop.
    * **TimerCreated**: tartós időzítő hozták létre. A `FireAt` oszlop tartalmaz ütemezett időpontja UTC idő szerint, ahol az időkorlát lejár.
    * **TimerFired**: tartós időzítő következik be.
    * **EventRaised**: külső esemény a vezénylési példány lett küldve. A `Name` oszlop rögzíti az esemény neve és a `Input` oszlop az esemény hasznos adatait rögzíti.
    * **OrchestratorCompleted**: az orchestrator függvény rá várakozni.
    * **ContinueAsNew**: az orchestrator függvény befejeződött, és új állapotú újraindul saját magát. A `Result` oszlop értékét, a bemeneti újraindított példány használt tartalmazza.
    * **ExecutionCompleted**: az orchestrator függvény befejezési futott (vagy sikertelen). A függvény vagy a hiba részletes adatait a kimenetek tárolódnak a `Result` oszlop.
* **Timestamp típusú**: az előzmények esemény időbélyegzője az UTC.
* **Név**: lett meghívva függvény nevét.
* **Bemeneti**: a függvény a JSON-formátumú bemenetet.
* **Eredmény**: annak a függvénynek a kimeneti; Ez azt jelenti, hogy a visszatérési érték.

> [!WARNING]
> Bár ez hasznos hibakeresési eszköz, ebben a táblázatban nem lép bármely függőségi. A tartós funkciók bővítmény fejlődésének meg.

Minden alkalommal, amikor a folytatja a függvény egy `await`, tartós feladat keretében Újrafuttatja a teljesen új orchestrator-függvény. Minden futtassa újból a futtatási előzményei, annak meghatározásához, hogy az aktuális aszinkron műveletet hajtott tekint, helyezze el.  A művelet került sor, ha a keretrendszer művelet eredményének replays azonnal, és áthelyezi a következő `await`. A folyamat folytatódik, amíg a teljes előzmények rendelkezik lett visszajátszani, amikor a helyi változókat az orchestrator függvényben visszaállnak az előző értékre.

## <a name="orchestrator-code-constraints"></a>Az orchestrator kód megkötések

A visszajátszás viselkedés kódot, amely egy orchestrator függvényben csak írható típusú megkötéseket hoz létre:

* Az orchestrator kódjának különböznie kell **determinisztikus**. Többször visszajátszani lesz, és ki kell dolgoznia ugyanazt az eredményt minden alkalommal, amikor. Például nincs közvetlen hívások lekérni az aktuális dátum/idő, véletlenszerű szám lekérése, véletlenszerű GUID generálni, vagy hívja az távoli végpontok.

  Ha az orchestrator kódot kell kapnia az aktuális dátum/idő, azt kell használni a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API-t a visszajátszás biztonságos.

  A nem determinisztikus műveletek tevékenység funkciók után kell elvégezni. Ez magában foglalja a bármely más bemeneti vagy kimeneti kötések való együttműködéshez. Ez biztosítja, hogy nem determinisztikus értékeket előállítja egyszer az első végrehajtásakor, és menti azokat a futtatási előzményei. A későbbi végrehajtások során fogja használni, a mentett érték automatikusan.

* Az orchestrator-kód legyen **nem blokkoló**. Például, amely azt jelenti, hogy nincs i/o- és a hívások nem `Thread.Sleep` vagy ezzel egyenértékű API-t.

  Ha az orchestrator késleltetés, képes használni a [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) API.

* Az orchestrator kódot kell **soha ne indítsa el a bármely aszinkron műveletet** használatával, kivéve a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API. Például nem `Task.Run`, `Task.Delay` vagy `HttpClient.SendAsync`. Orchestrator kód végrehajtása a egyetlen szálon és nem kommunikálhat más aszinkron API-k által sikerült beütemezni más szálak tartós feladat keretében.

* **Végtelen hurkok jelentésparaméterének** orchestrator-kódban. A tartós feladat keretrendszer futtatási előzményei menti a vezénylési függvény előrehaladtával, mert végtelen hurkot támadó orchestrator példány futtatásához nincs elegendő szabad memória. Végtelen hurkot forgatókönyvek esetén használjon API-k például [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) indítsa újra a függvény végrehajtása és előző futtatási előzményei.

Míg ezek a megkötések előfordulhat, hogy tűnhet, először a gyakorlatban nem nehéz kövesse. A tartós feladat keretrendszer megpróbálja észlelni a fenti szabályok megsértése és jelez a `NonDeterministicOrchestrationException`. Azonban ez a viselkedés észlelését legjobb, és nem ajánlott azt függ.

> [!NOTE]
> Ezek a szabályok alkalmazása csak által indított funkciók a `orchestrationTrigger` kötés. Tevékenység-funkciók által kiváltott a `activityTrigger` kötés, és a Funkciók, amelyek a `orchestrationClient` kötés, nincs ilyen korlátozásokkal rendelkezik.

## <a name="durable-tasks"></a>Állandó tevékenységek

> [!NOTE]
> Ez a szakasz ismerteti a tartós feladat keretrendszer belső megvalósítás részletei. Ez az információ ismerete nélkül tartós funkciók is használhatja. Célja, hogy csak annak megértéséhez a visszajátszás viselkedését.

Az orchestrator funkciók biztonságosan várakozni feladatokhoz alkalmanként nevezzük *tartós feladatok*. Ezek a feladatok létrehozása és a tartós feladat keretrendszer által felügyelt. Többek között a feladatok által visszaadott `CallActivityAsync`, `WaitForExternalEvent`, és `CreateTimer`.

Ezek *tartós feladatok* belsőleg kezeli listájával `TaskCompletionSource` objektumok. Során ismétlés ezeket a feladatokat az orchestrator kód végrehajtásának részeként létrehozása, és vannak befejezni, mert a kézbesítő számba veszi a megfelelő előzmények események. Az összes ehhez szinkron módon használja a egyetlen szálon, amíg az előzmények rendelkezik lett újból. Bármely tartós feladatokat, melyekhez nem fejeződtek be az előzmények ismétlési végéig rendelkezik megfelelő végzett műveleteket. Például a üzenetet a várólistában levő egy tevékenység függvény hívása lehet.

Az itt leírt végrehajtási viselkedésének kell segítenek megérteni, miért orchestrator függvény kód soha nem kell `await` nem tartós feladat: a dispatcher száltól nem Várjon, amíg az véget ér, és bármely visszahívási által ezt a feladatot sikerült eredményezhet a követési az orchestrator-funkció állapotának. Néhány futásidejű ellenőrzést próbálja Ennek megelőzése érdekében érvényben vannak.

Hogyan tartós feladat keretében végrehajtja az orchestrator funkciók további információt szeretne, a legjobb hozzá kell esetén kövesse a [tartós feladat forrás kódja a Githubon](https://github.com/Azure/durabletask). Különösen, lásd: [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) és [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók példánykezelés](durable-functions-instance-management.md)
