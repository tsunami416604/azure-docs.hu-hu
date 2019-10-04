---
title: Durable Functions – Azure
description: Az Durable Functions bővítmény használata Azure Functionshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: ba35999d5a7193ba691b14005dc8271120ac2be7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933226"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions)

A háttérben futó feladatokhoz gyakran kell gondoskodnia arról, hogy egy adott Orchestrator egyszerre csak egy példány fusson. Ezt [Durable functions](durable-functions-overview.md) teheti meg egy adott példány azonosítójának egy Orchestrator való hozzárendelésével a létrehozásakor.

## <a name="singleton-example"></a>Egyszeres példa

A következő C# és JavaScript példák egy http-trigger függvényt mutatnak be, amely létrehoz egy egypéldányos háttérben végzett előkészítést. A kód biztosítja, hogy csak egy példány létezik egy adott példány-AZONOSÍTÓhoz.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

Íme a function.json fájlban:
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

A következő JavaScript-kódot:
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

Alapértelmezés szerint a példány-azonosítók véletlenszerűen generált GUID azonosítók. Ebben az esetben azonban a rendszer átadja a példány AZONOSÍTÓját az útvonalon lévő adatokból az URL-címről. A kód meghívja aC# [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) ( `getStatus` ) vagy a (JavaScript) metódust annak vizsgálatára, hogy a megadott azonosítójú példány már fut-e. Ha nem, akkor létrejön egy példány ezzel az AZONOSÍTÓval.

> [!NOTE]
> Ebben a példában a verseny feltétele lehetséges. Ha a **HttpStartSingle** két példánya egyidejű végrehajtást hajt végre, mindkét függvényhívás sikeres lesz, de a rendszer csak egy hanghívási példányt fog elindulni. A követelményektől függően előfordulhat, hogy ez nem lenne lehetséges mellékhatása. Ezért fontos annak biztosítása, hogy ne lehessen egyszerre két kérelmet végrehajtani az trigger-függvényt.

A Orchestrator függvény implementációjának részletei valójában nem számítanak. Ez lehet egy normál Orchestrator-függvény, amely megkezdi és befejeződik, vagy az is lehet, hogy örökre fut (azaz egy [örök](durable-functions-eternal-orchestrations.md)kialakítás). A lényeg az, hogy egyszerre csak egy példány fut egyszerre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a rendszerelőkészítések natív HTTP-funkcióit](durable-functions-http-features.md)
