---
title: Ellenőrzőpontok és a visszajátszás a Durable Functions – Azure
description: Ismerje meg az ellenőrzőpontok létrehozása és a válasz működését a Durable Functions bővítmény az Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: b1fd31a758501620129fdbbc532b8defcf927045
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607347"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Ellenőrzőpontok és a visszajátszás a tartós függvények (az Azure Functions)

Egyik legfőbb attribútumai a Durable Functions **megbízható végrehajtását**. Tevékenység és az orchestrator függvények futnak különböző virtuális gépek egy adatközponton belül, és ezeken a virtuális gépeken vagy a mögöttes hálózati infrastruktúra nem megbízható 100 %-os.

Durable Functions értéket, ez biztosítja a vezénylések megbízható végrehajtását. Ezt úgy valósítja meg a meghajtó függvény meghívási tároló-üzenetsorok használatával, és rendszeres időközönként az ellenőrzőpontok használata futtatási előzményei szerint tárolási táblákba (más néven felhőalapú tervezési minta használatával [Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Adott előzmények is majd játssza vissza automatikusan újraépítheti a memórián belüli állapotát egy orchestrator-függvényt.

## <a name="orchestration-history"></a>Vezénylési előzmények

Tegyük fel, a következő orchestrator függvényt:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

Minden egyes `await` (C#) vagy `yield` (JavaScript) utasítás, a tartós feladat keretrendszer ellenőrzőpontokat a table storage-bA a függvény végrehajtási állapotát. Ebben az állapotban, hogy mi a neve a *vezénylési előzmények*.

## <a name="history-table"></a>Előzménytáblához

Általánosan fogalmazva tartós feladat keretében hajtja végre a következő parancsot minden egyes ellenőrzőpont:

1. Azure Storage-táblába menti a futtatási előzményei.
2. Functions Enqueues üzeneteket az orchestrator szeretne meghívni.
3. Az orchestrator maga Enqueues üzenetek &mdash; például tartós időzítő üzeneteket.

Az ellenőrzőpont után az orchestrator függvény díjmentes segítségével eltávolítható memória addig, amíg nincs több munkát rá.

> [!NOTE]
> Az Azure Storage nem tartalmaz minden tranzakciós garanciák adatok mentése a table storage és az üzenetsorok között. Hibáinak a kezelése, használja a Durable Functions-tároló társzolgáltatót *végleges konzisztencia* mintákat. Ezek a minták győződjön meg arról, hogy nem történik adatvesztés egy összeomlás vagy közepén ellenőrzőpont kapcsolat megszakadása esetén.

Befejezéskor előzményeit, a korábban bemutatott függvény a következőhöz hasonló lesz a következő az Azure Table Storage (az illusztráció rövidítése):

| PartitionKey (InstanceId)                     | EventType             | Időbélyeg               | Input (Bemenet) | Name (Név)             | Eredmény                                                    | status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
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
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Helló Tokió!" ",""Hello Seattle!" ",""Hello London!" "]" | Befejezve           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Néhány megjegyzés a kötegműveletekkel kapcsolatban az oszlopértékeket:

* **PartitionKey**: A vezénylési példány Azonosítóját tartalmazza.
* **Esemény típusa**: Az esemény típusát jelzi. Előfordulhat, hogy a következők egyike lehet:
  * **OrchestrationStarted**: Az orchestrator függvény egy await újra, vagy az első alkalommal fut-e. A `Timestamp` oszlop determinisztikus értéke feltöltésére szolgál a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API-t.
  * **ExecutionStarted**: Az orchestrator függvény lépések végrehajtása az első alkalommal. Ez az esemény is tartalmazza, a függvény bemeneti a `Input` oszlop.
  * **TaskScheduled**: Egy tevékenység függvény lett ütemezve. A tevékenység függvény neve van rögzítve a `Name` oszlop.
  * **TaskCompleted**: Egy tevékenység függvény befejeződött. A függvény eredménye a `Result` oszlop.
  * **TimerCreated**: Tartós időzítő jött létre. A `FireAt` oszlop tartalmazza a megadott (UTC) időpont, amikor az időzítő lejár.
  * **TimerFired**: Tartós időzítő lép működésbe.
  * **EventRaised**: Egy külső eseményre lett elküldve a vezénylési példányhoz. A `Name` oszlop rögzíti az esemény nevét, és a `Input` oszlop rögzíti a az esemény hasznos.
  * **OrchestratorCompleted**: Az orchestrator függvény várni.
  * **ContinueAsNew**: Az orchestrator függvény befejeződött, és magát az új állapot újraindul. A `Result` oszlop tartalmazza az értéket, amely a-példány újraindítása sikeresen megtörtént a bemenetként szolgál.
  * **ExecutionCompleted**: Az orchestrator függvény lefutott befejezését (vagy sikertelen). A függvény vagy a hiba részleteit a kimenetek vannak tárolva a `Result` oszlop.
* **Időbélyeg**: Az előzmények eseményt UTC-időbélyeg.
* **Név**: A függvény meghívása neve.
* **Bemeneti**: A JSON-formátumú bemeneti a függvény.
* **Eredmény**: A kimenet a függvény; Ez azt jelenti, hogy a visszaadott érték.

> [!WARNING]
> Bár a hasznos hibakeresési eszköz, a tábla nem kell minden olyan függőséget. Mivel a Durable Functions bővítmény haladásával is megváltoztathatja.

Minden alkalommal, amikor a függvény visszatér az egy `await` (C#) vagy `yield` (JavaScript), a tartós feladat keretrendszer Újrafuttatja a teljesen új orchestrator-függvény. Az egyes ismétlés meghatározhatja, hogy az aktuális aszinkron művelet rendelkezik, végrehajtási előzményeinek consults szintűre frissül, helyezze el.  A művelet került sor, ha a keretrendszer azonnal visszajátssza a művelet kimenetét, és áthelyezi a következő `await` (C#) vagy `yield` (JavaScript). Ez a folyamat folytatódik, amíg a teljes előzmények rendelkezik lett játssza vissza, mely a helyi változókat az orchestrator függvényben visszaállnak az előző értékekre.

## <a name="orchestrator-code-constraints"></a>Az orchestrator kód megkötései

A visszajátszás viselkedését a kódot, amely az orchestrator függvényben csak írható típusú megkötéseket hoz létre:

* Az orchestrator-kódot kell **determinisztikus**. Ez lesz többszöri megismétlését és a kell ugyanaz az eredmény minden alkalommal, amikor. Például nincs közvetlen hívások lekérése az aktuális dátumot és időpontot, véletlenszerű szám lekérése, véletlenszerű GUID azonosítókat létrehozni vagy hívásokat indítani olyan távoli végpontok.

  Ha az orchestrator-kódot kell lekérni az aktuális dátumot és időpontot, azt kell használnia a [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) vagy `currentUtcDateTime` (JavaScript) API-t, a visszajátszás biztonságos.

  Ha az orchestrator-kódot kell létrehozni a véletlenszerű GUID, azt kell használnia a [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) API-t, amely ebben a példában a visszajátszás, vagy delegált GUID generációs tevékenység függvény (JavaScript), biztonságos:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  A tevékenység függvények nem determinisztikus műveleteket kell elvégezni. Ez magában foglalja a más bemeneti vagy kimeneti kötések végzett minden művelet. Ez biztosítja, hogy nem determinisztikus értéket az első végrehajtás után hozza létre és menti a futtatási előzményei. Az azt követő végrehajtások fogja használni, a mentett érték automatikusan.

* Az orchestrator-kód legyen **nem blokkoló**. Ha például azt jelenti, hogy nem I/O- és a hívások nem `Thread.Sleep` (.NET) vagy ezzel egyenértékű API-k.

  Ha az orchestrator késleltetésre van szüksége, használhat a [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.NET) vagy `createTimer` (JavaScript) API-t.

* Az orchestrator-kódot kell **soha nem kezdeményez bármilyen aszinkron művelet** használatával, kivéve a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API vagy `context.df` objektum API. Például nem `Task.Run`, `Task.Delay` vagy `HttpClient.SendAsync` a .NET-ben, vagy `setTimeout()` és `setInterval()` javascriptben. Tartós feladat keretében hajtja végre az orchestrator-kódot egyetlen szálon, és bármely más olyan hozzászólásláncot, amelyet más API-k aszinkron sikerült ütemezni nem kezelheti.

* **Végtelen hurkok el kell kerülni** az orchestrator-kódban. Mivel a tartós feladat keretrendszer futtatási előzményei a vezénylési függvény előrehaladtával, végtelen ciklust eredményezheti egy orchestrator-példány futtatásához nincs elég memória. Végtelen ciklust esetekhez használhatja API-k például [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) vagy `continueAsNew` (JavaScript) indítsa újra a függvény végrehajtási és előző futtatási előzményei.

* Az orchestrator JavaScript-függvények nem lehet `async`. Ezek a szinkron funkciók értékűként kell deklarálni.

Bár ezek a korlátozások előfordulhat, hogy tűnhet, először a gyakorlatban nem nehéz kövesse. Tartós feladat keretében megkísérli a fenti szabályok megsértésének észlelése és jelez egy `NonDeterministicOrchestrationException`. Azonban ez a viselkedés észlelését a legjobb, és Ön nem függnek tőle.

> [!NOTE]
> Ezek a szabályok mindegyike csak érvényes által aktivált függvények a `orchestrationTrigger` kötést. Tevékenység függvények váltott a `activityTrigger` kötést, és a Funkciók, amelyek használják a `orchestrationClient` kötés esetén nincs ilyen korlátozásokkal rendelkezik.

## <a name="durable-tasks-net"></a>Tartós feladatok (.NET)

> [!NOTE]
> Ez a szakasz ismerteti a tartós feladat keretrendszer belső megvalósítási részletei. Durable Functions ezt az információt ismerete nélkül is használhatja. Célja, hogy csak a segítségével megismerheti a visszajátszás viselkedését.

Az orchestrator functions biztonságosan várni feladatok alkalmanként nevezzük *tartós feladatok*. Ezek a feladatok létrehozása és a tartós feladat keretrendszer által kezelt. Példák a tevékenységek által visszaadott `CallActivityAsync`, `WaitForExternalEvent`, és `CreateTimer`.

Ezek *tartós feladatok* belsőleg listájának használatával kezelt `TaskCompletionSource` objektumokat. Ezeket a feladatokat, ismétlés során jön létre az orchestrator kódfuttatás részeként, és végezhető el, mert a kézbesítő enumerálása a kapcsolódó előzmények eseményekre. Ez az összes történik szinkron módon használatával egyetlen szálból, mindaddig, amíg az összes előzményét rendelkezik lett játssza vissza. Bármely tartós feladatokat, és nem fejeződtek be az előzmények visszajátszását végéig rendelkezik a megfelelő műveleteket végrehajtani. Például az üzenet sorba helyezték az eseményközpontban meghívhat egy függvényt, tevékenység is lehet.

Az itt leírtak szerint végrehajtási viselkedésének kell segítségével megismerheti, hogy miért érdemes az orchestrator függvénykód soha nem kell `await` nem tartós feladat: dispatcher szál nem várja meg, amíg az befejeződik, és bármely visszahívási szerint ezt a feladatot sikerült eredményezhet a nyomon követés az orchestrator függvény állapotát. Ennek elkerülése érdekében tett érvényben vannak bizonyos modul ellenőrzi.

Ha szeretne további információ a hogyan tartós feladat keretében hajtja végre az orchestrator-funkciók, az ajánlott teendő, tekintse meg a [tartós feladat forráskód a Githubon](https://github.com/Azure/durabletask). Mindenekelőtt tekintse [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) és [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Instance management ismertetése](durable-functions-instance-management.md)
