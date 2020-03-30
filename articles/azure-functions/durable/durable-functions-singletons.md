---
title: Singletons tartós funkciókhoz - Azure
description: Singletons használata az Azure Functions durable functions bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262809"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton orchestrators tartós függvényekben (Azure Functions)

A háttérfeladatok hoz, gyakran kell biztosítania, hogy csak egy példánya egy adott orchestrator fut egy időben. Biztosíthatja az ilyen típusú singleton viselkedés [tartós függvények](durable-functions-overview.md) hozzárendelésével egy adott példányazonosítót egy orchestrator létrehozásakor.

## <a name="singleton-example"></a>Singleton példa

A következő példa egy HTTP-trigger függvényt mutat be, amely egy singleton háttérfeladat vezénylési. A kód biztosítja, hogy csak egy példány létezik egy adott példányazonosítóhoz.

# <a name="c"></a>[C #](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

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

---

Alapértelmezés szerint a példányazonosítók véletlenszerűen generált GUID azonosítók. Az előző példában azonban a példányazonosító átkerül az URL-címről származó útvonaladatokban. A kód `GetStatusAsync`meghívja (C#) vagy `getStatus` (JavaScript) annak ellenőrzésére, hogy a megadott azonosítóval rendelkező példány már fut-e. Ha ilyen példány nem fut, egy új példány jön létre ezzel az azonosítóval.

> [!NOTE]
> Van egy lehetséges faji helyzet ebben a mintában. Ha a **HttpStartSingle** két példánya egyidejűleg hajt végre, mindkét függvényhívás sikeresnek fog jelenteni, de csak egy vezénylési példány indul el. Attól függően, hogy a követelmények, Ez lehet nemkívánatos mellékhatások. Ezért fontos annak biztosítása, hogy egyetlen két kérelem ne hajthassa végre egyidejűleg ezt az eseményindító függvényt.

Az orchestrator függvény megvalósítási részletei valójában nem számítanak. Ez lehet egy rendszeres orchestrator funkció, amely elindul és befejeződik, vagy lehet az egyik, hogy fut örökre (azaz egy [örök vezénylés](durable-functions-eternal-orchestrations.md)). A lényeg az, hogy egyszerre csak egy példány fut.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a vezénylések natív HTTP-funkcióiról](durable-functions-http-features.md)
