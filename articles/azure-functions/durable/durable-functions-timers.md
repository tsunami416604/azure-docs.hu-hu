---
title: Időzítők a Durable Functionsban – Azure
description: Megtudhatja, hogyan implementálhatja a tartós időzítőket a Azure Functions Durable Functions-bővítményében.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: a24d6e96df3abf385b0a64ec4bc7e1f1c248998b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614637"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Időzítők Durable Functions (Azure Functions)

[](durable-functions-overview.md) A Durable functions *tartós időzítőket* biztosít a Orchestrator függvényekben a késések megvalósításához, illetve az aszinkron műveletek időtúllépésének beállításához. `Thread.Sleep` és `Task.Delay` (C#), vagy `setTimeout()` és `setInterval()` (JavaScript) helyett tartós időzítőket kell használni a Orchestrator függvényekben.

A tartós időzítőt úgy hozhatja létre, hogy meghívja a `CreateTimer` (.NET) metódust vagy a koordinációs [trigger kötésének](durable-functions-bindings.md#orchestration-trigger)`createTimer` (JavaScript) metódusát. A metódus egy olyan feladatot ad vissza, amely egy adott dátumon és időpontban fejeződik be.

## <a name="timer-limitations"></a>Időzítő korlátozásai

Amikor a 4:30 órakor lejáró időzítőt hoz létre, az alapul szolgáló tartós feladati keretrendszer enqueues egy olyan üzenetet, amely csak a 4:30 órakor lesz látható. Ha a Azure Functions használati tervben fut, akkor az újonnan látható időzítő üzenet biztosítja, hogy a Function alkalmazás aktiválva legyen egy megfelelő virtuális gépen.

> [!NOTE]
> * A tartós időzítők jelenleg legfeljebb 7 napig tartanak. Ha hosszabb késésre van szükség, akkor szimulálható az időzítő API-k használatával `while` hurokban.
> * Mindig a `CurrentUtcDateTime` használja a .NET-ben vagy a `Date.now` `currentUtcDateTime`-ben való `DateTime.UtcNow` helyett a .NET-ben vagy a `Date.UTC`-ban, ha a tartós időzítők esetében a tűzzel kapcsolatos időt a számítási További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

## <a name="usage-for-delay"></a>Használat késleltetéshez

Az alábbi példa azt szemlélteti, hogyan használható a tartós időzítő a végrehajtás késleltetéséhez. A példa minden nap 10 napra szóló számlázási értesítést állít ki.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Az előző C# példában Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

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
> Kerülje a végtelen hurkokat a Orchestrator functions szolgáltatásban. A végtelen hurkos forgatókönyvek biztonságos és hatékony megvalósításával kapcsolatos további információkért lásd: [örök összeszerelések](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Használat időtúllépés esetén

Ez a példa azt szemlélteti, hogyan használhatók a tartós időzítők az időtúllépések megvalósításához.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Az előző C# példában Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

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
> Használjon egy `CancellationTokenSource` egy tartós időzítő (.NET) törléséhez, vagy hívja meg `cancel()` a visszaadott `TimerTask` (JavaScript), ha a kód nem várja meg a betöltését. A tartós feladatok keretrendszere nem változtatja meg a "befejezett" állapotot, amíg az összes fennmaradó feladat be nem fejeződik vagy meg nem szakítva.

Ez a lemondási mechanizmus nem szakítja meg a folyamatban lévő tevékenység vagy az alhálózatok végrehajtásának folyamatát. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. Ha a Function alkalmazás a használati tervet használja, akkor az elhagyott tevékenység által felhasznált időt és memóriát továbbra is számlázza. Alapértelmezés szerint a használati tervben futó függvények öt percen belül időtúllépéssel rendelkeznek. Ha túllépi ezt a korlátot, a rendszer újrahasznosítja a Azure Functions gazdagépet az összes végrehajtás leállításához és a elszabadult számlázási helyzet elkerüléséhez. A [függvény időtúllépése konfigurálható](../functions-host-json.md#functiontimeout).

A Orchestrator functions-időtúllépések megvalósításának alaposabb példáját az [emberi interakció & időtúllépések – telefonos ellenőrzés](durable-functions-phone-verification.md) című cikkben találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a külső események növeléséről és kezeléséről](durable-functions-external-events.md)
