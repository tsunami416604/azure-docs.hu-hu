---
title: Ellenőrzőpontok és visszajátszás a Durable Functions-ben – Azure
description: Ismerje meg, hogyan működik az ellenőrzőpontok és a válasz a Azure Functions Durable Functions-bővítményében.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 79cb276f121c351a9954994038d9d826819edf5d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087456"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Ellenőrzőpontok és visszajátszás Durable Functions (Azure Functions)

Durable Functions egyik fő attribútuma a **megbízható végrehajtás**. Az Orchestrator functions és a Activity functions az adatközponton belüli különböző virtuális gépeken, illetve ezeknél a virtuális gépeknél vagy a mögöttes hálózati infrastruktúránál nem 100%-os megbízható.

Ennek ellenére Durable Functions biztosítja a munkafolyamatok megbízható végrehajtását. Ezt úgy végzi el, hogy a tárolási várólisták használatával hajtja végre a függvény meghívását, és rendszeres időközönként lekéri a végrehajtási előzményeket a tárolási táblákba (az [esemény forrásának](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)nevezett Felhőbeli tervezési minta használatával). Az előzmények ezután újra lejátszhatók, így automatikusan újraépíthető egy Orchestrator-függvény memóriában tárolt állapota.

## <a name="orchestration-history"></a>Előkészítési előzmények

Tegyük fel, hogy rendelkezik a következő Orchestrator-függvénnyel:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

Az állandó `await` feladatC#-keretrendszer `yield` minden () vagy (JavaScript) utasításban a függvény végrehajtási állapotát táblázatos tárolóba helyezi. Ezt az állapotot nevezzük a megszervezési *előzményeknek*.

## <a name="history-table"></a>Előzmények táblázat

Általánosságban elmondható, hogy az állandó feladathoz tartozó keretrendszer minden ellenőrzőponton a következőket végzi el:

1. Elmenti a végrehajtási előzményeket az Azure Storage-táblákba.
2. A Orchestrator által meghívott függvények Enqueues üzenetei.
3. A Orchestrator &mdash; Enqueues üzenetei, például tartós időzítő üzenetek.

Az ellenőrzőpont befejezését követően a Orchestrator függvény szabadon eltávolítható a memóriából, amíg még nem működik.

> [!NOTE]
> Az Azure Storage nem biztosít tranzakciós garanciát az adatmentés a Table Storage-ba és a várólistákba. A hibák kezelése érdekében a Durable Functions Storage-szolgáltató *végleges konzisztencia* -mintákat használ. Ezek a minták biztosítják, hogy az ellenőrzőpontok közepén lévő kapcsolat összeomlása vagy elvesztése esetén ne vesszenek el az adatvesztés.

Ha végzett, a korábban bemutatott függvény előzményei a következőhöz hasonlóan néznek ki az Azure Table Storageban (illusztrációs célokra rövidítve):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Input (Bemenet) | Name (Név)             | Eredmény                                                    | State |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tokyo!" "                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Helló Seattle!" "                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Helló Londonban!" "                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[" "Hello Tokyo!" "," "Helló Seattle!" "," "Hello London!" "]" | Befejeződött           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Néhány megjegyzés az oszlop értékeihez:

