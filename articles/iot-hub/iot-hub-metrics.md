---
title: Metrikák használata az Azure-IoT Hub figyeléséhez | Microsoft Docs
description: Az Azure IoT Hub metrikáinak használata az IoT-hubok általános állapotának felmérésére és figyelésére.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 948cdb2ab1af3fe93566497186c025f7f8f39b2e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877082"
---
# <a name="understand-iot-hub-metrics"></a>IoT Hub mérőszámok ismertetése

IoT Hub mérőszámok jobb adatokat biztosítanak az Azure-előfizetésben található Azure IoT-erőforrások állapotáról. IoT Hub mérőszámok lehetővé teszik a IoT Hub szolgáltatás és a hozzá csatlakozó eszközök általános állapotának felmérését. A felhasználók felé irányuló statisztikák azért fontosak, mert segítenek megtekinteni, hogy mi történik az IoT hub-ban, és segítsen a kiváltó problémák megoldásában anélkül, hogy fel kellene vennie a kapcsolatot az Azure ügyfélszolgálatával.

A metrikák alapértelmezés szerint engedélyezve vannak. A Azure Portal IoT Hub metrikákat is megtekintheti.

> [!NOTE]
> IoT Hub metrikák használatával megtekintheti a IoT Hubhoz csatlakoztatott IoT Plug and Play eszközök adatait. A IoT Plug and Play-eszközök a [IoT Plug and Play nyilvános előzetes](../iot-pnp/overview-iot-plug-and-play.md)verzió részét képezik.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub mérőszámok megtekintése

1. Hozzon létre egy IoT hubot. Az IoT hub létrehozásával kapcsolatos útmutatást a [telemetria küldése eszközről IoT hub](quickstart-send-telemetry-dotnet.md) útmutatóban találja.

2. Nyissa meg az IoT hub paneljét. Kattintson a metrikákelemre.
   
    ![Képernyőfelvétel: a metrikák beállításának helye IoT Hub erőforrás oldalon](./media/iot-hub-metrics/enable-metrics-1.png)

