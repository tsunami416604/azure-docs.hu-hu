---
title: Metrikák használatával figyelheti az Azure IoT Hub |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub mérőszámok segítségével mérheti fel, és az IoT-központok általános állapotának monitorozásához.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 6afebfe9a5db713e31fed0acd2e8ad7244f30037
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274919"
---
# <a name="understand-iot-hub-metrics"></a>Understand IoT Hub metrics

Az IoT Hub mérőszámok segítségével az Azure IoT-erőforrások állapotának jobb adatait az Azure-előfizetésében. Az IoT Hub-metrikák lehetővé teszik annak ellenőrzéséhez, az IoT Hub szolgáltatás és a hozzá csatlakozó eszközök általános állapotát. Felhasználói statisztikák fontosak, mert ezek segítenek tekintse meg, mi történik az IoT hub és a Súgó kiváltó problémákat anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Alapértelmezés szerint engedélyezve vannak a metrikákat. Az IoT Hub-mérőszámok az Azure Portalon tekintheti meg.

## <a name="how-to-view-iot-hub-metrics"></a>Az IoT Hub-metrikák megtekintése

1. Hozzon létre egy IoT hubot. Az IoT hub létrehozása a talál útmutatást a [telemetriát küldjön az eszközről az IoT Hub](quickstart-send-telemetry-dotnet.md) útmutató.

2. Az IoT hub panel megnyitásához. Itt kattintson **metrikák**.
   
    ![Ahol a metrikák lehetőség van az IoT Hub-erőforrások oldalának képernyőképe](./media/iot-hub-metrics/enable-metrics-1.png)

