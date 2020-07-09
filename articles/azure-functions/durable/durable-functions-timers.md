---
title: Időzítők a Durable Functionsban – Azure
description: Megtudhatja, hogyan implementálhatja a tartós időzítőket a Azure Functions Durable Functions-bővítményében.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76261483"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Időzítők Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) A Durable functions *tartós időzítőket* biztosít a Orchestrator függvényekben a késések megvalósításához, illetve az aszinkron műveletek időtúllépésének beállításához. Tartós időzítőket kell használni a `Thread.Sleep` `Task.Delay` (C#) és a `setTimeout()` `setInterval()` (JavaScript) helyett a Orchestrator függvényekben.

Egy tartós időzítőt úgy hozhat létre, hogy meghívja a `CreateTimer` (.net) metódust vagy a `createTimer` (JavaScript) metódust a koordinációs [trigger kötéséhez](durable-functions-bindings.md#orchestration-trigger). A metódus egy olyan feladatot ad vissza, amely egy adott dátumon és időpontban fejeződik be.

## <a name="timer-limitations"></a>Időzítő korlátozásai

Amikor a 4:30 órakor lejáró időzítőt hoz létre, az alapul szolgáló tartós feladati keretrendszer enqueues egy olyan üzenetet, amely csak a 4:30 órakor lesz látható. Ha a Azure Functions használati tervben fut, akkor az újonnan látható időzítő üzenet biztosítja, hogy a Function alkalmazás aktiválva legyen egy megfelelő virtuális gépen.

> [!NOTE]
> * A tartós időzítők jelenleg legfeljebb 7 napig tartanak. Ha hosszabb késésre van szükség, akkor szimulálható az időzítő API-k használatával egy `while` hurokban.
> * Mindig a `CurrentUtcDateTime` .net- `DateTime.UtcNow` ben vagy a `currentUtcDateTime` `Date.now` `Date.UTC` -ban vagy a-ben, illetve a-ban vagy a-ben, a tartós időzítők esetében pedig a tűz idejére További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

## <a name="usage-for-delay"></a>Használat késleltetéshez

Az alábbi példa azt szemlélteti, hogyan használható a tartós időzítő a végrehajtás késleltetéséhez. A példa minden nap 10 napra szóló számlázási értesítést állít ki.

# <a name="c"></a>[C#](#tab/csharp)

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
> Az előző C# példa Durable Functions 2. x-et céloz meg. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Kerülje a végtelen hurkokat a Orchestrator functions szolgáltatásban. A végtelen hurkos forgatókönyvek biztonságos és hatékony megvalósításával kapcsolatos további információkért lásd: [örök összeszerelések](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Használat időtúllépés esetén

Ez a példa azt szemlélteti, hogyan használhatók a tartós időzítők az időtúllépések megvalósításához.

# <a name="c"></a>[C#](#tab/csharp)

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
> Az előző C# példa Durable Functions 2. x-et céloz meg. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

> [!WARNING]
> A (z) `CancellationTokenSource` (.net) vagy `cancel()` a visszaadott hívás `TimerTask` (JavaScript) használatával szakítsa meg a tartós időzítőt, ha a kód nem várja meg a betöltését. A tartós feladatok keretrendszere nem változtatja meg a "befejezett" állapotot, amíg az összes fennmaradó feladat be nem fejeződik vagy meg nem szakítva.

Ez a lemondási mechanizmus nem szakítja meg a folyamatban lévő tevékenység vagy az alhálózatok végrehajtásának folyamatát. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. Ha a Function alkalmazás a használati tervet használja, akkor az elhagyott tevékenység által felhasznált időt és memóriát továbbra is számlázza. Alapértelmezés szerint a használati tervben futó függvények öt percen belül időtúllépéssel rendelkeznek. Ha túllépi ezt a korlátot, a rendszer újrahasznosítja a Azure Functions gazdagépet az összes végrehajtás leállításához és a elszabadult számlázási helyzet elkerüléséhez. A [függvény időtúllépése konfigurálható](../functions-host-json.md#functiontimeout).

A Orchestrator functions-időtúllépések megvalósításának alaposabb példáját az [emberi interakció & időtúllépések – telefonos ellenőrzés](durable-functions-phone-verification.md) című cikkben találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a külső események növeléséről és kezeléséről](durable-functions-external-events.md)
