---
title: "Kezelheti a tartós funkciók - Azure-példány"
description: "Útmutató a tartós funkciók bővítmény példánya kezelése az Azure Functions."
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
ms.openlocfilehash: 399bad6f00b61d582fdb077f33000b6c55cf8904
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Példányok a tartós függvények (az Azure Functions) kezelése

[Tartós funkciók](durable-functions-overview.md) vezénylési példányok indítható, megszakadt, kérdezhetők le, és értesítési események küldése. Minden példány felügyeleti történik használatával a [vezénylési ügyfél kötésének](durable-functions-bindings.md). Ez a cikk hiányzóra változik, minden példány felügyeleti művelet részleteit.

## <a name="starting-instances"></a>Példányok indítása

A [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) elindítja az orchestrator funkcióinak egy új példányát. Ez az osztály példányai használatával lehet lekérni a `orchestrationClient` kötés. Belső, a módszer enqueues vezérlő várakozási, majd elindítja egy függvény a megadott nevű használó start üzenetet a `orchestrationTrigger` indítás kötés.

A paraméterek a következők:

* **Név**: ütemezése az orchestrator függvény neve.
* **Bemeneti**: a bemenetként az orchestrator szolgáltatás legyen átadva szerializálható JSON adatokat.
* **InstanceId**: példány (nem kötelező) a egyedi azonosítója. Ha nincs megadva, a véletlenszerű Példányazonosítót jön létre.

Íme egy egyszerű C# példa:

```csharp
[FunctionName("HelloWorldManualStart")]
public static Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

A .NET nyelv, a függvény kimeneti kötése elindítani az új példányokat is használható. Ebben az esetben bármely JSON-szerializálható objektumhoz, melyhez a fenti három paraméterek van mezői használható. Vegyük példaként a következő Node.js-függvény:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Azt javasoljuk, hogy egy véletlenszerű azonosítót használja a példány azonosítója. Ez segít az egyenlőnek terhelést terjesztési győződjön meg arról, amikor az orchestrator funkciók skálázás több virtuális gépek között. A megfelelő időpontok nem véletlenszerű példány azonosítók akkor, ha az azonosító egy külső forrásból kell származnia, illetve végrehajtásakor a [egypéldányos orchestrator](durable-functions-singletons.md) mintát.

## <a name="querying-instances"></a>Példányok lekérdezése

A [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály vezénylési példány állapotának lekérdezése. Tart egy `instanceId` paraméterként és egy objektumot a következő tulajdonságokkal:

* **Név**: az orchestrator függvény neve.
* **InstanceId**: a vezénylési Példányazonosítója (meg kell egyeznie a `instanceId` bemeneti).
* **CreatedTime**: az orchestrator függvény kezdésének ideje futó.
* **LastUpdatedTime**: az időpontot, amikor az orchestration utolsó alkulcsaihoz.
* **Bemeneti**: A bemeneti JSON értékként a függvény.
* **Kimeneti**: a függvényt, egy JSON-érték (Ha a függvény befejeződött) kimenetét. Az orchestrator függvény futása sikertelen, ha ez a tulajdonság tartalmazza a hiba részletei. Ha az orchestrator-funkció le lett állítva, ez a tulajdonság (ha van ilyen) a megadott okának megszüntetése tartalmazza.
* **RuntimeStatus**: a következő értékek egyikét:
    * **Futó**: A példány elindult.
    * **Befejeződött**: Ez a példány általában befejeződött.
    * **ContinuedAsNew**: A példány újraindítása magát az új előzményeit. Ez egy átmeneti állapotban.
    * **Nem sikerült**: A példány egy hiba miatt sikertelen volt.
    * **Megszakadt**: A példány váratlanul leállt.
    
Ez a metódus visszaadja `null` , ha a példány nem létezik, vagy még nem kezdődött el futtatása.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Példány lekérdezés jelenleg csak a C# funkcióihoz támogatott.

## <a name="terminating-instances"></a>Leállítja a példányok

Egy futó példány használatával kell szüntetni a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metódusában a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. A két paraméter egy `instanceId` és egy `reason` karakterláncot, amely a naplókat, és a példány állapota lesz írva. A leállított példánya, amint a következő eléri le fog állni `await` pontot, vagy megszünteti az azonnal Ha már be egy `await`.

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Példány lezárást jelenleg csak a C# funkcióihoz támogatott.

## <a name="sending-events-to-instances"></a>Események küldése példányok

Eseményértesítések futó példányait használatával is küldhetők a [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metódusában a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. Ezek az események kezelhető példányok megegyeznek hívása váró [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). A bemeneti adatok a következők:

* **InstanceId**: példány egyedi azonosítója.
* **EventName**: küldendő esemény neve.
* **EventData**: A szerializálható JSON adattartalom küldéséhez példányához.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Események kiváltása jelenleg csak a C# funkcióihoz támogatott.

> [!WARNING]
> Ha nincs vezénylési példány a megadott *-példány azonosítója* , vagy ha a példány nem vár a megadott *eseménynév*, a rendszer törli az eseményüzenet. Ez a viselkedés kapcsolatos további információkért tekintse meg a [GitHub probléma](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Például a felügyeleti HTTP API-k használata](durable-functions-http-api.md)
