---
title: Hibák Durable Functions-Azure-ban való kezelésére
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functions Durable Functions bővítményében.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 5a3cfb78fe97b52abb1406dff64132fc1b3fb985
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933431"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hibák feldolgozása a Durable Functionsban (Azure Functions)

A tartós függvények összehangolása programkódban valósul meg, és a programozási nyelv beépített hibák kezelésére szolgáló funkcióit használhatja. Valójában nincs olyan új fogalma, amelyet meg kell tanulnia a hibák kezelésére és a kompenzációba való felvételre. Vannak azonban olyan viselkedések, amelyeket érdemes figyelembe vennie.

## <a name="errors-in-activity-functions"></a>Hibák a Activity functions szolgáltatásban

A tevékenységi függvényekben felmerülő kivételeket a rendszer visszaküldi a Orchestrator függvénynek `FunctionFailedException`. A Orchestrator függvényben az igényeinek megfelelő hibakezelés és kompenzációs kód is írható.

Vegyük például a következő Orchestrator függvényt, amely az egyik fiókból a másikba továbbítja a forrásokat:

### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>C#Parancsfájl

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

Ha az első **CreditAccount** -függvény hívása sikertelen, a Orchestrator függvény kompenzálja a források visszaküldését a forrás fiókba.

## <a name="automatic-retry-on-failure"></a>Sikertelen automatikus újrapróbálkozás

A Activity functions vagy a beosztási függvények meghívásakor megadhat egy automatikus újrapróbálkozási házirendet. A következő példa legfeljebb háromszor próbálkozik egy függvény hívásával, és minden újrapróbálkozás után 5 másodpercet vár:

### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C#Parancsfájl

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

A `CallActivityWithRetryAsync` (.net) vagy `callActivityWithRetry` (JavaScript) API egy `RetryOptions` paramétert használ. A (.net) vagy `CallSubOrchestratorWithRetryAsync` `callSubOrchestratorWithRetry` (JavaScript) API-t használó alfolyamati hívások ugyanezeket az újrapróbálkozási szabályzatokat használhatják.

Az automatikus újrapróbálkozási házirend testreszabására több lehetőség is van:

* **Kísérletek maximális száma**: Az újrapróbálkozási kísérletek maximális száma.
* **Első újrapróbálkozás időköze**: Az első újrapróbálkozási kísérlet előtti várakozási idő.
* **Leállítási együttható**: A leállítási növekedésének mértékét meghatározó együttható. Az alapértelmezett érték 1.
* **Maximális újrapróbálkozási időköz**: Az újrapróbálkozási kísérletek között elvárt maximális időtartam.
* **Újrapróbálkozás időkorlátja**: Az újrapróbálkozások elvégzéséhez szükséges maximális időtartam. Az alapértelmezett viselkedés az, ha határozatlan ideig próbálkozik.
* **Leíró**: Felhasználó által definiált visszahívás megadható annak megállapításához, hogy egy függvényt újra kell-e próbálni.

## <a name="function-timeouts"></a>Függvények időtúllépései

Előfordulhat, hogy egy Orchestrator függvény hívását is el szeretné hagyni, ha túl sokáig tart a Befejezés. Ez a megfelelő módszer a (z) (.net) vagy a ( `context.CreateTimer` JavaScript) együttes `context.df.Task.any` `Task.WhenAny` használatával `context.df.createTimer` történő [tartós időzítő](durable-functions-timers.md) létrehozásával a következő példában látható módon:

### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>C#Parancsfájl

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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
> Ez a mechanizmus valójában nem szakítja meg a folyamatban lévő tevékenységek működésének végrehajtását. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. További információ: [időzítők](durable-functions-timers.md#usage-for-timeout) dokumentációja.

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Ha egy Orchestrator függvény nem kezelt kivétel miatt meghiúsul, a rendszer naplózza a kivétel részleteit, és a példány `Failed` állapota állapottal fejeződik be.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg az örök összeszereléseket](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan diagnosztizálhatja a problémákat](durable-functions-diagnostics.md)
