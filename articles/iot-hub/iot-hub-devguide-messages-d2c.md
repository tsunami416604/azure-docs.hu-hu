---
title: Az Azure IoT Hub üzenetek útválasztásának megismerése | Microsoft Docs
description: Fejlesztői útmutató – az üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez. A telemetria és a nem telemetria adatok küldésére vonatkozó információkat tartalmaz.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370457"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az üzenet-útválasztás lehetővé teszi, hogy automatizált, méretezhető és megbízható módon küldjön üzeneteket az eszközeiről a Cloud Services szolgáltatásba. Az üzenet-útválasztás a következőhöz használható: 

* Az **eszköz telemetria üzeneteinek** , valamint az események, azaz az eszközök életciklusa eseményeinek és az eszközök kettős változási eseményeinek küldése a beépített végpontra és az egyéni végpontokra. További információ az [útválasztási végpontokról](#routing-endpoints).

* **Adatszűrés a különböző végpontokra való átirányításuk előtt** , Rich lekérdezések alkalmazásával. Az üzenet-útválasztás lehetővé teszi, hogy lekérdezze az üzenet tulajdonságait és az üzenet törzsét, valamint az eszköz Twin címkéit és az eszköz Twin tulajdonságait. További információ az [üzenet-útválasztásban található lekérdezések](iot-hub-devguide-routing-query-syntax.md)használatáról.

IoT Hub írási hozzáféréssel kell rendelkeznie ezekhez a szolgáltatási végpontokhoz az üzenet-útválasztás működéséhez. Ha a végpontokat a Azure Portalon keresztül konfigurálja, a rendszer hozzáadja a szükséges engedélyeket. Győződjön meg róla, hogy konfigurálja a szolgáltatásokat a várt átviteli sebesség támogatásához. Ha például egyéni végpontként használja a Event Hubst, akkor az adott Event hub **átviteli egységeit** úgy kell konfigurálnia, hogy az IoT hub üzenet-útválasztás segítségével elküldheti az elküldött események bejövő eseményeit. Hasonlóképpen, ha egy Service Bus üzenetsor végpontként való használatakor, a **maximális méretet** úgy kell konfigurálni, hogy a várólista az összes adatmennyiséget ingressed, amíg a felhasználók nem egressed. Előfordulhat, hogy a IoT-megoldás első beállításakor figyelnie kell a további végpontokat, és el kell végeznie a szükséges módosításokat a tényleges terheléshez.

A IoT Hub a protokollok közötti együttműködés [általános formátumát](iot-hub-devguide-messages-construct.md) határozza meg az összes eszközről a felhőbe irányuló üzenetkezeléshez. Ha egy üzenet több olyan útvonalnak felel meg, amely ugyanarra a végpontra mutat, IoT Hub csak egyszer továbbítja az üzenetet erre a végpontra. Ezért nincs szükség a deduplikálás konfigurálására a Service Bus-várólistán vagy a témakörben. A particionált várólistákban a partíciós affinitás garantálja az üzenetek rendezését. Ebből az oktatóanyagból megtudhatja, hogyan [konfigurálhatja az üzenet-útválasztást](tutorial-routing.md).

## <a name="routing-endpoints"></a>Útválasztási végpontok

Az IoT hub alapértelmezett beépített végpontja (**üzenetek/események**), amely kompatibilis a Event Hubsokkal. Létrehozhat [Egyéni végpontokat](iot-hub-devguide-endpoints.md#custom-endpoints) az üzenetek átirányításához az előfizetésben lévő egyéb szolgáltatások összekapcsolásával a IoT hub. 

Minden üzenet az összes olyan végponthoz van irányítva, amelynek útválasztási lekérdezése megfelel. Más szóval egy üzenet több végponthoz is átirányítható.

A IoT Hub jelenleg a következő szolgáltatásokat támogatja egyéni végpontként:

### <a name="built-in-endpoint"></a>Beépített végpont

Az eszközről a felhőbe irányuló üzenetek fogadásához a beépített végpontról (**üzenetek/események**) a standard [Event Hubs Integration és SDK](iot-hub-devguide-messages-read-builtin.md) -k használhatók. Az útvonal létrehozása után az adatforgalom a beépített végpontra áramlik, kivéve, ha egy útvonal jön létre a végponthoz.

### <a name="azure-storage"></a>Azure Storage

Két tárolási szolgáltatás IoT Hub képes üzeneteket átirányítani az Azure- [blob Storage](../storage/blobs/storage-blobs-introduction.md) és a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) fiókba. Azure Data Lake Storage-fiókok a blob Storage-ra épülő [hierarchikus névtereket](../storage/blobs/data-lake-storage-namespace.md)használó Storage-fiókok. Mindkét blob a tárolóhoz használható.

IoT Hub támogatja az Azure Storage-ba való adatírást az [Apache Avro](https://avro.apache.org/) formátumban, valamint JSON formátumban. Az alapértelmezett érték a AVRO. A kódolás formátuma csak akkor állítható be, ha a blob Storage-végpont konfigurálva van. Egy meglévő végpont formátuma nem szerkeszthető. JSON **-** kódolás használatakor az ContentType az **Application/JSON** és a contentEncoding értékre kell állítani az üzenetrendszer [tulajdonságai](iot-hub-devguide-routing-query-syntax.md#system-properties)között. Mindkét érték megkülönbözteti a kis-és nagybetűket. Ha nincs beállítva a tartalom kódolása, akkor a IoT Hub az üzeneteket az alap 64 kódolású formátumban fogja írni. A kódolási formátumot kiválaszthatja a IoT Hub létrehozás vagy frissítés REST API, konkrétan a [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), a Azure Portal, az [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)vagy a [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)használatával. Az alábbi ábrán látható, hogyan választható ki a kódolás formátuma a Azure Portalban.

![BLOB Storage-végpont kódolása](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub a kötegek üzeneteit, és az adatot a tárolóba írja, amikor a köteg elér egy adott méretet, vagy egy adott időtartam eltelt. IoT Hub alapértelmezett értéke a következő fájl elnevezési konvenció: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Bármilyen fájl elnevezési konvenciót használhat, azonban az összes felsorolt tokent kell használnia. A IoT Hub egy üres blobba ír, ha nincs írási adatként.

Javasoljuk, hogy a Blobok vagy fájlok felsorolását, majd azok ismételt megismétlését, hogy minden blobot vagy fájlt beolvasson a partíciós feltételezések elkészítése nélkül. A partíció tartománya esetleg változhat a [Microsoft által kezdeményezett feladatátvétel](iot-hub-ha-dr.md#microsoft-initiated-failover) vagy IoT hub [manuális feladatátvétel](iot-hub-ha-dr.md#manual-failover)során. A [Blobok listázása API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) -val enumerálhatja a Blobok listáját vagy a lista [ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) -ját a fájlok listájához. Tekintse át a következő mintát útmutatásként.

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
> Ha a Storage-fiók olyan tűzfal-konfigurációval rendelkezik, amely korlátozza IoT Hub kapcsolatát, érdemes lehet a [Microsoft megbízható első féltől származó kivételt](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) használni (az IoT-hubok számára elérhető régiókban, felügyelt szolgáltatás identitásával).

Azure Data Lake Gen2-kompatibilis Storage-fiók létrehozásához hozzon létre egy új v2-es Storage-fiókot, és válassza az *engedélyezve* lehetőséget a **speciális** lap *hierarchikus névtér* mezőjében az alábbi képen látható módon:

![Azure Date Lake Gen2-tároló kiválasztása](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus várólisták és Service Bus témakörök

Service Bus várólisták és a IoT Hub végpontként használt témakörök nem rendelkezhetnek engedélyezett **munkamenetekkel** vagy **duplikált észleléssel** . Ha bármelyik beállítás engedélyezve van, a végpont nem **érhető el** a Azure Portalban.

> [!NOTE]
> Ha a Service Bus-erőforrás olyan tűzfal-konfigurációval rendelkezik, amely korlátozza IoT Hub kapcsolatát, érdemes lehet a [Microsoft megbízható első féltől származó kivételt](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) használni (az IoT-hubok számára a felügyelt szolgáltatás identitásával érhető el).


### <a name="event-hubs"></a>Event Hubs

A beépített Event Hubs kompatibilis végponton kívül az adatok átirányítása Event Hubs típusú egyéni végpontokra is elvégezhető. 

> [!NOTE]
> Ha az Event hub-erőforrás olyan tűzfallal rendelkezik, amely korlátozza IoT Hub kapcsolatát, érdemes lehet a [Microsoft megbízható első féltől származó kivételt](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) használni (amely a felügyelt szolgáltatás identitásával IoT hubok esetében érhető el).


## <a name="reading-data-that-has-been-routed"></a>Az átirányított adatolvasás

Ezt az [oktatóanyagot](tutorial-routing.md)követve egy útvonalat is beállíthat.

Az alábbi oktatóanyagok segítségével megtudhatja, hogyan olvashatja el a végpontok üzeneteit.

* Olvasás a [beépített végpontról](quickstart-send-telemetry-node.md)

* Olvasás a [blob Storage](../storage/blobs/storage-blob-event-quickstart.md) -ból

* Olvasás [Event Hubsról](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Olvasás [Service Bus várólistákból](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Olvasás [Service Bus témakörökből](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Tartalék útvonal

A tartalék útvonal minden olyan üzenetet elküld, amely nem felel meg a lekérdezési feltételeknek a meglévő útvonalakon a beépített Event Hubs (**üzenetek/események**) számára, amely kompatibilis a [Event Hubsokkal](/azure/event-hubs/). Ha az üzenet-útválasztás be van kapcsolva, engedélyezheti a tartalék útvonal funkciót. Az útvonal létrehozása után az adatforgalom a beépített végpontra áramlik, hacsak nem jön létre útvonal a végponthoz. Ha nincs elérhető útvonal a beépített végponthoz, és a tartalék útvonal engedélyezve van, csak az útvonalakon nem egyező üzeneteket küld a rendszer a beépített végpontnak. Továbbá, ha az összes meglévő útvonal törölve van, a tartalék útvonalnak engedélyezve kell lennie az összes, a beépített végponton tárolt érték fogadásához.

Engedélyezheti vagy letilthatja a tartalék útvonalat a Azure Portal-> üzenet-útválasztás panelen. A [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) Azure Resource Manager is használhat egyéni végpontot a tartalék útvonalhoz.

## <a name="non-telemetry-events"></a>Nem telemetria események

Az eszköz telemetria mellett az üzenet-útválasztás is lehetővé teszi az eszköz kettős változási eseményeinek, az eszköz életciklusa eseményeinek és a digitális kettős változási események küldését (nyilvános előzetes verzióban). Ha például egy útvonal úgy jön létre, hogy az **eszköz kettős változási eseményre**van beállítva, akkor IoT hub üzeneteket küld a végpontnak, amely tartalmazza az eszköz kettős változását. Hasonlóképpen, ha egy útvonal az **eszköz életciklusára**beállított adatforrással jön létre, IoT hub üzenetet küld, amely jelzi, hogy az eszköz törölve lett vagy létrejött. Végül, a [IoT Plug and Play nyilvános előzetes](../iot-pnp/overview-iot-plug-and-play.md)verziójának részeként a fejlesztő olyan útvonalakat hozhat létre, amelyek **digitális kettős változási eseményekre** vannak beállítva, és IoT hub üzeneteket küld, amikor egy digitális Twin [tulajdonságot](../iot-pnp/iot-plug-and-play-glossary.md) állítanak be vagy módosítanak, a rendszer lecseréli a [digitális](../iot-pnp/iot-plug-and-play-glossary.md) twint, vagy ha változási esemény történik az alapul szolgáló eszköz esetében.

A IoT Hub a Azure Event Grid-nal [is integrálva van](iot-hub-event-grid.md) az eszköz eseményeinek közzétételéhez, hogy támogassa a valós idejű integrációkat és a munkafolyamatok automatizálását ezen események alapján. Tekintse meg az [üzenet-útválasztás és a Event Grid közötti fő különbségeket](iot-hub-event-grid-routing-comparison.md) , amelyekből megtudhatja, melyik a legmegfelelőbb a forgatókönyvhöz.

## <a name="testing-routes"></a>Útvonalak tesztelése

Új útvonal létrehozásakor vagy egy meglévő útvonal szerkesztésekor az útvonal lekérdezését egy minta üzenettel kell tesztelni. Az egyes útvonalak tesztelését vagy az összes útvonal tesztelését egyszerre hajthatja végre, és a rendszer nem irányítja át az üzeneteket a végpontokhoz a teszt során. A Azure Portal, a Azure Resource Manager, a Azure PowerShell és az Azure parancssori felület használható teszteléshez. Az eredmények segítenek megállapítani, hogy a minta üzenet megfelel-e a lekérdezésnek, az üzenet nem felelt meg a lekérdezésnek, vagy a teszt nem sikerült, mert a minta üzenet vagy a lekérdezés szintaxisa helytelen. További információ: az [útvonal tesztelése](/rest/api/iothub/iothubresource/testroute) és az [összes útvonal tesztelése](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Jótállás megrendelése legalább egyszeri kézbesítéssel

IoT Hub üzenet-útválasztási garancia megrendelt, és legalább egyszer kézbesíti az üzeneteket a végpontoknak. Ez azt jelenti, hogy előfordulhat, hogy a rendszer duplikált üzeneteket küld, és az eredeti üzenet megrendelése után újraküldhető az üzenetek sorozata. Ha például az eredeti üzenet sorrendje [1, 2, 3, 4], akkor a következőhöz hasonló üzenet jelenhet meg: [1, 2, 1, 2, 3, 1, 2, 3, 4]. A rendezési garancia az, hogy ha valaha is megkapja a következő üzenetet: [1], a rendszer mindig ezt követi [2, 3, 4].

A duplikált üzenetek kezelésére javasolt az üzenet alkalmazási tulajdonságaiban egy egyedi azonosítót kijelölni, amely általában egy eszköz vagy egy modul. Az üzeneteket használó szolgáltatás képes a duplikált üzenetek kezelésére ezzel az azonosítóval.

## <a name="latency"></a>Késés

Ha a beépített végpontok használatával irányítja az eszközről a felhőbe irányuló telemetria üzeneteket, az első útvonal létrehozása után kis mértékben megnő a végpontok közötti késés.

A legtöbb esetben a késés átlagos növekedése kevesebb, mint 500 MS. A késést a **következő útválasztással figyelheti: üzenetek/események** vagy **D2C. endpoints. látencia. beépített. events** IoT hub metrika. Az egyik útvonal létrehozása vagy törlése az első után nem befolyásolja a végpontok közötti késést.

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás

IoT Hub az útválasztáshoz és a végpontokhoz kapcsolódó metrikákat biztosít, hogy áttekintést nyújtson a hub állapotáról és az elküldött üzenetekről. Több mérőszámból is egyesítheti az információkat, így azonosíthatja a problémák alapvető okát. Használja például a metrika- **útválasztást: telemetria-üzenetek eldobott** vagy **D2C. telemetria. kimenő. eldobott** üzenet, amely meghatározza, hogy az egyes útvonalakon és a tartalék útvonalon lévő lekérdezések nem felelnek meg a letiltott üzenetek számának. [IoT hub mérőszámok](iot-hub-metrics.md) felsorolja az összes olyan metrikát, amely alapértelmezés szerint engedélyezve van a IoT hub számára.

A végpontok [állapotának beolvasásához](iot-hub-devguide-endpoints.md#custom-endpoints) használja a REST API a [végpont állapota](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) lehetőséget. Azt javasoljuk, hogy az útválasztási üzenet késéséhez kapcsolódó [IoT hub metrikák](iot-hub-metrics.md) használatával azonosítsa és hibakeresési hibákat, ha a végpont állapota meghalt vagy nem megfelelő. A végpont típusa Event Hubs esetében például figyelheti a **D2C. endpoints. késés. eventHubs**. A nem kifogástalan állapotú végpont állapota akkor frissül, ha a IoT Hub végül konzisztens állapotba került.

Az Azure Monitor [diagnosztikai beállításokban](../iot-hub/iot-hub-monitor-resource-health.md)található diagnosztikai naplók használatával nyomon követheti **az útválasztási** lekérdezések és a végpontok állapotának kiértékelése során felmerülő hibákat IoT hub, például ha egy végpont meghalt. Ezeket a diagnosztikai naplókat Azure Monitor naplókba, Event Hubsba vagy az Azure Storage-ba is elküldhetik egyéni feldolgozásra.

## <a name="next-steps"></a>Következő lépések

* Az üzenetküldési útvonalak létrehozásával kapcsolatos információkért lásd: [IoT hub eszközről a felhőbe irányuló üzenetek feldolgozása útvonalak használatával](tutorial-routing.md).

* [Az eszközről a felhőbe irányuló üzenetek küldése](quickstart-send-telemetry-node.md)

* Az eszközről a felhőbe irányuló üzenetek küldéséhez használható SDK-k információit az [Azure IoT SDK](iot-hub-devguide-sdks.md)-k című részben tekintheti meg.
