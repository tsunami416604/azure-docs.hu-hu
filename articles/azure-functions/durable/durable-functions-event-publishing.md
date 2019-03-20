---
title: Durable Functions közzétételét az Azure Event Grid (előzetes verzió)
description: Megtudhatja, hogyan Durable Functions automatikus az Azure Event Grid-közzététel konfigurálása.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136361"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions közzétételét az Azure Event Grid (előzetes verzió)

Ez a cikk bemutatja, hogyan állítható be Durable Functions egyéni vezénylési életciklussal kapcsolatos események (például a létrehozott, befejezett, és a sikertelen) közzétételére [Azure Event Grid-témakör](https://docs.microsoft.com/azure/event-grid/overview).

Az alábbiakban néhány olyan forgatókönyvekben, ahol ez a funkció hasznos:

* **DevOps-forgatókönyvekre, mint kék vagy zöld üzembe**: Ha bármely feladatokat futtató implementálása előtt tudni érdemes az [egymás melletti központi telepítési stratégiát](durable-functions-versioning.md#side-by-side-deployments).

* **Speciális monitorozás és diagnosztika támogatási**: Azt is nyomon követheti, orchestration állapotinformációit, például SQL-adatbázis vagy a cosmos DB lekérdezésekhez optimalizált külső tárban.

* **Hosszan futó háttértevékenység**: Durable Functions egy hosszú ideig futó háttérben futó tevékenység használja, ha ez a funkció funkció könnyíti meg a jelenlegi állapota.

## <a name="prerequisites"></a>Előfeltételek

* Telepítés [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc vagy újabb a Durable Functions-projekt.
* Telepítés [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Telepítés [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Egy egyéni event grid-témakör létrehozása

Hozzon létre egy event grid-témakör Durable Functions származó események küldéséhez. Az alábbi utasításokat a témakör létrehozásához az Azure CLI-vel szemléltetik. Megtudhatja, hogyan teheti a PowerShell vagy az Azure portal használatával kapcsolatos információkért tekintse meg a következő cikkeket:

* [EventGrid Gyorsútmutatók: Egyéni esemény létrehozása – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Gyorsútmutatók: Egyéni esemény létrehozása – Azure portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. Azure Event Grid jelenleg nem támogatja az összes régióban. Információ arról, hogy mely régiókban érhető el: a [Azure Event Grid áttekintése](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az event grid-témakör, amelybe elküldheti az esemény felhasználó által meghatározott végpontot biztosít. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert a DNS-bejegyzés lesz.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>A végpont és a kulcs beszerzése

A végpont, a következő témakörben kaphat. Cserélje le `<topic_name>` választott nevét.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

A témakör kulcs beszerzése. Cserélje le `<topic_name>` választott nevét.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most már küldhet eseményeket a témakörbe.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid-közzététel konfigurálása

Az Durable Functions-projektben keresse meg a `host.json` fájlt.

Adjon hozzá `eventGridTopicEndpoint` és `eventGridKeySettingName` a egy `durableTask` tulajdonság.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Az Azure Event Grid lehetséges konfigurációs tulajdonságok található a [host.json dokumentáció](../functions-host-json.md#durabletask). Miután konfigurálta a `host.json` fájlt, a függvényalkalmazás küld életciklussal kapcsolatos események az event grid-témakör. Ez akkor működik, ha a függvényalkalmazás futtatása helyileg és az Azure-ban. ""

A témakör kulcsot az Alkalmazásbeállítás beállítása a függvényalkalmazáshoz és `local.setting.json`. A következő JSON-ja mintát a `local.settings.json` helyi hibakereséshez. Cserélje le `<topic_key>` a témakör a kulccsal.  

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

Győződjön meg arról, hogy [Storage Emulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) működik. Tanácsos futtatni a `AzureStorageEmulator.exe clear all` parancs végrehajtása előtt.

## <a name="create-functions-that-listen-for-events"></a>Az események függvények létrehozása

Hozzon létre egy Függvényalkalmazást. Érdemes úgy elhelyezni, az event grid-témakör ugyanabban a régióban.

### <a name="create-an-event-grid-trigger-function"></a>Event grid eseményindító függvényének létrehozása

Hozzon létre egy függvényt az életciklus-események fogadására. Válassza ki **egyéni függvény**.

![Válassza ki a hozzon létre egy egyéni függvényt.](./media/durable-functions-event-publishing/functions-portal.png)

Event Grid-Trigger válassza, majd válasszon `C#`.

![Válassza ki az Event Grid-Trigger.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Adja meg a függvény nevét, és válassza `Create`.

![Az Event Grid-Trigger létrehozása.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Létrejön egy függvény a következő kóddal:

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

Válassza a(z) `Add Event Grid Subscription` lehetőséget. Ez a művelet hozzáad egy event grid-előfizetés az Ön által létrehozott event grid-témakör. További információkért lásd: [az Azure Event Griddel kapcsolatos fogalmak](https://docs.microsoft.com/azure/event-grid/concepts)

![Válassza ki az Event Grid-Trigger hivatkozásra.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Válassza ki `Event Grid Topics` a **témakörtípus**. Válassza ki az erőforráscsoportot, amelyet létrehozott az event grid-témakör. Ezután válassza ki az event grid-témakör példányát. Nyomja meg `Create`.

![Event Grid-előfizetés létrehozása.](./media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll életciklussal kapcsolatos események fogadásához.

## <a name="create-durable-functions-to-send-the-events"></a>Durable Functions szeretné küldeni az eseményeket létrehozásához

A Durable Functions-projekt elindításához a helyi gépen hibakeresés.  Az alábbi kód megegyezik a sablonban lévő kód a tartós függvények. Már konfigurált `host.json` és `local.settings.json` a helyi gépen.

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

Ha felhívja a `Sample_HttpStart` Postman vagy a böngésző, tartós függvény életciklus-események küldésére kezdődik. A végpont nem általában `http://localhost:7071/api/Sample_HttpStart` helyi hibakereséshez.

Tekintse meg a naplókat, a függvény, amely az Azure Portalon létrehozott.

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

Az alábbi lista ismerteti az életciklus-események séma:

* **`id`**: Az event grid-esemény egyedi azonosítója.
* **`subject`**: Az esemény tárgya elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` lesz `Running`, `Completed`, `Failed`, és `Terminated`.  
* **`data`**: Durable Functions-specifikus paramétereket.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) nevét.
  * **`functionName`**: Az orchestrator függvény neve.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: A követési esemény társított további adatokat. További információkért lásd: [(az Azure Functions) Durable Functions-diagnosztika](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Vezénylési futásidejű állapot. Fut, befejezett, sikertelen, meg lett szakítva.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Esemény időpontja (UTC).
* **`dataVersion`**: Az életciklus-esemény séma verziója.
* **`metadataVersion`**:  A metaadatok verziója.
* **`topic`**: Event grid témakör erőforrás.

## <a name="how-to-test-locally"></a>Helyi tesztelése

Helyi teszteléséhez használhatja a [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a példány Durable Functions-kezelés](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Ismerje meg, Durable Functions verziószámozást](durable-functions-versioning.md)
