---
title: Eseményútvonalak
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan irányíthatja az eseményeket az Azure Digital Twins szolgáltatásban és más Azure-szolgáltatásokba.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 96da89fa8d7e4783afa11807534bbaeba52b79fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334259"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Események irányítása az Azure digitális Twins-n belül és kívül

Az Azure Digital Twins **esemény-útvonalakat** használ az adatküldés céljából a szolgáltatáson kívüli felhasználók számára. 

Az előzetes verzióban két fő eset áll rendelkezésre az Azure Digital Twins-adatok küldéséhez:
* Adatok küldése az Azure digitális Twins gráfból egy másikba. Ha például egy adott digitális kettős változásra vonatkozó tulajdonságot módosít, érdemes lehet értesíteni és frissíteni egy másik digitális dupla, ennek megfelelően.
* Adatok küldése a downstream adatszolgáltatásoknak további tárhely vagy feldolgozás céljából (más néven *adatforgalom*). Például:
  - A kórház az Azure Digital Twins-események [Time Series Insights (ÁME)](../time-series-insights/time-series-insights-update-overview.md)szolgáltatásba való küldéséhez érdemes lehet a tömeges elemzéshez kapcsolódó, kézmosással kapcsolatos események idősoros adatainak rögzítése.
  - Egy olyan vállalat, amely már használ [Azure Maps](../azure-maps/about-azure-maps.md) , érdemes lehet az Azure digitális Twins használatával javítani a megoldást. Az Azure digitális Twins beállítása után gyorsan engedélyezhetik az Azure Map-t, hogy az Azure Map-entitásokat [digitális ikrekként](concepts-twins-graph.md) használják a Twin Graphban, vagy futtasson hatékony lekérdezéseket a Azure Maps és az Azure digitális Twins-adatszolgáltatások használatával.

Az esemény-útvonalak mindkét forgatókönyv esetében használhatók.

## <a name="about-event-routes"></a>Az események útvonala

Az Event Route lehetővé teszi, hogy az Azure Digital Twins digitális Twins-beli eseményeinek adatait az előfizetések egyéni meghatározott végpontjai számára küldje el. Jelenleg három Azure-szolgáltatás támogatott a végpontok számára: [Event hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)és [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Az Azure-szolgáltatások mindegyike kapcsolódhat más szolgáltatásokhoz, és a legközelebb álló adatokhoz, például az ÁME-hez vagy a Azure Mapshoz is elküldheti a szükséges feldolgozást.

Az alábbi ábrán az Event-adatforgalom egy nagyobb IoT-megoldáson keresztül látható az Azure digitális Twins aspektusával:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Az Azure digitális Twins több alsóbb rétegbeli szolgáltatás felé irányítja át a végpontokon keresztüli adattovábbítást" border="false":::

Az esemény-útvonalakra jellemző alsóbb rétegbeli célok olyan erőforrások, mint az ÁME, a Azure Maps, a Storage és az elemzési megoldások.

### <a name="event-routes-for-internal-digital-twin-events"></a>Esemény-útvonalak belső digitális kettős eseményekhez

Az aktuális előzetes kiadásban az esemény-útvonalak a Twin gráfon belüli események kezelésére is használhatók, és az adatok digitális Twin-ről digitális Twin-re való küldésére is sor kerül. Ezt úgy teheti meg, hogy összekapcsolja az esemény-útvonalakat Event Grid a számítási erőforrások, például a [Azure functions](../azure-functions/functions-overview.md)használatával. Ezek a függvények határozzák meg, hogy az ikrek hogyan fogadhatnak és reagálnak az eseményekre. 

Ha egy számítási erőforrás módosítani szeretné a Twin gráfot egy esemény-útvonalon keresztül kapott esemény alapján, akkor hasznos, ha tudni szeretné, hogy az idő előtt milyen kettős módosításokat kíván módosítani. 

Azt is megteheti, hogy az üzenet az üzenetet küldő forrás-iker AZONOSÍTÓját is tartalmazza, így a számítási erőforrások lekérdezéseket használhatnak, illetve a kapcsolatok bejárásával megkereshetik a kívánt művelethez tartozó dupla célt. 

A számítási erőforrásnak emellett a biztonsági és hozzáférési engedélyeket egymástól függetlenül kell létrehoznia.

Az Azure-függvények digitális kettős események feldolgozására való beállításának folyamatát lásd: [*útmutató: Azure-függvény beállítása az adat feldolgozásához*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Végpont létrehozása

Az események útvonalának definiálásához a fejlesztőknek először meg kell határozniuk a végpontokat. A **végpont** az Azure Digital ikreken kívüli cél, amely támogatja az útvonal-kapcsolatokat. Az aktuális előzetes verzióban támogatott célhelyek a következők:
* Egyéni témakörök Event Grid
* Eseményközpont
* Service Bus

A végpontokat a vezérlési sík API-k (az [Azure digitális Twins parancssori](how-to-use-cli.md)felület vagy a Azure Portal segítségével lehet beállítani. A végpont definíciója a következőket biztosítja:
* A végpont neve
* A végpont típusa (Event Grid, Event hub vagy Service Bus)
* A hitelesítő elsődleges kapcsolódási sztring és másodlagos kapcsolódási sztring 
* A végpont témakörének elérési útja, például *Your-topic.westus2.eventgrid.Azure.net*

A vezérlési síkon elérhető Endpoint API-k a következők:
* Végpont létrehozása
* Végpontok listájának beolvasása
* Végpont beolvasása név alapján
* Végpont törlése név szerint

## <a name="create-an-event-route"></a>Esemény útvonalának létrehozása
 
Az események útvonalait egy ügyfélalkalmazás hozza létre. Ennek egyik módja a `CreateEventRoute` [.net (C#) SDK](how-to-use-apis-sdks.md) -hívás: 

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

1. Először létrejön egy `EventRoute` objektum, és a konstruktor a végpont nevét veszi át. Ez a `endpointName` mező egy végpontot azonosít, például egy Event hub, Event Grid vagy Service Bus. Ezeket a végpontokat az előfizetésében kell létrehoznia, és az Azure Digital Twins-hoz kell csatolni a vezérlési sík API-kkal a regisztrációs hívás előtt.

2. Az Event Route objektumhoz tartozik egy [**szűrő**](./how-to-manage-routes-apis-cli.md#filter-events) mező is, amely az útvonalat követő események típusának korlátozására használható. A szűrője `true` lehetővé teszi az útvonal további szűrés nélküli használatát (a szűrő `false` letiltja az útvonalat). 

3. Ekkor a rendszer átadja ezt az esemény `CreateEventRoute` -útválasztási objektumot, valamint az útvonal nevét.

> [!TIP]
> Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

Az útvonalakat az [Azure Digital Twins CLI](how-to-use-cli.md)használatával is létrehozhatja.

### <a name="types-of-event-messages"></a>Az esemény típusú üzenetek típusai

A IoT Hub és az Azure Digital Twins különböző típusú eseményei különböző típusú értesítési üzeneteket hoznak létre az alább leírtak szerint.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Következő lépések

Lásd: események útvonalának beállítása és kezelése:
* [*Útmutató: végpontok és útvonalak kezelése*](how-to-manage-routes-apis-cli.md)

Vagy tekintse meg a Azure Functions használata az események útválasztásához az Azure digitális Ikrekben:
* [*Útmutató: Azure-függvény beállítása az adat feldolgozásához*](how-to-create-azure-function.md)