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
ms.openlocfilehash: abc32b726eea55f08a052f29a12f1eb237d4f5d6
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311320"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Üzenet-útválasztása használatával eszköz – felhő üzeneteket küldhet a különböző végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Üzenet-útválasztása lehetővé teszi az eszközökről, a felhőalapú szolgáltatások automatizált, méretezhető és megbízható módon küldhet üzeneteket. Üzenet-útválasztása használható: 

* **Eszköz telemetriai messsages, valamint eseményeket küld** nevezetesen, eszköz-életciklussal kapcsolatos események, és módosítsa a beépített végpont, és egyéni végpontok események ikereszköz. Ismerje meg [útválasztási végpontok](#routing-endpoints).

* **Előtt érdemes átirányítására a különböző végpontok** részletes lekérdezéseket alkalmazásával. Üzenet-útválasztása lehetővé teszi az üzenet tulajdonságai és üzenet szövegét, valamint device twin címkék és eszköz-ikertulajdonságok lekérdezést. További információ [lekérdezések az üzenet-útválasztása](iot-hub-devguide-routing-query-syntax.md).

Az IoT Hub szolgáltatás a végpontokkal való írási hozzáférés van szüksége üzenet-útválasztása működjön. Ha a konfigurálja a végpontokat az Azure Portalon keresztül, a szükséges engedélyekkel meg lesz hozzáadva. Ellenőrizze, hogy konfigurálja a szolgáltatások, a várt teljesítményről támogatásához. Amikor először konfigurálja az IoT-megoldás, szükség lehet a további végpontok monitorozása és a tényleges betöltést a szükséges módosításokat.

Az IoT Hub meghatározása egy [gyakran alkalmazott formátum](iot-hub-devguide-messages-construct.md) protokollok között interoperatbility az üzenetkezelés összes eszköz-felhő. Ha egy üzenet, amelyek egyazon végpont több útvonal megfelel, az IoT Hub továbbítja az üzenet, hogy a végpont csak egyszer. Ezért nem szükséges konfigurálni a deduplikáció a Service Bus-üzenetsor vagy témakör. A particionált üzenetsorok a partíció affinitás üzenetrendezés garantálja. Ez az oktatóanyag segítségével megtudhatja, hogyan [üzenet-útválasztása konfigurálása] (az oktatóanyag-routing.md).

## <a name="routing-endpoints"></a>Útválasztási végpontok

Az IoT hub tartalmaz egy alapértelmezett beépített-az-végpont (**üzenetek/események**), amely az Event Hubs-kompatibilis. Létrehozhat [egyéni végpontok](iot-hub-devguide-endpoints.md#custom-endpoints) üzeneteknek az IoT hub más szolgáltatások az előfizetésében kapcsolásával. Az IoT Hub, az egyedi végpontok jelenleg a következő szolgáltatásokat támogatják:

### <a name="built-in-endpoint"></a>Beépített végpont

Használhat standard [Event Hubs-integráción és SDK-k](iot-hub-devguide-messages-read-builtin.md) eszköz – felhő üzenetek fogadása a beépített végpontról (**üzenetek/események**). Vegye figyelembe, hogy egy útvonal létrehozása után adatok leáll, ha az adott végpontra hozzon létre egy útvonalat a beépített-az-végpont halad.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub által támogatott csak, írja az adatokat az Azure Blob Storage-ban a [Apache Avro](http://avro.apache.org/) formátumban. Az IoT Hub kötegeli az üzeneteket, és adatokat ír egy blobot, ha a köteg bizonyos méretet elér egy bizonyos mennyi idő telt el.

Az IoT Hub az alapértelmezett fájl alábbi elnevezési szabályt követik:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Bármely fájl elnevezési konvenciót, használhatja azonban a listában szereplő összes jogkivonatok kell használnia. Az IoT Hub üres blob fog írni, ha ott nem szerepel megjeleníthető adat írni.

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus-üzenetsorok és Service Bus-témakörök

Service Bus-üzenetsorok és témakörök használt IoT Hub-végpontok nesmí mít **munkamenetek** vagy **duplikáltelem-észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **nem elérhető** az Azure Portalon.

### <a name="event-hubs"></a>Event Hubs

A beépített-Event-Hubs kompatibilis végpontot, szereplőkkel is irányíthatja adatokat az Event Hubs típusú egyéni végpontok. 

Útválasztási és egyéni végpontok használata esetén üzenetek jelenjenek meg csak a beépített végpont, ha a szabályok nem egyeznek. A beépített végpont és az egyedi végpontok kézbesíti az üzeneteket, adjon hozzá egy útvonalat, amely üzeneteket küld az események végpont.

## <a name="reading-data-that-has-been-routed"></a>Az adatok olvasása, amely rendelkezik lett irányítva

Ennek egy útvonalat konfigurálhat [oktatóanyag](tutorial-routing.md).

Az alábbi oktatóanyagok segítségével megismerheti, hogyan végpontról való olvasáshoz üzenetet egy.

* A olvasásakor [beépített végpont](quickstart-send-telemetry-node.md)

* A olvasásakor [Blob storage-ban](../storage/blobs/storage-blob-event-quickstart.md)

* A olvasásakor [az Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* A olvasásakor [Service Bus-üzenetsorok](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Olvassa el a [Service Bus-témakörök](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Tartalék útvonal

A tartalék útvonal az üzeneteket, amelyek nem felelnek meg a meglévő útvonalakat a beépített Event Hubs valamelyik lekérdezési feltételek küldi (**üzenetek/események**), azaz kompatibilis [az Event Hubs](/azure/event-hubs/). Üzenet-útválasztása be van kapcsolva, ha a tartalék útvonal funkció engedélyezheti. Figyelje meg, hogy egy útvonal létrehozása után adatok leállítja halad a beépített-az-végpont, kivéve, ha az adott végpontra hozzon létre egy útvonalat. Ha nem léteznek útvonalak a beépített-az-végponthoz, és a egy tartalék útvonalat engedélyezve van, csak az útvonalakra lekérdezési feltételek nem egyező üzeneteket küld a beépített-az-végpontra. Ezenkívül az összes meglévő útvonal törlése, ha tartalék útvonal engedélyezni kell a beépített-az-végpont összes adatok fogadására. 

Engedélyezheti vagy letilthatja a tartalék útvonal az Azure Portal -> üzenet-útválasztása panelen. Használhatja az Azure Resource Manager- [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) tartalék útvonalak egyéni végpontok használata.

## <a name="non-telemetry-events"></a>Nem-telemetria-eseményeinek

Eszköztelemetria, mellett üzenet-útválasztással is lehetővé teszi a küldő eszköz ikermódosítási események és eszköz-életciklussal kapcsolatos események. Például, ha az adatforrással beállítása hozzon létre egy útvonalat **eszköz ikermódosítási események**, az IoT Hub üzeneteket küld a végpontot, amely tartalmazza a módosítás az ikereszközben. Hasonlóképpen ha egy útvonal jön létre az adatforrás beállítása **eszköz-életciklussal kapcsolatos események**, az IoT Hub küld egy üzenet jelzi, hogy az eszköz törölték vagy létre. 

[Az IoT Hub is integrálható az Azure Event Griddel](iot-hub-event-grid.md) támogatásához a valós idejű Integrációk és munkafolyamatok be ezen események alapján automatizálási eszköz események közzététele. Tekintse meg a kulcs [üzenet-útválasztással és az Event Grid közötti különbségek](iot-hub-event-grid-routing-comparison.md) , ismerje meg, amely a leginkább a forgatókönyvhöz.

## <a name="testing-routes"></a>Útvonal tesztelése

Hozzon létre egy új útvonalat, vagy szerkesztheti a meglévő útvonalat, amikor az útválasztási lekérdezés minta üzenetet kell tesztelni. Egyéni útvonalak tesztelése, vagy egyszerre az összes útvonal tesztelése, és nincsenek üzenetek a végpontok legyenek átirányítva a vizsgálat során. Az Azure Portal, Azure Resource Manager, az Azure PowerShell és Azure parancssori felület teszteléséhez használható. Eredmények érdekében adja meg, hogy a minta üzenet egyezik a lekérdezést, üzenet nem egyezik meg a lekérdezés vagy teszt nem sikerült futtatni, mert a minta üzenet vagy a lekérdezés szintaxisát helytelenek. További tudnivalókért lásd: [vizsgálati útvonal](/rest/api/iothub/iothubresource/testroute) és [összes útvonal tesztelése](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Késés

Ha irányíthatja a beépített végpontokról eszköz – felhő telemetriát üzenetek, akkor a végpontok közötti késés enyhe növekedése van, az első útvonal a létrehozása után.

A legtöbb esetben az átlagos késés növekedése kisebb, mint 500ms. Figyelhető a késés **útválasztás: közel valós idejű üzenetek/események üzenet** vagy **d2c.endpoints.latency.builtIn.events** az IoT Hub-metrikát. Létrehozása vagy törlése az útvonalakat az első után nincs hatással a végpontok közötti késését.

## <a name="monitoring-and-troubleshooting"></a>Megfigyelés és hibaelhárítás

IoT Hub által biztosított több útválasztási, és a végpont kapcsolódó metrikák, hogy a hub és elküldött üzenetek állapotának áttekintése. Több metrika azonosíthatja a problémák kiváltó információkat kombinálhatók. Például használja a metrikát **útválasztás: eldobott telemetriai üzeneteket** vagy **d2c.telemetry.egress.dropped** azonosításához, amelyek el lettek dobva, amikor azok az útvonalak valamelyik lekérdezések nem egyeztek üzenetek száma és a tartalék útvonal le lett tiltva. [Az IoT Hub-metrikák](iot-hub-metrics.md) felsorolja az összes metrikát, amely az IoT hub alapértelmezés szerint engedélyezve vannak.

Használatával a **útvonalak** diagnosztikai naplók az Azure Monitor [diagnosztikai beállítások](../iot-hub/iot-hub-monitor-resource-health.md), akkor is, például az IoT Hub, által érzékelt egy útválasztási lekérdezés és a végpont állapotának kiértékelése során felmerülő hibák nyomon követi Ha a végpont nem működik. Ezek a diagnosztikai naplók is kell küldeni a Log Analytics, az Event Hubs vagy Azure Storage egyéni feldolgozáshoz.

## <a name="next-steps"></a>További lépések

* Üzenet útvonalak létrehozásával kapcsolatban lásd: [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak](tutorial-routing.md).

* [Eszköz a felhőbe irányuló üzenetek küldésének módja](quickstart-send-telemetry-node.md)

* Eszköz a felhőbe irányuló üzenetek küldéséhez használhatja az SDK-kkal kapcsolatos információk: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).
