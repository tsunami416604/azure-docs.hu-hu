---
title: Metrikák használata az Azure-IoT Hub figyeléséhez | Microsoft Docs
description: Az Azure IoT Hub metrikáinak használata az IoT-hubok általános állapotának felmérésére és figyelésére.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 808320f89c4dbeca835fc5a710ea1566199f6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791843"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>IoT Hub mérőszámok ismertetése

IoT Hub mérőszámok információkkal szolgálnak az Azure-előfizetésében található Azure IoT-erőforrások állapotáról. IoT Hub mérőszámok segítenek felmérni a IoT Hub szolgáltatás és a hozzá csatlakoztatott eszközök általános állapotát. Ezek a felhasználók felé irányuló statisztikák segítenek megtekinteni, hogy mi történik az IoT hub-ban, és segítsen a problémák kiváltó okának elemzésében anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

A metrikák alapértelmezés szerint engedélyezve vannak. A Azure Portal IoT Hub metrikákat is megtekintheti.

> [!NOTE]
> IoT Hub metrikák használatával megtekintheti a IoT Hubhoz csatlakoztatott IoT Plug and Play eszközök adatait. A IoT Plug and Play-eszközök a [IoT Plug and Play nyilvános előzetes](../iot-pnp/overview-iot-plug-and-play.md)verzió részét képezik.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub mérőszámok megtekintése

1. Hozzon létre egy IoT hubot. Az IoT hub létrehozásával kapcsolatos útmutatást a [telemetria küldése eszközről IoT hub](quickstart-send-telemetry-dotnet.md) útmutatóban találja.

2. Nyissa meg az IoT hub paneljét. Kattintson a **metrikák**elemre.
   
    ![Képernyőfelvétel: a metrikák beállításának helye IoT Hub erőforrás oldalon](./media/iot-hub-metrics/enable-metrics-1.png)

