---
title: "A tartós funkciók - Azure időzítők"
description: "Útmutató az Azure Functions a tartós funkciók bővítmény tartós időzítők valósít meg."
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
ms.openlocfilehash: e29e472860890e3f44af79c42c31ff524acb9276
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="timers-in-durable-functions-azure-functions"></a>A tartós függvények (az Azure Functions) időzítők

[Tartós funkciók](durable-functions-overview.md) biztosít *tartós időzítők* orchestrator funkciók késések végrehajtásához vagy aszinkron műveletek időtúllépése beállításához használható. Orchestrator funkcióit a tartós időzítők használandó `Thread.Sleep` vagy `Task.Delay`.

Létrehozhat egy tartós időzítő meghívásával a [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metódus a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). A metódus visszaadja egy feladatot, amely a megadott napon és időpontban folytatódik.

## <a name="timer-limitations"></a>Időzítő korlátozásai

Amikor létrehoz egy időzítőt, amely egy üzenetet, amely csak du. 4:30 lesz látható du. 4:30, az alapul szolgáló tartós feladat keretrendszer enqueues lejár. Ha fut az Azure Functions fogyasztás terv, az újonnan látható időzítő üzenet biztosítja, hogy a függvény app aktiválva lekérdezi egy megfelelő méretű.

> [!WARNING]
> * Tartós időzítők legfeljebb 7 napig az Azure Storage-korlátozások miatt nem utoljára. Dolgozunk a [szolgáltatás kérelmet időzítők 7 napon túl](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Mindig [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) helyett `DateTime.UtcNow` ahogy az alábbi példák a tartós időzítő relatív határidő számításakor.

## <a name="usage-for-delay"></a>Késleltetés használata

A következő példa bemutatja, hogyan használható a tartós időzítők a végrehajtási késleltetése. A példa kiállító tíz napja naponta számlázási értesítést.

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> Az orchestrator funkciók végtelen hurkok elkerülése érdekében. További információ a biztonságos és hatékony végtelen hurkot forgatókönyvek megvalósításához: [Eternal álló üzenettípusok összehangolását](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Időtúllépés használata

Ez a példa bemutatja, hogyan tartós időzítők időtúllépések végrehajtásához használandó.

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!WARNING]
> Használja a `CancellationTokenSource` tartós időzítő megszakítja, ha a kód nem megvárja, amíg befejeződik. A tartós feladat keretében nem módosítja az orchestration állapota "kész" mindaddig, amíg az összes függőben lévő feladatok befejeződött vagy megszakítva.

A mechanizmus ténylegesen állítsa le a folyamatban lévő tevékenységek függvény végrehajtása. Ehelyett egyszerűen lehetővé teszi az orchestrator függvény figyelmen kívül hagyja az eredményt, és helyezze át. Ha a funkció alkalmazása használja-e a felhasználási terv, továbbra is Számlázott idő és a elhagyott tevékenység függvény által használt memória. Alapértelmezés szerint a fogyasztás tervben futtató funkciók rendelkeznek egy öt perces időkorlát. Ha túllépi ezt a korlátot, az Azure Functions állomás újrahasznosított összes végrehajtási és elkerülése érdekében egy elszabadult számlázási. A [függvény időtúllépési érték konfigurálható](functions-host-json.md#functiontimeout).

Például egy részletesebb időtúllépések megvalósításának orchestrator funkciókkal, tekintse meg a [emberi beavatkozást igényel & időtúllépések - Telefonszám ellenőrzése a](durable-functions-phone-verification.md) forgatókönyv.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan és külső események kezelésére](durable-functions-external-events.md)

