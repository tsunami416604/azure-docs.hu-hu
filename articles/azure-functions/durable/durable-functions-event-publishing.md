---
title: Tartós függvények közzététele az Azure Event Gridben (előzetes verzió)
description: Ismerje meg, hogyan konfigurálhatja az automatikus Azure Event Grid közzétételt a tartós függvényekhez.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535485"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Tartós függvények közzététele az Azure Event Gridben (előzetes verzió)

Ez a cikk bemutatja, hogyan állíthatja be a durable functions-et a vezénylési életciklus-események (például a létrehozott, befejezett és sikertelen) egyéni [Azure Event Grid-témakörben](https://docs.microsoft.com/azure/event-grid/overview)való közzétételéhez.

Az alábbiakban felma> néhány olyan forgatókönyv, ahol ez a funkció hasznos:

* **DevOps-forgatókönyvek, például kék/zöld központi telepítések:** Az egymás [melletti telepítési stratégia megvalósítása](durable-functions-versioning.md#side-by-side-deployments)előtt érdemes tudni, hogy futnak-e valamilyen feladat.

* **Speciális figyelési és diagnosztikai támogatás:** Nyomon követheti a vezénylési állapotinformációkat egy lekérdezésekre optimalizált külső tárolóban, például az Azure SQL Database vagy az Azure Cosmos DB.

* **Hosszú ideig futó háttértevékenység:** Ha tartós függvényeket használ egy hosszú ideig futó háttértevékenységhez, ez a funkció segít az aktuális állapot megismerésén.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) alkalmazást a Durable Functions projektbe.
* Telepítse [az Azure Storage-emulátort](../../storage/common/storage-use-emulator.md) (csak Windows) vagy használjon egy meglévő Azure Storage-fiókot.
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) telepítése vagy az [Azure Cloud Shell](../../cloud-shell/overview.md) használata

## <a name="create-a-custom-event-grid-topic"></a>Egyéni Eseményrács-témakör létrehozása

Hozzon létre egy Eseményrács-témakört a tartós függvényekből történő események küldéséhez. Az alábbi utasítások bemutatják, hogyan hozhat létre egy témakört az Azure CLI használatával. Ezt a [PowerShell vagy](../../event-grid/custom-event-quickstart-powershell.md) az [Azure Portal használatával](../../event-grid/custom-event-quickstart-portal.md)is megteheti.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. Jelenleg az Azure Event Grid nem támogatja az összes régióban. A támogatott régiókról az Azure Event Grid áttekintése című [témakörben olvashat.](../../event-grid/overview.md)

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid témakör egy felhasználó által definiált végpontot biztosít, amelybe az eseményt közzéteszi. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert DNS-bejegyzéssé válik.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>A végpont és a kulcs beszerezése

A témakör végpontjának beszereznie. Cserélje `<topic_name>` le a választott névre.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Szerezd meg a téma gombot. Cserélje `<topic_name>` le a választott névre.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most már küldhet eseményeket a témához.

## <a name="configure-event-grid-publishing"></a>Eseményrács közzétételének konfigurálása

A Durable Functions projektben `host.json` keresse meg a fájlt.

### <a name="durable-functions-1x"></a>Tartós funkciók 1.x

Add `eventGridTopicEndpoint` `eventGridKeySettingName` és `durableTask` egy tulajdonság.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Tartós funkciók 2.x

Vegyen `notifications` fel egy `durableTask` szakaszt a `<topic_name>` fájl tulajdonságába, és helyettesítse a választott nevet. Ha `durableTask` a `extensions` vagy tulajdonságok nem léteznek, hozza létre őket, mint ebben a példában:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

