---
title: Külső események kezelése a tartós funkciókban - Azure
description: Ismerje meg, hogyan kezelje a külső eseményeket az Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262962"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Külső események kezelése a tartós függvényekben (Azure Functions)

Az Orchestrator függvények képesek várni és figyelni a külső eseményeket. A [tartós funkciók ezen funkciója](durable-functions-overview.md) gyakran hasznos az emberi interakció vagy más külső eseményindítók kezeléséhez.

> [!NOTE]
> A külső események egyirányú aszinkron műveletek. Nem alkalmasak olyan helyzetekben, amikor az eseményt küldő ügyfélnek szinkron választ kell adnia az orchestrator függvénytől.

## <a name="wait-for-events"></a>Várakozás az eseményekre

A `WaitForExternalEvent` `waitForExternalEvent` [vezénylési eseményindító kötés](durable-functions-bindings.md#orchestration-trigger) (.NET) és (JavaScript) metódusai lehetővé teszik, hogy az orchestrator függvény aszinkron módon várjon és figyeljen egy külső eseményt. A figyelő orchestrator függvény deklarálja az esemény *nevét* és a várhatóan fogadni várt *adatok alakját.*

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
> Az előző C# kód a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Az előző példa egy adott esemény figyelése, és a fogadásakor műveleteket tesz.

Egyidejűleg több eseményt is figyelhet, például a következő példában, amely a három lehetséges eseményértesítés egyikére vár.

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
> Az előző C# kód a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Az előző példa több esemény *bármelyikét* figyeli. Az is lehetséges, hogy várjon *az összes* eseményt.

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
> Az előző kód a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

Ha a .NET-ben az esemény hasznos adata nem konvertálható a várt típusra, `T`a program kivételt okoz.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

`WaitForExternalEvent`vár a végtelenségig néhány bemenet.  A függvényalkalmazás biztonságosan eltávolítható várakozás közben. Ha és amikor egy esemény érkezik ehhez a vezénylési példányhoz, automatikusan felébred, és azonnal feldolgozza az eseményt.

> [!NOTE]
> Ha a függvényalkalmazás a Felhasználási sémát használja, nem merülnek fel számlázási díjak, amíg egy orchestrator függvény a `WaitForExternalEvent` (.NET) vagy `waitForExternalEvent` a (JavaScript) feladatra vár, függetlenül attól, hogy mennyi ideig vár.

## <a name="send-events"></a>Események küldése

A `RaiseEventAsync` [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client) (.NET) vagy `WaitForExternalEvent` `waitForExternalEvent` `raiseEvent` (JavaScript) metódusa elküldi azokat az eseményeket, amelyekre (.NET) vagy (JavaScript) vár.  A `RaiseEventAsync` metódus *az eventName-t* és *az eventData-t* paraméterként veszi fel. Az eseményadatoknak JSON-szerializálhatónak kell lenniük.

Az alábbiakban egy példa várólista által aktivált függvény, amely egy "Jóváhagyás" eseményt küld egy orchestrator függvénypéldány. A vezénylési példány azonosítója a várólista-üzenet törzséből származik.

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
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Belsőleg, `RaiseEventAsync` (.NET) `raiseEvent` vagy (JavaScript) várólistára egy üzenetet, amely lesz felvette a várakozó orchestrator függvény. Ha a példány nem a megadott *eseménynévre vár,* az eseményüzenet hozzáadódik egy memórián belüli várólistához. Ha a vezénylési példány később megkezdi az *esemény nevének figyelését,* akkor ellenőrzi az eseményüzenetek várólistáját.

> [!NOTE]
> Ha nincs vezénylési példány a megadott *példányazonosítóval,* az eseményüzenet elvetésre kerül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a hibakezelés megvalósításáról](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Futtasson egy mintát, amely megvárja az emberi interakciót](durable-functions-phone-verification.md)