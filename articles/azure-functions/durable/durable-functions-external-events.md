---
title: Külső események feldolgozása a Durable Functionsban – Azure
description: Ismerje meg, hogyan kezelheti a külső eseményeket a Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7d7fcc725d78b24a93b09cb9c76cf7dc0231cac2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232895"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Külső események Durable Functionsban való feldolgozása (Azure Functions)

A Orchestrator függvények megvárhatják és megfigyelheti a külső eseményeket. A [Durable functions](durable-functions-overview.md) ez a funkciója gyakran hasznos az emberi interakciók vagy más külső triggerek kezeléséhez.

> [!NOTE]
> A külső események egyirányú aszinkron műveletek. Nem alkalmasak olyan helyzetekben, amikor az eseményt küldő ügyfélnek szinkron választ kell kapnia a Orchestrator függvénytől.

## <a name="wait-for-events"></a>Várakozás az eseményekre

A Orchestrator függvény `WaitForExternalEvent` (.NET) és `waitForExternalEvent` (JavaScript) metódusa [lehetővé teszi, hogy a rendszer](durable-functions-bindings.md#orchestration-trigger) aszinkron módon várjon, és figyelje a külső eseményt. A Listening Orchestrator függvény deklarálja az esemény *nevét* és a fogadni kívánt *adatok alakját* .

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

Az előző példa egy adott eseményt figyel, és a fogadásakor végrehajtja a műveletet.

Egyszerre több eseményt is megfigyelheti, például az alábbi példához hasonlóan, amely a három lehetséges esemény értesítésének egyikét várja.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

Az előző példa a több esemény *bármelyikét* figyeli. Az *összes* eseményre várni is lehet.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

`WaitForExternalEvent` határozatlan ideig vár néhány bemenetre.  A Function alkalmazás a várakozás közben biztonságosan eltávolítható a memóriából. Ha és ha egy esemény érkezik ehhez a beszerelési példányhoz, a rendszer automatikusan felébreszti az eseményt, és azonnal feldolgozza azt.

> [!NOTE]
> Ha a Function alkalmazás a használati tervet használja, akkor nem számítunk fel számlázási díjat, amikor egy Orchestrator függvény `WaitForExternalEvent` (.NET) vagy `waitForExternalEvent` (JavaScript) feladatra vár, függetlenül attól, hogy mennyi ideig vár.

Ha a .NET-ben az esemény adattartalma nem alakítható át a várt típusú `T`ba, kivétel keletkezik.

## <a name="send-events"></a>Események küldése

A koordináló [ügyfél kötésének](durable-functions-bindings.md#orchestration-client) `RaiseEventAsync` (.net) vagy `raiseEvent` (JavaScript) metódusa elküldi a `WaitForExternalEvent` (.net) vagy `waitForExternalEvent` (JavaScript) által várt eseményeket.  A `RaiseEventAsync` metódus a *eventName* és a *eventData* paramétert veszi igénybe. Az eseménynek JSON-szerializálható kell lennie.

Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely a "jóváhagyás" eseményt küld egy Orchestrator függvény példányára. Az előkészítési példány azonosítója az üzenetsor-üzenet törzsében található.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Belsőleg, `RaiseEventAsync` (.NET) vagy `raiseEvent` (JavaScript) enqueues egy üzenetet, amely a Waiting Orchestrator függvény által beolvasott üzenetbe kerül. Ha a példány nem várakozik a megadott *esemény nevére,* a rendszer hozzáadja az eseményt a memóriában tárolt várólistához. Ha az *esemény neve* később megkezdi a figyelést, akkor az esemény üzeneteinek várólistáját fogja ellenőriznie.

> [!NOTE]
> Ha nincs a megadott *példány-azonosítóval*rendelkező előkészítési példány, az esemény üzenetét a rendszer elveti.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók a hibakezelés megvalósításáról](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Olyan minta futtatása, amely megvárja az emberi interakciót](durable-functions-phone-verification.md)