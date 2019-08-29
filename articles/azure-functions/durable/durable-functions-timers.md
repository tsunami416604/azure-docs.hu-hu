---
title: Időzítők a Durable Functionsban – Azure
description: Megtudhatja, hogyan implementálhatja a tartós időzítőket a Azure Functions Durable Functions-bővítményében.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: 11edfc11fc1e54684a99774c21517d4c322348b1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087045"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Időzítők Durable Functions (Azure Functions)

[](durable-functions-overview.md) A Durable functions *tartós időzítőket* biztosít a Orchestrator függvényekben a késések megvalósításához, illetve az aszinkron műveletek időtúllépésének beállításához. Tartós időzítőket kell `Thread.Sleep` használni a és `Task.Delay` a (C#), vagy `setTimeout()` `setInterval()` a (JavaScript) helyett a Orchestrator függvényekben.

Tartós időzítőt hoz létre a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metódusának meghívásával a .net-ben `createTimer` vagy `DurableOrchestrationContext` a JavaScript-metódusban. A metódus egy olyan feladatot ad vissza, amely egy megadott dátumon és időpontban folytatja a műveletet.

## <a name="timer-limitations"></a>Időzítő korlátozásai

Amikor a 4:30 órakor lejáró időzítőt hoz létre, az alapul szolgáló tartós feladati keretrendszer enqueues egy üzenetet, amely csak a 4:30 órakor lesz látható. Ha a Azure Functions használati tervben fut, akkor az újonnan látható időzítő üzenet biztosítja, hogy a Function alkalmazás aktiválva legyen egy megfelelő virtuális gépen.

> [!NOTE]
> * Az Azure Storage korlátozásai miatt a tartós időzítők legfeljebb 7 napig tarthatnak. Egy szolgáltatási kérelemben dolgozunk [, amely 7 napon túli kibővíti az időzítőket](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Mindig használjon [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) `DateTime.UtcNow` -t a `Date.now` .net `currentUtcDateTime` helyett, vagy `Date.UTC` a JavaScript helyett, az alábbi példákban látható módon, amikor a tartós időzítő relatív határidejét számítja ki.

## <a name="usage-for-delay"></a>Használat késleltetéshez

Az alábbi példa azt szemlélteti, hogyan használható a tartós időzítő a végrehajtás késleltetéséhez. A példa minden nap tíz napra szóló számlázási értesítést állít ki.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Kerülje a végtelen hurkokat a Orchestrator functions szolgáltatásban. A végtelen hurkos forgatókönyvek biztonságos és hatékony megvalósításával kapcsolatos további információkért lásd: [örök](durable-functions-eternal-orchestrations.md)összeszerelések.

## <a name="usage-for-timeout"></a>Használat időtúllépés esetén

Ez a példa azt szemlélteti, hogyan használhatók a tartós időzítők az időtúllépések megvalósításához.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> A (z) használatával szakítson megC#egy tartós időzítőt (), vagy hívja `cancel()` meg a visszaadott `TimerTask` értéket (JavaScript) ,haakódnemvárjamegabetöltését.`CancellationTokenSource` A tartós feladatok keretrendszere nem változtatja meg a "befejezett" állapotot, amíg az összes fennmaradó feladat be nem fejeződik vagy meg nem szakítva.

Ez a mechanizmus valójában nem szakítja meg a folyamatban lévő tevékenységek működésének végrehajtását. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. Ha a Function alkalmazás a használati tervet használja, akkor az elhagyott tevékenység által felhasznált időt és memóriát is számlázni fogja. Alapértelmezés szerint a használati tervben futó függvények öt percen belül időtúllépéssel rendelkeznek. Ha túllépi ezt a korlátot, a rendszer újrahasznosítja a Azure Functions gazdagépet az összes végrehajtás leállításához és a elszabadult számlázási helyzet elkerüléséhez. A [függvény időtúllépése konfigurálható](../functions-host-json.md#functiontimeout).

A Orchestrator functions-időtúllépések megvalósításának alaposabb példáját az [emberi interakció & időtúllépések – telefonos ellenőrzési](durable-functions-phone-verification.md) útmutató című témakörben tekintheti meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a külső események növeléséről és kezeléséről](durable-functions-external-events.md)
