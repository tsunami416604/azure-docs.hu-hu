---
title: Az Azure IoT Hub üzenetútválasztásának ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató - hogyan használható az üzenet-útválasztás eszközről a felhőbe irányuló üzenetek küldésére. Telemetriai és nem telemetriai adatok küldésével kapcsolatos információkat tartalmaz.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370457"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Az IoT Hub üzenet-útválasztásának használata eszközről felhőbe irányuló üzenetek küldésére különböző végpontokra

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az üzenet-útválasztás lehetővé teszi, hogy automatikus, méretezhető és megbízható módon küldjön üzeneteket az eszközeiről a felhőszolgáltatásokba. Az üzenettovábbítás a következő célokra használható: 

* **Eszköz telemetriai üzenetek küldése, valamint az események** nevezetesen, eszköz életciklus-események és az eszköz iker változásesemények a beépített végpont és egyéni végpontok. További információ [az útválasztási végpontokról.](#routing-endpoints)

* **Adatok szűrése, mielőtt különböző végpontokra irányítana** rich queries alkalmazásával. Az üzenet-útválasztás lehetővé teszi az üzenet tulajdonságainak és az üzenettörzsnek, valamint az ikercímkéknek és az ikereszköz-tulajdonságoknak a lekérdezését. További információ a [lekérdezések üzenettovábbításban](iot-hub-devguide-routing-query-syntax.md)való használatáról.

Az IoT Hubnak írási hozzáféréssel kell rendelkeznie ezekhez a szolgáltatásvégpontokhoz az üzenet-útválasztás működéséhez. Ha konfigurálja a végpontok az Azure Portalon keresztül, a szükséges engedélyeket adja hozzá az Ön számára. Győződjön meg arról, hogy konfigurálja a szolgáltatásokat, hogy támogassa a várt átviteli. Ha például az Event Hubs-ot egyéni végpontként használja, konfigurálnia kell az adott eseményközpont **átviteli egységeit,** hogy kezelni tudja az IoT Hub-üzenet-útválasztáson keresztül küldeni kívánt események be- és éi. Hasonlóképpen, ha egy service bus-várólistát használ végpontként, konfigurálnia kell a **maximális méretet** annak érdekében, hogy a várólista képes legyen az összes adat be- ésbeszállítva, amíg a fogyasztók ki nem lépnek. Az IoT-megoldás első konfigurálásakor előfordulhat, hogy figyelnie kell a további végpontokat, és el kell végeznie a szükséges módosításokat a tényleges terheléshez.

Az IoT Hub közös [formátumot](iot-hub-devguide-messages-construct.md) határoz meg az összes eszközről a felhőbe irányuló üzenetküldéshez a protokollok közötti együttműködés érdekében. Ha egy üzenet több, ugyanarra a végpontra mutató útvonalnak felel meg, az IoT Hub csak egyszer kézbesíti az üzenetet a végpontnak. Ezért nem kell konfigurálnia a deduplikációt a Service Bus várólistáján vagy témakörén. Particionált várólistákban a partíció-affinitás garantálja az üzenetrendezést. Az oktatóanyag ból megtudhatja, hogyan konfigurálható az [üzenet-útválasztás.](tutorial-routing.md)

## <a name="routing-endpoints"></a>Útválasztási végpontok

Az IoT-központ rendelkezik egy alapértelmezett beépített végpont (**üzenetek/események**), amely kompatibilis az Event Hubs. [Egyéni végpontokat](iot-hub-devguide-endpoints.md#custom-endpoints) hozhat létre az üzenetek továbbításához az előfizetésben lévő más szolgáltatások és az IoT Hub összekapcsolásával. 

Minden üzenet minden olyan végponthoz kerül, amelynek útválasztási lekérdezései megegyeznek. Más szóval egy üzenet több végpontra is átirányítható.

Az IoT Hub jelenleg a következő szolgáltatásokat támogatja egyéni végpontként:

### <a name="built-in-endpoint"></a>Beépített végpont

A szabványos [Event Hubs integráció és az SDK-k](iot-hub-devguide-messages-read-builtin.md) segítségével fogadhat eszközről felhőbe irányuló üzeneteket a beépített végpontról (**üzenetek/események).** Az útvonal létrehozása után az adatok leállnak a beépített végpontra, kivéve, ha egy útvonal jön létre az adott végponthoz.

### <a name="azure-storage"></a>Azure Storage

Két tárolási szolgáltatás IoT Hub képes üzeneteket küldeni - [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) és az Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) fiókok. Az Azure Data Lake Storage-fiókok [hierarchikus névtér-kompatibilis](../storage/blobs/data-lake-storage-namespace.md)tárfiókok, amelyek a blob storage-on alapulnak. Mindkét blobok a tároló.

Az IoT Hub támogatja az adatok írását az Azure Storage-ba [Apache Avro](https://avro.apache.org/) formátumban és JSON formátumban. Az alapértelmezett érték az AVRO. A kódolási formátum csak akkor állítható be, ha a blob storage-végpont konfigurálva van. A formátum nem szerkeszthető meglévő végponthoz. JSON-kódolás használatakor a contentType-ot **alkalmazás/json** ra, a contentEncoding-t pedig **UTF-8-ra** kell állítania az [üzenetrendszer tulajdonságai](iot-hub-devguide-routing-query-syntax.md#system-properties)között. Mindkét érték nem tartalmaz kis- és nagybetűket. Ha a tartalomkódolás nincs beállítva, majd az IoT Hub alap 64 kódolású formátumban írja az üzeneteket. A kódolási formátumot az IoT Hub létrehozása vagy rest-frissítése API-val választhatja ki, különösen a [RoutingStorageContainerProperties,](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)az Azure Portal, az [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)vagy az [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)használatával. Az alábbi ábrán bemutatja, hogyan választhatja ki a kódolási formátumot az Azure Portalon.

![Blob-tárolóvégpont-kódolás](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Az IoT Hub kötegeli az üzeneteket, és adatokat ír a tárolóba, amikor a köteg elér egy bizonyos méretet vagy egy bizonyos idő eltelte. Az IoT Hub alapértelmezés szerint a következő fájlelnevezési konvenciókat követi: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Bármilyen fájlelnevezési konvenciót használhat, azonban az összes felsorolt jogkivonatot használnia kell. Az IoT Hub egy üres blobba fog írni, ha nincs enek írandó adat.

Azt javasoljuk, hogy felsorolja a blobok vagy fájlok, majd iterálás felettük, annak érdekében, hogy minden blobok vagy fájlok olvasása nélkül a partíció feltételezéseket. A partíciótartomány potenciálisan változhat a [Microsoft által kezdeményezett feladatátvétel](iot-hub-ha-dr.md#microsoft-initiated-failover) vagy az IoT Hub [manuális feladatátvételsorán.](iot-hub-ha-dr.md#manual-failover) A List [Blobs API segítségével](https://docs.microsoft.com/rest/api/storageservices/list-blobs) felsorolhatja a blobok listáját, vagy [lista ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) a fájlok listáját. Kérjük, tekintse meg a következő mintát útmutatásként.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

> [!NOTE]
> Ha a tárfiók rendelkezik olyan tűzfal-konfigurációkkal, amelyek korlátozzák az IoT Hub kapcsolatát, fontolja meg [a Microsoft megbízható, első féltől származó kivétel](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) használatát (amely a felügyelt szolgáltatásidentitással rendelkező IoT-központok egyes régióiban érhető el).

Azure Data Lake Gen2-kompatibilis tárfiók létrehozásához hozzon létre egy új V2-tárfiókot, és válassza *az engedélyezve lehetőséget* a **Speciális** lap *Hierarchikus névtér* mezőjében, ahogy az az alábbi képen látható:

![Válassza ki az Azure Date Lake Gen2 tárolót](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Szolgáltatásbusz-várólisták és szolgáltatásbusz-témakörök

Az IoT Hub-végpontként használt Service Bus-várólisták és témakörök nem rendelkeznek **munkamenetek** vagy **duplikáltelem-észlelés** idoszakokkal. Ha ezek közül bármelyik lehetőség engedélyezve van, a végpont **elérhetetlenként** jelenik meg az Azure Portalon.

> [!NOTE]
> Ha a szolgáltatásbusz-erőforrás olyan tűzfal-konfigurációkkal rendelkezik, amelyek korlátozzák az IoT Hub kapcsolatát, fontolja meg [a Microsoft megbízható, első féltől származó kivétel](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) használatát (amely a felügyelt szolgáltatásidentitással rendelkező IoT-központok egyes régióiban érhető el).


### <a name="event-hubs"></a>Event Hubs

A beépített eseményközpontok kompatibilis végponton kívül az adatokat eseményközpontok típusú egyéni végpontokra is továbbíthatja. 

> [!NOTE]
> Ha az eseményközpontok erőforrás tűzfal-konfigurációk, amelyek korlátozzák az IoT Hub kapcsolatát, fontolja meg a [Microsoft megbízható első fél kivétel](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) (elérhető egyes régiókban ioT hubok felügyelt szolgáltatásidentitással).


## <a name="reading-data-that-has-been-routed"></a>Irányított adatok olvasása

Az útvonalat az [oktatóanyag](tutorial-routing.md)követésével állíthatja be.

Az alábbi oktatóanyagok segítségével megtudhatja, hogyan olvashat ja az üzeneteket egy végpontról.

* Olvasás [beépített végpontról](quickstart-send-telemetry-node.md)

* Olvasás a [Blob storage-ból](../storage/blobs/storage-blob-event-quickstart.md)

* Olvasás [az Eseményközpontokból](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Olvasás [a szolgáltatásbusz-várólistákból](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Olvasás a [Service Bus témaköreiből](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Tartalék útvonal

A tartalék útvonal az összes olyan üzenetet elküldi, amely nem felel meg a lekérdezési feltételeknek a meglévő útvonalakon a beépített eseményközpontoknak (**üzeneteknek/eseményeknek**), amelyek kompatibilisek az [Event Hubs](/azure/event-hubs/)rendszerrel. Ha az üzenet-útválasztás be van kapcsolva, engedélyezheti a tartalék útvonal funkciót. Az útvonal létrehozása után az adatok leállnak a beépített végpontra, kivéve, ha létrejön egy útvonal az adott végponthoz. Ha nincsenek útvonalak a beépített végponthoz, és a tartalék útvonal engedélyezve van, csak az útvonalakon semmilyen lekérdezési feltételeknek nem megfelelő üzeneteket küld a rendszer a beépített végpontnak. Ha az összes meglévő útvonal törlődik, a tartalék útvonalat engedélyezni kell az összes adat fogadásához a beépített végponton.

Engedélyezheti/letilthatja a tartalék útvonalat az Azure Portal->Message Routing panelen. Az Azure Resource Manager for [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) egyéni végpontot is használhat a tartalék útvonalhoz.

## <a name="non-telemetry-events"></a>Nem telemetriai események

Az eszköztelemetria mellett az üzenet-útválasztás lehetővé teszi az eszköz ikermódosítási eseményeinek, az eszköz életciklus-eseményeinek és a digitális ikermódosítási események küldését is (nyilvános előzetes verzióban). Ha például egy útvonal at jön létre az adatforrás beállítása **az eszköz iker változási események,** az IoT Hub üzeneteket küld a végpont, amely tartalmazza a változást az ikereszköz. Hasonlóképpen, ha egy útvonal jön létre az **eszköz életciklus-eseményeihez**beállított adatforrás-készlettel, az IoT Hub üzenetet küld, jelezve, hogy az eszközt törölték vagy létrehozták. Végül az [IoT Plug and Play nyilvános előzetes verzió](../iot-pnp/overview-iot-plug-and-play.md)részeként a fejlesztők létrehozhatnak olyan útvonalakat, amelyek adatforrása digitális **ikermódosítási eseményekre** van beállítva, és az IoT Hub üzeneteket küld, amikor egy digitális [ikertulajdonságot](../iot-pnp/iot-plug-and-play-glossary.md) beállítanak vagy módosítanak, egy [digitális ikertestvért](../iot-pnp/iot-plug-and-play-glossary.md) cserélnek le, vagy ha az alapul szolgáló ikereszköz nél változási esemény történik.

[Az IoT Hub az Azure Event Griddel is integrálható](iot-hub-event-grid.md) az eszközesemények közzétételéhez, hogy támogassa a valós idejű integrációkat és a munkafolyamatok automatizálását ezen események alapján. Tekintse meg az [üzenet-útválasztás és az Eseményrács közötti főbb különbségeket,](iot-hub-event-grid-routing-comparison.md) hogy megtudja, melyik a legmegfelelőbb a forgatókönyvnek.

## <a name="testing-routes"></a>Vizsgálati útvonalak

Amikor új útvonalat hoz létre, vagy egy meglévő útvonalat szerkeszt, az útvonallekérdezést mintaüzenettel kell tesztelni. Az egyes útvonalakat tesztelheti, vagy az összes útvonalat egyszerre tesztelheti, és a teszt során egyetlen üzenet sem kerül a végpontokhoz. Az Azure Portal, az Azure Resource Manager, az Azure PowerShell és az Azure CLI tesztelésre használható. Az eredmények segítenek azonosítani, hogy a mintaüzenet megfelelt-e a lekérdezésnek, az üzenet nem felelt meg a lekérdezésnek, vagy a teszt nem futtatva, mert a mintaüzenet vagy a lekérdezés szintaxisa helytelen. További információ: [Útvonal tesztelése](/rest/api/iothub/iothubresource/testroute) és [az összes útvonal tesztelése](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Rendelési garanciák legalább egyszer szállítás

Az IoT Hub-üzenet-útválasztási garanciák at rendezett, és legalább egyszer az üzenetek kézbesítése a végpontok. Ez azt jelenti, hogy lehetnek ismétlődő üzenetek, és egy sor üzenet újraítható az eredeti üzenetrendelés tiszteletben fogadására. Ha például az eredeti üzenetsorrend [1,2,3,4], akkor egy olyan üzenetsorozatot kaphat, mint például [1,2,1,2,3,1,2,3,4]. A rendelési garancia az, hogy ha valaha is üzenetet kap [1], azt mindig [2,3,4] követi.

Az üzenetek duplikálásának kezeléséhez azt javasoljuk, hogy az üzenet alkalmazástulajdonságaiban egy egyedi azonosítót bélyegzsenek a származási helyen, amely általában egy eszköz vagy egy modul. Az üzeneteket használó szolgáltatás ezzel az azonosítóval képes kezelni az ismétlődő üzeneteket.

## <a name="latency"></a>Késés

Ha az eszköz-felhő telemetriai üzeneteket beépített végpontok használatával továbbítja, az első útvonal létrehozása után a végpontok közötti késés kismértékben növekszik.

A legtöbb esetben a késés átlagos növekedése kevesebb, mint 500 ms. Figyelheti a **késést az Útválasztás: üzenetek/események üzenetkésés e** vagy **d2c.endpoints.latency.builtIn.events** IoT Hub metrika használatával. Bármely útvonal létrehozása vagy törlése az első után nincs hatással a végpontok késésére.

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás

Az IoT Hub számos, az útválasztáshoz és a végpontokhoz kapcsolódó metrikát biztosít, hogy áttekintést nyújtson a központ és az elküldött üzenetek állapotáról. Több metrikából származó információk kombinálásával azonosíthatja a problémák kiváltó okait. Például használja **metrika útválasztás: telemetriai üzenetek eldobott** vagy **d2c.telemetry.egress.dropped** azonosítani az üzenetek számát, amelyek eldobták, ha nem egyezik lekérdezések egyik útvonalon, és tartalék útvonal le van tiltva. [Az IoT Hub-metrikák](iot-hub-metrics.md) felsorolja az összes metrikák, amelyek alapértelmezés szerint az IoT Hub.

A REST API [Végpontok begetése állapot](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) a végpontok [állapotának](iot-hub-devguide-endpoints.md#custom-endpoints) lehívásához használhatja. Azt javasoljuk, hogy az [IoT Hub metrikák](iot-hub-metrics.md) az útválasztási üzenet késése a hibák azonosításához és hibakereséséhez, ha a végpont állapota halott vagy nem kifogástalan. Az Event Hubs végponttípus nál például figyelheti **a d2c.endpoints.latency.eventHubs mezőt.** Egy nem megfelelő állapotú végpont állapota kifogástalan állapotú lesz, ha az IoT Hub létrehozott egy végül konzisztens állapot.

Az **útvonalak** diagnosztikai naplók használatával az Azure Monitor [diagnosztikai beállításokat,](../iot-hub/iot-hub-monitor-resource-health.md)nyomon követheti az útválasztási lekérdezés és a végpont állapota kiértékelése során az IoT Hub által érzékelt hibákat, például ha egy végpont halott. Ezek a diagnosztikai naplók elküldhetők az Azure Monitor naplóiba, az Event Hubs vagy az Azure Storage egyéni feldolgozásra.

## <a name="next-steps"></a>További lépések

* Az Üzenetútvonalak létrehozásáról az [IoT Hub-eszközök közötti üzenetek feldolgozása útvonalak használatával című](tutorial-routing.md)témakörben olvashat.

* [Eszközről felhőbe irányuló üzenetek küldése](quickstart-send-telemetry-node.md)

* Az eszközök ről a felhőbe irányuló üzenetek küldéséhez használható SDK-król az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)című témakörben talál további információt.
