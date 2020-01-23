---
title: Durable Functions Publishing to Azure Event Grid (előzetes verzió)
description: Ismerje meg, hogyan konfigurálhatja a Durable Functions automatikus Azure Event Grid közzétételét.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 768af2e89d6523f50bd9fcc3d13cc84b711cc6f0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547472"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions Publishing to Azure Event Grid (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthatja be a Durable Functionst a munkafolyamatok életciklusával kapcsolatos események (például a létrehozás, a Befejezés és a sikertelen) közzétételéhez egy egyéni [Azure Event Grid témakörben](https://docs.microsoft.com/azure/event-grid/overview).

Az alábbiakban néhány olyan forgatókönyvet ismertetünk, amelyekben ez a funkció hasznos:

* **DevOps-forgatókönyvek, például kék/zöld környezetek**: érdemes tudnia, hogy a [párhuzamos üzembe helyezési stratégia](durable-functions-versioning.md#side-by-side-deployments)megvalósítása előtt futnak-e a feladatok.

* **Speciális monitorozási és diagnosztikai támogatás**: egy lekérdezésekre optimalizált külső tárolóban, például az SQL Database-ben vagy a CosmosDB-ban is nyomon követheti a munkafolyamati állapot adatait.

* **Hosszan futó háttérbeli tevékenység**: ha a Durable Functionst hosszan futó háttérbeli tevékenységhez használja, ez a funkció segít megismerni az aktuális állapotot.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) programot a Durable functions-projektbe.
* Telepítse az [Azure Storage emulatort](../../storage/common/storage-use-emulator.md).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) telepítése vagy a [Azure Cloud Shell](../../cloud-shell/overview.md) használata

## <a name="create-a-custom-event-grid-topic"></a>Egyéni Event Grid-témakör létrehozása

Hozzon létre egy Event Grid-témakört az események Durable Functionsból való küldéséhez. Az alábbi utasítások bemutatják, hogyan hozhat létre egy témakört az Azure CLI használatával. A PowerShell vagy a Azure Portal használatával történő végrehajtásával kapcsolatos információkért tekintse meg a következő cikkeket:

* [EventGrid gyors útmutató: egyéni esemény létrehozása – PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [EventGrid gyors útmutató: egyéni esemény létrehozása – Azure Portal](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. A Azure Event Grid jelenleg nem támogatja az összes régiót. További információ a támogatott régiókról: [Azure Event Grid Overview (áttekintés](../../event-grid/overview.md)).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakör egy felhasználó által definiált végpontot biztosít, amelyre az eseményt közzéteszi. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert DNS-bejegyzés lesz.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>A végpont és a kulcs lekérése

A témakör végpontjának beolvasása. Cserélje le a `<topic_name>`t a kiválasztott névre.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

A témakör kulcsának beolvasása. Cserélje le a `<topic_name>`t a kiválasztott névre.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most már küldhet eseményeket a témakörbe.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid közzétételének konfigurálása

A Durable Functions-projektben keresse meg a `host.json` fájlt.

`eventGridTopicEndpoint` és `eventGridKeySettingName` hozzáadása `durableTask` tulajdonságban.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

A lehetséges Azure Event Grid konfigurációs tulajdonságok a [Host. JSON dokumentációjában](../functions-host-json.md#durabletask)találhatók. A `host.json` fájl konfigurálása után a Function alkalmazás életciklus-eseményeket küld az Event Grid témakörbe. Ez akkor működik, ha a Function alkalmazást helyileg és az Azure-ban futtatja.

Adja meg a témakörhöz tartozó függvényalkalmazás és `local.settings.json`alkalmazásának beállításait. A következő JSON a helyi hibakeresési `local.settings.json` mintája. Cserélje le a `<topic_key>`t a témakör kulcsára.  

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

Győződjön meg arról, hogy a [Storage Emulator](../../storage/common/storage-use-emulator.md) működik. A végrehajtás előtt érdemes futtatni a `AzureStorageEmulator.exe clear all` parancsot.

## <a name="create-functions-that-listen-for-events"></a>Eseményeket figyelő függvények létrehozása

Hozzon létre egy függvényalkalmazás. A legjobb megoldás, ha ugyanabban a régióban található, mint az Event Grid-témakör.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid eseményindító-függvény létrehozása

Hozzon létre egy függvényt az életciklus eseményeinek fogadásához. Válassza az **Egyéni függvény**lehetőséget.

![Válassza az egyéni létrehozása funkciót.](./media/durable-functions-event-publishing/functions-portal.png)

Válassza ki Event Grid triggert, és válassza a `C#`lehetőséget.

![Válassza ki a Event Grid triggert.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Adja meg a függvény nevét, majd válassza a `Create`lehetőséget.

![Hozza létre a Event Grid triggert.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

A rendszer létrehoz egy függvényt a következő kóddal:

#### <a name="precompiled-c"></a>ElőfordítottC#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C#Parancsfájl

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

Válassza ki `Event Grid Topics` a **témakör típusa mezőben**. Válassza ki az Event Grid-témakörhöz létrehozott erőforráscsoportot. Ezután válassza ki az Event Grid-témakör példányát. Nyomja meg az `Create`gombot.

![Event Grid-előfizetés létrehozása.](./media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll az életciklus-események fogadására.

## <a name="create-durable-functions-to-send-the-events"></a>Durable Functions létrehozása az események elküldéséhez

A Durable Functions-projektben indítsa el a hibakeresést a helyi gépen.  A következő kód megegyezik a Durable Functions sablon kódjával. Már konfigurálta `host.json` és `local.settings.json` a helyi gépen.

### <a name="precompiled-c"></a>ElőfordítottC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett a `IDurableOrchestrationContext`helyett a `OrchestrationClient` attribútumot kell `DurableOrchestrationContext` használnia, és `DurableOrchestrationClient` helyett a `IDurableOrchestrationClient`paramétert kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

Ha a `Sample_HttpStart` a Poster vagy a böngészővel hívja, a tartós függvény elkezdi életciklus-események küldését. A végpont általában `http://localhost:7071/api/Sample_HttpStart` a helyi hibakereséshez.

Tekintse meg a Azure Portalban létrehozott függvény naplóit.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esemény sémája

Az alábbi lista az életciklus-események sémáját mutatja be:

* **`id`** : az Event Grid esemény egyedi azonosítója.
* **`subject`** : az esemény tárgyának elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}` kérdésre adott válaszban foglalt lépéseket. a `{orchestrationRuntimeStatus}` `Running`, `Completed`, `Failed`és `Terminated`lesz.  
* **`data`** : Durable functions megadott paraméterek.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) neve.
  * **`functionName`** : a Orchestrator függvény neve.
  * **`instanceId`** : Durable functions instanceId.
  * **`reason`** : a nyomkövetési eseményhez kapcsolódó további információk. További információ: [diagnosztika a Durable functions (Azure functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : a hangolási futtatókörnyezet állapota. Fut, befejezett, sikertelen, megszakítva.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : esemény időpontja (UTC).
* **`dataVersion`** : az életciklus-esemény sémájának verziója.
* **`metadataVersion`** : a metaadatok verziója.
* **`topic`** : Event Grid-témakör erőforrása.

## <a name="how-to-test-locally"></a>Helyi tesztelés

A helyi teszteléshez olvassa el az [Azure Function Event Grid a helyi hibakeresés elindítása](../functions-debug-event-grid-trigger-local.md)című cikkét.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A példányok kezelésének megismerése Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [A verziószámozás megismerése Durable Functions](durable-functions-versioning.md)
