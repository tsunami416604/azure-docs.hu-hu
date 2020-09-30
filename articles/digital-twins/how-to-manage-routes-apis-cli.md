---
title: Végpontok és útvonalak kezelése (API-k és parancssori felület)
titleSuffix: Azure Digital Twins
description: 'Lásd: végpontok és esemény-útvonalak beállítása és kezelése az Azure Digital Twins-adatszolgáltatásokhoz.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7365e4904bb8e1920e7d4c57c165e489f2ff302e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540591"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Végpontok és útvonalak kezelése az Azure Digital Twinsban (API-k és parancssori felület)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Az Azure Digital Twins-ban átirányíthatja az [eseményekre vonatkozó értesítéseket](how-to-interpret-event-data.md) az alsóbb rétegbeli szolgáltatásokhoz vagy a csatlakoztatott számítási erőforrásokhoz. Ezt úgy teheti meg, hogy először beállítja az eseményeket fogadó **végpontokat** . Ezután létrehozhat olyan  [**esemény-útvonalakat**](concepts-route-events.md) , amelyekkel megadhatja, hogy az Azure Digital Twins mely eseményeit adja meg a végpontok.

A végpontokat és útvonalakat a [EventRoutes API](how-to-use-apis-sdks.md)-kkal, a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)-val vagy az [Azure Digital Twins CLI](how-to-use-cli.md)-vel lehet felügyelni. Ez a cikk végigvezeti a végpontok és útvonalak ezen mechanizmusok használatával történő létrehozásának folyamatán.

A [Azure Portal](https://portal.azure.com)is kezelhetők. A jelen cikk a portált használó verziója esetén olvassa el a következő témakört [*: útmutató: végpontok és útvonalak kezelése (portál)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Előfeltételek

* Szüksége lesz egy **Azure-fiókra** ( [itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)akár ingyenesen is beállíthatja)
* Azure-beli **digitális Twins-példányra** lesz szüksége az Azure-előfizetésében. Ha még nem rendelkezik példányokkal, létrehozhat egyet a következő útmutató lépéseit követve [*: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md). A telepítésből származó alábbi értékekkel a cikk későbbi részében használhatja a következő értékeket:
    - Példány neve
    - Erőforráscsoport
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Végpont létrehozása az Azure Digital Twins számára

Ezek a példányok által létrehozható támogatott típusú végpontok:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Szolgáltatásbusz](../service-bus-messaging/service-bus-messaging-overview.md)

További információ a különböző végpontok típusairól: [*választás az Azure Messaging Services között*](../event-grid/compare-messaging-services.md).

Ahhoz, hogy egy végpontot az Azure digitális Ikrekhöz lehessen kapcsolni, a végponthoz használt Event Grid-témakör, az Event hub vagy a Service Bus már léteznie kell. 

### <a name="create-an-event-grid-endpoint"></a>Event Grid végpont létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre Event Grid típusú végpontot az Azure CLI használatával. [Azure Cloud Shell](https://shell.azure.com)használhat, vagy helyileg is [TELEPÍTheti a CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)-t.

Először hozzon létre egy Event Grid-témakört. Használhatja az alábbi parancsot, vagy további részletekben tekintse meg a lépéseket. Ehhez látogasson el a Event Grid *Egyéni események* rövid útmutatójának [ *Egyéni témakör létrehozása* szakaszára](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) .

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Futtassa a következő parancsot az Azure-régiók azon neveinek listájához, amelyek átadhatók az Azure CLI parancsaihoz:
> ```azurecli
> az account list-locations -o table
> ```

