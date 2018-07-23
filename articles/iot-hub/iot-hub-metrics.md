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
ms.openlocfilehash: b41458f0201c46b99c09d0bfffd219743a36ad50
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186755"
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

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetriai üzenetet küld kísérletek|Darabszám|Összes|Küldését az IoT hub próbált eszköz – felhő telemetriát üzenetek száma|
|d2c.telemetry.ingress.success|Telemetriai üzeneteket küldi|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz – felhő telemetriát üzenetek száma|
|c2d.commands.egress.complete.success|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott művelet felhőből az eszközre irányuló parancsok száma|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Darabszám|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|
|c2d.commands.egress.reject.success|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhőből az eszközre irányuló parancsok száma|
|devices.totalDevices|Összes eszköz|Darabszám|Összes|Az IoT hubban regisztrált eszközök száma|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök|Darabszám|Összes|Az IoT hubhoz csatlakoztatott eszközök száma|
|d2c.telemetry.egress.success|Telemetriai üzenetet|Darabszám|Összes|Hányszor üzenetek írása sikeres volt a végpontok (összesen)|
|d2c.telemetry.egress.dropped|Az eldobott üzenetek|Darabszám|Összes|Üzenetek száma vetve, mert nem felelt meg minden olyan és a tartalék útvonal le lett tiltva.|
|d2c.telemetry.egress.orphaned|Árva üzenetek|Darabszám|Összes|Nem megfelelő az összes olyan esetleges útvonalat, többek között a tartalék útvonal üzenetek száma|
|d2c.telemetry.egress.invalid|Érvénytelen üzenet|Darabszám|Összes|Az alkalmazással a végponthoz való inkompatibilitás miatt nem lett kézbesítve üzenetek száma|
|d2c.telemetry.egress.fallback|Tartalék feltételnek megfelelő üzenetek|Darabszám|Összes|A tartalék végpont üzenetek száma|
|d2c.endpoints.egress.eventHubs|Event Hub-végpontok kézbesíti az üzeneteket|Darabszám|Összes|Üzenetek írása sikeres volt Event Hub-végpontok száma|
|d2c.endpoints.latency.eventHubs|Event Hub-végpontok üzenet késése|Ezredmásodperc|Átlag|Az Eseményközpont-végpontra, ezredmásodpercben telemetriabevitelt üzenetet az IoT hub és az üzenet bejövő közötti átlagos késése|
|d2c.endpoints.egress.serviceBusQueues|Service Bus-üzenetsor végpontok kézbesíti az üzeneteket|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-üzenetsor végpontok száma|
|d2c.endpoints.latency.serviceBusQueues|Service Bus-üzenetsor végpontok üzenet késése|Ezredmásodperc|Átlag|A Service Bus-üzenetsor végpontba ezredmásodpercben telemetriabevitelt üzenetet az IoT hub és az üzenet bejövő közötti átlagos késése|
|d2c.endpoints.egress.serviceBusTopics|Service Bus-témakör végpontok kézbesíti az üzeneteket|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-témakör végpontok száma|
|d2c.endpoints.latency.serviceBusTopics|Service Bus-témakör végpontok üzenet késése|Ezredmásodperc|Átlag|A Service Bus-témakör végpontba ezredmásodpercben telemetriabevitelt üzenetet az IoT hub és az üzenet bejövő közötti átlagos késése|
|d2c.endpoints.egress.builtIn.events|Kézbesíti az üzeneteket a beépített végpont (üzenetek/események)|Darabszám|Összes|Hányszor üzenetek írása sikeres volt a beépített végpont (üzenetek/események)|
|d2c.endpoints.latency.builtIn.events|A beépített végpont (üzenetek/események) üzenet késése|Ezredmásodperc|Átlag|Az IoT hub üzenet telemetriabevitelt a bejövő és kimenő üzenetek között átlagos késése ezredmásodpercben a beépített végpontba (üzenetek/események) |
|d2c.twin.read.success|A sikeres ikerírási eszközökről|Darabszám|Összes|Az összes sikeres eszköz által kezdeményezett ikerírási száma.|
|d2c.twin.read.failure|Nem sikerült az iker írási eszközökről|Darabszám|Összes|A teljes számát a eszköz által kezdeményezett ikerírási nem sikerült.|
|d2c.twin.read.size|Válasz mérete ikerírási eszközökről|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres, a páros olvasási.|
|d2c.twin.update.success|Eszközök sikeres ikereszköz-frissítések|Darabszám|Összes|Az összes sikeres eszköz által kezdeményezett ikereszköz-frissítések száma.|
|d2c.twin.update.failure|Nem sikerült az eszköz az ikereszköz-frissítések|Darabszám|Összes|A szám az összes eszköz által kezdeményezett ikereszköz-frissítések nem sikerült.|
|d2c.twin.update.size|Ikereszköz-frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres ikereszköz frissítéseket.|
|c2d.methods.success|A sikeres közvetlen metódus meghívásához|Darabszám|Összes|Közvetlen metódus az összes sikeres hívások száma.|
|c2d.methods.failure|Nem sikerült a közvetlen metódus meghívásához|Darabszám|Összes|A teljes számát nem sikerült a közvetlen metódust hívja.|
|c2d.methods.requestSize|Kérés mérete közvetlen metódus meghívásához|Bájt|Átlag|Az átlagos, minimális és közvetlen metódus az összes sikeres kérelmek maximális száma.|
|c2d.methods.responseSize|Közvetlen megpróbálkozni a válasz mérete|Bájt|Átlag|Az átlagos, minimális és a közvetlen metódus az összes sikeres válaszok maximális száma.|
|c2d.twin.read.success|A sikeres ikerírási háttérrendszerből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-olvasások száma.|
|c2d.twin.read.failure|Nem sikerült a páros olvasási háttérrendszerből|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikerírási nem sikerült.|
|c2d.twin.read.size|Válasz mérete ikerírási háttérrendszerből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett a páros olvasási.|
|c2d.twin.update.success|Háttér sikeres ikereszköz-frissítések|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-frissítések száma.|
|c2d.twin.update.failure|Háttér sikertelen ikereszköz-frissítések|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikereszköz-frissítések nem sikerült.|
|c2d.twin.update.size|A háttérben ikereszköz-frissítések mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett ikereszköz frissítéseket.|
|twinQueries.success|A sikeres ikereszköz-lekérdezések|Darabszám|Összes|Az összes sikeres ikereszköz-lekérdezések száma.|
|twinQueries.failure|Sikertelen ikereszköz-lekérdezések|Darabszám|Összes|Összes sikertelen ikereszköz-lekérdezések száma.|
|twinQueries.resultSize|Ikereszköz-lekérdezések eredmény mérete|Bájt|Átlag|Az átlagos, minimális és az összes sikeres ikereszköz-lekérdezés eredményének mérete legfeljebb.|
|jobs.createTwinUpdateJob.success|Ikereszköz-frissítési feladatok sikeres létrehozás|Darabszám|Összes|Az összes sikeres létrehozás ikereszköz-frissítési feladatok száma.|
|jobs.createTwinUpdateJob.failure|Sikertelen létrehozás ikereszköz-frissítési feladatok|Darabszám|Összes|Minden létrehozására tett sikertelen ikereszköz frissítési feladatok száma.|
|jobs.createDirectMethodJob.success|Metódus meghívása feladatok sikeres létrehozás|Darabszám|Összes|Az összes sikeres létrehozás közvetlen metódus meghívása feladatok száma.|
|jobs.createDirectMethodJob.failure|Metódus meghívása feladatok sikertelen létrehozás|Darabszám|Összes|Összes sikertelen létrehozása a közvetlen metódus meghívása feladatok száma.|
|jobs.listJobs.success|Listázhatók a feladatok sikeres hívások|Darabszám|Összes|Az összes sikeres hívások listázhatók a feladatok száma.|
|jobs.listJobs.failure|Sikertelen hívások listázhatók a feladatok|Darabszám|Összes|Az összes sikertelen hívás listázhatók a feladatok száma.|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Darabszám|Összes|Megszakítja a feladatot az összes sikeres hívások száma.|
|jobs.cancelJob.failure|Sikertelen feladat általi hóközi lemondás|Darabszám|Összes|Megszakítja a feladatot az összes sikertelen hívások száma.|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|Lekérdezés projektekhez az összes sikeres hívások száma.|
|jobs.queryJobs.failure|Sikertelen feladat lekérdezések|Darabszám|Összes|Lekérdezés projektekhez az összes sikertelen hívások száma.|
|jobs.Completed|Befejezett feladatok|Darabszám|Összes|Az összes befejezett feladatok száma.|
|jobs.Failed|Sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|

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
