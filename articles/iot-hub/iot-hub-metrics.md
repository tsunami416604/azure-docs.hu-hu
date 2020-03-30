---
title: Metrikák használata az Azure IoT Hub figyeléséhez | Microsoft dokumentumok
description: Az Azure IoT Hub-metrikák használata az IoT-központok általános állapotának felméréséhez és figyeléséhez.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284433"
---
# <a name="understand-iot-hub-metrics"></a>Az IoT Hub-metrikák ismertetése

Az IoT Hub-metrikák jobb adatokat nyújtanak az Azure IoT-erőforrások állapotáról az Azure-előfizetésben. Az IoT Hub-metrikák lehetővé teszik az IoT Hub-szolgáltatás és a hozzá csatlakoztatott eszközök általános állapotának felmérését. A felhasználók felé irányuló statisztikák azért fontosak, mert segítenek látni, hogy mi történik az IoT-központtal, és segítenek a kiváltó problémák megoldásában anélkül, hogy kapcsolatba kellene lépnie az Azure-támogatással.

A mérőszámok alapértelmezés szerint engedélyezve vannak. Az IoT Hub-metrikák az Azure Portalon megtekintheti.

> [!NOTE]
> Az IoT Hub-metrikák segítségével megtekintheti az IoT Plug and Play-eszközök, az IoT Hub hoz csatlakoztatott információkat. Az IoT Plug and Play eszközök az IoT Plug and Play nyilvános előzetes verzió részét [képezik.](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="how-to-view-iot-hub-metrics"></a>Az IoT Hub-metrikák megtekintése

1. Hozzon létre egy IoT hubot. Az IoT-központ létrehozásáról a [Telemetriai adatok küldése az eszközről](quickstart-send-telemetry-dotnet.md) az IoT Hub-útmutatóba című útmutatóban található.

2. Nyissa meg az IoT hub paneljét. Itt kattintson **a Mérőszámok gombra.**
   
    ![Képernyőkép, amely en látható, hogy hol található a metrikák beállítás az IoT Hub erőforráslapján](./media/iot-hub-metrics/enable-metrics-1.png)

3. A metrikák panelen megtekintheti az IoT hub metrikák, és a metrikák egyéni nézeteket hozhat létre. 
   
    ![Az IoT Hub metrikalapját megjelenítő képernyőkép](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. A **diagnosztikai beállítások**, majd a **Diagnosztikai beállítások hozzáadása parancsra** kattintva elküldheti a metrikák adatait egy Event Hubs-végpontnak vagy egy Azure Storage-fióknak.

   ![Képernyőkép a diagnosztikai beállítások gombjának látható](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Az IoT Hub-metrikák és használatuk

Az IoT Hub számos metrikát biztosít a hub állapotának és a csatlakoztatott eszközök teljes számának áttekintéséhez. Több metrika adatait kombinálva nagyobb képet festhet az IoT hub állapotáról. Az alábbi táblázat ismerteti az egyes IoT-központ-pályák metrikákat, és azt, hogy az egyes metrikák hogyan viszonyulnak az IoT hub általános állapotához.

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemettry.ingress.allProtocol|Telemetriai üzenet küldési kísérletei|Darabszám|Összesen|Az IoT-központba küldeni próbált eszközről felhőbe irányuló telemetriai üzenetek száma|None|
|d2c.telemetry.ingress.success|Elküldött telemetriai üzenetek|Darabszám|Összesen|Az IoT hubra küldött eszközök ről a felhőbe irányuló telemetriai üzenetek száma|None|
|c2d.commands.egress.complete.success|A C2D-üzenetek kézbesítése befejeződött|Darabszám|Összesen|Az eszköz által sikeresen végrehajtott felhőből eszközre irányuló üzenetkézbesítések száma|None|
|c2d.commands.egress.abandon.success|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott, felhőről eszközre irányuló üzenetek száma|None|
|c2d.commands.egress.reject.success|Elutasított C2D-üzenetek|Darabszám|Összesen|Az eszköz által elutasított, felhőről eszközre irányuló üzenetek száma|None|
|C2DMessagesExpired|C2D üzenetek lejárt (előzetes verzió)|Darabszám|Összesen|Az eszközről az eszközre irányuló lejárt üzenetek száma|None|
|devices.totalDevices|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hubhoz regisztrált eszközök száma|None|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hubhoz csatlakoztatott eszközök száma|None|
|d2c.telemettry.egress.success|Útválasztás: telemetriai üzenetek kézbesítve|Darabszám|Összesen|Az üzenetek sikeres kézbesítésének száma az IoT Hub útválasztáshasználatával az összes végpontra. Ha egy üzenet több végpontra van irányítva, ez az érték minden sikeres kézbesítésesetén eggyel nő. Ha egy üzenetet többször kézbesítenek ugyanarra a végpontra, ez az érték minden sikeres kézbesítésesetén eggyel nő.|None|
|d2c.telemettry.egress.dropped|Útválasztás: eldobott telemetriai üzenetek |Darabszám|Összesen|Az üzenetek zsákutca miatti eldobásának száma az IoT Hub útválasztása. Ez az érték nem számít a tartalék útvonalra kézbesített üzeneteknek, mivel az eldobott üzenetek nem kézbesítve vannak.|None|
|d2c.telemettry.egress.orphaned|Útválasztás: telemetriai üzenetek árva |Darabszám|Összesen|Az Üzenetek árván maradásának száma az IoT Hub-útválasztás miatt, mert nem felelnek meg semmilyen útválasztási szabálynak (beleértve a tartalék szabályt is). |None|
|d2c.telemettry.egress.invalid|Útválasztás: a telemetriai üzenetek nem kompatibilisek|Darabszám|Összesen|Az IoT Hub-útválasztás végpontdal való inkompatibilitás miatt nem tudott üzeneteket kézbesíteni. Ez az érték nem tartalmazza az újrapróbálkozásokat.|None|
|d2c.telemettry.egress.fallback|Útválasztás: tartalékba küldött üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás a tartalék útvonalhoz társított végpontra kézbesített üzenetek száma.|None|
|d2c.endpoints.egress.eventHubs|Útválasztás: az Event Hubba kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket Event Hub végpontok száma.|None|
|d2c.endpoints.latency.eventHubs|Útválasztás: üzenetkésés az Eseményközpontszámára|Ezredmásodperc|Átlag|Az IoT Hubba irányuló üzenetek és az Event Hub-végpontba való üzenet-be- és üzenet-be- és üzenetbe- és üzenetbe- és üzenetküldés közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.serviceBusQueues|Útválasztás: a Service Bus várólistájába kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket Service Bus várólista végpontok száma.|None|
|d2c.endpoints.latency.serviceBusQueues|Útválasztás: üzenetkésa Service Bus-várólistához|Ezredmásodperc|Átlag|Az IoT Hubba irányuló üzenet-be- és telemetriai üzenetek egy Service Bus-várólista-végpontba való be- és telemetriai üzenet közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.serviceBusTopics|Útválasztás: a Service Bus topic szolgáltatásba küldött üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket Service Bus témakör végpontok száma.|None|
|d2c.endpoints.latency.serviceBusTopics|Útválasztás: üzenetkésés a Service Bus-témakörhöz|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek egy Service Bus-témakör végpontjába való be- és telemetriai üzenet közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.builtIn.events|Útválasztás: üzenetekbe/eseményekre kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket a beépített végpont (üzenetek/események) száma.|None|
|d2c.endpoints.latency.builtIn.events|Útválasztás: üzenetek/események üzenetkéste|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek beépített végpontba (üzenetek/események) való be- és telemetriai üzenetek közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.storage|Útválasztás: a tárolóba kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen szállított üzeneteket a tárolási végpontok száma.|None|
|d2c.endpoints.latency.storage|Útválasztás: üzenet késése a tároláshoz|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek tárolási végpontba való be- és telemetriai üzenete közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.storage.bytes|Útválasztás: a tárolóba szállított adatok|Bájt|Összesen|A tárolási végpontokhoz kézbesített adatok (bájt) adatok mennyisége (bájt).|None|
|d2c.endpoints.egress.storage.blobok|Útválasztás: a tárolóba szállított blobok|Darabszám|Összesen|Az IoT Hub-útválasztás blobok tárolási végpontok kézbesítésének száma.|None|
|EventGridDeliveres|Eseményrács-szállítások(előzetes verzió)|Darabszám|Összesen|Az Event Gridben közzétett IoT Hub-események száma. Használja az Eredmény dimenziót a sikeres és sikertelen kérelmek számához. Az EventType dimenzió azhttps://aka.ms/ioteventgrid)esemény típusát ( .|Erőforrásazonosító,<br/>Eredmény<br/>EventType (Eseménytípus)|
|EventGridLatency|Eseményrács késése (előzetes verzió)|Ezredmásodperc|Átlag|Az iot hub esemény létrehozásának időpontjától az esemény eseménynaptárban való közzétételének időpontjáig eltelt átlagos késés (ezredmásodperc). Ez a szám az összes eseménytípus átlaga. Az EventType dimenzió val egy adott eseménytípus késésének megtekintéséhez.|Erőforrásazonosító,<br/>EventType (Eseménytípus)|
|d2c.twin.read.success|Sikeres ikerolvasás eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett ikerolvasás száma.|None|
|d2c.twin.read.failure|Nem sikerült ikerbeolvasás az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett ikerolvasás száma.|None|
|d2c.twin.read.méret|Az eszközökről beolvasások válaszmérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett ikerolvasás átlaga, percés max.|None|
|d2c.twin.update.success|Sikeres ikerfrissítések az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett ikerfrissítés száma.|None|
|d2c.twin.update.failure|Nem sikerült ikerfrissítés az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett ikerfrissítés száma.|None|
|d2c.twin.update.size|Az eszközökről származó ikerfrissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett ikerfrissítés átlagos, min és maximális mérete.|None|
|c2d.methods.success|Sikeres közvetlen metódus-meghívások|Darabszám|Összesen|Az összes sikeres közvetlen metódushívás száma.|None|
|c2d.methods.failure|Sikertelen közvetlen metódus-meghívások|Darabszám|Összesen|A sikertelen közvetlen metódushívások száma.|None|
|c2d.methods.requestMéret|A közvetlen metódusmetódusok kérésének mérete|Bájt|Átlag|Az összes sikeres közvetlen metóduskérés átlaga, perce és max.|None|
|c2d.methods.responseMéret|A közvetlen metódusmetódusok válaszmérete|Bájt|Átlag|Az összes sikeres közvetlen metódusválasz átlaga, percés max.|None|
|c2d.twin.read.success|Sikeres ikerolvasás háttérből|Darabszám|Összesen|Az összes sikeres háttér-kezdeményezett ikerolvasás oka.|None|
|c2d.twin.read.failure|Sikertelen ikerolvasás háttérből|Darabszám|Összesen|Az összes sikertelen háttér-kezdeményezett ikerolvasások száma.|None|
|c2d.twin.read.size|A háttérből beolvasások válaszmérete|Bájt|Átlag|Az összes sikeres háttér-kezdeményezett ikerolvasás átlaga, min és max.|None|
|c2d.twin.update.success|Sikeres ikerfrissítések a háttérrendszerből|Darabszám|Összesen|Az összes sikeres háttér-kezdeményezett ikerfrissítés száma.|None|
|c2d.twin.update.failure|Sikertelen ikerfrissítések a háttérrendszerből|Darabszám|Összesen|A rendszer az összes sikertelen háttér-kezdeményezett ikerfrissítés számát.|None|
|c2d.twin.update.size|Két frissítés mérete háttérrendszerről|Bájt|Átlag|Az összes sikeres háttér-kezdeményezett ikerfrissítés átlagos, min és maximális mérete.|None|
|twinQueries.success|Sikeres ikerlekérdezések|Darabszám|Összesen|Az összes sikeres ikerlekérdezés száma.|None|
|twinQueries.failure|Sikertelen ikerlekérdezések|Darabszám|Összesen|A sikertelen ikerlekérdezések száma.|None|
|twinQueries.resultSize|Ikerlekérdezések eredménymérete|Bájt|Átlag|Az összes sikeres ikerlekérdezés eredményméretének átlagos, min és max.|None|
|jobs.createTwinUpdateJob.success|Két frissítési feladat sikeres létrehozása|Darabszám|Összesen|Az ikerfrissítési feladatok sikeres létrehozásának száma.|None|
|jobs.createTwinUpdateJob.failure|Két frissítési feladat sikertelen létrehozása|Darabszám|Összesen|Az összes sikertelen ikerfrissítési feladat létrehozása.|None|
|jobs.createDirectMethodJob.success|A metódus-meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódusok meghívási feladatainak sikeres létrehozásának száma.|None|
|jobs.createDirectMethodJob.failure|Metódusmeghívási feladatok sikertelen létrehozása|Darabszám|Összesen|A közvetlen metódus-meghívási feladatok összes sikertelen létrehozásának száma.|None|
|jobs.listJobs.success|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázásához sikeres hívások száma.|None|
|jobs.listJobs.failure|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázásához sikertelen hívások száma.|None|
|jobs.cancelJob.success|Sikeres feladattörlések|Darabszám|Összesen|A feladat megszakításához sikeres hívások száma.|None|
|jobs.cancelJob.failure|Sikertelen feladatmegszakítások|Darabszám|Összesen|A feladat megszakításához sikertelen hívások száma.|None|
|jobs.query.success|Sikeres feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|None|
|jobs.query.failure|Sikertelen feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikertelen hívásának száma.|None|
|jobs.befejezett|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|None|
|jobs.failed|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|None|
|d2c.telemettry.ingress.sendThrottle|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli fojtószelepei miatti szabályozási hibák száma|None|
|dailyMessageQuotaHasznált|A felhasznált üzenetek száma összesen|Darabszám|Átlag|A ma használt összes üzenet száma. Ez egy olyan összegző érték, amely minden nap 00:00 UTC-kor nullára áll vissza.|None|
|deviceDataUsage|Az eszköz összes adathasználata|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|None|
|deviceDataUsageV2|Az eszköz teljes adathasználata (előzetes verzió)|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|None|
|totalDeviceCount (összeseszközszám)|Eszközök összesen (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz regisztrált eszközök száma|None|
|connectedDeviceCount (connectedDeviceCount)|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz csatlakoztatott eszközök száma|None|
|Konfigurációk|Konfigurációs mutatók|Darabszám|Összesen|A konfigurációs műveletek metrikája|None|

## <a name="next-steps"></a>További lépések

Most, hogy már látta az IoT Hub-metrikák áttekintését, ezt a hivatkozást követve többet is megtudhat az Azure IoT Hub kezeléséről:

* [Diagnosztikai naplók beállítása](iot-hub-monitor-resource-health.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
