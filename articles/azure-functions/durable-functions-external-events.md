---
title: "A tartós funkciók - Azure külső események kezelésére"
description: "Útmutató az Azure Functions a tartós funkciók bővítményben külső események leíróját."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 1f581be0abaff542285abc0d4c2f4bffe7281d20
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>A tartós függvények (az Azure Functions) külső események kezelésére

Az orchestrator várja meg, és a külső események figyelésére van. Ez a szolgáltatás a [tartós funkciók](durable-functions-overview.md) hasznos emberi beavatkozást igényel, vagy más külső eseményindítók kezelésére.

## <a name="wait-for-events"></a>Várjon, amíg az események

A [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) módszer lehetővé teszi, hogy az orchestrator függvény aszinkron módon várja meg, és a külső esemény figyelésére. A figyelő az orchestrator függvény deklarálja a *neve* az esemény és a *alakzat adatok* kíván fogadni.

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

Az előző példában figyeli az adott eseményhez, és végrehajtja a műveletet, amikor a fogadja.

Több események figyelheti meg egyidejűleg, például a következő példának, amely három lehetséges eseményértesítések vár a.

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

Az előző példában figyeli a *bármely* több események. Akkor is várakozási *összes* események.

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) néhány bemeneti határozatlan ideig vár.  Lehet, hogy a függvény app biztonságosan memóriából való várakozás során. Egy esemény érkezik a vezénylési példány, ha automatikusan aktiválva van, és azonnal dolgozza fel az eseményt.

> [!NOTE]
> Függvény alkalmazása használja-e a felhasználási tervezze meg, ha adatforgalmi díjak sem merülnek fel, amíg az orchestrator függvény vár a feladat `WaitForExternalEvent`, függetlenül attól, hogy mennyi ideig vár.

Ha az eseménytartalom nem konvertálható a várt típus `T`, kivétel történik.

## <a name="send-events"></a>Események küldése

A [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metódusában a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály küldi az eseményeket, amelyek `WaitForExternalEvent` vár.  A `RaiseEventAsync` metódus *eventName* és *eventData* paraméterekként. Az eseményadatok JSON-elemnek szerializálhatónak kell lennie.

Az alábbiakban van egy példa, várólista-eseményindítóval aktivált függvény küld egy "Jóváhagyás" eseményt egy orchestrator-funkció példányon. A vezénylési Példányazonosító származik a várólista üzenet törzsét.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Belsőleg `RaiseEventAsync` enqueues egy üzenetet, amely a várakozási orchestrator függvény által felvett lekérdezi.

> [!WARNING]
> Ha nincs vezénylési példány a megadott *-példány azonosítója* , vagy ha a példány nem vár a megadott *eseménynév*, a rendszer törli az eseményüzenet. Ez a viselkedés kapcsolatos további információkért tekintse meg a [GitHub probléma](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan állíthat be eternal álló üzenettípusok összehangolását](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Futtasson egy mintát, amely megvárja-e a külső események](durable-functions-counter.md)

> [!div class="nextstepaction"]
> [Futtasson egy mintát, amely megvárja, emberi beavatkozást igényel](durable-functions-phone-verification.md)

