---
title: "Hibák a tartós funkciók - Azure kezelése"
description: "További tudnivalók az Azure Functions a tartós funkciók bővítményben hibák kezelésének módját."
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
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hibák a tartós funkciók (az Azure Functions) kezelése

Tartós függvény álló üzenettípusok összehangolását kódba, és a hiba-kezelési lehetőségei a programozási nyelv. Ennek tudatában hogy valóban nincs bármely új elveket, megismerni hiba- és kompenzációs beépítése a álló üzenettípusok összehangolását. Van azonban néhány olyan viselkedéseket, amelyek akkor érdemes figyelembe.

## <a name="errors-in-activity-functions"></a>Hibák a tevékenység-funkciók

Bármely, amely egy tevékenység függvényben történt kivétel vissza az orchestrator függvény a hívott és vált ki, mint egy `TaskFailedException`. Kezelési és a hibakódot az orchestrator függvényben igényeinek megfelelő írhat.

Vegyük példaként a következő orchestrator függvény alapok átviszi az egyik fiókból másikba:

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

Ha a hívást a **CreditAccount** függvény a célként megadott fiók nem sikerül, akkor az orchestrator függvény kiegyenlíti ez által a alapok jóváírására vissza a forrás-fiókjának.

## <a name="automatic-retry-on-failure"></a>Automatikus újrapróbálkozási hiba esetén

Ha a tevékenység függvényeinek meghívása, vagy a alárendelt vezénylési funkciók is megadhat az automatikus újrapróbálkozási házirendet. Az alábbi példa megpróbálja meghívni a függvényt legfeljebb 3-szor, és megvárja-e 5 másodperc közötti minden újra:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

A `CallActivityWithRetryAsync` API vesz egy `RetryOptions` paraméter. Alárendelt vezénylési meghívja a használatával a `CallSubOrchestratorWithRetryAsync` API ezek azonos újrapróbálkozási házirendek is használhatók.

Az automatikus újrapróbálkozási házirendje testreszabásához több lehetőség áll rendelkezésre. Ezek a következők:

* **A kísérletek maximális száma**: az újrapróbálkozások maximális számát.
* **Első újrapróbálkozás**: mennyi ideig várjon, mielőtt az első újrapróbálkozásnál.
* **Leállítási együttható**: határozza meg a leállítási üteme együttható. Az alapértelmezett érték 1.
* **Maximális újrapróbálkozási időköz**: az újrapróbálkozások között a várakozási idő maximális mennyisége.
* **Ismételje meg a timeout**: A maximális időt ezzel egy újrapróbálkozik. Az alapértelmezés lesz határozatlan ideig próbálja meg újra.
* **Egyéni**: egy felhasználói visszahívás amely meghatározza, hogy-e egy adott hívás meg kell ismételni adható meg.

## <a name="function-timeouts"></a>Függvény időtúllépések

Előfordulhat, hogy szeretne az orchestrator függvényen belül függvényhívás abandon, ha túl sokáig tart. A megfelelő Ez ma módja hozzon létre egy [tartós időzítő](durable-functions-timers.md) használatával `context.CreateTimer` együtt `Task.WhenAny`, az alábbi példa:

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

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Ha egy orchestrator-függvény sikertelen, és nem kezelt kivételt, a kivétel részletei naplózza, és a példány befejeződik, az egy `Failed` állapotát.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató a problémák diagnosztizálásához](durable-functions-diagnostics.md)
