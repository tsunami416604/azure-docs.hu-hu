---
title: Végpontok és útvonalak kezelése (API-k és parancssori felület)
titleSuffix: Azure Digital Twins
description: 'Lásd: végpontok és esemény-útvonalak beállítása és kezelése az Azure Digital Twins-adatszolgáltatásokhoz.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 10/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ce922e3ce39bc3df9f4c242558644922e5713300
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494814"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Végpontok és útvonalak kezelése az Azure Digital Twinsban (API-k és parancssori felület)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Az Azure Digital Twins-ban átirányíthatja az [eseményekre vonatkozó értesítéseket](how-to-interpret-event-data.md) az alsóbb rétegbeli szolgáltatásokhoz vagy a csatlakoztatott számítási erőforrásokhoz. Ezt úgy teheti meg, hogy először beállítja az eseményeket fogadó **végpontokat** . Ezután létrehozhat olyan  [**esemény-útvonalakat**](concepts-route-events.md) , amelyekkel megadhatja, hogy az Azure Digital Twins mely eseményeit adja meg a végpontok.

A végpontok és útvonalak az [Event Routes API](/rest/api/digital-twins/dataplane/eventroutes)-kkal, a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)-val vagy az [Azure digitális Twins parancssori](how-to-use-cli.md)felülettel kezelhetők. Ez a cikk végigvezeti a végpontok és útvonalak ezen mechanizmusok használatával történő létrehozásának folyamatán.

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