3. A metrikák panelen megtekintheti az IoT hub metrikáit, és egyéni nézeteket hozhat létre a metrikák alapján. 
   
    ![Képernyőkép a IoT Hub metrikáit megjelenítő oldalról](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Kiválaszthatja, hogy a metrikák adatait egy Event Hubs-végpontba vagy egy Azure Storage-fiókba küldje el, ehhez kattintson a **diagnosztikai beállítások**, majd a **diagnosztikai beállítás hozzáadása**lehetőségre.

   ![A diagnosztikai beállítások gombjának helyét bemutató képernyőfelvétel](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub mérőszámok és azok használatának módjai

A IoT Hub számos mérőszámot biztosít, amely áttekintést nyújt a központ állapotáról és a csatlakoztatott eszközök teljes számáról. Több mérőszámból is egyesítheti az adatokat, így az IoT hub állapotának nagyobb képe is kifesthető. A következő táblázat ismerteti az egyes IoT hub-sávok metrikáit, valamint azt, hogy az egyes mérőszámok hogyan kapcsolódnak az IoT hub általános állapotához.

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|RoutingDeliveries | Útválasztási kézbesítési kísérletek (előzetes verzió) | Darabszám | Összesen |Ez az útválasztási kézbesítési metrika. A méretek segítségével azonosíthatja egy adott végpont vagy egy adott útválasztási forrás kézbesítési állapotát.| ResourceId<br>Találat<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Végpontneve<br>*A dimenziókkal kapcsolatos [**here**](#dimensions)további részletek*. |
|RoutingDeliveryLatency| Útválasztási késés (előzetes verzió) | Ezredmásodpercben | Átlag |Ez az útválasztási kézbesítés késésének mérőszáma. A méretek segítségével azonosíthatja egy adott végpont késését, vagy egy adott útválasztási forrás esetében.| ResourceId<br>RoutingSource,<br>EndpointType,<br>Végpontneve<br>*A dimenziókkal kapcsolatos [**here**](#dimensions)további részletek*.|
|RoutingDataSizeInBytesDelivered| A kézbesítési adatmennyiség mérete bájtban (előzetes verzió)| Bájt | Összesen |A IoT Hub által az egyéni végponthoz és a beépített végponthoz irányított bájtok teljes száma. A méretek használatával azonosíthatja az adott végponthoz vagy egy adott útválasztási forráshoz irányított adatméretet.| ResourceId<br>RoutingSource,<br>EndpointType<br>Végpontneve<br>*A dimenziókkal kapcsolatos [**here**](#dimensions)további részletek*.|
|D2C. telemetria. behatolások.<br>allProtocol|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|None|
|D2C. telemetria. behatolások.<br>sikeres|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|None|
|C2D. commands. kimenő.<br>Befejezés. sikeres|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|None|
|C2D. commands. kimenő.<br>a művelet elhagyása|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|None|
|C2D. commands. kimenő.<br>elutasítás. sikeres|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|None|
|C2DMessagesExpired|C2D-üzenetek lejárt (előzetes verzió)|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|None|
|Devices. totalDevices|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|None|
|Devices. connectedDevices.<br>allProtocol|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|None|
|D2C. telemetria. kimenő.<br>sikeres|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|None|
|D2C. telemetria. kimenő.<br>csökkent|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|None|
|D2C. telemetria. kimenő.<br>Árva|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|Az üzenetek számának IoT Hub az útválasztása, mert nem feleltek meg egyetlen útválasztási lekérdezésnek sem, ha a tartalék útvonal le van tiltva.|None|
|D2C. telemetria. kimenő.<br>Érvénytelen|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Egy üzenet nem kompatibilis egy végponttal, amikor az IOT hub megkísérli az üzenetet egy végpontnak kézbesíteni, és nem átmeneti hibával meghiúsul. Érvénytelen üzenetek nem lesznek újrapróbálkozva. Ez az érték nem tartalmazza az újrapróbálkozásokat.|None|
|D2C. telemetria. kimenő.<br>tartalék|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|None|
|D2C. endpoints. kimenő.<br>eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket az Event hub típusú egyéni végpontokra. Ez nem tartalmazza a beépített végponthoz (eseményekhez) tartozó üzenetek útvonalait.|None|
|D2C. endpoints. késleltetés.<br>eventHubs|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub típusú egyéni végpontokra. Ez nem tartalmazza a beépített végponthoz (eseményekhez) tartozó üzenetek útvonalait.|None|
|D2C. endpoints. kimenő.<br>serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|None|
|D2C. endpoints. késleltetés.<br>serviceBusQueues|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|A Service Bus üzenetsor-végpontba beérkező üzenetek IoT Hub és az üzenetbe való belépések közötti átlagos késés (ezredmásodpercben).|None|
|D2C. endpoints. kimenő.<br>serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|None|
|D2C. endpoints. késleltetés.<br>serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) Service Bus témakör-végpontba.|None|
|D2C. endpoints. kimenő.<br>Beépített események|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon esetek száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket a beépített végpontba (üzenetek/események) és a tartalék útvonalba.|None|
|D2C. endpoints. késleltetés.<br>Beépített események|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A bejövő végpont (üzenetek/események) és a tartalék útvonal között a IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben).|None|
|D2C. endpoints. kimenő.<br>storage|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|None|
|D2C. endpoints. késleltetés.<br>storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő elérésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|None|
|D2C. endpoints. kimenő.<br>Storage. Bytes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|None|
|D2C. endpoints. kimenő.<br>Storage. Blobok|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|None|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|ResourceId<br/>Találat<br/>EventType|
|EventGridLatency|Event Grid késés (előzetes verzió)|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|ResourceId<br/>EventType|
|D2C. Twin. Read. success|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|None|
|D2C. Twin. Read. failure|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|None|
|D2C. Twin. Read. size|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|None|
|D2C. Twin. Update. success|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|None|
|D2C. Twin. Update. failure|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|None|
|D2C. Twin. Update. size|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés teljes mérete.|None|
|C2D. Methods. success|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|None|
|C2D. Methods. failure|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|None|
|C2D. Methods. requestSize|A közvetlen metódus meghívásának mérete|Bájt|Átlag|A közvetlen metódusok összes sikeres kérelmének száma.|None|
|C2D. Methods. responseSize|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának száma.|None|
|C2D. Twin. Read. success|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Read. failure|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Read. size|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Update. success|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|None|
|C2D. Twin. Update. failure|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|None|
|C2D. Twin. Update. size|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér által kezdeményezett dupla frissítés teljes mérete.|None|
|twinQueries. success|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|None|
|twinQueries. hiba|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|None|
|twinQueries.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének teljes mérete.|None|
|Jobs. createTwinUpdateJob.<br>sikeres|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|None|
|Jobs. createTwinUpdateJob.<br>hiba|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|None|
|Jobs. createDirectMethodJob.<br>sikeres|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|None|
|Jobs. createDirectMethodJob.<br>hiba|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|None|
|feladatok. listJobs. sikeres|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|None|
|feladatok. listJobs. hiba|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|None|
|feladatok. cancelJob. sikeres|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|None|
|feladatok. cancelJob. hiba|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|None|
|feladatok. queryJobs. sikeres|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|None|
|feladatok. queryJobs. hiba|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|None|
|feladatok. kész|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|None|
|feladatok. sikertelen|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|None|
|D2C. telemetria. behatolások.<br>sendThrottle|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|None|
|dailyMessageQuotaUsed|A felhasznált üzenetek teljes száma|Darabszám|Átlag|A jelenleg használt üzenetek teljes száma. Ez egy kumulatív érték, amely minden nap 00:00-kor, a nulla időpontra visszaáll.|None|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|None|
|deviceDataUsageV2|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|None|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|None|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|None|
|konfigurációk|Konfigurációs mérőszámok|Darabszám|Összesen|Az eszközök konfigurálásához és IoT Edge üzembe helyezéséhez végrehajtott összes szifilisz-művelet száma a céleszköz készletén. Ebbe beletartozik azoknak a műveleteknek a száma is, amelyek az eszköz Twin vagy modulját módosítják ezen konfigurációk miatt.|None|

### <a name="dimensions"></a>Dimenziók
A dimenziók segítenek azonosítani a metrikák további részleteit. Az útválasztási metrikák némelyike végponti információkat biztosít. Az alábbi táblázat felsorolja a méretek lehetséges értékeit.

|Dimenzió|Értékek|
|---|---|
|ResourceID|az IoT Hub erőforrás|
|Eredmény|sikeres<br>hiba|
|RoutingSource|Eszköz üzenetei<br>Kettős változási események<br>Eszközök életciklusával kapcsolatos események|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>beépített<br>blobStorage|
|FailureReasonCategory|Érvénytelen<br>csökkent<br>Árva<br>null|
|Végpontneve|végpont neve|

## <a name="next-steps"></a>További lépések

Most, hogy már látott áttekintést IoT Hub mérőszámokról, kövesse ezt a hivatkozást az Azure IoT Hub kezelésével kapcsolatos további információkért:

* [Diagnosztikai naplók beállítása](iot-hub-monitor-resource-health.md)

* [Útmutató az üzenetek útválasztásának hibakereséséhez](troubleshoot-message-routing.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
