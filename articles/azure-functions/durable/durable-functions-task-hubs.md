---
title: Durable Functions – az Azure a feladatközpontok
description: Ismerje meg, milyen feladatot a központ egy az Azure Functions Durable Functions-bővítményben. Ismerje meg, hogyan konfigurálhatja feladatközpontok konfigurálása.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 4e48956e42942761abec0143ba2849601dbb1cf4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336900"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Feladatközpontok a tartós függvények (az Azure Functions)

A *feladat hub* a [Durable Functions](durable-functions-overview.md) vezénylések használt Azure Storage-erőforrások logikai tárolója. Orchestrator, illetve a tevékenység funkciók tartoznak, a tevékenység egy központban csak is kapcsolatba egymással.

Minden függvényalkalmazáshoz egy külön feladat központ rendelkezik. Ha több függvényalkalmazás egy tárfiókot, a storage-fiókot a több feladatközpontok tartalmazza. A következő ábra szemlélteti egy feladat hub függvényalkalmazást a közös vagy dedikált storage-fiókok száma.

![Bemutató ábra. Ez a megosztott, és a storage-fiókok dedikált.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Az Azure Storage-erőforrások

Egy feladat hub a következő tároló-erőforrások áll:

* Egy vagy több vezérlő üzenetsorok.
* Egy munkaelem várólistát.
* Egy előzménytábla.
* Példányok egy olyan táblát.
* Egy storage-tárolót tartalmazó egy vagy több bérletet blobot.

Összes ilyen erőforrásról jönnek létre automatikusan az alapértelmezett Azure Storage-fiókban az orchestrator vagy tevékenység függvények futtatása vagy ütemezésére. A [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md) a cikk azt ismerteti, hogyan használhatók a ezeket az erőforrásokat.

## <a name="task-hub-names"></a>A feladat értesítésiközpont-nevek

Feladatközpontok van deklarálva a név azonosítja a *host.json* fájljához a következő példában látható módon:

### <a name="hostjson-functions-1x"></a>Host.JSON (1.x függvények)

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host.JSON (2.x függvények)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "MyTaskHub"
    }
  }
}
```

Feladatközpontok is konfigurálhatja az alkalmazás beállításokkal, ahogyan az az alábbi *host.json* példa fájlt:

### <a name="hostjson-functions-1x"></a>Host.JSON (1.x függvények)

```json
{
  "durableTask": {
    "HubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host.JSON (2.x függvények)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "%MyTaskHub%"
    }
  }
}
```

A feladat hub name értékét állítja be a `MyTaskHub` alkalmazásbeállítást. A következő `local.settings.json` bemutatja, hogyan adhat meg a `MyTaskHub` fejléccé `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Íme egy előre lefordított C# példa bemutatja, hogyan írhat egy függvényt, amely használja egy [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) működjön, az alkalmazás-beállítás konfigurálva van a feladat hubbal:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

És alább a JavaScript szükséges konfigurációval. A feladat hub tulajdonságot a `function.json` fájl keresztül Alkalmazásbeállítás értéke:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

A feladat értesítésiközpont-nevek kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, az alapértelmezett név az **DurableFunctionsHub**.

> [!NOTE]
> A név egy feladat hub másik kódjába, ha több feladatközpontok megosztott tárfiók található. Ha egy megosztott tárfiókot megosztása több függvényalkalmazás, hogy konfigurálnia minden egyes feladat hub, a különböző neveket a *host.json* fájlokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan kezelje a verziókezelés](durable-functions-versioning.md)