* **PartitionKey**: A koordinálás példányának AZONOSÍTÓját tartalmazza.
* **EventType**: Az esemény típusát jelöli. A következő típusok egyike lehet:
  * **OrchestrationStarted**: A Orchestrator függvény újraindul egy várakozási időszakból, vagy első alkalommal fut. Az `Timestamp` oszlop a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API determinisztikus értékének feltöltésére szolgál.
  * **ExecutionStarted**: A Orchestrator függvény első alkalommal indította el a végrehajtást. Ez az esemény az `Input` oszlop függvény bemenetét is tartalmazza.
  * **TaskScheduled**: Egy tevékenység-függvény ütemezése megtörtént. A tevékenység-függvény neve az `Name` oszlopban van rögzítve.
  * **TaskCompleted**: Egy tevékenység-függvény befejeződött. A függvény `Result` eredménye az oszlopban található.
  * **TimerCreated**: Egy tartós időzítő lett létrehozva. Az `FireAt` oszlop azt az ütemezett UTC-időt tartalmazza, amelyen az időzítő lejár.
  * **TimerFired**: Tartós időzítő.
  * **EventRaised**: A rendszer egy külső eseményt kapott a rendszerelőkészítési példánynak. Az `Name` oszlop rögzíti az esemény nevét, és az `Input` oszlop rögzíti az esemény hasznos adatait.
  * **OrchestratorCompleted**: A Orchestrator függvény vár.
  * **ContinueAsNew**: A Orchestrator függvény befejeződött, és új állapottal indult újra. Az `Result` oszlop tartalmazza azt az értéket, amelyet a rendszer az újraindított példány bemenetként használ.
  * **ExecutionCompleted**: A Orchestrator függvény befejeződött (vagy sikertelen). A függvény kimeneteit vagy a hiba részleteit az `Result` oszlopban tárolja a rendszer.
* **Időbélyeg**: Az előzmények eseményének UTC-időbélyege
* **Név**: A meghívott függvény neve.
* **Bemenet**: A függvény JSON formátumú bemenete.
* **Eredmény**: A függvény kimenete; Ez a visszatérési értéke.

> [!WARNING]
> Habár hibakeresési eszközként hasznos, ne tegyen függőséget ezen a táblán. Előfordulhat, hogy a Durable Functions bővítmény fejlődése megváltozhat.

Minden alkalommal, amikor a függvény egy `await` (C#) vagy `yield` (JavaScript) állapotból tér vissza, a tartós feladat-keretrendszer újra lefuttatja a Orchestrator függvényt. Minden újrafuttatáskor a végrehajtási előzmények alapján dönti el, hogy az aktuális aszinkron művelet megtörtént-e.  Ha a művelet lezajlott, a keretrendszer azonnal visszajátssza a művelet kimenetét, és továbblép a következőre `await` (C#) vagy `yield` (JavaScript). Ez a folyamat addig folytatódik, amíg a teljes előzmények újra le lettek állítva, ekkor a Orchestrator függvényben lévő összes helyi változó visszaállítható a korábbi értékekre.

## <a name="orchestrator-code-constraints"></a>Orchestrator-kód megkötései

Az újrajátszás viselkedése korlátozásokat hoz létre a Orchestrator függvényben írható kód típusától függően:

* A Orchestrator kódnak **determinisztikus**kell lennie. Többször is le lesz játszva, és minden alkalommal ugyanazt az eredményt kell létrehoznia. Például nincs közvetlen hívás az aktuális dátum/idő lekéréséhez, véletlenszerű számok beszerzéséhez, véletlenszerű GUID azonosítók létrehozásához vagy távoli végpontok hívásához.

  Ha a Orchestrator-kódnak le kell kérnie az aktuális dátumot és időpontot, akkor a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) ( `currentUtcDateTime` .net) vagy a (JavaScript) API-t kell használnia, amely biztonságos az újrajátszás számára.

  Ha a Orchestrator-kódnak véletlenszerű GUID-azonosítót kell létrehoznia, akkor a [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net) API-t kell használnia, amely biztonságos az újrajátszás számára, vagy delegálja a GUID-generációt egy Activity függvénynek (JavaScript), ahogy az a következő példában is látható:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  A nem determinisztikus műveleteket tevékenységi funkciókban kell elvégezni. Ez magában foglalja a más bemeneti vagy kimeneti kötésekkel való interakciót is. Ez biztosítja, hogy az első végrehajtáskor és a végrehajtási előzményekbe való mentés után minden nem determinisztikus értéket generáljon a rendszer. A következő végrehajtások ezt követően automatikusan a mentett értéket fogják használni.

* A Orchestrator kód **nem blokkolható**. Ez azt jelenti például, hogy nincs I/O, és nem `Thread.Sleep` a (.net) vagy az egyenértékű API-k hívása.

  Ha egy Orchestrator késleltetésre van szüksége, használhatja a [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.net) vagy `createTimer` a (JavaScript) API-t.