3. A metrikák panelen megtekintheti az IoT hub metrikáit, és egyéni nézeteket hozhat létre a metrikák alapján. 
   
    ![Képernyőkép a IoT Hub metrikáit megjelenítő oldalról](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Kiválaszthatja, hogy a metrikák adatait egy Event Hubs-végpontba vagy egy Azure Storage-fiókba küldje el a **diagnosztikai beállítások**, majd a **diagnosztikai beállítás hozzáadása** lehetőségre kattintva.

   ![A diagnosztikai beállítások gombjának helyét bemutató képernyőfelvétel](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub mérőszámok és azok használatának módjai

A IoT Hub számos mérőszámot biztosít, amely áttekintést nyújt a központ állapotáról és a csatlakoztatott eszközök teljes számáról. Több mérőszámból is egyesítheti az adatokat, így az IoT hub állapotának nagyobb képe is kifesthető. A következő táblázat ismerteti az egyes IoT hub-sávok metrikáit, valamint azt, hogy az egyes mérőszámok hogyan kapcsolódnak az IoT hub általános állapotához.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|D2C<br>. telemetria<br>behatolása.<br>allProtocol|Telemetria üzenetek küldése|Count|Összes|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincs dimenzió|
|D2C<br>. telemetria<br>. bejövő forgalom<br>. sikeres|Telemetria üzenetek elküldése|Count|Összes|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincs dimenzió|
|C2D<br>. parancsok<br>. kimenő forgalom<br>. Befejezés<br>. sikeres|A parancsok befejeződtek|Count|Összes|Az eszköz által sikeresen befejeződött a felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|C2D<br>. parancsok<br>. kimenő forgalom<br>.abandon<br>. sikeres|Elhagyott parancsok|Count|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|C2D<br>. parancsok<br>. kimenő forgalom<br>. elutasítás<br>. sikeres|Elutasított parancsok|Count|Összes|Az eszköz által visszautasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|eszközök<br>.totalDevices|Összes eszköz (elavult)|Count|Összes|Az IoT hub-ban regisztrált eszközök száma|Nincs dimenzió|
|eszközök<br>.connectedDevices<br>.allProtocol|Csatlakoztatott eszközök (elavult) |Count|Összes|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs dimenzió|
|D2C<br>. telemetria<br>. kimenő forgalom<br>. sikeres|Útválasztás: telemetria üzenetek kézbesítése|Count|Összes|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincs dimenzió|
|D2C<br>. telemetria<br>. kimenő forgalom<br>. eldobott|Útválasztás: telemetria üzenetek elvetve |Count|Összes|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincs dimenzió|
|D2C<br>. telemetria<br>. kimenő forgalom<br>. árva|Útválasztás: árva telemetria üzenetek |Count|Összes|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincs dimenzió|
|D2C<br>. telemetria<br>. kimenő forgalom<br>. érvénytelen|Útválasztás: Inkompatibilis telemetria-üzenetek|Count|Összes|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|D2C<br>. telemetria<br>. kimenő forgalom<br>. tartalék|Útválasztás: tartalékként továbbított üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>.eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincs dimenzió|
|D2C<br>. végpontok<br>. késés<br>.eventHubs|Útválasztás: az Event hub üzenetének késése|Ezredmásodperc|Average|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>.serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincs dimenzió|
|D2C<br>. végpontok<br>. késés<br>.serviceBusQueues|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodperc|Average|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>.serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincs dimenzió|
|D2C<br>. végpontok<br>. késés<br>.serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodperc|Average|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>. beépített<br>. események|Útválasztás: üzenetek/események küldésére küldött üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események). Ez a metrika csak akkor kezd működni, ha az https://aka.ms/iotrouting) útválasztás engedélyezve van (az IoT hub esetében.|Nincs dimenzió|
|D2C<br>. végpontok<br>. késés<br>. beépített események|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodperc|Average|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események). Ez a metrika csak akkor kezd működni, ha az https://aka.ms/iotrouting) útválasztás engedélyezve van (az IoT hub esetében.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>. Storage|Útválasztás: a tárolóba küldött üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincs dimenzió|
|D2C<br>. végpontok<br>. késés<br>. Storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodperc|Average|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>. Storage<br>. bájt|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összes|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincs dimenzió|
|D2C<br>. végpontok<br>. kimenő forgalom<br>. Storage<br>. Blobok|Útválasztás: tárolóba szállított Blobok|Count|Összes|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincs dimenzió|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Count|Összes|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát https://aka.ms/ioteventgrid) (. Ha szeretné megtekinteni, hogy a kérelmek honnan származnak, használja a EventType dimenziót.|Eredmény, EventType|
|EventGridLatency|Event Grid késés (előzetes verzió)|Ezredmásodperc|Average|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|D2C<br>. Twin<br>. Read<br>. sikeres|Sikeres dupla olvasások az eszközökről|Count|Összes|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|D2C<br>. Twin<br>. Read<br>. hiba|Sikertelen dupla olvasások az eszközökről|Count|Összes|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincs dimenzió|
|D2C<br>. Twin<br>. Read<br>. size|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Average|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincs dimenzió|
|D2C<br>. Twin<br>. frissítés<br>. sikeres|Sikeres dupla frissítések az eszközökről|Count|Összes|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincs dimenzió|
|D2C<br>. Twin<br>. frissítés<br>. hiba|Sikertelen dupla frissítések az eszközökről|Count|Összes|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|D2C<br>. Twin<br>. frissítés<br>. size|Az eszközökből származó kettős frissítések mérete|Bájt|Average|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincs dimenzió|
|C2D<br>. metódusok<br>. sikeres|Közvetlen metódusok sikeres meghívása|Count|Összes|Az összes sikeres közvetlen metódus hívásának száma.|Nincs dimenzió|
|C2D<br>. metódusok<br>. hiba|Sikertelen közvetlen metódusok meghívása|Count|Összes|A sikertelen közvetlen metódusok összes hívásának száma.|Nincs dimenzió|
|C2D<br>. metódusok<br>.requestSize|A közvetlen metódus meghívásának mérete|Bájt|Average|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincs dimenzió|
|C2D<br>. metódusok<br>.responseSize|A közvetlen metódus-meghívások válaszának mérete|Bájt|Average|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincs dimenzió|
|C2D<br>. Twin<br>. Read<br>. sikeres|Sikeres dupla olvasások a háttérből|Count|Összes|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|C2D<br>. Twin<br>. Read<br>. hiba|Sikertelen dupla olvasások a háttérből|Count|Összes|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|C2D<br>. Twin<br>. Read<br>. size|Dupla olvasások válaszának mérete a háttérből|Bájt|Average|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincs dimenzió|
|C2D<br>. Twin<br>. frissítés<br>. sikeres|Sikeres dupla frissítések a háttérből|Count|Összes|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|C2D<br>. Twin<br>. frissítés<br>. hiba|Sikertelen dupla frissítések a háttérből|Count|Összes|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|C2D<br>. Twin<br>. frissítés<br>. size|Dupla frissítések mérete a háttérből|Bájt|Average|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincs dimenzió|
|twinQueries<br>. sikeres|Sikeres Twin-lekérdezések|Count|Összes|Az összes sikeres dupla lekérdezés száma.|Nincs dimenzió|
|twinQueries<br>. hiba|Sikertelen dupla lekérdezések|Count|Összes|Az összes sikertelen dupla lekérdezés száma.|Nincs dimenzió|
|twinQueries<br>.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Average|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincs dimenzió|
|feladatok<br>.createTwinUpdateJob<br>. sikeres|A kettős frissítési feladatok sikeres létrehozása|Count|Összes|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincs dimenzió|
|feladatok<br>.createTwinUpdateJob<br>. hiba|A kettős frissítési feladatok sikertelen létrehozása|Count|Összes|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincs dimenzió|
|feladatok<br>.createDirectMethodJob<br>. sikeres|Metódus-Meghívási feladatok sikeres létrehozása|Count|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincs dimenzió|
|feladatok<br>.createDirectMethodJob<br>. hiba|Nem sikerült létrehozni a metódus Meghívási feladatait|Count|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincs dimenzió|
|feladatok<br>.listJobs<br>. sikeres|Sikeres hívások a feladatok listázásához|Count|Összes|A feladatok listázására irányuló sikeres hívások száma.|Nincs dimenzió|
|feladatok<br>.listJobs<br>. hiba|Sikertelen hívások a feladatok listázásához|Count|Összes|A feladatokat listázó sikertelen hívások száma.|Nincs dimenzió|
|feladatok<br>.cancelJob<br>. sikeres|Sikeres feladatok törlése|Count|Összes|A feladat megszakítására irányuló sikeres hívások száma.|Nincs dimenzió|
|feladatok<br>.cancelJob<br>. hiba|Sikertelen feladatok megszakítása|Count|Összes|A feladat megszakítására irányuló sikertelen hívások száma.|Nincs dimenzió|
|feladatok<br>.queryJobs<br>. sikeres|Sikeres feladatok lekérdezése|Count|Összes|A lekérdezési feladatok összes sikeres hívásának száma.|Nincs dimenzió|
|feladatok<br>.queryJobs<br>. hiba|Sikertelen feladatok lekérdezése|Count|Összes|A lekérdező feladatok összes sikertelen hívásának száma.|Nincs dimenzió|
|feladatok<br>. befejezve|Befejezett feladatok|Count|Összes|Az összes befejezett feladat száma.|Nincs dimenzió|
|feladatok<br>. sikertelen|Sikertelen feladatok|Count|Összes|Az összes sikertelen feladat száma.|Nincs dimenzió|
|D2C<br>. telemetria<br>. bejövő forgalom<br>.sendThrottle|Szabályozási hibák száma|Count|Összes|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincs dimenzió|
|dailyMessage<br>QuotaUsed|A felhasznált üzenetek teljes száma|Count|Average|A jelenleg használt üzenetek teljes száma. Ez egy kumulatív érték, amely minden nap 00:00-kor, a nulla időpontra visszaáll.|Nincs dimenzió|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összes|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Count|Average|Az IoT hub-ban regisztrált eszközök száma|Nincs dimenzió|
|csatlakoztatott<br>Eszközök száma|Csatlakoztatott eszközök (előzetes verzió)|Count|Average|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs dimenzió|
|konfigurációk|Konfigurációs mérőszámok|Count|Összes|A konfigurációs műveletek metrikái|Nincs dimenzió|

## <a name="next-steps"></a>További lépések

Most, hogy már látott áttekintést IoT Hub mérőszámokról, kövesse ezt a hivatkozást az Azure IoT Hub kezelésével kapcsolatos további információkért:

* [Műveletek figyelése](iot-hub-operations-monitoring.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)

* [AI üzembe helyezése az Edge-eszközökön Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
