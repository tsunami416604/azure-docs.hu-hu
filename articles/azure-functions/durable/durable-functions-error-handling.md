---
title: Hibák Durable Functions-Azure-ban való kezelésére
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277855"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hibák feldolgozása a Durable Functionsban (Azure Functions)

A tartós függvények összehangolása programkódban valósul meg, és a programozási nyelv beépített hibák kezelésére szolgáló funkcióit használhatja. Valójában nincs olyan új fogalma, amelyet meg kell tanulnia a hibák kezelésére és a kompenzációba való felvételre. Vannak azonban olyan viselkedések, amelyeket érdemes figyelembe vennie.

## <a name="errors-in-activity-functions"></a>Hibák a Activity functions szolgáltatásban

A tevékenységi függvényekben felmerülő kivételeket a rendszer visszaküldi a Orchestrator függvénynek `FunctionFailedException`. A Orchestrator függvényben az igényeinek megfelelő hibakezelés és kompenzációs kód is írható.

Vegyük például a következő Orchestrator függvényt, amely az egyik fiókból a másikba továbbítja a forrásokat:

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
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a `DurableOrchestrationContext` `IDurableOrchestrationContext`értéket kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Ha az első **CreditAccount** -függvény hívása sikertelen, a Orchestrator függvény kompenzálja a források visszaküldését a forrás fiókba.

## <a name="automatic-retry-on-failure"></a>Sikertelen automatikus újrapróbálkozás

A Activity functions vagy a beosztási függvények meghívásakor megadhat egy automatikus újrapróbálkozási házirendet. A következő példa legfeljebb háromszor próbálkozik egy függvény hívásával, és minden újrapróbálkozás után 5 másodpercet vár:

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
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a `DurableOrchestrationContext` `IDurableOrchestrationContext`értéket kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Az előző példában szereplő tevékenység függvény hívása egy paramétert fogad el az automatikus újrapróbálkozási házirend konfigurálásához. Az automatikus újrapróbálkozási házirend testreszabására több lehetőség is van:

* **Kísérletek maximális száma**: az újrapróbálkozási kísérletek maximális száma.
* **Első újrapróbálkozás időköze**: az első újrapróbálkozási kísérlet előtti várakozási idő.
* **Leállítási együttható**: a leállítási növelésének mértékét meghatározó együttható. Az alapértelmezett érték 1.
* **Maximális újrapróbálkozási időköz**: az újrapróbálkozási kísérletek között elvárt maximális időtartam.
* **Újrapróbálkozás időtúllépése**: az újrapróbálkozások maximálisan elkölthető ideje. Az alapértelmezett viselkedés az, ha határozatlan ideig próbálkozik.
* **Leíró**: a felhasználó által definiált visszahívás megadható annak megállapításához, hogy egy függvényt újra kell-e próbálni.

## <a name="function-timeouts"></a>Függvények időtúllépései

Előfordulhat, hogy egy Orchestrator függvény hívását is el szeretné hagyni, ha túl sokáig tart a Befejezés. Ez a megfelelő módszer a (z) (.net) vagy a ( `context.CreateTimer` JavaScript `context.df.createTimer` ) `Task.WhenAny` együttes `context.df.Task.any` használatával történő [tartós időzítő](durable-functions-timers.md) létrehozásával a következő példában látható módon:

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
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a `DurableOrchestrationContext` `IDurableOrchestrationContext`értéket kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
> Ez a mechanizmus valójában nem szakítja meg a folyamatban lévő tevékenységek működésének végrehajtását. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. További információ: [időzítők](durable-functions-timers.md#usage-for-timeout) dokumentációja.

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Ha egy Orchestrator függvény nem kezelt kivétel miatt meghiúsul, a rendszer naplózza a kivétel részleteit, és a példány `Failed` állapota állapottal fejeződik be.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg az örök összeszereléseket](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan diagnosztizálhatja a problémákat](durable-functions-diagnostics.md)
