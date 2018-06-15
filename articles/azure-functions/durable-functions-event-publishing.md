---
title: Tartós működik teszik közzé az Azure Event rács (előzetes verzió)
description: Útmutató a tartós funkciók automatikus Azure esemény rács közzététel konfigurálása.
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
ms.openlocfilehash: 50e517e5719fb102fd91072abe59d3908176278e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762462"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Tartós működik teszik közzé az Azure Event rács (előzetes verzió)

Ez a cikk bemutatja, hogyan állíthat be Azure tartós funkciók vezénylési életciklus-események (például a létrehozott, befejezett, és sikertelen) közzététele egy egyéni [Azure esemény rács témakör](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

Az alábbiakban néhány forgatókönyvekben, ahol ez a funkció hasznos:

* **DevOps forgatókönyvek például a kék/zöld központi telepítések**: Ha bármilyen feladatok futnak-e még a megvalósítás előtt tudni érdemes a [egymás melletti központi telepítési stratégiát](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Figyelés és diagnosztika támogatás speciális**: akkor is nyomon követheti, orchestration állapotadatait a lekérdezések, például az SQL-adatbázis vagy CosmosDB optimalizálva külső áruházban.

* **Hosszan futó háttértevékenység**: használatakor a tartós funkciók hosszan futó háttér tevékenységhez, ez a szolgáltatás segítségével tudni, hogy a jelenlegi állapot.

## <a name="prerequisites"></a>Előfeltételek

* Telepítés [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc vagy újabb a tartós funkciók projektben.
* Telepítés [az Azure Storage Emulator](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Telepítés [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) , vagy használjon [Azure Cloud rendszerhéj](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Hozzon létre egy egyéni esemény rács témakör

Hozzon létre egy esemény rács témakör események küldhetők a tartós funkciók. Az alábbi utasítások alapján létrehozását mutatják be a témakör az Azure parancssori felület használatával. Megtudhatja, hogyan teheti PowerShell vagy az Azure-portál használatával kapcsolatos információkért tekintse meg a következő cikkeket:

* [EventGrid Quickstarts: Hozzon létre egyéni esemény - PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Quickstarts: Hozzon létre egyéni esemény - Azure-portálon](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot a a `az group create` parancsot. Esemény rács jelenleg nem támogatja minden régióban. Támogatott régiók kapcsolatos információkért lásd: a [esemény rács áttekintése](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Egy esemény rács a témakör egy felhasználó által definiált végpontot, amelybe az esemény. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert a DNS-bejegyzés válik.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>A végpont és kulcs beszerzése

A témakör a végpont beolvasása. Cserélje le `<topic_name>` úgy döntött, hogy a névvel.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

A témakör kulcs beszerzése. Cserélje le `<topic_name>` úgy döntött, hogy a névvel.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most események küldhet a témakörben.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event rács közzétételének konfigurálása

A tartós funkciók projekthez, keresse meg a `host.json` fájlt.

Adja hozzá `EventGridTopicEndpoint` és `EventGridKeySettingName` a egy `durableTask` tulajdonság.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint** -végpontja: az esemény a rács témakör.
* **EventGridKeySettingName** – az alkalmazás beállítása az Azure-függvény kulcsa. Tartós funkciók az esemény a rács témakör kulcs beszerezni az értéket.

Ha megfelelően konfigurált a `host.json` fájl, a tartós funkciók projekt elkezdte életciklus-események küldése az esemény rács témakör. Ez a módszer a függvény alkalmazást, és amikor helyileg futtatta futtatásakor.

Alkalmazás beállítani a a témakör kulcs az függvény alkalmazásban és `local.setting.json`. A következő JSON-ja mintáját a `local.settings.json` helyi hibakereséshez. Cserélje le `<topic_key>` a témakör a kulccsal.  

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

Győződjön meg arról, hogy [Storage Emulator](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) működik-e. Jó ötlet futtatásához a `AzureStorageEmulator.exe clear all` parancs végrehajtása előtt.

## <a name="create-functions-that-listen-for-events"></a>Az események funkciók létrehozása

Hozzon létre egy függvény alkalmazást. Célszerű, és keresse meg az esemény a rács témakör ugyanabban a régióban.

### <a name="create-an-event-grid-trigger-function"></a>Egy esemény rács eseményindító-függvény létrehozása

Hozzon létre egy függvényt, az életciklus-események fogadásához. Válassza ki **egyéni függvény**. 

![Válassza ki a hozzon létre egy egyéni függvény.](media/durable-functions-event-publishing/functions-portal.png)

Válassza ki a rács eseményindító, és válassza ki `C#`.

![Válassza ki a rács eseményindító.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Adja meg a függvény nevét, majd válassza ki `Create`.

![A rács eseményindító létrehozása.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

A függvény a következő kóddal jön létre: 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Válassza a(z) `Add Event Grid Subscription` lehetőséget. Ez a művelet hozzáadja az esemény rács előfizetést, az az esemény rács témakör, amely létrehozta. További információkért lásd: [Azure esemény rácsban fogalmak](https://docs.microsoft.com/en-us/azure/event-grid/concepts)

![Jelölje ki a rács eseményindító kapcsolatot.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Válassza ki `Event Grid Topics` a **témakörtípus**. Az esemény rács témakör létrehozott erőforráscsoport kiválasztását. Ezután válassza ki az esemény rács témakör példányát. Nyomja le az `Create`.

![Event Grid-előfizetés létrehozása.](media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll életciklus-események fogadásához. 

## <a name="create-durable-functions-to-send-the-events"></a>Az események küldésére tartós funkciók létrehozása.

A tartós funkciók projektben a helyi számítógépen a hibakeresés elindításához.  Az alábbi kód megegyezik a tartós funkciók sablon kódját. Ön már be van állítva `host.json` és `local.settings.json` a helyi számítógépen. 

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

Ha meghívja a `Sample_HttpStart` Postman vagy a böngésző, a tartós függvény kezdődik, életciklus-események küldésére. A végpont esetében általában `http://localhost:7071/api/Sample_HttpStart` helyi hibakereséshez.

Tekintse meg a naplókat, a függvény az Azure-portálon létrehozott.

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

## <a name="event-schema"></a>Esemény séma

Az alábbi lista ismerteti a életciklus-események séma:

* **azonosító**: az esemény rács esemény egyedi azonosítója.
* **tulajdonos**: az esemény tulajdonos elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` lesz `Running`, `Completed`, `Failed`, és `Terminated`.  
* **adatok**: tartós függvényeket paraméterekkel.
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) nevét.
    * **Függvénynév**: Orchestrator függvény neve.
    * **instanceId**: tartós funkciók instanceId.
    * **OK**: a nyomkövetési esemény társított további adatokat. További információkért lásd: [diagnosztika a tartós funkciók (az Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: Vezénylési futási állapotának. Rendszert futtató, befejezett, sikertelen, megszakítva. 
* **esemény típusa**: "orchestratorEvent"
* **eventTime**: az esemény időpontja (UTC).
* **dataVersion**: az életciklus-esemény séma változatát.
* **metadataVersion**: a metaadatok verzióját.
* **a témakör**: EventGrid témakör erőforrás.

## <a name="how-to-test-locally"></a>Helyi tesztelése

Helyi teszteléséhez [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, a tartós funkciók példánykezelés](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Ismerje meg, a tartós funkciók versioning](durable-functions-versioning.md)
