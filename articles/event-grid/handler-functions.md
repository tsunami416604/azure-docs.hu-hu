---
title: Azure-függvény Azure Event Grid események eseménykezelőként
description: Leírja, hogyan használhatja az Azure functions-t Event Grid eseményekhez tartozó eseménykezelőként.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: cd500eed180096388eede96f768f08b896ca6456
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873727"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure-függvény Event Grid események eseménykezelőként

Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő végrehajt egy műveletet az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és **Azure functions** az egyikük. 


Ha Azure-függvényt szeretne használni az események kezelőjéként, kövesse az alábbi módszerek egyikét: 

-   [Event Grid trigger](../azure-functions/functions-bindings-event-grid-trigger.md)használata.  Adja meg az **Azure-függvényt** **végpont típusaként**. Ezután adja meg az Azure Function alkalmazást és az eseményeket kezelő függvényt. 
-   [Http-trigger](../azure-functions/functions-bindings-http-webhook.md)használata.  Adja meg a **Webhookot** **végpont típusaként**. Ezután adja meg annak az Azure-függvénynek az URL-címét, amely az eseményeket fogja kezelni. 

Javasoljuk, hogy az első módszert (Event Grid triggert) használja, mivel a második módszernél a következő előnyökkel jár:
-   Event Grid automatikusan érvényesíti Event Grid eseményindítókat. Általános HTTP-eseményindítók esetén saját magának kell végrehajtania az [érvényesítési választ](webhook-event-delivery.md) .
-   Event Grid automatikusan módosítja azt a sebességet, amellyel az eseményeket egy Event Grid esemény által aktivált függvénynek küldi a függvény az események feldolgozásakor észlelt gyakoriság alapján. Ez a díjszabás megfelel a szolgáltatás által az események feldolgozására való képtelenségből eredő kézbesítési hibáknak, mivel a függvény esemény-feldolgozási sebessége változhat az idő múlásával. A nagy adatátviteli teljesítmény növelése érdekében engedélyezze a kötegelt feldolgozást az esemény-előfizetésben. További információ: [Batch engedélyezése](#enable-batching).

    > [!NOTE]
    > Jelenleg nem használhat Event Grid eseményindítót egy Azure Functions alkalmazáshoz, ha az esemény a **CloudEvents** -sémában kerül kézbesítésre. Ehelyett használjon HTTP-triggert.

## <a name="tutorials"></a>Oktatóanyagok

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: események kezelése a függvénnyel](custom-event-to-function.md) | Egyéni eseményt küld egy függvénynek feldolgozásra. |
| [Oktatóanyag: feltöltött képek átméretezésének automatizálása Event Grid használatával](resize-images-on-storage-blob-upload-event.md) | A felhasználók képeket töltenek fel a webalkalmazásból a Storage-fiókba. Storage-blob létrehozásakor Event Grid egy eseményt küld a Function alkalmazásnak, amely átméretezi a feltöltött képet. |
| [Oktatóanyag: stream big data adattárházba](event-grid-event-hubs-integration.md) | Amikor Event Hubs létrehoz egy rögzítési fájlt, Event Grid küld egy eseményt egy Function alkalmazásnak. Az alkalmazás lekéri a rögzítési fájlt, és áttelepíti az adattárházba. |
| [Oktatóanyag: Azure Service Bus Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből egy Function-alkalmazásba és egy logikai alkalmazásba. |

## <a name="rest-example-for-put"></a>REST-példa (PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Kötegelt feldolgozás engedélyezése
Magasabb átviteli sebesség esetén engedélyezze a kötegelt feldolgozást az előfizetésben. Ha a Azure Portal használja, beállíthatja a kötegek maximális számát és az előnyben részesített köteg méretét kilogramm bájtban az előfizetés létrehozásakor vagy a létrehozás után. 

A Batch beállításait a Azure Portal, a PowerShell, a CLI vagy a Resource Manager-sablon használatával konfigurálhatja. 

### <a name="azure-portal"></a>Azure Portal
Amikor létrehoz egy előfizetést a felhasználói felületen, az **esemény-előfizetés létrehozása** lapon váltson a **speciális szolgáltatások** lapra, és állítsa be az értékek **maximális száma a Batch** és **az előnyben részesített köteg mérete kilobájtban**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Az előfizetés létrehozásának időpontjában engedélyezze a kötegelt feldolgozást":::

A meglévő előfizetések ezen értékeit a **Event Grid témakör** lap **szolgáltatások** lapján frissítheti. 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Az előfizetés létrehozásának időpontjában engedélyezze a kötegelt feldolgozást":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
**MaxEventsPerBatch** és **preferredBatchSizeInKilobytes** is beállíthat egy Azure Resource Manager sablonban. További információ: [Microsoft. EventGrid eventSubscriptions-sablon referenciája](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
A Batch szolgáltatáshoz kapcsolódó beállításokat az az [eventgrid Event-előfizetés Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) vagy [az eventgrid Event-előfizetés Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) paranccsal konfigurálhatja a következő paraméterekkel: `--max-events-per-batch` vagy `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
A [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) vagy az [Update-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) parancsmaggal konfigurálhatja a Batch szolgáltatáshoz kapcsolódó beállításokat a következő paraméterekkel: `-MaxEventsPerBatch` vagy `-PreferredBatchSizeInKiloBytes` .

## <a name="next-steps"></a>Következő lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
