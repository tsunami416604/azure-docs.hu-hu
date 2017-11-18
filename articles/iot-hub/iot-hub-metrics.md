---
title: "Metrikák segítségével figyelheti az Azure IoT Hub |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub mérőszámok segítségével mérheti fel, és az IoT-központok általános állapotának figyelésére."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe5009b8e96b147b5bbed1957024f6d96feb58
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="understand-iot-hub-metrics"></a>Az IoT-központ metrikák ismertetése
Az IoT-központ metrikák biztosítanak az Azure IoT-erőforrások jobb adatait az Azure-előfizetése. Az IoT-központ metrikák lehetővé teszik a felmérheti az IoT-központ szolgáltatás és a hozzá csatlakozó eszközök általános állapotát. Felhasználók számára is elérhető statisztika fontosak, mert azok segíti a jelenít meg az IoT hub és súgó kiváltó okának kapcsolatos problémákkal rendelkező anélkül, hogy az Azure ügyfélszolgálatához.

Alapértelmezés szerint engedélyezve vannak a metrikákat. Az Azure-portálon az IoT-központ metrikák tekintheti meg.

## <a name="how-to-view-iot-hub-metrics"></a>Az IoT-központ metrikák megtekintése
1. Létrehoz egy IoT-központot. Az IoT-központ a létrehozásával találhat útmutatót a [Ismerkedés] [ lnk-get-started] útmutató.
2. Az IoT hub panel megnyitásához. Itt kattintson **metrikák**.
   
    ![][1]