Miután létrehozta a témakört, összekapcsolhatja azt az Azure Digital Twins-val a következő [Azure digitális Twins CLI-paranccsal](how-to-use-cli.md):

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Az Event Grid témakör az Azure Digital Twins-beli végpontként érhető el, az `--endpoint-name` argumentummal megadott néven. Ezt a nevet általában egy **esemény-útvonal**céljaként fogja használni, amelyet [később a jelen cikkben](#event-routes-with-apis-and-the-c-sdk) , az Azure Digital Twins szolgáltatás API használatával hozhat létre.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Event Hubs vagy Service Bus végpont létrehozása

Event Hubs vagy Service Bus végpontok létrehozásának folyamata hasonló a fent látható Event Grid folyamathoz.

Először hozza létre a végpontként használni kívánt erőforrásokat. A következők szükségesek:
* Service Bus: _Service Bus névtér_, _Service Bus témakör_, _engedélyezési szabály_
* Event Hubs: _Event Hubs névtér_, _Event hub_, _engedélyezési szabály_

Ezután használja a következő parancsokat a végpontok létrehozásához az Azure Digital Twinsban: 

* Service Bus témakör végpontjának hozzáadása (egy előre létrehozott Service Bus erőforrást igényel)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event Hubs végpont hozzáadása (előre létrehozott Event Hubs erőforrást igényel)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>Esemény-útvonalak (API-kkal és C# SDK-val)

Az Azure digitális Twins-ból egy végpontba küldött adatok tényleges elküldéséhez meg kell határoznia egy **esemény-útvonalat**. Az Azure Digital Twins **EventRoutes API-jai** lehetővé teszik a fejlesztők számára, hogy az események áramlását a rendszeren és az alárendelt szolgáltatásokon keresztül. További információk az esemény-útvonalakról a [*fogalmakban: az Azure Digital Twins eseményeinek továbbítása*](concepts-route-events.md).

Az ebben a szakaszban szereplő minták a [.net (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core)-t használják.

**Előfeltétel**: a cikk korábbi részében leírtak szerint hozzon létre végpontokat, mielőtt továbblép az útvonal létrehozásához. Ha befejezte a végpontok beállítását, folytassa az esemény-útvonal létrehozásával.

>[!NOTE]
>Ha nemrég telepítette a végpontokat, ellenőrizze, hogy készen állnak-e a telepítésre, **mielőtt** új esemény-útvonalra próbálja használni őket. Ha az útválasztás telepítése sikertelen, mert a végpontok nem állnak készen, várjon néhány percet, és próbálkozzon újra.
>
> Ha a folyamat futtatásakor ezt a folyamatot szeretné használni, akkor érdemes lehet ezt megvárnia, ha 2-3 perc várakozási idő alatt a végponti szolgáltatás befejezi a telepítést, mielőtt továbblép az útvonal beállítására.

### <a name="create-an-event-route"></a>Esemény útvonalának létrehozása

Az események útvonala az [adatsík API](how-to-use-apis-sdks.md#overview-data-plane-apis)-k használatával van definiálva. 

Az útvonalak definíciója a következő elemeket tartalmazza:
* A használni kívánt útvonal neve
* A használni kívánt végpont neve
* Egy szűrő, amely meghatározza, hogy mely eseményeket küldi a rendszer a végpontnak. 

Ha nincs útvonal neve, az Azure digitális Ikreken kívül egyetlen üzenet sem lesz átirányítva. Ha van egy útvonal neve, és a szűrő `true` , az összes üzenet a végponthoz lesz irányítva. Ha van egy útvonal neve, és egy másik szűrő van hozzáadva, az üzenetek a szűrő alapján lesznek szűrve.

Az egyik útvonalnak engedélyezni kell több értesítés és eseménytípus kijelölését. 

`CreateEventRoute` egy esemény-útvonal hozzáadására szolgáló SDK-hívás. Íme egy példa a használatra:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

### <a name="event-route-sample-code"></a>Event Route-mintakód

Az alábbi mintakód bemutatja, hogyan hozhat létre, listázhat és törölhet egy esemény-útvonalat:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Események szűrése

Szűrés nélkül a végpontok számos eseményt kapnak az Azure Digital ikrektől:
* Az Azure Digital Twins szolgáltatás API-ját használó [digitális Twins](concepts-twins-graph.md) telemetria
* A Twin Property változási értesítései az Azure Digital Twins-példányon található bármelyik Twin tulajdonság változásakor
* Életciklussal kapcsolatos események, az ikrek vagy kapcsolatok létrehozásakor vagy törlésekor
* Modell-változási események, az Azure digitális Twins-példányban konfigurált [modellek](concepts-models.md) hozzáadása vagy törlése

Az elküldött eseményeket úgy korlátozhatja, hogy hozzáad egy **szűrőt** egy végponthoz az esemény-útvonalhoz.

Szűrő hozzáadásához használjon PUT-kérést a *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview-* ra a következő törzstel:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Itt láthatók a támogatott útválasztási szűrők. A *szűrő szöveg sémája* oszlopban található részletek használatával cserélje le a `<filter-text>` helyőrzőt a fenti kérelem törzsében.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Végpontok és útvonalak kezelése a CLI-vel

A végpontok és útvonalak az Azure Digital Twins CLI használatával is kezelhetők. További információ a parancssori felület használatáról és a rendelkezésre álló parancsokról [*: útmutató: az Azure digitális Twins parancssori*](how-to-use-cli.md)felületének használata.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>További lépések

További információ a fogadott üzenetek különböző típusairól:
* [*Útmutató: az események értelmezése*](how-to-interpret-event-data.md)
