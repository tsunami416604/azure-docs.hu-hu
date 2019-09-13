---
title: Durable Functions – Azure
description: A Azure Functionshoz Durable Functions-bővítményben lévő előkészítési folyamatokat hívhatja.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: azfuncdf
ms.openlocfilehash: 74f33a1ce1026424a6cdb97699223aeb5ff8277f
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933130"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions)

A Orchestrator függvények más Orchestrator-függvényeket is hívhatnak. Létrehozhat például egy nagyobb, a Orchestrator-függvények könyvtárainak összeszerelését. Vagy egy Orchestrator függvény több példányát is futtathatja párhuzamosan.

Egy Orchestrator függvény meghívhat egy másik Orchestrator-függvényt úgy, hogy meghívja a [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) vagy a [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metódusokat a .net-ben, illetve a `callSubOrchestrator` JavaScriptben található vagy `callSubOrchestratorWithRetry` metódusokat. A [& Compensation](durable-functions-error-handling.md#automatic-retry-on-failure) szolgáltatással kapcsolatos hiba további információkat tartalmaz az automatikus Újrapróbálkozással kapcsolatban.

Az Orchestrator függvények a hívó szemszögéből hasonlóan működnek a tevékenységi funkciókkal. Egy értéket adhatnak vissza, kivételt jeleznek, és a szülő Orchestrator függvénytől is megtekinthetők.

## <a name="example"></a>Példa

Az alábbi példa egy IoT ("eszközök internetes hálózata") forgatókönyvet mutat be, ahol több eszközt kell kiépíteni. Az egyes eszközökhöz szükség van egy adott előkészítésre, amely a következőhöz hasonlóan néz ki:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

Ez a Orchestrator függvény használható az egyszeri eszköz kiosztásához, vagy egy nagyobb előkészítés része lehet. Az utóbbi esetben a szülő Orchestrator függvény `DeviceProvisioningOrchestration` a (C#) vagy `callSubOrchestrator` a `CallSubOrchestratorAsync` (JavaScript) API-t használó példányokat is ütemezhet.

Íme egy példa, amely bemutatja, hogyan futtathat párhuzamosan több Orchestrator-függvényt.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

> [!NOTE]
> Az alrendszereket a szülő-összehangolás során megegyező Function alkalmazásban kell meghatározni. Ha egy másik Function-alkalmazásban kell meghívnia és várnia a meghívást, érdemes lehet használni a HTTP API-k beépített támogatását és a HTTP 202 lekérdezési fogyasztói mintát. További információkért lásd a http- [funkciók](durable-functions-http-features.md) témakört.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan állíthat be egyéni előkészítési állapotot](durable-functions-custom-orchestration-status.md)