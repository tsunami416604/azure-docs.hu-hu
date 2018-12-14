---
title: Durable Functions – az Azure a hibák kezelése
description: Ismerje meg, hogy az a Durable Functions bővítmény hibáinak kezelése az Azure Functions szolgáltatáshoz.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7a55e28f34f36cd02b67e56c6262b9e1f06dde8f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338192"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) a hibák kezelése

Tartós függvény vezénylések kódban vannak megvalósítva, és használhatja a hibakezelési képességekkel programozási nyelv. Ezt szem valójában nincs ismerje meg hiba- és kártalanítási beépítése a vezénylések kell minden olyan új fogalmakat. Vannak azonban érdemes figyelembe vennie, néhány működés.

## <a name="errors-in-activity-functions"></a>Hibák a tevékenységfüggvényeket

Bármely, amely egy tevékenység függvényben történt kivétel vissza az orchestrator függvény a hívott, és lépett fel, mint egy `FunctionFailedException`. Az állapotkezelés és kártalanítási hibakódot az orchestrator-funkció az igényeinek megfelelő írhat.

Vegyük példaként a következő az orchestrator-függvény, amely alapok továbbítja az egyik fiókból a másikba:

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Ha a hívást a **CreditAccount** függvény nem sikerül, a cél-fiók, az orchestrator függvény kompenzálja ez által a alapok jóváírására térjen vissza a forrás-fiók.

## <a name="automatic-retry-on-failure"></a>Automatikus újrapróbálkozás hiba esetén

Tevékenységfüggvényeket vagy alárendelt vezénylési függvényt hívja, megadhat egy automatikus újrapróbálkozási szabályzat. Az alábbi példa meghívhat egy függvényt, legfeljebb három alkalommal próbál, és az egyes újrapróbálkozások közötti 5 másodpercet vár:

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

A `CallActivityWithRetryAsync` (.NET) vagy `callActivityWithRetry` (JavaScript) API-t vesz igénybe egy `RetryOptions` paraméter. Suborchestration meghívja a használatával a `CallSubOrchestratorWithRetryAsync` (.NET) vagy `callSubOrchestratorWithRetry` (JavaScript) API-t ezek azonos újrapróbálkozási szabályzatokat használhatja.

Többféle módon is automatikus újrapróbálkozási szabályzat testreszabása. Ezek a következők:

* **Maximális számú kísérlet**: Az újrapróbálkozási kísérletek maximális számát.
* **Első újrapróbálkozás**: Mennyi ideig kell várni az első újrapróbálkozási kísérlet előtt.
* **Leállítási együttható**: A hányados leállítási üteme határozza meg. Alapértelmezett értéke 1.
* **Maximális újrapróbálkozási időköz**: A köztes várakozási idő maximális mennyisége újrapróbálkozások száma.
* **Ismételje meg a timeout**: A legnagyobb ezzel időnk újrapróbálkozik. Az alapértelmezett viselkedést, hogy határozatlan ideig próbálja újra.
* **Kezelni**: Egy felhasználó által meghatározott visszahívást adható meg, amely meghatározza, hogy e egy adott hívás meg kell ismételni.

## <a name="function-timeouts"></a>Függvény időtúllépések

Előfordulhat, hogy szeretné abandon egy függvény hívásához szükséges egy orchestrator függvényen belül, ha túl sokáig tart. A megfelelő módszer ehhez még ma, hozzon létre egy [tartós időzítő](durable-functions-timers.md) használatával `context.CreateTimer` (.NET) vagy `context.df.createTimer` (JavaScript) együtt `Task.WhenAny` (.NET) vagy `context.df.Task.any` (JavaScript), az alábbi példában látható módon:

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Ez a mechanizmus ténylegesen nem szünteti meg a folyamatban lévő tevékenység függvény-végrehajtási. Inkább egyszerűen lehetővé teszi a az orchestrator-függvény, amely figyelmen kívül hagyja az eredményt, és lépjen tovább. További információkért lásd: a [időzítők](durable-functions-timers.md#usage-for-timeout) dokumentációját.

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Az orchestrator függvény egy nem kezelt kivétel miatt nem sikerül, ha a részleteket a kivétel jelentkezett, és a példány befejeződik, az egy `Failed` állapotát.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan diagnosztizálhatja a problémákat](durable-functions-diagnostics.md)
