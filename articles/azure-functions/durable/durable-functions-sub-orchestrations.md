---
title: Szubvezénylések tartós függvényekhez – Azure
description: Vezénylési vezénylések hívása az Azure Functions durable functions bővítményében.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261517"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Szubvezénylések a tartós függvényekben (Azure Functions)

A tevékenységfüggvények hívása mellett az orchestrator függvények más orchestrator függvényeket is meghívhatnak. Például hozhat létre egy nagyobb vezénylési kisebb orchestrator függvények könyvtárából. Vagy futtathat több példányt egy orchestrator függvény párhuzamosan.

Az orchestrator függvény hívhat egy másik `CallSubOrchestratorAsync` orchestrator függvényt a vagy a metódusok használatával a `CallSubOrchestratorWithRetryAsync` .NET, vagy a `callSubOrchestrator` vagy `callSubOrchestratorWithRetry` a javascript-ben metódusok használatával. A [hibakezelés & kompenzációs](durable-functions-error-handling.md#automatic-retry-on-failure) cikk további információt tartalmaz az automatikus újrapróbálkozásról.

Sub-orchestrator függvények viselkednek, mint a tevékenység függvények a hívó szemszögéből. Visszaadhatnak egy értéket, kivételt vethetnek, és a szülő orchestrator függvény megvárhatja őket. 
## <a name="example"></a>Példa

A következő példa egy IoT ("az eszközök internetes hálózata") forgatókönyv, ahol több eszköz, amelyki kell építeni. A következő függvény az egyes eszközökhöz végrehajtandó létesítési munkafolyamatot jelöli:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Ez az orchestrator-függvény használható, mint az egyszeri eszköz kiépítése, vagy része lehet egy nagyobb vezénylési. Az utóbbi esetben a szülő orchestrator függvény `DeviceProvisioningOrchestration` ütemezheti példányait a `CallSubOrchestratorAsync` (.NET) vagy `callSubOrchestrator` (JavaScript) API használatával.

Íme egy példa, amely bemutatja, hogyan kell futtatni több orchestrator függvények párhuzamosan.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Az előző C# példák a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Alvezénylési kell definiálni ugyanabban a függvényalkalmazásban, mint a szülő vezénylési. Ha meg kell hívnia, és meg kell várnia a vezényléseket egy másik függvényalkalmazásban, fontolja meg a HTTP API-k beépített támogatását és a HTTP 202 lekérdezési fogyasztói mintát. További információt a [HTTP-szolgáltatások témakörben talál.](durable-functions-http-features.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az egyéni vezénylési állapot beállítása](durable-functions-custom-orchestration-status.md)