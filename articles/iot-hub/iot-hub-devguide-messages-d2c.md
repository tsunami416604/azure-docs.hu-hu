---
title: Megismerheti az Azure IoT Hub üzenet-útválasztása |} A Microsoft Docs
description: Fejlesztői útmutató – hogyan használható az üzenet-útválasztása küldhet eszköz – felhő üzeneteket. Telemetria és a nem telemetriai adatokat küldő kapcsolatos információkat tartalmaz.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: dc5bfe6b431659b7b99140eb29a0e64922a42275
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576336"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Használja az IoT Hub üzenet-útválasztása eszköz – felhő üzeneteket küldeni a különböző végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Üzenet-útválasztása lehetővé teszi az eszközökről, a felhőalapú szolgáltatások automatizált, méretezhető és megbízható módon küldhet üzeneteket. Üzenet-útválasztása használható: 

* **Eszköz telemetriai üzeneteket, valamint eseményeket küld** nevezetesen, eszköz-életciklussal kapcsolatos események, és módosítsa a beépített végpont, és egyéni végpontok események ikereszköz. Ismerje meg [útválasztási végpontok](#routing-endpoints).

* **Előtt érdemes átirányítására a különböző végpontok** részletes lekérdezéseket alkalmazásával. Üzenet-útválasztása lehetővé teszi az üzenet tulajdonságai és üzenet szövegét, valamint device twin címkék és eszköz-ikertulajdonságok lekérdezést. További információ [lekérdezések az üzenet-útválasztása](iot-hub-devguide-routing-query-syntax.md).

Az IoT Hub szolgáltatás a végpontokkal való írási hozzáférés van szüksége üzenet-útválasztása működjön. Ha a konfigurálja a végpontokat az Azure Portalon keresztül, a szükséges engedélyekkel meg lesz hozzáadva. Ellenőrizze, hogy konfigurálja a szolgáltatások, a várt teljesítményről támogatásához. Amikor először konfigurálja az IoT-megoldás, szükség lehet a további végpontok monitorozása és a tényleges betöltést a szükséges módosításokat.

Az IoT Hub meghatározása egy [gyakran alkalmazott formátum](iot-hub-devguide-messages-construct.md) az összes eszköz – felhő üzenetküldéshez együttműködés protokollok között. Ha egy üzenet, amelyek egyazon végpont több útvonal megfelel, az IoT Hub továbbítja az üzenet, hogy a végpont csak egyszer. Ezért nem szükséges konfigurálni a deduplikáció a Service Bus-üzenetsor vagy témakör. A particionált üzenetsorok a partíció affinitás üzenetrendezés garantálja. Ez az oktatóanyag segítségével megtudhatja, hogyan [üzenet útválasztás konfigurálása](tutorial-routing.md).

## <a name="routing-endpoints"></a>Útválasztási végpontok

Az IoT hub tartalmaz egy alapértelmezett beépített-az-végpont (**üzenetek/események**), amely az Event Hubs-kompatibilis. Létrehozhat [egyéni végpontok](iot-hub-devguide-endpoints.md#custom-endpoints) üzeneteknek az IoT hub más szolgáltatások az előfizetésében kapcsolásával. Az IoT Hub, az egyedi végpontok jelenleg a következő szolgáltatásokat támogatják:

### <a name="built-in-endpoint"></a>Beépített végpont

Használhat standard [Event Hubs-integráción és SDK-k](iot-hub-devguide-messages-read-builtin.md) eszköz – felhő üzenetek fogadása a beépített végpontról (**üzenetek/események**). Útvonal létrehozása után adatok leállítja, kivéve, ha az adott végpontra hozzon létre egy útvonalat a beépített-az-végpont halad.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub által támogatott írja az adatokat az Azure Blob Storage-ban a [Apache Avro](https://avro.apache.org/) és JSON-formátumban. A szolgáltatás JSON formátumban kódolása előzetes verzióként érhető minden IoT Hub érhető el, kivéve az USA keleti RÉGIÓJA, USA nyugati Régiójában és Nyugat-európai régióban. Az alapértelmezett érték az avro-hoz. A kódolási formátum csak állítható, ha a blob storage-végpont konfigurálva van. A formátum nem szerkeszthető egy meglévő végpontot. JSON-kódolás használatakor be kell a contentType JSON-ra, az üzenetben UTF-8 contentEncoding [Rendszertulajdonságok](iot-hub-devguide-routing-query-syntax.md#system-properties). A kódolási formátum használatával az IoT Hub létrehozása vagy frissítése – REST API-t, kifejezetten választhatja a [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), az Azure Portal [Azure CLI-vel](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) vagy a [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). A következő ábra bemutatja az Azure Portalon válassza ki a kódolási formátum.

![A BLOB storage endpoint kódolás](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Az IoT Hub kötegeli az üzeneteket, és adatokat ír egy blobot, ha a köteg bizonyos méretet elér egy bizonyos mennyi idő telt el. Az IoT Hub az alapértelmezett fájl alábbi elnevezési szabályt követik:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Bármely fájl elnevezési konvenciót, használhatja azonban a listában szereplő összes jogkivonatok kell használnia. Az IoT Hub üres blob fog írni, ha ott nem szerepel megjeleníthető adat írni.

Útválasztás blob storage-ba, amikor ajánlott felvétel a blobok és majd léptetés át őket, anélkül, hogy a partíció jósolható olvasható az összes tárolót. A partíciótartomány sikerült potenciálisan módosítása során egy [a Microsoft által kezdeményezett feladatátvételi](iot-hub-ha-dr.md#microsoft-initiated-failover) vagy IoT hubot [manuális feladatátvétel](iot-hub-ha-dr.md#manual-failover-preview). Használhatja a [lista Blobok API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) enumerálásakor a blobok listáját. Tekintse át az alábbi minta útmutatásként.

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

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus-üzenetsorok és Service Bus-témakörök

Service Bus-üzenetsorok és témakörök használt IoT Hub-végpontok nesmí mít **munkamenetek** vagy **duplikáltelem-észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **nem elérhető** az Azure Portalon.

### <a name="event-hubs"></a>Event Hubs

A beépített-Event-Hubs kompatibilis végpontot, szereplőkkel is irányíthatja adatokat az Event Hubs típusú egyéni végpontok. 

## <a name="reading-data-that-has-been-routed"></a>Az adatok olvasása, amely rendelkezik lett irányítva

Ennek egy útvonalat konfigurálhat [oktatóanyag](tutorial-routing.md).

Az alábbi oktatóanyagok segítségével megismerheti, hogyan végpontról való olvasáshoz üzenetet egy.

* A olvasásakor [beépített végpont](quickstart-send-telemetry-node.md)

* A olvasásakor [Blob storage-ban](../storage/blobs/storage-blob-event-quickstart.md)

* A olvasásakor [az Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* A olvasásakor [Service Bus-üzenetsorok](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Olvassa el a [Service Bus-témakörök](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Tartalék útvonal

A tartalék útvonal az üzeneteket, amelyek nem felelnek meg a meglévő útvonalakat a beépített Event Hubs valamelyik lekérdezési feltételek küldi (**üzenetek/események**), azaz kompatibilis [az Event Hubs](/azure/event-hubs/). Üzenet-útválasztása be van kapcsolva, ha a tartalék útvonal funkció engedélyezheti. Útvonal létrehozása után adatok leállítja halad a beépített-az-végpont, kivéve, ha az adott végpontra hozzon létre egy útvonalat. Ha nem léteznek útvonalak a beépített-az-végponthoz, és a egy tartalék útvonalat engedélyezve van, csak az útvonalakra lekérdezési feltételek nem egyező üzeneteket küld a beépített-az-végpontra. Ezenkívül az összes meglévő útvonal törlése, ha tartalék útvonal engedélyezni kell a beépített-az-végpont összes adatok fogadására. 

Engedélyezheti vagy letilthatja a tartalék útvonal az Azure Portal -> üzenet-útválasztása panelen. Használhatja az Azure Resource Manager- [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) tartalék útvonalak egyéni végpontok használata.

## <a name="non-telemetry-events"></a>Nem-telemetria-eseményeinek

Eszköztelemetria, mellett üzenet-útválasztással is lehetővé teszi a küldő eszköz ikermódosítási események és eszköz-életciklussal kapcsolatos események. Például, ha az adatforrással beállítása hozzon létre egy útvonalat **eszköz ikermódosítási események**, az IoT Hub üzeneteket küld a végpontot, amely tartalmazza a módosítás az ikereszközben. Hasonlóképpen ha egy útvonal jön létre az adatforrás beállítása **eszköz-életciklussal kapcsolatos események**, az IoT Hub küld egy üzenet jelzi, hogy az eszköz törölték vagy létre. 

[Az IoT Hub is integrálható az Azure Event Griddel](iot-hub-event-grid.md) támogatásához a valós idejű Integrációk és munkafolyamatok be ezen események alapján automatizálási eszköz események közzététele. Tekintse meg a kulcs [üzenet-útválasztással és az Event Grid közötti különbségek](iot-hub-event-grid-routing-comparison.md) , ismerje meg, amely a leginkább a forgatókönyvhöz.

## <a name="testing-routes"></a>Útvonal tesztelése

Hozzon létre egy új útvonalat, vagy szerkesztheti a meglévő útvonalat, amikor az útválasztási lekérdezés minta üzenetet kell tesztelni. Egyéni útvonalak tesztelése, vagy egyszerre az összes útvonal tesztelése, és nincsenek üzenetek a végpontok legyenek átirányítva a vizsgálat során. Az Azure Portal, Azure Resource Manager, az Azure PowerShell és Azure parancssori felület teszteléséhez használható. Eredmények érdekében adja meg, hogy a minta üzenet egyezik a lekérdezés, üzenet nem egyezik meg a lekérdezés vagy teszt nem sikerült futtatni, mert a minta üzenet vagy a lekérdezés szintaxisa helytelen. További tudnivalókért lásd: [vizsgálati útvonal](/rest/api/iothub/iothubresource/testroute) és [összes útvonal tesztelése](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Késés

Ha irányíthatja a beépített végpontokról eszköz – felhő telemetriát üzenetek, akkor a végpontok közötti késés enyhe növekedése van, az első útvonal a létrehozása után.

A legtöbb esetben az átlagos késés növekedése kevesebb, mint 500 ms. Figyelhető a késés **útválasztás: közel valós idejű üzenetek/események üzenet** vagy **d2c.endpoints.latency.builtIn.events** az IoT Hub-metrikát. Létrehozása vagy törlése az útvonalakat az első után nincs hatással a végpontok közötti késését.

## <a name="monitoring-and-troubleshooting"></a>Megfigyelés és hibaelhárítás

IoT Hub által biztosított több útválasztási, és a végpont kapcsolódó metrikák, hogy a hub és elküldött üzenetek állapotának áttekintése. Több metrika azonosíthatja a problémák kiváltó információkat kombinálhatók. Például használja a metrikát **útválasztás: eldobott telemetriai üzeneteket** vagy **d2c.telemetry.egress.dropped** azonosításához, amelyek el lettek dobva, amikor azok az útvonalak valamelyik lekérdezések nem egyeztek üzenetek száma és a tartalék útvonal le lett tiltva. [Az IoT Hub-metrikák](iot-hub-metrics.md) felsorolja az összes metrikát, amely az IoT hub alapértelmezés szerint engedélyezve vannak.

A REST API-hoz [végpont állapotának lekérése](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) beolvasni [állapot](iot-hub-devguide-endpoints.md#custom-endpoints) a végpontok. Javasoljuk, hogy használja a [az IoT Hub-metrikák](iot-hub-metrics.md) útválasztási üzenet késése alapján azonosíthatja és elháríthatja a hibákat, amikor végpontonkénti állapotot kézbesíthetetlen vagy nem megfelelő állapotú kapcsolódó. Ha például az Event Hubs típusú végpont, figyelheti **d2c.endpoints.latency.eventHubs**. Nem megfelelő állapotú végpont állapota kifogástalanra frissül, az IoT Hub állapotának egy végül konzisztens állapotba létrejöttekor.

Használatával a **útvonalak** diagnosztikai naplók az Azure Monitor [diagnosztikai beállítások](../iot-hub/iot-hub-monitor-resource-health.md), akkor is, például az IoT Hub, által érzékelt egy útválasztási lekérdezés és a végpont állapotának kiértékelése során felmerülő hibák nyomon követi Ha a végpont nem működik. Ezek a diagnosztikai naplók küldhetők az Azure Monitor naplók, az Event Hubs vagy Azure Storage egyéni feldolgozáshoz.

## <a name="next-steps"></a>További lépések

* Üzenet útvonalak létrehozásával kapcsolatban lásd: [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak](tutorial-routing.md).

* [Eszköz a felhőbe irányuló üzenetek küldésének módja](quickstart-send-telemetry-node.md)

* Eszköz a felhőbe irányuló üzenetek küldéséhez használhatja az SDK-kkal kapcsolatos információk: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).