3. A metrikák paneljén a metrikákat tekinthet meg az IoT hub, és a metrikák, egyéni nézeteket hozhat létre. 
   
    ![Az IoT Hub a metrikák lapot ábrázoló képernyőfelvétel](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. A metrikák adatai küld az Event Hubs a végpont vagy egy Azure Storage-fiók kattintva választhatja **diagnosztikai beállítások**, majd **diagnosztikai beállítás hozzáadása**

   ![Képernyőfelvétel: hol van a diagnosztikai beállítások gomb](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Az IoT Hub-mérőszámok és azok használatát

IoT Hub által biztosított több metrikát, hogy a hub és az összes csatlakoztatott eszközök állapotának áttekintése. Egyesíthet több metrikát, hogy nagyobb méretű képet kapjon az IoT hub állapotának adatait. A következő táblázat ismerteti a mérőszámok nyomon követi az egyes IoT hubot, és mindegyik metrikát hogyan viszonyul az IoT hub összesített állapotát.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|Telemetriai üzenetet küld kísérletek|Count|Összes|Küldését az IoT hub próbált eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|d2c<br>.telemetry<br>.ingress<br>.SUCCESS|Telemetriai üzeneteket küldi|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|c2d<br>.commands<br>.egress<br>.Complete<br>.SUCCESS|A parancsok befejeződött|Count|Összes|Az eszköz által végrehajtott művelet felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d<br>.commands<br>.egress<br>.abandon<br>.SUCCESS|Elhagyott parancsok|Count|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d<br>.commands<br>.egress<br>.Reject<br>.SUCCESS|Elutasított parancsok|Count|Összes|Az eszköz által elutasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|eszközök<br>.totalDevices|(Elavult az) összes eszköz|Count|Összes|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|eszközök<br>.connectedDevices<br>.allProtocol|(Elavult) csatlakoztatott eszközök |Count|Összes|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c<br>.telemetry<br>.egress<br>.SUCCESS|Útválasztási: telemetriai üzenetet|Count|Összes|Többször is sikeresen kézbesíti az üzeneteket az IoT Hub útválasztás használatával az összes végpontok száma. Ha egy üzenet van irányítva több végpontot, ez az érték minden egyes sikeres kézbesítés eggyel nő. Egy üzenet szállítják egyazon végpont több alkalommal, ha ez az érték minden egyes sikeres kézbesítés eggyel nő.|Nincs dimenzió|
|d2c<br>.telemetry<br>.egress<br>.dropped|Útválasztási: telemetriai üzeneteket eldobása |Count|Összes|Hányszor üzenetek kézbesíthetetlen végpontok miatt útválasztási IoT Hub által el lettek dobva. Ez az érték nem számít üzenetet tartalék útvonal, az eldobott üzenetek jelenjenek meg nem létezik.|Nincs dimenzió|
|d2c<br>.telemetry<br>.egress<br>.Orphaned|Útválasztási: telemetriai üzeneteket árva |Darabszám|Összes|Hányszor üzenetek által az IoT Hub-útválasztás árva is, mert (beleértve a tartalék szabály) minden olyan útválasztási szabályok nem egyeznek. |Nincs dimenzió|
|d2c<br>.telemetry<br>.egress<br>.Invalid|Útválasztási: telemetria inkompatibilis üzenetek|Darabszám|Összes|Hányszor útválasztás az IoT Hub kézbesíti az üzeneteket egy alkalmazással a végponthoz való inkompatibilitás miatt nem sikerült. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|d2c<br>.telemetry<br>.egress<br>.fallback|Útválasztási: kézbesíti az üzeneteket tartalék|Darabszám|Összes|Hányszor útválasztás az IoT Hub-üzenetek a végponthoz társított a tartalék útvonal i.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|Útválasztási: kézbesíti az üzeneteket az Eseményközpont|Count|Összes|Hányszor sikeresen útválasztás az IoT Hub Event Hub-végpontok üzenetek érkeznek.|Nincs dimenzió|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|Útválasztási: Event Hub üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a bejövő üzenet be egy Eseményközpont-végpontra.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|Útválasztási: üzenetek kézbesítése Service Bus-üzenetsorba helyezése|Count|Összes|Hányszor sikeresen útválasztás az IoT Hub-üzenetek i a Service Bus üzenetsor-végpontokra irányuló.|Nincs dimenzió|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|Útválasztási: Service Bus-üzenetsor üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-üzenetsor végpont.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|Útválasztási: üzenetek kézbesítése Service Bus-témakörbe|Count|Összes|Hányszor sikeresen útválasztás az IoT Hub-i üzenetek a Service Bus-témakör végpontokat.|Nincs dimenzió|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|Útválasztási: Service Bus-témakör üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-témakör végpont.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|Útválasztási: kézbesíti az üzeneteket üzenetek/események|Count|Összes|Hányszor az IoT Hub sikeresen útválasztási üzenetek érkeznek a beépített végpont (üzenetek/események). Ez a metrika csak megkezdi a munkát, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub.|Nincs dimenzió|
|d2c<br>.endpoints<br>.latency<br>.builtIn.events|Útválasztási: üzenet közel valós idejű üzenetek/események|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő forgalom, a beépített végpont (üzenetek/események). Ez a metrika csak megkezdi a munkát, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.Storage|Útválasztási: kézbesíti az üzeneteket storage|Darabszám|Összes|Többször az IoT Hub sikeresen útválasztási üzenetek érkeznek tárolási végpontok száma.|Nincs dimenzió|
|d2c<br>.endpoints<br>.latency<br>.Storage|Útválasztási: üzenet késése storage|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy storage-végponthoz.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.Storage<br>.bytes|Útválasztási: az adatokat kézbesíti storage|Bájt|Összes|Adatok (bájt) mennyisége az IoT Hub útválasztás kézbesíti a tárolási végpontok.|Nincs dimenzió|
|d2c<br>.endpoints<br>.egress<br>.Storage<br>.blobs|Útválasztási: blobok kézbesíteni storage|Count|Összes|The number of times IoT Hub routing delivered blobs to storage endpoints.|Nincs dimenzió|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Count|Összes|Az IoT Hub-események száma az Event Gridbe közzé. Az eredmény dimenzió használata a sikeres és sikertelen kérések száma. Esemény típusa dimenzió jeleníti meg az esemény típusát (https://aka.ms/ioteventgrid). A hol a kérelmeket a biztosítja, az esemény típusa dimenzió használatával.|Eredmény, eseménytípus|
|EventGridLatency|Event Grid késés (előzetes verzió)|Ezredmásodperc|Átlag|Átlagos késése (ezredmásodpercben), amikor az Iot Hub event jött létre amikor az esemény közzé lett téve az Event Gridbe. Ez a szám az összes eseménytípust közötti átlagos. Az esemény típusa dimenzió segítségével megtekintheti az esemény egy adott típusú késését.|EventType|
|d2c<br>.twin<br>.Read<br>.SUCCESS|A sikeres ikerírási eszközökről|Count|Összes|Az összes sikeres eszköz által kezdeményezett ikerírási száma.|Nincs dimenzió|
|d2c<br>.twin<br>.Read<br>.failure|Nem sikerült az iker írási eszközökről|Count|Összes|A teljes számát a eszköz által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|d2c<br>.twin<br>.Read<br>.size|Válasz mérete ikerírási eszközökről|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres, a páros olvasási.|Nincs dimenzió|
|d2c<br>.twin<br>.Update<br>.SUCCESS|Eszközök sikeres ikereszköz-frissítések|Count|Összes|Az összes sikeres eszköz által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|d2c<br>.twin<br>.Update<br>.failure|Nem sikerült az eszköz az ikereszköz-frissítések|Darabszám|Összes|A szám az összes eszköz által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|d2c<br>.twin<br>.Update<br>.size|Ikereszköz-frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres ikereszköz frissítéseket.|Nincs dimenzió|
|c2d<br>.Methods<br>.SUCCESS|A sikeres közvetlen metódus meghívásához|Count|Összes|Közvetlen metódus az összes sikeres hívások száma.|Nincs dimenzió|
|c2d<br>.Methods<br>.failure|Nem sikerült a közvetlen metódus meghívásához|Darabszám|Összes|A teljes számát nem sikerült a közvetlen metódust hívja.|Nincs dimenzió|
|c2d<br>.Methods<br>.requestSize|Kérés mérete közvetlen metódus meghívásához|Bájt|Átlag|Az átlagos, minimális és közvetlen metódus az összes sikeres kérelmek maximális száma.|Nincs dimenzió|
|c2d<br>.Methods<br>.responseSize|Közvetlen megpróbálkozni a válasz mérete|Bájt|Átlag|Az átlagos, minimális és a közvetlen metódus az összes sikeres válaszok maximális száma.|Nincs dimenzió|
|c2d<br>.twin<br>.Read<br>.SUCCESS|A sikeres ikerírási háttérrendszerből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-olvasások száma.|Nincs dimenzió|
|c2d<br>.twin<br>.Read<br>.failure|Nem sikerült a páros olvasási háttérrendszerből|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|c2d<br>.twin<br>.Read<br>.size|Válasz mérete ikerírási háttérrendszerből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett a páros olvasási.|Nincs dimenzió|
|c2d<br>.twin<br>.Update<br>.SUCCESS|Háttér sikeres ikereszköz-frissítések|Count|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|c2d<br>.twin<br>.Update<br>.failure|Háttér sikertelen ikereszköz-frissítések|Count|Összes|A teljes számát a back-end által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|c2d<br>.twin<br>.Update<br>.size|A háttérben ikereszköz-frissítések mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett ikereszköz frissítéseket.|Nincs dimenzió|
|TwinQueries<br>.SUCCESS|A sikeres ikereszköz-lekérdezések|Count|Összes|Az összes sikeres ikereszköz-lekérdezések száma.|Nincs dimenzió|
|TwinQueries<br>.failure|Sikertelen ikereszköz-lekérdezések|Darabszám|Összes|Összes sikertelen ikereszköz-lekérdezések száma.|Nincs dimenzió|
|TwinQueries<br>.resultSize|Ikereszköz-lekérdezések eredmény mérete|Bájt|Átlag|Az átlagos, minimális és az összes sikeres ikereszköz-lekérdezés eredményének mérete legfeljebb.|Nincs dimenzió|
|Feladatok<br>.createTwinUpdateJob<br>.SUCCESS|Ikereszköz-frissítési feladatok sikeres létrehozás|Count|Összes|Az összes sikeres létrehozás ikereszköz-frissítési feladatok száma.|Nincs dimenzió|
|Feladatok<br>.createTwinUpdateJob<br>.failure|Sikertelen létrehozás ikereszköz-frissítési feladatok|Count|Összes|Minden létrehozására tett sikertelen ikereszköz frissítési feladatok száma.|Nincs dimenzió|
|Feladatok<br>.createDirectMethodJob<br>.SUCCESS|Metódus meghívása feladatok sikeres létrehozás|Count|Összes|Az összes sikeres létrehozás közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|Feladatok<br>.createDirectMethodJob<br>.failure|Metódus meghívása feladatok sikertelen létrehozás|Count|Összes|Összes sikertelen létrehozása a közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|Feladatok<br>.listJobs<br>.SUCCESS|Listázhatók a feladatok sikeres hívások|Count|Összes|Az összes sikeres hívások listázhatók a feladatok száma.|Nincs dimenzió|
|Feladatok<br>.listJobs<br>.failure|Sikertelen hívások listázhatók a feladatok|Darabszám|Összes|Az összes sikertelen hívás listázhatók a feladatok száma.|Nincs dimenzió|
|Feladatok<br>.cancelJob<br>.SUCCESS|Megszakított feladatok sikeres|Count|Összes|Megszakítja a feladatot az összes sikeres hívások száma.|Nincs dimenzió|
|Feladatok<br>.cancelJob<br>.failure|Sikertelen feladat általi hóközi lemondás|Count|Összes|Megszakítja a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|Feladatok<br>.queryJobs<br>.SUCCESS|Feladat sikeres lekérdezések|Count|Összes|Lekérdezés projektekhez az összes sikeres hívások száma.|Nincs dimenzió|
|Feladatok<br>.queryJobs<br>.failure|Sikertelen feladat lekérdezések|Count|Összes|Lekérdezés projektekhez az összes sikertelen hívások száma.|Nincs dimenzió|
|Feladatok<br>.completed|Befejezett feladatok|Count|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|Feladatok<br>.Failed|Sikertelen feladatok|Count|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|Szabályozási hibák száma|Darabszám|Összes|Eszköz átviteli miatt szabályozási hibák számának szabályozza|Nincs dimenzió|
|dailyMessage<br>QuotaUsed|Használt üzenetek teljes száma|Count|Átlag|Ma használt teljes üzenetek száma. Ez az összesített érték, amely lenullázódik: 00:00 (UTC) minden nap.|Nincs dimenzió|
|deviceDataUsage|Eszköz teljes adathasználat|Bájt|Összes|És bármilyen eszközről csatlakozik az IotHub átvitt bájtok száma|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Count|Átlag|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|Csatlakoztatva<br>DeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|Konfigurációk|Konfiguráció-metrikák|Count|Összes|A konfigurálási műveletek metrikái|Nincs dimenzió|

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az IoT Hub-metrikák áttekintése, hajtsa végre ezt a hivatkozást az Azure IoT Hub kezeléséről további:

* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)

* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