3. A metrikák paneljéről a metrikák megtekintheti az IoT hub és a metrikák egyéni nézeteket. Ha szeretné a metrikai adatok küldése az Event Hubs végpont vagy egy Azure Storage-fiók kattintva **diagnosztikai beállítások**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Az IoT-központ metrikák és a használatukat
IoT-központ biztosít több metrikák Önnek egy áttekintést nyújt a központ és a csatlakoztatott eszközök teljes száma. Több mérni kívánt az IoT hub állapotának áttekintő képet festeni a következőről kombinálhatja. A következő táblázat ismerteti a metrikákat, minden IoT-központ nyomon követi, és hogyan mindegyik metrikát vonatkozik-e az IoT-központ az általános állapotának.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetria üzenet küldési kísérlet|Darabszám|Összes|Az IoT hub küldendő próbált eszköz-felhő telemetriai üzenetek száma|
|d2c.telemetry.ingress.SUCCESS|Küldött telemetriai üzenetek|Mennyiség|Összes|Sikerült elküldeni az IoT hub eszköz-felhő telemetriai üzenetek száma|
|c2d.commands.egress.Complete.SUCCESS|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott felhő eszközre parancsok száma|
|c2d.commands.egress.Abandon.SUCCESS|Elhagyott parancsok|Darabszám|Összes|Az eszköz által félbe hagyott felhő eszközre parancsok száma|
|c2d.commands.egress.Reject.SUCCESS|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhő eszközre parancsok száma|
|devices.totalDevices|Eszközök teljes száma|Mennyiség|Összes|Az IoT hub regisztrált eszközök száma|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök|Darabszám|Összes|Az IoT hub csatlakoztatott eszközök száma|
|d2c.telemetry.egress.SUCCESS|Telemetria kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt végpontok (összesen)|
|d2c.telemetry.egress.dropped|Az eldobott üzenetek|Mennyiség|Összes|Az üzenetek száma dobva, mert nem felelt meg egyetlen útvonalak és a tartalék útvonal le lett tiltva.|
|d2c.telemetry.egress.Orphaned|Árva üzenetek|Mennyiség|Összes|A nem megfelelő bármely útvonalakat, beleértve a tartalék útvonal üzenetek száma|
|d2c.telemetry.egress.Invalid|Érvénytelen üzenet|Mennyiség|Összes|A végpont alkalmazással való inkompatibilitás miatt nem kézbesített üzenetek száma|
|d2c.telemetry.egress.fallback|Tartalék feltételnek megfelelő üzenetek|Darabszám|Összes|A tartalék végpont üzenetek száma|
|d2c.endpoints.egress.eventHubs|Az Event Hubs végpontok kézbesített üzenetek|Mennyiség|Összes|Üzenetek írása sikeres volt az Event Hubs végpontok száma|
|d2c.endpoints.latency.eventHubs|Az Event Hubs végpontok üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között az Eseményközpont végpont, ezredmásodpercben|
|d2c.endpoints.egress.serviceBusQueues|Service Bus-üzenetsorba végpontok kézbesített üzenetek|Mennyiség|Összes|Üzenetek írása sikeres volt Service Bus-üzenetsorba végpontok száma|
|d2c.endpoints.latency.serviceBusQueues|A Service Bus-üzenetsorba végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-üzenetsorba végpont, ezredmásodpercben|
|d2c.endpoints.egress.serviceBusTopics|Service Bus-témakörbe végpontok kézbesített üzenetek|Mennyiség|Összes|Üzenetek írása sikeres volt Service Bus-témakörbe végpontok száma|
|d2c.endpoints.latency.serviceBusTopics|A Service Bus-témakörbe végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-témakörbe végpont, ezredmásodpercben|
|d2c.endpoints.egress.builtIn.events|A beépített végpont (üzenet/események) kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt a beépített végpont (üzenet/esemény)|
|d2c.endpoints.latency.builtIn.events|Üzenet várakozási ideje a beépített végpont (üzenet/esemény)|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között történő (üzenet/esemény), beépített végpont ezredmásodpercben |
|d2c.Twin.Read.SUCCESS|Sikeres iker olvassa be az eszközökön|Mennyiség|Összes|Az összes sikeres a két eszköz által kezdeményezett olvasások száma.|
|d2c.Twin.Read.failure|Nem sikerült a kettős olvasások eszközökről|Mennyiség|Összes|Teljes számát a két eszköz által kezdeményezett olvasás sikertelen volt.|
|d2c.Twin.Read.size|A két válasz mérete olvassa be az eszközökön|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres iker olvasási műveletek.|
|d2c.Twin.Update.SUCCESS|Sikeres iker frissítések eszközökről|Darabszám|Összes|A két eszköz által kezdeményezett összes sikeres frissítések száma.|
|d2c.Twin.Update.failure|Nem sikerült a két frissítések eszközökről|Mennyiség|Összes|A teljes számát a két eszköz által kezdeményezett frissítések nem sikerült.|
|d2c.Twin.Update.size|A két frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres iker frissítések.|
|c2d.methods.SUCCESS|Sikeres közvetlen módszer meghívásához|Mennyiség|Összes|Az összes sikeres közvetlen módszer hívások száma.|
|c2d.methods.failure|Nem sikerült a közvetlen módszer meghívásához|Darabszám|Összes|A teljes számát a közvetlen metódushívások nem sikerült.|
|c2d.methods.requestSize|A közvetlen módszer meghívásához kérelem mérete|Bájt|Átlag|Az átlagos, a minimális és a közvetlen módszer minden sikeres kérelmek maximális.|
|c2d.methods.responseSize|Válasz mérete közvetlen módszer meghívásához|Bájt|Átlag|Az átlagos, a minimális és a maximális minden sikeres közvetlen módszer válaszok.|
|c2d.Twin.Read.SUCCESS|Sikeres iker háttérből olvassa be|Mennyiség|Összes|Az összes sikeres back-end által kezdeményezett iker olvasási műveletek számát.|
|c2d.Twin.Read.failure|Sikertelen a két háttérből olvassa be|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett olvasások nem sikerült.|
|c2d.Twin.Read.size|A válasz méretének iker olvasások háttérből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett iker olvasási műveletek.|
|c2d.Twin.Update.SUCCESS|Sikeres iker frissítések háttérből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker frissítések száma.|
|c2d.Twin.Update.failure|Sikertelen a két frissítések háttérből|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett frissítések nem sikerült.|
|c2d.Twin.Update.size|A két frissítések háttérből mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett iker frissítések.|
|twinQueries.success|Sikeres iker lekérdezések|Darabszám|Összes|Minden sikeres iker lekérdezések száma.|
|twinQueries.failure|Sikertelen a két lekérdezések|Darabszám|Összes|Összes sikertelen iker lekérdezések száma.|
|twinQueries.resultSize|A két lekérdezések eredmény méretének|Bájt|Átlag|Az átlagos, a minimális és a maximális az eredmény méretének minden sikeres iker lekérdezések.|
|jobs.createTwinUpdateJob.success|A sikeres és a két frissítési feladatok|Darabszám|Összes|Minden sikeres létrehozása két frissítési feladatok számát.|
|jobs.createTwinUpdateJob.failure|Sikertelen és a két frissítési feladatok|Darabszám|Összes|A két frissítési feladatok során létrehozására tett sikertelen kísérlet száma.|
|jobs.createDirectMethodJob.success|A metódus meghívása feladatok sikeres létrehozása|Darabszám|Összes|Minden sikeres létrehozása közvetlen metódus meghívása feladatok száma.|
|jobs.createDirectMethodJob.failure|A metódus meghívása feladatok sikertelen számát|Mennyiség|Összes|A közvetlen metódus meghívása feladatok során létrehozására tett sikertelen kísérlet száma.|
|jobs.listJobs.success|Lista feladatok sikeres hívások|Darabszám|Összes|Az összes sikeres indított hívások száma a lista feladatok.|
|jobs.listJobs.failure|Nem sikerült a lista feladatok hívások|Mennyiség|Összes|A feladatok lista összes sikertelen hívások száma.|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Mennyiség|Összes|Az összes sikeres indított hívások száma megszakítja a feladatot.|
|jobs.cancelJob.failure|Sikertelen feladat sikertelen|Darabszám|Összes|A visszavonni a feladatot az összes sikertelen hívások száma.|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|A lekérdezés feladatok minden sikeres hívások száma.|
|jobs.queryJobs.failure|Sikertelen feladat-lekérdezések|Darabszám|Összes|A lekérdezés feladatok összes sikertelen hívások száma.|
|jobs.Completed|Befejezett feladatokhoz|Darabszám|Összes|Az összes befejezett feladatok száma.|
|jobs.Failed|A sikertelen feladatok|Mennyiség|Összes|Összes sikertelen feladatok száma.|

## <a name="next-steps"></a>Következő lépések
Most, hogy korábban már látott IoT-központ metrikák áttekintését, kattintson erre a hivatkozásra, tudhat meg többet az Azure IoT Hub kezelése:

* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Az Azure IoT peremhálózati peremeszközök AI központi telepítése][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
