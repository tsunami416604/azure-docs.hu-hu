---
title: Metrikák használatával figyelheti az Azure IoT Hub |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub mérőszámok segítségével mérheti fel, és az IoT-központok általános állapotának monitorozásához.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: b1c9854d794c88c28c82a4b6e40c81a29552223a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984073"
---
# <a name="understand-iot-hub-metrics"></a>Megismerheti az IoT Hub-metrikák
Az IoT Hub mérőszámok segítségével az Azure IoT-erőforrások állapotának jobb adatait az Azure-előfizetésében. Az IoT Hub-metrikák lehetővé teszik annak ellenőrzéséhez, az IoT Hub szolgáltatás és a hozzá csatlakozó eszközök általános állapotát. Felhasználói statisztikák fontosak, mert ezek segítenek tekintse meg, mi történik az IoT hub és a Súgó kiváltó problémákat anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Alapértelmezés szerint engedélyezve vannak a metrikákat. Az IoT Hub-mérőszámok az Azure Portalon tekintheti meg.

## <a name="how-to-view-iot-hub-metrics"></a>Az IoT Hub-metrikák megtekintése
1. Hozzon létre egy IoT hubot. Az IoT hub létrehozása a talál útmutatást a [Ismerkedés] [ lnk-get-started] útmutató.
2. Az IoT hub panel megnyitásához. Itt kattintson **metrikák**.
   
    ![][1]