* A Orchestrator-kódnak **soha nem kell aszinkron műveletet kezdeményeznie** , kivéve a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API vagy `context.df` az objektum API-ját. `Task.Run`Például: nem ,`setTimeout()` .net `Task.Delay` vagy`setInterval()` JavaScript esetén. `HttpClient.SendAsync` A tartós feladat-keretrendszer egyetlen szálon hajtja végre a Orchestrator-kódot, és nem tud kommunikálni más aszinkron API-k által ütemezhető más szálakkal. Ha ez történik, `InvalidOperationException` a kivételt a rendszer kidobja.

* A **végtelen hurkokat el kell kerülni** a Orchestrator-kódban. Mivel a tartós feladatok keretrendszere menti a végrehajtási előzményeket, mivel a folyamat előrehaladtával egy végtelen hurok miatt előfordulhat, hogy egy Orchestrator-példány elfogyott a memóriából. Végtelen hurkos forgatókönyvek esetén használjon API-kat (például [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) ( `continueAsNew` .net) vagy (JavaScript)) a függvény végrehajtásának újraindításához és a korábbi végrehajtási előzmények elvetéséhez.

* A JavaScript Orchestrator functions `async`nem lehet. Ezeket szinkron létrehozó függvényekként kell deklarálni.

Habár ezek a megkötések először is ijesztőnek tűnhet, a gyakorlatban nem nehéz követni őket. A tartós feladat-keretrendszer megkísérli a fenti szabályok megsértésének észlelését, és egy `NonDeterministicOrchestrationException`. Ez az észlelési viselkedés azonban a legjobb erőfeszítés, és nem függ tőle.

> [!NOTE]
> Az összes szabály csak a `orchestrationTrigger` kötés által aktivált függvényekre vonatkozik. A `activityTrigger` kötés által aktivált tevékenység-függvények, valamint a `orchestrationClient` kötést használó függvények nem rendelkeznek ilyen korlátozásokkal.

## <a name="durable-tasks-net"></a>Tartós feladatok (.NET)

> [!NOTE]
> Ez a szakasz a tartós feladatok keretrendszerének belső megvalósítási részleteit ismerteti. A Durable Functions az információk ismerete nélkül is használhatja. Ez csak az újrajátszás viselkedésének megismerésére szolgál.

A Orchestrator függvényekben biztonságosan megtekinthető feladatok időnként *tartós*feladatoknak is nevezik. Ezek olyan feladatok, amelyeket a tartós feladatok keretrendszere hozott létre és felügyel. Ilyenek például a, `CallActivityAsync` `WaitForExternalEvent`a és `CreateTimer`a által visszaadott feladatok.

Ezek a *tartós feladatok* belsőleg kezelhetők `TaskCompletionSource` objektumok listájának használatával. Az újrajátszás során ezek a feladatok a Orchestrator-kód végrehajtásának részeként jönnek létre, és a kiosztók a megfelelő előzmény-események enumerálásával lesznek végrehajtva. Mindez szinkron módon történik egyetlen szál használatával, amíg az összes előzmény újra nem lett játszva. Minden olyan tartós feladat, amelyet az előzmények végén nem végez el, a megfelelő műveleteket hajtja végre. Előfordulhat például, hogy egy várólistán lévő hívni egy üzenetet.

Az itt leírt végrehajtási viselkedésnek meg kell ismernie, hogy miért nem tartós `await` feladat a Orchestrator funkció kódjának: a diszpécser szál nem várja meg, amíg befejeződik, és az adott feladat által visszaadott visszahívások esetleg megsértik a követést a Orchestrator függvény állapota. A rendszer bizonyos futásidejű ellenőrzéseket végez, hogy elkerülje ennek elkerülését.

Ha további információra van szüksége arról, hogy az állandó feladati keretrendszer hogyan hajtja végre a Orchestrator functions szolgáltatást, a [](https://github.com/Azure/durabletask)legjobb megoldás, ha az állandó feladathoz tartozó forráskódot a githubon tekinti át. Különösen lásd: [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) és [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A példányok kezelésének megismerése](durable-functions-instance-management.md)
