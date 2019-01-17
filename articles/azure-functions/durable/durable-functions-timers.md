---
title: Időzítők a Durable Functions – Azure
description: Ismerje meg, tartós időzítők megvalósítása a Durable Functions bővítmény az Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: e81e842e059e09f24627138ba9fbf6510a603efe
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353294"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Időzítők a tartós függvények (az Azure Functions)

[Durable Functions](durable-functions-overview.md) biztosít *tartós időzítők* orchestrator funkciók megvalósításához az késleltetések vagy időtúllépések, az aszinkron műveletek beállításához használható. Tartós időzítők célszerű használni az orchestrator funkcióit `Thread.Sleep` és `Task.Delay` (C#), vagy `setTimeout()` és `setInterval()` (JavaScript).

Létrehozhat egy tartós időzítő meghívásával a [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metódusa [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) a .NET-ben, vagy a `createTimer` metódusa `DurableOrchestrationContext` a JavaScript. A metódus egy feladatot, amely a megadott napon és időpontban folytatódik adja vissza.

## <a name="timer-limitations"></a>Időzítő korlátozások

Amikor létrehoz egy időzítőt, amely egy üzenetet, amely csak a 4:30 = 1997031213: láthatóvá válik, 4:30 pm, az alapul szolgáló tartós feladat keretrendszer enqueues lejár. Az Azure Functions használatalapú csomagban fut, ha az újonnan látható időzítő üzenetet fog aktiválja az a függvényalkalmazás lekérdezi egy megfelelő virtuális gépre.

> [!NOTE]
> * Tartós időzítők nem még az Azure Storage-korlátozások miatt 7 napnál tovább. Folyamatban van egy [kiterjesztésére időzítők 7 napos időszak letelte után a szolgáltatás vonatkozó kérelem](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Mindig használjon [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) helyett `DateTime.UtcNow` .NET-keretrendszerben és `currentUtcDateTime` helyett `Date.now` vagy `Date.UTC` a JavaScript, ahogyan az alábbi példák a tartós időzítő relatív határidő kiszámításakor .

## <a name="usage-for-delay"></a>Késleltetés használata

A következő példa szemlélteti a végrehajtási késleltetése tartós időzítők használata. A példában minden nap 10 nappal számlázási értesítést ad ki.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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
> Az orchestrator funkciók végtelen hurkok elkerülése érdekében. Biztonságosan és hatékonyan végtelen ciklust forgatókönyvek megvalósítása kapcsolatos információkért lásd: [külső Vezénylések](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Használati időkorlát

Ebben a példában azt ábrázolja, hogyan használja a tartós időzítők időtúllépések megvalósításához.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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
> Használja a `CancellationTokenSource` egy tartós időzítő (C#) vagy a hívás megszakításához `cancel()` a által visszaadott `TimerTask` (JavaScript), ha a kód nem várakozik, amíg az befejeződik. Tartós feladat keretében nem módosítja egy vezénylési állapot "kész" mindaddig, amíg az összes függőben lévő feladatokról befejeződött vagy megszakítva.

Ez a mechanizmus ténylegesen nem szünteti meg a folyamatban lévő tevékenység függvény-végrehajtási. Inkább egyszerűen lehetővé teszi a az orchestrator-függvény, amely figyelmen kívül hagyja az eredményt, és lépjen tovább. A függvényalkalmazást a Használatalapú csomag használja, ha meg fog díjat idő-és a elhagyott tevékenység függvény által felhasznált memória. Alapértelmezés szerint a függvények a Használatalapú csomagban fut, a időkorlát van meghatározva öt perc alatt. Ha meghaladja a korlátot, az Azure Functions házigazdája újrahasznosított állítsa le az összes végrehajtás, és a egy elszabadult számlázási helyzet elkerülése. A [függvény időtúllépési érték konfigurálható](../functions-host-json.md#functiontimeout).

Időtúllépések megvalósítása az orchestrator funkciók részletesebb példát, tekintse meg a [emberi beavatkozás i & dőkorlátja - telefonos ellenőrzés](durable-functions-phone-verification.md) forgatókönyv.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan előléptetése és kezeli a külső események](durable-functions-external-events.md)