3. A metrikák paneljén a metrikákat tekinthet meg az IoT hub, és a metrikák, egyéni nézeteket hozhat létre. A metrikák adatai küld az Event Hubs a végpont vagy egy Azure Storage-fiók kattintva választhatja **diagnosztikai beállítások**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Az IoT Hub-mérőszámok és azok használatát
IoT Hub által biztosított több metrikát, hogy a hub és az összes csatlakoztatott eszközök állapotának áttekintése. Egyesíthet több metrikát, hogy nagyobb méretű képet kapjon az IoT hub állapotának adatait. A következő táblázat ismerteti a mérőszámok nyomon követi az egyes IoT hubot, és mindegyik metrikát hogyan viszonyul az IoT hub összesített állapotát.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetriai üzenetet küld kísérletek|Darabszám|Összes|Küldését az IoT hub próbált eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Telemetriai üzeneteket küldi|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott művelet felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Darabszám|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|devices.totalDevices|(Elavult az) összes eszköz|Darabszám|Összes|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|(Elavult) csatlakoztatott eszközök |Darabszám|Összes|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Útválasztási: telemetriai üzenetet|Darabszám|Összes|Többször is sikeresen kézbesíti az üzeneteket az IoT Hub útválasztás használatával az összes végpontok száma. Ha egy üzenet van irányítva több végpontot, ez az érték minden egyes sikeres kézbesítés eggyel nő. Egy üzenet szállítják egyazon végpont több alkalommal, ha ez az érték minden egyes sikeres kézbesítés eggyel nő.|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Útválasztási: telemetriai üzeneteket eldobása |Darabszám|Összes|Hányszor üzenetek kézbesíthetetlen végpontok miatt útválasztási IoT Hub által el lettek dobva. Ez az érték nem számít üzenetet tartalék útvonal, az eldobott üzenetek jelenjenek meg nem létezik.|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Útválasztási: telemetriai üzeneteket árva |Darabszám|Összes|Hányszor üzenetek által az IoT Hub-útválasztás árva is, mert (beleértve a tartalék szabály) minden olyan útválasztási szabályok nem egyeznek. |Nincs dimenzió|
|d2c.telemetry.egress.invalid|Útválasztási: telemetria inkompatibilis üzenetek|Darabszám|Összes|Hányszor útválasztás az IoT Hub kézbesíti az üzeneteket egy alkalmazással a végponthoz való inkompatibilitás miatt nem sikerült. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Útválasztási: kézbesíti az üzeneteket tartalék|Darabszám|Összes|Hányszor útválasztás az IoT Hub-üzenetek a végponthoz társított a tartalék útvonal i.|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Útválasztási: kézbesíti az üzeneteket az Eseményközpont|Darabszám|Összes|Hányszor sikeresen útválasztás az IoT Hub Event Hub-végpontok üzenetek érkeznek.|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Útválasztási: Event Hub üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a bejövő üzenet be egy Eseményközpont-végpontra.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Útválasztási: üzenetek kézbesítése Service Bus-üzenetsorba helyezése|Darabszám|Összes|Hányszor sikeresen útválasztás az IoT Hub-üzenetek i a Service Bus üzenetsor-végpontokra irányuló.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|Útválasztási: Service Bus-üzenetsor üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-üzenetsor végpont.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Útválasztási: üzenetek kézbesítése Service Bus-témakörbe|Darabszám|Összes|Hányszor sikeresen útválasztás az IoT Hub-i üzenetek a Service Bus-témakör végpontokat.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|Útválasztási: Service Bus-témakör üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-témakör végpont.|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|Útválasztási: kézbesíti az üzeneteket üzenetek/események|Darabszám|Összes|Hányszor az IoT Hub sikeresen útválasztási üzenetek érkeznek a beépített végpont (üzenetek/események).|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Útválasztási: üzenet közel valós idejű üzenetek/események|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő forgalom, a beépített végpont (üzenetek/események).|Nincs dimenzió|
|d2c.endpoints.egress.storage|Útválasztási: kézbesíti az üzeneteket storage|Darabszám|Összes|Többször az IoT Hub sikeresen útválasztási üzenetek érkeznek tárolási végpontok száma.|Nincs dimenzió|
|d2c.endpoints.latency.storage|Útválasztási: üzenet késése storage|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy storage-végponthoz.|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Útválasztási: az adatokat kézbesíti storage|Bájt|Összes|Adatok (bájt) mennyisége az IoT Hub útválasztás kézbesíti a tárolási végpontok.|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Útválasztási: blobok kézbesíteni storage|Darabszám|Összes|Hányszor útválasztás az IoT Hub blobok tárolási végpontok lett elküldve.|Nincs dimenzió|
|d2c.twin.read.success|A sikeres ikerírási eszközökről|Darabszám|Összes|Az összes sikeres eszköz által kezdeményezett ikerírási száma.|Nincs dimenzió|
|d2c.twin.read.failure|Nem sikerült az iker írási eszközökről|Darabszám|Összes|A teljes számát a eszköz által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|d2c.twin.read.size|Válasz mérete ikerírási eszközökről|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres, a páros olvasási.|Nincs dimenzió|
|d2c.twin.update.success|Eszközök sikeres ikereszköz-frissítések|Darabszám|Összes|Az összes sikeres eszköz által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|d2c.twin.update.failure|Nem sikerült az eszköz az ikereszköz-frissítések|Darabszám|Összes|A szám az összes eszköz által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|d2c.twin.update.size|Ikereszköz-frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres ikereszköz frissítéseket.|Nincs dimenzió|
|c2d.methods.success|A sikeres közvetlen metódus meghívásához|Darabszám|Összes|Közvetlen metódus az összes sikeres hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen metódus meghívásához|Darabszám|Összes|A teljes számát nem sikerült a közvetlen metódust hívja.|Nincs dimenzió|
|c2d.methods.requestSize|Kérés mérete közvetlen metódus meghívásához|Bájt|Átlag|Az átlagos, minimális és közvetlen metódus az összes sikeres kérelmek maximális száma.|Nincs dimenzió|
|c2d.methods.responseSize|Közvetlen megpróbálkozni a válasz mérete|Bájt|Átlag|Az átlagos, minimális és a közvetlen metódus az összes sikeres válaszok maximális száma.|Nincs dimenzió|
|c2d.twin.read.success|A sikeres ikerírási háttérrendszerből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-olvasások száma.|Nincs dimenzió|
|c2d.twin.read.failure|Nem sikerült a páros olvasási háttérrendszerből|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|c2d.twin.read.size|Válasz mérete ikerírási háttérrendszerből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett a páros olvasási.|Nincs dimenzió|
|c2d.twin.update.success|Háttér sikeres ikereszköz-frissítések|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|c2d.twin.update.failure|Háttér sikertelen ikereszköz-frissítések|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|c2d.twin.update.size|A háttérben ikereszköz-frissítések mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett ikereszköz frissítéseket.|Nincs dimenzió|
|twinQueries.success|A sikeres ikereszköz-lekérdezések|Darabszám|Összes|Az összes sikeres ikereszköz-lekérdezések száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen ikereszköz-lekérdezések|Darabszám|Összes|Összes sikertelen ikereszköz-lekérdezések száma.|Nincs dimenzió|
|twinQueries.resultSize|Ikereszköz-lekérdezések eredmény mérete|Bájt|Átlag|Az átlagos, minimális és az összes sikeres ikereszköz-lekérdezés eredményének mérete legfeljebb.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|Ikereszköz-frissítési feladatok sikeres létrehozás|Darabszám|Összes|Az összes sikeres létrehozás ikereszköz-frissítési feladatok száma.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|Sikertelen létrehozás ikereszköz-frissítési feladatok|Darabszám|Összes|Minden létrehozására tett sikertelen ikereszköz frissítési feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|Metódus meghívása feladatok sikeres létrehozás|Darabszám|Összes|Az összes sikeres létrehozás közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|Metódus meghívása feladatok sikertelen létrehozás|Darabszám|Összes|Összes sikertelen létrehozása a közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.listJobs.success|Listázhatók a feladatok sikeres hívások|Darabszám|Összes|Az összes sikeres hívások listázhatók a feladatok száma.|Nincs dimenzió|
|jobs.listJobs.failure|Sikertelen hívások listázhatók a feladatok|Darabszám|Összes|Az összes sikertelen hívás listázhatók a feladatok száma.|Nincs dimenzió|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Darabszám|Összes|Megszakítja a feladatot az összes sikeres hívások száma.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladat általi hóközi lemondás|Darabszám|Összes|Megszakítja a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|Lekérdezés projektekhez az összes sikeres hívások száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladat lekérdezések|Darabszám|Összes|Lekérdezés projektekhez az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.Completed|Befejezett feladatok|Darabszám|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.Failed|Sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Szabályozási hibák száma|Darabszám|Összes|Eszköz átviteli miatt szabályozási hibák számának szabályozza|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Darabszám|Átlag|Ma használt teljes üzenetek száma. Ez az összesített érték, amely lenullázódik: 00:00 (UTC) minden nap.|Nincs dimenzió|
|deviceDataUsage|Teljes devicedata használat (elavult)|Bájt|Összes|És bármilyen eszközről csatlakozik az IotHub átvitt bájtok száma|Nincs dimenzió|
|deviceDataUsageV2|Eszköz teljes adathasználat (előzetes verzió)|Bájt|Összes|És bármilyen eszközről csatlakozik az IotHub átvitt bájtok száma|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|Konfigurációk|Konfiguráció-metrikák|Darabszám|Összes|A konfigurálási műveletek metrikái|Nincs dimenzió|

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az IoT Hub-metrikák áttekintése, hajtsa végre ezt a hivatkozást az Azure IoT Hub kezeléséről további:

* [Műveletek figyelése][lnk-monitor]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
