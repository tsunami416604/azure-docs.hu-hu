---
title: Durable Functions – az Azure a hibák kezelése
description: Ismerje meg, hogy az a Durable Functions bővítmény hibáinak kezelése az Azure Functions szolgáltatáshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: azfuncdf
ms.openlocfilehash: 6bf9eb2cd2ebdf5f6d53e00923146bab49a142bf
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377905"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) a hibák kezelése

Tartós függvény vezénylések kódban vannak megvalósítva, és használhatja a hibakezelési képességekkel programozási nyelv. Ezt szem valójában nincs ismerje meg hiba- és kártalanítási beépítése a vezénylések kell minden olyan új fogalmakat. Vannak azonban érdemes figyelembe vennie, néhány működés.

## <a name="errors-in-activity-functions"></a>Hibák a tevékenységfüggvényeket

Bármely, amely egy tevékenység függvényben történt kivétel vissza az orchestrator függvény a hívott, és lépett fel, mint egy `FunctionFailedException`. Az állapotkezelés és kártalanítási hibakódot az orchestrator-funkció az igényeinek megfelelő írhat.

Vegyük példaként a következő az orchestrator-függvény, amely alapok továbbítja az egyik fiókból a másikba:

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

Ha a hívást a **CreditAccount** függvény nem sikerül, a cél-fiók, az orchestrator függvény kompenzálja ez által a alapok jóváírására térjen vissza a forrás-fiók.

## <a name="automatic-retry-on-failure"></a>Automatikus újrapróbálkozás hiba esetén

Tevékenységfüggvényeket vagy alárendelt vezénylési függvényt hívja, megadhat egy automatikus újrapróbálkozási szabályzat. Az alábbi példa meghívhat egy függvényt, legfeljebb három alkalommal próbál, és az egyes újrapróbálkozások közötti 5 másodpercet vár:

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

A `CallActivityWithRetryAsync` API vesz igénybe egy `RetryOptions` paraméter. Suborchestration meghívja a használatával a `CallSubOrchestratorWithRetryAsync` API ezek azonos újrapróbálkozási szabályzatokat használhatja.

Többféle módon is automatikus újrapróbálkozási szabályzat testreszabása. Ezek a következők:

* **Maximális számú kísérlet**: az újrapróbálkozások maximális számát.
* **Első újrapróbálkozás**: mennyi ideig kell várni, mielőtt az első újrapróbálkozási kísérlet.
* **Leállítási együttható**: A hányados leállítási üteme határozza meg. Alapértelmezett értéke 1.
* **Maximális újrapróbálkozási időköz**: köztes várakozási idő maximális mennyisége újrapróbálkozások száma.
* **Ismételje meg a timeout**: A legnagyobb ezzel időnk újrapróbálkozik. Az alapértelmezett viselkedést, hogy határozatlan ideig próbálja újra.
* **Kezelni**: egy felhasználó által meghatározott visszahívást is adható meg, amely meghatározza, hogy e egy adott hívás meg kell ismételni.

## <a name="function-timeouts"></a>Függvény időtúllépések

Előfordulhat, hogy szeretné abandon egy függvény hívásához szükséges egy orchestrator függvényen belül, ha túl sokáig tart. A megfelelő módszer ehhez még ma, hozzon létre egy [tartós időzítő](durable-functions-timers.md) használatával `context.CreateTimer` együtt `Task.WhenAny`, ahogy az alábbi példában:

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

> [!NOTE]
> Ez a mechanizmus ténylegesen nem szünteti meg a folyamatban lévő tevékenység függvény-végrehajtási. Inkább egyszerűen lehetővé teszi a az orchestrator-függvény, amely figyelmen kívül hagyja az eredményt, és lépjen tovább. További információkért lásd: a [időzítők](durable-functions-timers.md#usage-for-timeout) dokumentációját.

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Az orchestrator függvény egy nem kezelt kivétel miatt nem sikerül, ha a részleteket a kivétel jelentkezett, és a példány befejeződik, az egy `Failed` állapotát.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan diagnosztizálhatja a problémákat](durable-functions-diagnostics.md)
