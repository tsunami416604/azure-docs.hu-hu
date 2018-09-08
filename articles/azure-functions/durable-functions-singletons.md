---
title: Durable Functions – Azure singletons
description: Hogyan singletons tartós Functons kiterjesztése az Azure Functions szolgáltatáshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 8177fb6e8dea83ab2b8b12183cdcca6e43f92ade
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095096"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Egyszeres vezénylők a tartós függvények (az Azure Functions)

A háttérben futó feladatok vagy az aktor stílusú vezénylések, hogy milyen gyakran kell győződjön meg arról, hogy csak egy példánya egy adott vezénylőt egy időpontban lefut. Ezt megteheti [Durable Functions](durable-functions-overview.md) rendel egy adott példány azonosítója egy orchestrator létrehozásakor.

## <a name="singleton-example"></a>Egyedülálló – példa

Az alábbi C#-példa egy HTTP-eseményindító függvény, amely létrehoz egy egypéldányos háttérben futó feladatok vezénylésével jeleníti meg. A kód biztosítja, hogy csak egy példány létezik a megadott példány azonosítóját.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Alapértelmezés szerint példány azonosítói véletlenszerűen létrehozott GUID. Azonban ebben az esetben a példány azonosítója az URL-címből átadott útvonal adatai. A kód meghívja [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) ellenőrizze, hogy a megadott Azonosítóval rendelkező példány már fut-e. Ha nem, akkor egy példány jön létre, hogy az azonosítóval.

> [!NOTE]
> Ez a példa egy lehetséges versenyhelyzet szerepel. Ha két példánya **HttpStartSingle** hajtható végre párhuzamosan, az eredmény két különböző létrehozható az egyedülálló, gyakorlatilag felülírja a másik egy példányát. A követelményeitől függően ez lehet a nem kívánt mellékhatásokkal. Ezért fontos győződjön meg arról, hogy nincsenek két kérelmek egyidejűleg futtathatja az eseményindító függvény.

Az orchestrator-függvény a gyakorlati kivitelezés részleteinek ténylegesen nem számít. Lehet, hogy az orchestrator szokásos függvény, amely indulásakor és befejezésekor, vagy lehet egy örökre futtató (azaz egy [külső Vezénylési](durable-functions-eternal-orchestrations.md)). A fontos pontot, hogy minden eddiginél csak egy példány egy időben futnak.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan hívhat meg alárendelt vezénylések](durable-functions-sub-orchestrations.md)
