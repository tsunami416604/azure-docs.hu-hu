---
title: Durable Functions – Azure alárendelt vezénylések
description: Hogyan hívhat meg az Azure Functions a Durable Functions bővítmény a vezénylések vezénylések.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 59e8eb41b7e9fe3d57196f6844d1a768c3ef598b
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094436"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) az alárendelt vezénylések

Mellett tevékenységfüggvényeket hívja meg, az orchestrator-funkciók meghívhatja az orchestrator-függvényekkel. Ha például hozhat létre egy nagyobb vezénylési orchestrator funkciók kívül egy könyvtár. Vagy egy orchestrator-függvényt több példánya is futtatható egyszerre.

Az orchestrator függvény meghívhat egy másik orchestrator függvény meghívásával a [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) vagy a [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metódust. A [hibakezelés & kompenzációs](durable-functions-error-handling.md#automatic-retry-on-failure) cikk tartalmaz további információkat az automatikus újrapróbálkozáskor.

Alárendelt orchestrator funkciók csak a hívó szempontból tevékenységfüggvényeket viselkednek. Akkor adhat vissza értéket, kivételt, és a szülő orchestrator függvény által is kell várni.

> [!NOTE]
> A `CallSubOrchestratorAsync` és `CallSubOrchestratorWithRetryAsync` módszerek még nem érhető el a JavaScript.

## <a name="example"></a>Példa

Az alábbi példa egy ("IOT-") IoT-forgatókönyvet mutatja be, ha több eszköz, amely szükség lesz. Egy adott vezénylési az szükségességéről a következőhöz hasonló a következő eszközre van:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Az orchestrator függvény is használható – van az egyszeri eszközök üzembe helyezését, vagy pedig egy nagyobb vezénylési része lehet. Az utóbbi esetben a szülő orchestrator függvény példányait is ütemezheti `DeviceProvisioningOrchestration` használatával a `CallSubOrchestratorAsync` API-t.

Íme egy példa, amely bemutatja, hogyan párhuzamosan több orchestrator függvények futnak.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Feladatközpontok vannak, és a konfigurálásukról további](durable-functions-task-hubs.md)
