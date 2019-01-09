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
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3eaa5de1b1378ba78a7c57172fd0a155f72cd6c5
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102516"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Egyszeres vezénylők a tartós függvények (az Azure Functions)

Gyakran biztosítania kell a háttérben futó feladatok egy adott vezénylőt, hogy csak egy példányban futtatható egyszerre. Ezt megteheti [Durable Functions](durable-functions-overview.md) rendel egy adott példány azonosítója egy orchestrator létrehozásakor.

## <a name="singleton-example"></a>Egyedülálló – példa

A következő C# és JavaScript-példák azt szemléltetik, egy HTTP-eseményindító függvény, amely létrehoz egy egypéldányos háttérben futó feladatok vezénylésével. A kód biztosítja, hogy csak egy példány létezik a megadott példány azonosítóját.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionsName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Alapértelmezés szerint példány azonosítói véletlenszerűen létrehozott GUID. Azonban ebben az esetben a példány azonosítója az URL-címből átadott útvonal adatai. A kód meghívja [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) vagy `getStatus` (JavaScript) ellenőrizze, hogy a megadott Azonosítóval rendelkező példány már fut-e. Ha nem, akkor egy példány jön létre, hogy az azonosítóval.

> [!WARNING]
> A JavaScript fejlesztésének helyileg, kell beállítania a környezeti változót `WEBSITE_HOSTNAME` való `localhost:<port>`, például. `localhost:7071` a módszer használatához `DurableOrchestrationClient`. Ezzel a követelménnyel kapcsolatban további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> Ez a példa egy lehetséges versenyhelyzet szerepel. Ha két példánya **HttpStartSingle** egyidejűleg hajtsa végre, mindkét függvényhívások jártak, de csak egy vezénylési példány ténylegesen indul el. A követelményeitől függően ez lehet a nem kívánt mellékhatásokkal. Ezért fontos győződjön meg arról, hogy nincsenek két kérelmek egyidejűleg futtathatja az eseményindító függvény.

Az orchestrator-függvény a gyakorlati kivitelezés részleteinek ténylegesen nem számít. Lehet, hogy az orchestrator szokásos függvény, amely indulásakor és befejezésekor, vagy lehet egy örökre futtató (azaz egy [külső Vezénylési](durable-functions-eternal-orchestrations.md)). A fontos pontot, hogy minden eddiginél csak egy példány egy időben futnak.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan hívhat meg alárendelt vezénylések](durable-functions-sub-orchestrations.md)
