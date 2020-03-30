---
title: Hibák kezelése a tartós funkciókban - Azure
description: Ismerje meg, hogyan kezelje a hibákat az Azure Functions tartós függvények bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277855"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>A tartós függvények (Azure Functions) hibáinak kezelése

A tartós függvény vezénylései kódban vannak megvalósítva, és használhatják a programozási nyelv beépített hibakezelési funkcióit. Tényleg nincs enek olyan új fogalmak, amelyeket meg kell tanulnia hibakezelés és kompenzáció hozzáadásához a vezénylésekhez. Van azonban néhány viselkedés, amit tudnia kell.

## <a name="errors-in-activity-functions"></a>Tevékenységfüggvények hibái

A tevékenységfüggvényekben megjelenő kivételeket a függvény visszairányítja az orchestrator függvénybe, és a. `FunctionFailedException` Írhat hibakezelési és kompenzációs kódot, amely megfelel az igényeinek az orchestrator függvényben.

Vegyük például a következő orchestrator függvényt, amely pénzeszközöket utal át egyik számláról a másikra:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Ha az első **CreditAccount** függvényhívás sikertelen lesz, az orchestrator függvény kompenzálja az alapok jóváírásával a forrásszámlára.

## <a name="automatic-retry-on-failure"></a>Hiba automatikus újrapróbálkozása

Tevékenységfüggvények vagy alvezőfunkciók hívásakor megadhat egy automatikus újrapróbálkozási házirendet. A következő példa legfeljebb háromszor próbál meg meghívni egy függvényt, és 5 másodpercet vár az egyes újrapróbálkozások között:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

Az előző példában szereplő tevékenységfüggvény-hívás egy paramétert vesz igénybe az automatikus újrapróbálkozási házirend konfigurálásához. Az automatikus újrapróbálkozási házirend testreszabására több lehetőség is kínálkodhat:

* **A kísérletek maximális száma**: Az újrapróbálkozások maximális száma.
* **Első újrapróbálkozási időköz**: Az első újrapróbálkozási kísérlet előtti várakozási idő.
* **Visszamaradási együttható:** A visszamaradás növekedésének mértékének meghatározására használt együttható. Alapértelmezés szerint 1.
* **Maximális újrapróbálkozási időköz**: Az újrapróbálkozási kísérletek közötti várakozási idő maximális ideje.
* **Újrapróbálkozási időtúltöltés:** Az újrapróbálkozások maximális eltöltési ideje. Az alapértelmezett viselkedés az, hogy határozatlan ideig újra próbálkozik.
* **Leíró**: Megadható a felhasználó által definiált visszahívás annak meghatározásához, hogy meg kell-e próbálni egy függvényt.

## <a name="function-timeouts"></a>Függvény időmegtat/időpontjai

Előfordulhat, hogy el szeretne hagyni egy függvényhívást egy orchestrator-függvényen belül, ha túl sokáig tart a befejezés. Ennek megfelelő módja ma egy [tartós](durable-functions-timers.md) időzítő `context.CreateTimer` létrehozása a `context.df.createTimer` (.NET) vagy `Task.WhenAny` a (JavaScript) használatával a (.NET) vagy `context.df.Task.any` a (JavaScript) használatával, mint a következő példában:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!NOTE]
> Ez a mechanizmus valójában nem állítja le a folyamatban lévő tevékenység függvény végrehajtását. Inkább egyszerűen lehetővé teszi, hogy az orchestrator függvény figyelmen kívül hagyja az eredményt, és lépni. További információt az [Időzítők](durable-functions-timers.md#usage-for-timeout) dokumentációjában talál.

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Ha egy orchestrator függvény sikertelen egy nem kezelt kivétellel, a kivétel részleteit `Failed` naplózza, és a példány állapottal fejeződik be.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az örök vezénylésekről](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [További információ a problémák diagnosztizálásáról](durable-functions-diagnostics.md)
