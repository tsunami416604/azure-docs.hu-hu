---
title: Alárendelt álló üzenettípusok összehangolását tartós funkciók – Azure
description: Hogyan hívhatja meg a álló üzenettípusok összehangolását a tartós funkciók bővítményben Azure Functions álló üzenettípusok összehangolását.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 7545a371749ed9af88f08af23cce3a513f494374
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33761333"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>A tartós függvények (az Azure Functions) alárendelt álló üzenettípusok összehangolását

Az orchestrator funkciók mellett hívja meg a tevékenység funkciók, más orchestrator függvényeinek meghívása. Például hozhat létre egy nagyobb vezénylési orchestrator funkciók eltávolítva a szalagtárból. Vagy párhuzamosan az orchestrator függvény több példányát is futtathatja.

Az orchestrator függvény meghívásával meghívhatja másik orchestrator függvény a [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) vagy a [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) metódust. A [hiba kezelése & kompenzációs](durable-functions-error-handling.md#automatic-retry-on-failure) cikk automatikus újrapróbálkozáskor további részleteket tartalmaz.

Alárendelt orchestrator funkciók ugyanúgy viselkednek tevékenység funkciók a hívó szempontjából. Ezek adhat vissza értéket, kivételt jelez, és a szülő orchestrator függvény is kell várni.

> [!NOTE]
> A `CallSubOrchestratorAsync` és `CallSubOrchestratorWithRetryAsync` módszerek még nem állnak rendelkezésre a JavaScript.

## <a name="example"></a>Példa

Az alábbi példában látható egy IoT ("az eszközök internetes hálózatát") forgatókönyvet, ahol több eszközön, amely ki kell építeni. Van egy adott vezénylési, amely az egyes eszközök, és előfordulhat, hogy keresse meg a következő hasonlót kell elvégezni:

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

Az orchestrator függvény használható-van egyszeri eszközök kiépítését, vagy egy nagyobb vezénylési része lehet. Az utóbbi esetben a fölérendelt orchestrator függvény ütemezheti a példányai `DeviceProvisioningOrchestration` használatával a `CallSubOrchestratorAsync` API.

Íme egy példa, amely bemutatja, hogyan több orchestrator funkciók párhuzamosan futnak.

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
> [Ismertetése feladat hubok és konfigurálásuk módját](durable-functions-task-hubs.md)
