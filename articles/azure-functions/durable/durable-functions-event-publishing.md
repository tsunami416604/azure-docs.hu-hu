---
title: Durable Functions Publishing to Azure Event Grid (előzetes verzió)
description: Ismerje meg, hogyan konfigurálhatja a Durable Functions automatikus Azure Event Grid közzétételét.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: 837e29731b617fcb8da95b89668403638c4d049a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087406"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions Publishing to Azure Event Grid (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthatja be a Durable Functionst a munkafolyamatok életciklusával kapcsolatos események (például a létrehozás, a Befejezés és a sikertelen) közzétételéhez egy egyéni [Azure Event Grid témakörben](https://docs.microsoft.com/azure/event-grid/overview).

Az alábbiakban néhány olyan forgatókönyvet ismertetünk, amelyekben ez a funkció hasznos:

* **DevOps-forgatókönyvek, például kék/zöld telepítések**: A [párhuzamos üzembe helyezési stratégia](durable-functions-versioning.md#side-by-side-deployments)megvalósítása előtt érdemes tudnia, hogy a feladatok futnak-e.

* **Fejlett monitorozási és diagnosztikai támogatás**: Nyomon követheti a munkafolyamatok állapotát egy olyan külső tárolóban, amely lekérdezésekre van optimalizálva (például SQL Database vagy CosmosDB).

* **Hosszan futó háttérbeli tevékenység**: Ha hosszan futó háttérbeli tevékenységhez Durable Functions használ, ez a funkció segít az aktuális állapot megismerésében.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-RC vagy újabb verziót a Durable functions projektben.
* Telepítse az [Azure Storage emulatort](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) telepítése vagy a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) használata

## <a name="create-a-custom-event-grid-topic"></a>Egyéni Event Grid-témakör létrehozása

Hozzon létre egy Event Grid-témakört az események Durable Functionsból való küldéséhez. Az alábbi utasítások bemutatják, hogyan hozhat létre egy témakört az Azure CLI használatával. A PowerShell vagy a Azure Portal használatával történő végrehajtásával kapcsolatos információkért tekintse meg a következő cikkeket:

* [EventGrid-gyors útmutató: Egyéni esemény létrehozása – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid-gyors útmutató: Egyéni esemény létrehozása – Azure Portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. A Azure Event Grid jelenleg nem támogatja az összes régiót. További információ a támogatott régiókról: [Azure Event Grid Overview (áttekintés](https://docs.microsoft.com/azure/event-grid/overview)).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakör egy felhasználó által definiált végpontot biztosít, amelyre az eseményt közzéteszi. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert DNS-bejegyzés lesz.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>A végpont és a kulcs lekérése

A témakör végpontjának beolvasása. Cserélje `<topic_name>` le a nevet a kiválasztott névre.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

A témakör kulcsának beolvasása. Cserélje `<topic_name>` le a nevet a kiválasztott névre.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most már küldhet eseményeket a témakörbe.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid közzétételének konfigurálása

A Durable functions-projektben keresse meg `host.json` a fájlt.

Adjon `eventGridTopicEndpoint` hozzá `eventGridKeySettingName` és egy`durableTask` tulajdonságot.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

A lehetséges Azure Event Grid konfigurációs tulajdonságok a [Host. JSON dokumentációjában](../functions-host-json.md#durabletask)találhatók. A `host.json` fájl konfigurálása után a Function alkalmazás életciklus-eseményeket küld az Event Grid-témakörbe. Ez akkor működik, ha a Function alkalmazást helyileg és az Azure-ban futtatja.

Adja meg a témakörben a függvényalkalmazás és `local.setting.json`a témakörhöz tartozó Alkalmazásbeállítások beállításait. A következő JSON egy minta a `local.settings.json` helyi hibakereséshez. Cserélje `<topic_key>` le a billentyűt a témakörre.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Győződjön meg arról, hogy a [Storage Emulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) működik. A parancs végrehajtása előtt érdemes futtatni a `AzureStorageEmulator.exe clear all` parancsot.

## <a name="create-functions-that-listen-for-events"></a>Eseményeket figyelő függvények létrehozása

Hozzon létre egy függvényalkalmazás. A legjobb megoldás, ha ugyanabban a régióban található, mint az Event Grid-témakör.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid eseményindító-függvény létrehozása

Hozzon létre egy függvényt az életciklus eseményeinek fogadásához. Válassza az **Egyéni függvény**lehetőséget.

![Válassza az egyéni létrehozása funkciót.](./media/durable-functions-event-publishing/functions-portal.png)

Válassza ki Event Grid triggert, `C#`majd válassza a elemet.

![Válassza ki a Event Grid triggert.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Adja meg a függvény nevét, majd válassza a elemet `Create`.

![Hozza létre a Event Grid triggert.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

A rendszer létrehoz egy függvényt a következő kóddal:

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Válassza a(z) `Add Event Grid Subscription` lehetőséget. Ez a művelet hozzáadja az Event Grid-előfizetést az Ön által létrehozott Event Grid-témakörhöz. További információ: [fogalmak a Azure Event Grid-ban](https://docs.microsoft.com/azure/event-grid/concepts)

![Válassza ki a Event Grid trigger hivatkozást.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Válassza `Event Grid Topics` a **témakör típusa**lehetőséget. Válassza ki az Event Grid-témakörhöz létrehozott erőforráscsoportot. Ezután válassza ki az Event Grid-témakör példányát. Nyomja `Create`meg a gombot.

![Event Grid-előfizetés létrehozása.](./media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll az életciklus-események fogadására.

## <a name="create-durable-functions-to-send-the-events"></a>Durable Functions létrehozása az események elküldéséhez

A Durable Functions-projektben indítsa el a hibakeresést a helyi gépen.  A következő kód megegyezik a Durable Functions sablon kódjával. Ön már konfigurálva `host.json` van `local.settings.json` és a helyi gépen.

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Ha a Poster `Sample_HttpStart` vagy a böngészőt hívja, a tartós függvény elkezdi életciklus-események küldését. A végpont általában `http://localhost:7071/api/Sample_HttpStart` helyi hibakereséshez használható.

Tekintse meg a Azure Portalban létrehozott függvény naplóit.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Eseményséma

Az alábbi lista az életciklus-események sémáját mutatja be:

* **`id`** : Az Event Grid esemény egyedi azonosítója.
* **`subject`** : Az esemény tárgyának elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`Ekkor:,, és`Terminated`. `Running` `Completed` `Failed`  
* **`data`** : Durable Functions megadott paraméterek.
  * **`hubName`** : A [TaskHub](durable-functions-task-hubs.md) neve.
  * **`functionName`** : A Orchestrator függvény neve.
  * **`instanceId`** : Durable Functions instanceId.
  * **`reason`** : A nyomkövetési eseményhez kapcsolódó további információk. További információ: [diagnosztika a Durable functions (Azure functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : A koordináló futtatókörnyezet állapota. Fut, befejezett, sikertelen, megszakítva.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Esemény időpontja (UTC).
* **`dataVersion`** : Az életciklus-esemény sémájának verziója.
* **`metadataVersion`** :  A metaadatok verziója.
* **`topic`** : Event Grid-témakör erőforrása.

## <a name="how-to-test-locally"></a>Helyi tesztelés

A helyi teszteléshez használja a [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A példányok kezelésének megismerése Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [A verziószámozás megismerése Durable Functions](durable-functions-versioning.md)
