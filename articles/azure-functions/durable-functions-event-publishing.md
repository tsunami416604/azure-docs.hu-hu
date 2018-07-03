---
title: Durable Functions közzétételét az Azure Event Grid (előzetes verzió)
description: Megtudhatja, hogyan Durable Functions automatikus az Azure Event Grid-közzététel konfigurálása.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 0179a48b74ef0e37d3ac2e7fd18d43e488a89823
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341382"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions közzétételét az Azure Event Grid (előzetes verzió)

Ez a cikk bemutatja, hogyan állítható be Azure Durable Functions egyéni vezénylési életciklussal kapcsolatos események (például a létrehozott, befejezett, és a sikertelen) közzétételére [Azure Event Grid-témakör](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

Az alábbiakban néhány olyan forgatókönyvekben, ahol ez a funkció hasznos:

* **DevOps-forgatókönyvekre, mint kék vagy zöld üzembe**: érdemes tudni, hogy ha implementálása előtt minden feladatot futtatja a [egymás melletti központi telepítési stratégiát](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Speciális monitorozás és diagnosztika támogatási**: Ön is nyomon követheti, orchestration állapotinformációit, például SQL-adatbázis vagy a cosmos DB lekérdezésekhez optimalizált külső tárban.

* **Hosszan futó háttértevékenység**: Durable Functions egy hosszú ideig futó háttérben futó tevékenység használja, ha ez a funkció funkció könnyíti meg a jelenlegi állapota.

## <a name="prerequisites"></a>Előfeltételek

* Telepítés [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc vagy újabb a Durable Functions-projekt.
* Telepítés [Azure Storage Emulator](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Telepítés [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) vagy [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Egy egyéni Event Grid-témakör létrehozása

Hozzon létre egy Event Grid-témakör Durable Functions származó események küldéséhez. Az alábbi utasításokat a témakör létrehozásához az Azure CLI-vel szemléltetik. Megtudhatja, hogyan teheti a PowerShell vagy az Azure portal használatával kapcsolatos információkért tekintse meg a következő cikkeket:

* [EventGrid rövid útmutató: Egyéni esemény létrehozása – PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid rövid útmutató: Egyéni esemény létrehozása – Azure portal](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot a `az group create` parancsot. Event Grid jelenleg nem támogatja az összes régióban. Információ arról, hogy mely régiókban érhető el: a [Event Grid áttekintése](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Egy Event Grid-témakört, amelyben közzéteheti az esemény felhasználó által meghatározott végpontot biztosít. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert a DNS-bejegyzés lesz.

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

Adjon hozzá `EventGridTopicEndpoint` és `EventGridKeySettingName` a egy `durableTask` tulajdonság.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

Az Azure Event Grid lehetséges konfigurációs tulajdonságai a következők:

* **EventGridTopicEndpoint** -végpontja: az Event Grid-témakör. A *AppSettingName %* szintaxis segítségével oldja meg ezt az értéket az alkalmazásbeállítások és a környezeti változókat.
* **EventGridKeySettingName** – az Azure-függvény meg az Alkalmazásbeállítás kulcsát. Durable Functions az Event Grid-témakör kulcs le az értéket.
* **EventGridPublishRetryCount** – [nem kötelező] Ha közzétételét az Event Grid-témakör újrapróbálkozások száma sikertelen lesz.
* **EventGridPublishRetryInterval** -[opcionális] a Event Grid közzététele az újrapróbálkozási időköznek a *óó* formátumban. Ha nincs megadva, az alapértelmezett újrapróbálkozási időköz értéke 5 perc.

Miután beállította a `host.json` fájlt, a Durable Functions-projekt elindul, és életciklus-események küldése az Event Grid-témakör. Ez a módszer a függvényalkalmazáshoz, és helyi futtatása futtatásakor.

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

Győződjön meg arról, hogy [Storage Emulator](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) működik. Tanácsos futtatni a `AzureStorageEmulator.exe clear all` parancs végrehajtása előtt.

## <a name="create-functions-that-listen-for-events"></a>Az események függvények létrehozása

Hozzon létre egy Függvényalkalmazást. Érdemes úgy elhelyezni, az Event Grid-témakör ugyanabban a régióban.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid eseményindító függvényének létrehozása

Hozzon létre egy függvényt az életciklus-események fogadására. Válassza ki **egyéni függvény**. 

![Válassza ki a hozzon létre egy egyéni függvényt.](media/durable-functions-event-publishing/functions-portal.png)

Event Grid-Trigger válassza, majd válasszon `C#`.

![Válassza ki az Event Grid-Trigger.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Adja meg a függvény nevét, és válassza `Create`.

![Az Event Grid-Trigger létrehozása.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Létrejön egy függvény a következő kóddal: 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Válassza a(z) `Add Event Grid Subscription` lehetőséget. Ez a művelet hozzáad egy Event Grid-előfizetést, az Ön által létrehozott Event Grid-témakör. További információkért lásd: [az Azure Event Griddel kapcsolatos fogalmak](https://docs.microsoft.com/en-us/azure/event-grid/concepts)

![Válassza ki az Event Grid-Trigger hivatkozásra.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Válassza ki `Event Grid Topics` a **témakörtípus**. Válassza ki az erőforráscsoportot, amelyet létrehozott az Event Grid-témakör. Ezután válassza ki az Event Grid-témakör példányát. Nyomja meg `Create`.

![Event Grid-előfizetés létrehozása.](media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll életciklussal kapcsolatos események fogadásához. 

## <a name="create-durable-functions-to-send-the-events"></a>Durable Functions szeretné küldeni az eseményeket létrehozásához.

A Durable Functions-projekt elindításához a helyi gépen hibakeresés.  Az alábbi kód megegyezik a sablonban lévő kód a tartós függvények. Már konfigurált `host.json` és `local.settings.json` a helyi gépen. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
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
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
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

* **ID**: az Event Grid-esemény egyedi azonosítója.
* **tulajdonos**: az esemény tárgya elérési útját. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` lesz `Running`, `Completed`, `Failed`, és `Terminated`.  
* **adatok**: Durable Functions-specifikus paramétereket.
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) nevét.
    * **Függvénynév**: Orchestrator függvény neve.
    * **instanceId**: instanceId Durable Functions.
    * **OK**: a követési esemény társított további adatokat. További információkért lásd: [(az Azure Functions) Durable Functions-diagnosztika](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: Vezénylési futásidejű állapot. Fut, befejezett, sikertelen, meg lett szakítva. 
* **esemény típusa**: "orchestratorEvent"
* **eventTime**: esemény időpontja (UTC).
* **dataVersion**: életciklus esemény séma verziója.
* **metadataVersion**: a metaadatok verziója.
* **a témakör**: EventGrid témakör erőforrás.

## <a name="how-to-test-locally"></a>Helyi tesztelése

Helyi teszteléséhez használhatja a [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a példány Durable Functions-kezelés](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Ismerje meg, Durable Functions verziószámozást](durable-functions-versioning.md)