Az Azure Event Grid lehetséges konfigurációs tulajdonságai a [host.json dokumentációjában](../functions-host-json.md#durabletask)találhatók. A fájl `host.json` konfigurálása után a függvényalkalmazás életciklus-eseményeket küld az Event Grid témakörbe. Ez akkor működik, ha a függvényalkalmazást helyileg és az Azure-ban is futtatja.

Állítsa be a témakörkulcs alkalmazásbeállítását `local.settings.json`a Function App és a alkalmazásban. A következő JSON egy `local.settings.json` példa a helyi hibakeresés. Cserélje `<topic_key>` le a témakörbillentyűre.  

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

Ha a [storage emulátort](../../storage/common/storage-use-emulator.md) (csak Windows) használja, ellenőrizze, hogy működik-e. A `AzureStorageEmulator.exe clear all` parancs végrehajtása előtt célszerű futtatni a parancsot.

Ha egy meglévő Azure Storage-fiókot használ, cserélje le `UseDevelopmentStorage=true` a kapcsolati `local.settings.json` karakterlánc.

## <a name="create-functions-that-listen-for-events"></a>Eseményeket figyelő függvények létrehozása

Az Azure Portal használatával hozzon létre egy másik függvényalkalmazást a Durable Functions alkalmazás által közzétett események figyelésére. A legjobb, ha az Event Grid témakörével azonos régióban találja meg.

### <a name="create-an-event-grid-trigger-function"></a>Eseményrács eseményindító funkciójának létrehozása

Hozzon létre egy függvényt az életciklus-események fogadásához. Válassza az **Egyéni függvény lehetőséget**.

![Egyéni függvény létrehozása jelölőnégyzetet.](./media/durable-functions-event-publishing/functions-portal.png)

Válassza az Eseményrács eseményindító jának kiválasztását, és válasszon egy nyelvet.

![Jelölje ki az Eseményrács eseményindítót.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Írja be a függvény nevét, `Create`majd válassza a lehetőséget.

![Hozza létre az Eseményrács eseményindítót.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Létrejön egy függvény a következő kóddal:

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

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

Válassza a(z) `Add Event Grid Subscription` lehetőséget. Ez a művelet egy Eseményrács-előfizetést ad hozzá a létrehozott Event Grid témakörhöz. További információ: [Concepts in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Jelölje ki az Eseményrács eseményindító hivatkozását.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Válassza `Event Grid Topics` ki a **Témakörtípushoz**lehetőséget. Válassza ki az Eseményrács témakörhöz létrehozott erőforráscsoportot. Ezután válassza ki az Event Grid témakör példányát. Nyomja `Create`meg a gombot.

![Event Grid-előfizetés létrehozása.](./media/durable-functions-event-publishing/eventsubscription.png)

Most már készen áll az életciklus-események fogadására.

## <a name="run-durable-functions-app-to-send-the-events"></a>A Durable Functions alkalmazás futtatása az események elküldéséhez

A korábban konfigurált Tartós függvények projektben indítsa el a hibakeresést a helyi gépen, és indítsa el a vezénylést. Az alkalmazás közzéteszi a Durable Functions életciklus-eseményeit az Event Gridben. Ellenőrizze, hogy az Event Grid elindítja-e a létrehozott figyelő funkciót az Azure Portalon lévő naplók ellenőrzésével.

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

## <a name="event-schema"></a>Eseményséma

Az alábbi lista ismerteti az életciklus-események sémáját:

* **`id`**: Az Eseményrács esemény egyedi azonosítója
* **`subject`**: Az esemény témájának elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`lesz `Running`, `Completed` `Failed`, `Terminated`és .  
* **`data`**: Tartós függvények specifikus paraméterek.
  * **`hubName`**: [TaskHub-név.](durable-functions-task-hubs.md)
  * **`functionName`**: Az Orchestrator függvény neve.
  * **`instanceId`**: Tartós függvénypéldányazonosító.
  * **`reason`**: A nyomon követési eseményhez kapcsolódó további adatok. További információ: [Diagnostics in Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Vezénylési futásidejű állapot. Futás, Befejezett, Sikertelen, Megszakítva.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Esemény időpontja (UTC).
* **`dataVersion`**: Az életciklus-eseményséma verziója.
* **`metadataVersion`**: A metaadatok verziója.
* **`topic`**: Eseményrács témakör erőforrás.

## <a name="how-to-test-locally"></a>Hogyan kell tesztelni a helyi

A helyi teszteléshez olvassa el az [Azure-függvény eseményrács-eseményindító helyi hibakeresési funkcióját.](../functions-debug-event-grid-trigger-local.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Példánykezelés megismerése a tartós függvényekben](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [A verziószámozás megismerése a Tartós függvények funkcióban](durable-functions-versioning.md)
