---
title: Durable Functions – Azure külső események kezeléséhez
description: Ismerje meg, hogyan legyen kezelve a Durable Functions bővítmény külső események az Azure Functions szolgáltatáshoz.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fefbdffdeb3db86447038a3b3d4d24e8c7cd3803
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340436"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) a külső események kezelése

Az orchestrator-függvények, várjon, és a külső események figyelésére. Ez a funkció a [Durable Functions](durable-functions-overview.md) emberi beavatkozás vagy más külső triggereken kezeléséhez gyakran hasznos.

## <a name="wait-for-events"></a>Várjon, amíg események

A [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) módszer lehetővé teszi, hogy egy orchestrator-függvény aszinkron módon Várjon, és a egy külső eseményre figyelésére. A figyelő az orchestrator függvény deklarálja a *neve* az esemény, és a *adat alakzat* kíván kapni.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

Az előző példában figyeli az adott eseményhez, és hajt végre műveletet, amikor a.

Több esemény figyelheti egyidejűleg, például a következő példában, amely megvárja, amíg három lehetséges eseményértesítések egyikét.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

Az előző példában figyeli a *bármely* több esemény. Várja meg, hogy lehetőség arra is *összes* eseményeket.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) határozatlan ideig vár a bemeneti adatokat.  Lehet, hogy a függvényalkalmazás biztonságosan memóriából való várakozás során. Egy esemény érkezik a vezénylési példány, ha automatikusan aktiválva van, és azonnal dolgozza fel az eseményt.

> [!NOTE]
> Ha a függvényalkalmazást a Használatalapú csomagban, nincs számlázási számítunk fel díjat, míg egy orchestrator-függvényt vár a feladat `WaitForExternalEvent` (.NET) vagy `waitForExternalEvent` (JavaScript), függetlenül attól, hogy mennyi ideig vár.

A .NET-ben, ha az eseménytartalom nem konvertálható a várt típus `T`, függvény kivételt vált ki.

## <a name="send-events"></a>Események küldése

A [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) módszere a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztályban küldi az eseményeket, amelyek `WaitForExternalEvent` (.NET) vagy `waitForExternalEvent` megvárja, amíg (JavaScript).  A `RaiseEventAsync` módszer *eventName* és *eventData* paraméterekként. Az eseményadatok szerializálható JSON kell lennie.

Alul látható egy példa üzenetsor által aktivált függvényt, amely a "Jóváhagyás" eseményt küld az orchestrator-funkció példányát. A vezénylési Példányazonosító származik az üzenetsorban található üzenet törzse.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Belsőleg `RaiseEventAsync` (.NET) vagy `raiseEvent` (JavaScript) enqueues egy üzenet, amely a Várakozás az orchestrator-funkció beolvasása észlelnie.

> [!WARNING]
> Ha nincs vezénylési példánya és a megadott *példány azonosítója* , vagy ha a példány nem vár a megadott *eseménynév*, a rendszer törli az eseményüzenet. Ezzel a viselkedéssel kapcsolatos további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/29).

> [!WARNING]
> A JavaScript fejlesztésének helyileg, kell beállítania a környezeti változót `WEBSITE_HOSTNAME` való `localhost:<port>`, például. `localhost:7071` a módszer használatához `DurableOrchestrationClient`. Ezzel a követelménnyel kapcsolatban további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan állítható be külső vezénylések](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Futtasson egy mintát, amely megvárja, amíg a külső eseményeket](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Futtasson egy mintát, amely emberi beavatkozás](durable-functions-phone-verification.md)