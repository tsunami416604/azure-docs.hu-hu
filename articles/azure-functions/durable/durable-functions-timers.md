---
title: Időzítők tartós függvényekben – Azure
description: Ismerje meg, hogyan valósíthat meg tartós időzítőket az Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261483"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Időzítők tartós függvényekben (Azure-függvények)

[A Durable Functions](durable-functions-overview.md) *tartós időzítőket* biztosít az orchestrator-függvényekben való használatra a késések megvalósításához vagy az aszinkron műveletek időmegadásához. Tartós időzítők kell használni orchestrator `Thread.Sleep` függvények helyett `Task.Delay` és `setTimeout()` `setInterval()` (C#), vagy (JavaScript).

Tartós időzítőt úgy hozhat `CreateTimer` létre, hogy meghívja a `createTimer` [vezénylési eseményindító kötés](durable-functions-bindings.md#orchestration-trigger)(.NET) metódusát vagy (JavaScript) metódusát. A metódus egy megadott dátumon és időpontban befejeződő feladatot ad vissza.

## <a name="timer-limitations"></a>Időzítő korlátozások

Amikor 16:30-kor lejár időzítőt hoz létre, az alapul szolgáló tartós feladatkeretrendszer várólistára helyezi az üzenetet, amely csak 16:30-kor válik láthatóvá. Az Azure Functions consumptioni csomagfuttatásakor az újonnan látható időzítő üzenet biztosítja, hogy a függvényalkalmazás aktiválódik a megfelelő virtuális gépen.

> [!NOTE]
> * Tartós időzítő jelenleg csak 7 nap. Ha hosszabb késésekre van szükség, az időzítő API-k `while` használatával szimulálható.
> * Mindig `CurrentUtcDateTime` használja `DateTime.UtcNow` a .NET `currentUtcDateTime` helyett, vagy javascript `Date.now` helyett, `Date.UTC` amikor a tartós időzítők tűzidejét számítja ki. További információkért tekintse meg az [orchestrator függvénykód megkötések](durable-functions-code-constraints.md) cikket.

## <a name="usage-for-delay"></a>Késleltetési használat

A következő példa bemutatja, hogyan használható tartós időzítők a végrehajtás késleltetéséhez. A példa egy számlázási értesítés kiadása minden nap 10 napig.

# <a name="c"></a>[C #](#tab/csharp)

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
> Az előző C# példa a Durable Functions 2.x-et célozza. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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
> Kerülje a végtelen hurkok orchestrator függvények. A végtelen ciklusok biztonságos és hatékony megvalósításáról az [Örök vezénylések](durable-functions-eternal-orchestrations.md)című témakörben talál további információt.

## <a name="usage-for-timeout"></a>Idő-eltakadt

Ez a példa bemutatja, hogyan használható tartós időzítők időmegtorások megvalósításához.

# <a name="c"></a>[C #](#tab/csharp)

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
> Az előző C# példa a Durable Functions 2.x-et célozza. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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
> Használja `CancellationTokenSource` a (.NET) `cancel()` vagy `TimerTask` hívja a visszaadott (JavaScript) a tartós időzítő megszakításához, ha a kód nem vár, amíg befejeződik. A tartós feladatkeretrendszer nem módosítja a vezénylési állapot "befejezett" állapotot, amíg az összes függőben lévő feladat el nem fejeződik, vagy meg nem szakítja.

Ez a törlési mechanizmus nem szünteti meg a folyamatban lévő tevékenység függvény vagy al-vezénylési végrehajtások. Inkább egyszerűen lehetővé teszi, hogy az orchestrator függvény figyelmen kívül hagyja az eredményt, és lépni. Ha a függvényalkalmazás a felhasználási sémát használja, akkor is bármikor számlázunk önnek, és az elhagyott tevékenységfunkció által felhasznált memóriát. Alapértelmezés szerint a Felhasználási tervben futó függvények időtúlszáma öt perc. Ha ezt a korlátot túllépi, az Azure Functions gazdagép újrahasznosítja az összes végrehajtás leállítása és egy elszabadult számlázási helyzet megelőzése érdekében. A [függvény időmeghosszabbítása konfigurálható.](../functions-host-json.md#functiontimeout)

Az időműveletek orchestrator függvényekben való megvalósításának részletesebb példáját az [Emberi interakció & időkiidők – Telefonos ellenőrzés](durable-functions-phone-verification.md) című cikkben olvashat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a külső események előteremtése és kezelése](durable-functions-external-events.md)