Az alábbi példa bemutatja, hogyan hozhat létre Event Grid típusú végpontot az Azure CLI használatával. [Azure Cloud Shell](https://shell.azure.com)használhat, vagy helyileg is [TELEPÍTheti a CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)-t.

Először hozzon létre egy Event Grid-témakört. Használhatja az alábbi parancsot, vagy további részletekben tekintse meg a lépéseket. Ehhez látogasson el a Event Grid *Egyéni események* rövid útmutatójának [ *Egyéni témakör létrehozása* szakaszára](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) .

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Futtassa a következő parancsot az Azure-régiók azon neveinek listájához, amelyek átadhatók az Azure CLI parancsaihoz:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Miután létrehozta a témakört, összekapcsolhatja azt az Azure Digital Twins-val a következő [Azure digitális Twins CLI-paranccsal](how-to-use-cli.md):

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Az Event Grid témakör az Azure Digital Twins-beli végpontként érhető el, az `--endpoint-name` argumentummal megadott néven. Ezt a nevet általában egy **esemény-útvonal**céljaként fogja használni, amelyet [később a jelen cikkben](#create-an-event-route) , az Azure Digital Twins szolgáltatás API használatával hozhat létre.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Event Hubs vagy Service Bus végpont létrehozása

Event Hubs vagy Service Bus végpontok létrehozásának folyamata hasonló a fent látható Event Grid folyamathoz.

Először hozza létre a végpontként használni kívánt erőforrásokat. A következők szükségesek:
* Service Bus: _Service Bus névtér_, _Service Bus témakör_, _engedélyezési szabály_
* Event Hubs: _Event Hubs névtér_, _Event hub_, _engedélyezési szabály_

Ezután használja a következő parancsokat a végpontok létrehozásához az Azure Digital Twinsban: 

* Service Bus témakör végpontjának hozzáadása (egy előre létrehozott Service Bus erőforrást igényel)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event Hubs végpont hozzáadása (előre létrehozott Event Hubs erőforrást igényel)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Végpont létrehozása a kézbesítetlen levelekkel

Ha egy végpont nem tud eseményt kézbesíteni egy adott időszakon belül, vagy ha az eseményt bizonyos számú alkalommal próbálta kézbesíteni, akkor a kézbesítetlen eseményt elküldheti egy Storage-fiókba. Ezt a folyamatot **Kézbesítetlen levélnek**nevezzük.

Ha olyan végpontot szeretne létrehozni, amelyen engedélyezve van a kézbesítetlen üzenetek használata, a végpont létrehozásához a [ARM API-kat](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) kell használnia. 

A kézbesítetlen levelek helyének beállítása előtt egy tárolóval rendelkező Storage-fiókkal kell rendelkeznie. Adja meg a tároló URL-címét a végpont létrehozásakor. A kézbesítetlen levél egy SAS-tokent tároló URL-ként van megadva. A jogkivonat csak a `write` Storage-fiókban lévő cél tárolóra vonatkozó engedélyre van szüksége. A teljesen formázott URL-cím a (z) formátumban jelenik meg: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

További információ az SAS-tokenekről: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](/azure/storage/common/storage-sas-overview)

A kézbesítetlen levelekről további információt a [*fogalmak: események útvonalai*](concepts-route-events.md#dead-letter-events)című témakörben talál.

#### <a name="configuring-the-endpoint"></a>A végpont konfigurálása

Végpont létrehozásakor vegyen fel egy `deadLetterSecret` objektumot a `properties` kérelem törzsébe, amely tartalmazza a Storage-fiókhoz tartozó tároló-URL-címet és Sas-jogkivonatot.

```json
{
  "properties": {
    "endpointType": "EventGrid",
    "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
    "accessKey1": "xxxxxxxxxxx",
    "accessKey2": "xxxxxxxxxxx",
    "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
  }
}
```

További információkért tekintse meg az Azure digitális Twins REST API dokumentációját: [endpoints-DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Üzenet tárolási sémája

A kézbesítetlen üzenetek a következő formátumban lesznek tárolva a Storage-fiókban:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

A kézbesítetlen üzenetek megegyeznek az eredeti végpontnak kézbesíteni kívánt eredeti esemény sémájával.

Itt látható egy példa a [kettős létrehozási értesítésre](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Esemény útvonalának létrehozása

Az Azure digitális Twins-ból egy végpontba küldött adatok tényleges elküldéséhez meg kell határoznia egy **esemény-útvonalat**. Az Azure Digital Twins **EventRoutes API-jai** lehetővé teszik a fejlesztők számára, hogy az események áramlását a rendszeren és az alárendelt szolgáltatásokon keresztül. További információk az esemény-útvonalakról a [*fogalmakban: az Azure Digital Twins eseményeinek továbbítása*](concepts-route-events.md).

Az ebben a szakaszban szereplő minták a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)-t használják.

**Előfeltétel**: a cikk korábbi részében leírtak szerint hozzon létre végpontokat, mielőtt továbblép az útvonal létrehozásához. Ha befejezte a végpontok beállítását, folytassa az esemény-útvonal létrehozásával.

>[!NOTE]
>Ha nemrég telepítette a végpontokat, ellenőrizze, hogy készen állnak-e a telepítésre, **mielőtt** új esemény-útvonalra próbálja használni őket. Ha az útválasztás telepítése sikertelen, mert a végpontok nem állnak készen, várjon néhány percet, és próbálkozzon újra.
>
> Ha a folyamat futtatásakor ezt a folyamatot szeretné használni, akkor érdemes lehet ezt megvárnia, ha 2-3 perc várakozási idő alatt a végponti szolgáltatás befejezi a telepítést, mielőtt továbblép az útvonal beállítására.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Kód létrehozása API-kkal és C# SDK-val

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
er.Filter = "true"; //Filter allows all messages
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
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
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

## <a name="filter-events"></a>Események szűrése

Szűrés nélkül a végpontok számos eseményt kapnak az Azure Digital ikrektől:
* Az Azure Digital Twins szolgáltatás API-ját használó [digitális Twins](concepts-twins-graph.md) telemetria
* A Twin Property változási értesítései az Azure Digital Twins-példányon található bármelyik Twin tulajdonság változásakor
* Életciklussal kapcsolatos események, az ikrek vagy kapcsolatok létrehozásakor vagy törlésekor

Az elküldött eseményeket úgy korlátozhatja, hogy hozzáad egy **szűrőt** egy végponthoz az esemény-útvonalhoz.

Szűrő hozzáadásához használhat egy PUT-kérést a *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-10-31* értékre a következő törzstel:

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

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Következő lépések

További információ a fogadott üzenetek különböző típusairól:
* [*Útmutató: az események értelmezése*](how-to-interpret-event-data.md)