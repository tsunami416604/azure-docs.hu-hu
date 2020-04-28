---
title: Külső események feldolgozása a Durable Functionsban – Azure
description: Ismerje meg, hogyan kezelheti a külső eseményeket a Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76262962"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Külső események Durable Functionsban való feldolgozása (Azure Functions)

A Orchestrator függvények megvárhatják és megfigyelheti a külső eseményeket. A [Durable functions](durable-functions-overview.md) ez a funkciója gyakran hasznos az emberi interakciók vagy más külső triggerek kezeléséhez.

> [!NOTE]
> A külső események egyirányú aszinkron műveletek. Nem alkalmasak olyan helyzetekben, amikor az eseményt küldő ügyfélnek szinkron választ kell kapnia a Orchestrator függvénytől.

## <a name="wait-for-events"></a>Várakozás az eseményekre

A `WaitForExternalEvent` koordináló `waitForExternalEvent` [eseményindító kötésének](durable-functions-bindings.md#orchestration-trigger) (.net) és (JavaScript) metódusai lehetővé teszik, hogy egy Orchestrator függvény aszinkron módon várjon, és figyelje a külső eseményt. A Listening Orchestrator függvény deklarálja az esemény *nevét* és a fogadni kívánt *adatok alakját* .

# <a name="c"></a>[C #](#tab/csharp)

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
> Az előző C#-kód Durable Functions 2. x. Durable Functions 1. x esetén a helyett a `DurableOrchestrationContext` `IDurableOrchestrationContext`értéket kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

Az előző példa egy adott eseményt figyel, és a fogadásakor végrehajtja a műveletet.

Egyszerre több eseményt is megfigyelheti, például az alábbi példához hasonlóan, amely a három lehetséges esemény értesítésének egyikét várja.

# <a name="c"></a>[C #](#tab/csharp)

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
> Az előző C#-kód Durable Functions 2. x. Durable Functions 1. x esetén a helyett a `DurableOrchestrationContext` `IDurableOrchestrationContext`értéket kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

Az előző példa a több esemény *bármelyikét* figyeli. Az *összes* eseményre várni is lehet.

# <a name="c"></a>[C #](#tab/csharp)

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
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a helyett a `DurableOrchestrationContext` `IDurableOrchestrationContext`értéket kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

A .NET-ben, ha az esemény adattartalma nem konvertálható a `T`várt típusba, kivétel keletkezik.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

`WaitForExternalEvent`határozatlan ideig vár néhány bemenetre.  A Function alkalmazás a várakozás közben biztonságosan eltávolítható a memóriából. Ha és ha egy esemény érkezik ehhez a beszerelési példányhoz, a rendszer automatikusan felébreszti az eseményt, és azonnal feldolgozza azt.

> [!NOTE]
> Ha a Function alkalmazás a használati tervet használja, akkor nem számítunk fel számlázási díjat, amikor egy Orchestrator-függvény a `WaitForExternalEvent` (.net) vagy `waitForExternalEvent` (JavaScript) feladatra vár, függetlenül attól, hogy mennyi ideig vár.

## <a name="send-events"></a>Események küldése

A `RaiseEventAsync` koordinációs [ügyfél kötésének](durable-functions-bindings.md#orchestration-client) (.net) vagy `WaitForExternalEvent` `waitForExternalEvent` `raiseEvent` (JavaScript) metódusa elküldi a (.net) vagy a (JavaScript) által várt eseményeket.  A `RaiseEventAsync` metódus a *EventName* és a *eventData* paraméterként veszi igénybe. Az eseménynek JSON-szerializálható kell lennie.

Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely a "jóváhagyás" eseményt küld egy Orchestrator függvény példányára. Az előkészítési példány azonosítója az üzenetsor-üzenet törzsében található.

# <a name="c"></a>[C #](#tab/csharp)

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
> Az előző C#-kód Durable Functions 2. x. Durable Functions 1. x esetén `OrchestrationClient` az attribútum helyett `DurableClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia a helyett. `IDurableOrchestrationClient` A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Belsőleg, `RaiseEventAsync` (.net) vagy `raiseEvent` (JavaScript) enqueues egy üzenetet, amely a Waiting Orchestrator függvény által beolvasott üzenetbe kerül. Ha a példány nem várakozik a megadott *esemény nevére,* a rendszer hozzáadja az eseményt a memóriában tárolt várólistához. Ha az *esemény neve* később megkezdi a figyelést, akkor az esemény üzeneteinek várólistáját fogja ellenőriznie.

> [!NOTE]
> Ha nincs a megadott *példány-azonosítóval*rendelkező előkészítési példány, az esemény üzenetét a rendszer elveti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudnivalók a hibakezelés megvalósításáról](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Olyan minta futtatása, amely megvárja az emberi interakciót](durable-functions-phone-verification.md)