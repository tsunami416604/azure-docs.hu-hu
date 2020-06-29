---
title: Végpontok és útvonalak kezelése
titleSuffix: Azure Digital Twins
description: 'Lásd: végpontok és esemény-útvonalak beállítása és kezelése az Azure Digital Twins-adatszolgáltatásokhoz.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d6ecc2ddab7bc0fa739989e9cfdc2645cc1ccb27
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85476903"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Végpontok és útvonalak kezelése az Azure digitális Ikrekben

Az Azure Digital Twins-ban átirányíthatja az [eseményekre vonatkozó értesítéseket](how-to-interpret-event-data.md) az alsóbb rétegbeli szolgáltatásokhoz, vagy kapcsolódhat a számítási erőforrásokhoz. Ezt úgy teheti meg, hogy először olyan **végpontokat** állít be, amelyek megkapják az eseményeket, majd azokat az [**esemény-útvonalakat**](concepts-route-events.md) , amelyek meghatározzák, hogy az Azure Digital Twins mely eseményeit kapja meg a végpontok.

A támogatott végpontok típusai a következők:
* [Eseményközpont](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

További információ a különböző végpontokról: [választás az Azure Messaging Services között](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

A végpontokat és útvonalakat a [**EventRoutes API**](how-to-use-apis-sdks.md)-k, a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)vagy az [Azure digitális Twins parancssori](how-to-use-cli.md)felülete kezeli. A [Azure Portal](https://portal.azure.com)is kezelhetők.

> [!NOTE]
> Az események útvonalának Azure Portalon keresztüli kezelése jelenleg csak az Azure-felhasználók számára érhető el a vállalati-tartományi fiókokban. Ha személyes [Microsoft-fiók (MSA)](https://account.microsoft.com/account/Account)használ, használja az Azure Digital Twins API-kat vagy a CLI-t az események útvonalának kezeléséhez a jelen cikkben leírtak szerint.

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Végpont létrehozása az Azure Digital Twins számára

Ahhoz, hogy egy végpontot az Azure digitális Ikrekhöz lehessen kapcsolni, az Event hub, az Event Grid témakör vagy Service Bus, amelyet a végponthoz használ, már léteznie kell. 

Az alábbi példa bemutatja, hogyan hozhat létre egy Event Grid-témakört az Azure CLI használatával:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Futtassa a következő parancsot az Azure-régiók azon neveinek listájához, amelyek átadhatók az Azure CLI parancsaihoz:
> ```azurecli
> az account list-locations -o table
> ```

Miután létrehozta a témakört, összekapcsolhatja azt az Azure Digital Twins-val a következő paranccsal:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Így az Event Grid-témakör elérhetővé válik az Azure digitális Twins-ban, az `--endpoint-name` argumentummal megadott néven. Ezt a nevet általában egy **esemény-útvonal**céljaként fogja használni, amelyet a következő szakaszban fog létrehozni az Azure Digital Twins szolgáltatás API használatával.

Az Event hub és a Service Bus végpontok egyenértékű parancsai léteznek:

* Service Bus témakör végpontjának hozzáadása (egy előre létrehozott Service Bus erőforrást igényel)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event hub-végpont hozzáadása (előre létrehozott Event hub-erőforrást igényel)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Események útvonalának kezelése API-kkal

Az Azure digitális Twins-ból egy végpontba küldött adatok tényleges küldéséhez meg kell határoznia egy esemény-útvonalat. Az Azure Digital Twins **EventRoutes API-jai** lehetővé teszik a fejlesztők számára, hogy az események áramlását a rendszeren és az alárendelt szolgáltatásokon keresztül. További információk az esemény-útvonalakról a [fogalmakban: az Azure Digital Twins eseményeinek továbbítása](concepts-route-events.md).

A cikkben szereplő minták a C# SDK-t használják.

Az események útvonala az adatsík API-k használatával van definiálva. Az útvonalak definíciója a következő elemeket tartalmazza:
* A használni kívánt útvonal-azonosító
* A használni kívánt végpont neve
* Egy szűrő, amely meghatározza, hogy mely eseményeket küldi a rendszer a végpontnak. 

Ha nincs útvonal-azonosító, a rendszer nem irányítja át az üzeneteket az Azure digitális Twins szolgáltatáson kívül. Ha van egy útvonal-azonosító, és a szűrő `true` , az összes üzenet a végponthoz lesz irányítva. Ha van egy útvonal-azonosító, és egy másik szűrő van hozzáadva, az üzenetek a szűrő alapján lesznek szűrve.

Az egyik útvonalnak engedélyezni kell több értesítés és eseménytípus kijelölését. 

`CreateEventRoute`egy esemény-útvonal hozzáadására szolgáló SDK-hívás. Íme egy példa a használatra:

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

Az elküldött eseményeket korlátozhatja egy szűrő egy végponthoz való hozzáadásával.

Szűrő hozzáadásához használjon PUT-kérést a *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview-* ra a következő törzstel:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Itt láthatók a támogatott útválasztási szűrők.

| Szűrő neve | Leírás | Séma szűrése | Támogatott értékek | 
| --- | --- | --- | --- |
| Típus | A digitális kettős példányon keresztül áramló [esemény típusa](./concepts-route-events.md#types-of-event-messages) | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Forrás | Az Azure Digital Twins-példány neve | `"filter" : "source = '<hostname>'"`|  **Értesítésekhez**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Telemetria esetén**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Tárgy | Az esemény leírása a fenti eseményforrás kontextusában | `"filter": " subject = '<subject>'"` | **Értesítésekhez**: a tulajdonos`<twinid>` <br> vagy a témák URI-formátuma, amelyeket a több rész vagy azonosító egyedileg azonosít:<br>`<twinid>/relationships/<relationshipid>`<br> **Telemetria esetén**: a tulajdonos az összetevő elérési útja (ha a telemetria egy Twin összetevőből származik), például: `comp1.comp2` . Ha a telemetria nem egy összetevőből származik, akkor a tárgy mezőjének üresnek kell lennie. |
| Adatséma | DTDL modell azonosítója | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Telemetria esetében**: az Adatséma a Twin vagy a telemetria kibocsátó összetevő modell-azonosítója. <br>**Értesítések esetén**: az Adatséma nem támogatott|
| Tartalomtípus | Adatérték tartalomtípusa | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Spec verziója | Az Ön által használt esemény-séma verziója | `"filter": "specversion = '<version>'"` | Kell lennie `1.0` . Ez a CloudEvents séma 1,0-es verzióját jelzi. |
| Igaz/hamis | Lehetővé teszi az útvonalak szűrés nélküli létrehozását vagy az útvonal letiltását. | `"filter" : "<true/false>"` | `true`= az útvonal engedélyezve van szűrés nélkül <br> `false`= az útvonal le van tiltva |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

A szűrők a következő műveletek használatával is kombinálhatók:

| Szűrő neve | Séma szűrése | Példa | 
| --- | --- | --- |
| ÉS/VAGY | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| ÉS/VAGY | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Beágyazott műveletek | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Az előzetes verzióban csak a karakterlánc-egyenlőség támogatott (=,! =).

Egy szűrő implementálása vagy frissítése során a módosítás néhány percet is igénybe vehet, hogy az adatfolyamatot tükrözze.

## <a name="manage-endpoints-and-routes-with-cli"></a>Végpontok és útvonalak kezelése a CLI-vel

A végpontok és útvonalak az Azure Digital Twins CLI használatával is kezelhetők. A parancsok a következő [útmutatóban találhatók: az Azure digitális Twins parancssori](how-to-use-cli.md)felületének használata.

## <a name="monitor-event-routes"></a>Események útvonalának figyelése

Az útválasztási metrikák, például a darabszám, a késés és a hibák aránya a [Azure Portal](https://portal.azure.com/)tekinthető meg. 

A portál kezdőlapján keresse meg az Azure Digital Twins-példányát, és adja meg a részleteit. Válassza ki a **mérőszámok** lehetőséget az Azure Digital Twins-példány menüjében a *metrikák* lap megjelenítéséhez.

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="A Azure Portalban található Azure Digital Twins-példány metrikáinak lapja":::

Itt megtekintheti a példány metrikáit, és létrehozhat egyéni nézeteket is.

## <a name="next-steps"></a>További lépések

További információ a fogadott üzenetek különböző típusairól:
* [Útmutató: az események értelmezése](how-to-interpret-event-data.md)
