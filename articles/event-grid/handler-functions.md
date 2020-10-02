---
title: Azure-függvény Azure Event Grid események eseménykezelőként
description: Leírja, hogyan használhatja az Azure functions-t Event Grid eseményekhez tartozó eseménykezelőként.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: db06962c020eb954bf0c595e5a4019b1df774898
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629688"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure-függvény Event Grid események eseménykezelőként

Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő végrehajt egy műveletet az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és **Azure functions** az egyikük. 

A kiszolgáló nélküli architektúrában **Azure functions** használatával válaszolhat a Event Grid eseményeire. Ha egy Azure-függvényt kezelőként használ, használja az általános HTTP-trigger helyett a Event Grid triggert. Event Grid automatikusan érvényesíti Event Grid eseményindítókat. Általános HTTP-eseményindítók esetén saját magának kell végrehajtania az [érvényesítési választ](webhook-event-delivery.md) .

További információ: [Event Grid trigger a Azure functions](../azure-functions/functions-bindings-event-grid.md) számára az Event Grid trigger függvények használatával való használatának áttekintése.

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

## <a name="next-steps"></a>További lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
