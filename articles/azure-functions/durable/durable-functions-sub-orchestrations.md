---
title: Durable Functions – Azure alárendelt vezénylések
description: Hogyan hívhat meg az Azure Functions a Durable Functions bővítmény a vezénylések vezénylések.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: df4cfd8cdf720dd085c3f14ad518c557f270ffa4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340861"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) az alárendelt vezénylések

Mellett tevékenységfüggvényeket hívja meg, az orchestrator-funkciók meghívhatja az orchestrator-függvényekkel. Ha például hozhat létre egy nagyobb vezénylési orchestrator funkciók kívül egy könyvtár. Vagy egy orchestrator-függvényt több példánya is futtatható egyszerre.

Az orchestrator függvény meghívhat egy másik orchestrator függvény meghívásával a [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) vagy a [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) módszerek a .NET-ben, vagy a `callSubOrchestrator` vagy `callSubOrchestratorWithRetry` módszerek a JavaScript. A [hibakezelés & kompenzációs](durable-functions-error-handling.md#automatic-retry-on-failure) cikk tartalmaz további információkat az automatikus újrapróbálkozáskor.

Alárendelt orchestrator funkciók csak a hívó szempontból tevékenységfüggvényeket viselkednek. Akkor adhat vissza értéket, kivételt, és a szülő orchestrator függvény által is kell várni.

## <a name="example"></a>Példa

Az alábbi példa egy ("IOT-") IoT-forgatókönyvet mutatja be, ha több eszköz, amely szükség lesz. Egy adott vezénylési az szükségességéről a következőhöz hasonló a következő eszközre van:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

Az orchestrator függvény is használható – van az egyszeri eszközök üzembe helyezését, vagy pedig egy nagyobb vezénylési része lehet. Az utóbbi esetben a szülő orchestrator függvény példányait is ütemezheti `DeviceProvisioningOrchestration` használatával a `CallSubOrchestratorAsync` (C#) vagy `callSubOrchestrator` (JavaScript) API-t.

Íme egy példa, amely bemutatja, hogyan párhuzamosan több orchestrator függvények futnak.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Feladatközpontok vannak, és a konfigurálásukról további](durable-functions-task-hubs.md)
