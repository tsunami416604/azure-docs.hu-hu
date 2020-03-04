---
title: Durable Functions Publishing to Azure Event Grid (előzetes verzió)
description: Ismerje meg, hogyan konfigurálhatja a Durable Functions automatikus Azure Event Grid közzétételét.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249762"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions Publishing to Azure Event Grid (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthatja be a Durable Functionst a munkafolyamatok életciklusával kapcsolatos események (például a létrehozás, a Befejezés és a sikertelen) közzétételéhez egy egyéni [Azure Event Grid témakörben](https://docs.microsoft.com/azure/event-grid/overview).

Az alábbiakban néhány olyan forgatókönyvet ismertetünk, amelyekben ez a funkció hasznos:

* **DevOps-forgatókönyvek, például kék/zöld környezetek**: érdemes tudnia, hogy a [párhuzamos üzembe helyezési stratégia](durable-functions-versioning.md#side-by-side-deployments)megvalósítása előtt futnak-e a feladatok.

* **Speciális monitorozási és diagnosztikai támogatás**: a munkafolyamatok állapotával kapcsolatos információkat a lekérdezésekhez optimalizált külső tárolóban, például Azure SQL Database vagy Azure Cosmos db is nyomon követheti.

* **Hosszan futó háttérbeli tevékenység**: ha a Durable Functionst hosszan futó háttérbeli tevékenységhez használja, ez a funkció segít megismerni az aktuális állapotot.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) programot a Durable functions-projektbe.
* Telepítse az [Azure Storage emulatort](../../storage/common/storage-use-emulator.md) (csak Windows), vagy használjon egy meglévő Azure Storage-fiókot.
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) telepítése vagy a [Azure Cloud Shell](../../cloud-shell/overview.md) használata

## <a name="create-a-custom-event-grid-topic"></a>Egyéni Event Grid-témakör létrehozása

Hozzon létre egy Event Grid témakört az események Durable Functionsból való küldéséhez. Az alábbi utasítások bemutatják, hogyan hozhat létre egy témakört az Azure CLI használatával. Ezt a [PowerShell](../../event-grid/custom-event-quickstart-powershell.md) vagy [a Azure Portal](../../event-grid/custom-event-quickstart-portal.md)használatával is elvégezheti.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. A Azure Event Grid jelenleg nem támogatja az összes régiót. További információ a támogatott régiókról: [Azure Event Grid Overview (áttekintés](../../event-grid/overview.md)).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Egy Event Grid témakör egy felhasználó által definiált végpontot biztosít, amelyre az eseményt közzéteszi. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert DNS-bejegyzés lesz.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>A végpont és a kulcs lekérése

A témakör végpontjának beolvasása. Cserélje le a `<topic_name>`t a kiválasztott névre.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

A témakör kulcsának beolvasása. Cserélje le a `<topic_name>`t a kiválasztott névre.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most már küldhet eseményeket a témakörbe.

## <a name="configure-event-grid-publishing"></a>Event Grid közzétételének konfigurálása

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

A lehetséges Azure Event Grid konfigurációs tulajdonságok a [Host. JSON dokumentációjában](../functions-host-json.md#durabletask)találhatók. A `host.json` fájl konfigurálása után a Function alkalmazás életciklus-eseményeket küld a Event Grid témakörnek. Ez akkor működik, ha a Function alkalmazást helyileg és az Azure-ban futtatja.

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

Ha a [Storage emulatort](../../storage/common/storage-use-emulator.md) (csak Windows) használja, ellenőrizze, hogy működik-e. A végrehajtás előtt érdemes futtatni a `AzureStorageEmulator.exe clear all` parancsot.

Ha meglévő Azure Storage-fiókot használ, cserélje le a `UseDevelopmentStorage=true` a `local.settings.json` a kapcsolati karakterlánccal.

## <a name="create-functions-that-listen-for-events"></a>Eseményeket figyelő függvények létrehozása

A Azure Portal használatával hozzon létre egy másik Function alkalmazást a Durable Functions alkalmazás által közzétett események figyelésére. A legjobb megoldás, ha ugyanabban a régióban található, mint a Event Grid témakör.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid trigger függvény létrehozása

Hozzon létre egy függvényt az életciklus eseményeinek fogadásához. Válassza az **Egyéni függvény**lehetőséget.

![Válassza az egyéni létrehozása funkciót.](./media/durable-functions-event-publishing/functions-portal.png)

Válassza ki Event Grid triggert, és válasszon nyelvet.

![Válassza ki a Event Grid triggert.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Adja meg a függvény nevét, majd válassza a `Create`lehetőséget.

![Hozza létre a Event Grid triggert.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

A rendszer létrehoz egy függvényt a következő kóddal:

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Válassza a(z) `Add Event Grid Subscription` lehetőséget. Ez a művelet egy Event Grid-előfizetést hoz létre a létrehozott Event Grid témakörhöz. További információ: [fogalmak a Azure Event Grid-ban](https://docs.microsoft.com/azure/event-grid/concepts)

![Válassza ki a Event Grid trigger hivatkozást.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Válassza ki `Event Grid Topics` a **témakör típusa mezőben**. Válassza ki az Event Grid témakörhöz létrehozott erőforráscsoportot. Ezután válassza ki a Event Grid témakör példányát. Nyomja meg az `Create`gombot.

![Event Grid-előfizetés létrehozása.](./media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll az életciklus-események fogadására.

## <a name="run-durable-functions-app-to-send-the-events"></a>Durable Functions alkalmazás futtatása az események elküldéséhez

A korábban konfigurált Durable Functions-projektben indítsa el a hibakeresést a helyi gépen, és indítson el egy előkészítést. Az alkalmazás Durable Functions életciklus-eseményeket tesz közzé a Event Grid. Ellenőrizze, hogy a Event Grid elindítja-e a figyelő függvényt, amelyet a naplóinak a Azure Portalban való ellenőrzésével hozott létre.

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

* **`id`** : a Event Grid esemény egyedi azonosítója.
* **`subject`** : az esemény tárgyának elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}`. a `{orchestrationRuntimeStatus}` `Running`, `Completed`, `Failed`és `Terminated`lesz.  
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
