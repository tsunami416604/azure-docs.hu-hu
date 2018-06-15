---
title: A távoli figyelési előre konfigurált megoldás bemutatója | Microsoft Docs
description: Az Azure IoT távoli figyelési előre konfigurált megoldás és architektúrájának leírása.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 871098642f586a123d056e2da22d2fceae9bdfe5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638075"
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>A távoli figyelési előre konfigurált megoldás bemutatója

Az IoT Suite [előre konfigurált][lnk-preconfigured-solutions] távoli figyelési megoldással egy teljes körű figyelési megoldást implementálhat, ha több gépet működtetnek távoli helyeken. A megoldás fontos Azure-szolgáltatások kombinációját kínálja az üzleti forgatókönyv általános megvalósítása érdekében. A megoldást kiindulási pontként használhatja a saját implementációjához, és a saját üzleti igényeinek megfelelően [testreszabhatja][lnk-customize] azt.

Ebben a cikkben bemutatjuk a távoli figyelési megoldás néhány fontos elemét, hogy jobban megismerhesse a szolgáltatás működését. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek. 
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram az előre konfigurált megoldás logikai összetevőit vázolja fel:

![Logikai architektúra](media/iot-suite-v1-remote-monitoring-sample-walkthrough/remote-monitoring-architecture-updated.png)

## <a name="microservices--docker-containers"></a>Mikroszolgáltatások és Docker-tárolók
A távoli monitorozás az első olyan előre konfigurált megoldásunk, amely mikroszolgáltatásokon alapuló architektúrát használ. A megoldás [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) és [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) változatban is elérhető.
A mikroszolgáltatások használata olyan elterjedt módszer, amely skálázhatóságot és rugalmasságot biztosít (a tárolók egyenkénti skálázásának lehetősége révén) a fejlesztés sebességének korlátozása nélkül.
A mikroszolgáltatások felosztják a kódot és jól meghatározott felületeket biztosítanak, könnyebben érthetővé és kevésbé monolitikussá téve a megoldásokat. A módszer emellett további lehetőségeket nyit meg az olyan partnerek előtt, akik szeretnék kibővíteni jelenlegi megoldásgyorsítóinkat, hogy teljes, értékesíthető megoldásokat hozhassanak létre.

**További információ a Docker-tárolókról**
* [A Docker telepítése](https://docs.docker.com/engine/installation/)
* [Gyakran használt Docker-parancsok távoli monitorozáshoz](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#common-docker-commands)
* [Docker – Első lépések](https://docs.docker.com/get-started/)

## <a name="simulated-devices"></a>Szimulált eszközök

Az előre konfigurált megoldásban a szimulált eszköz egy hűtőeszközt (például egy épület légkondicionálóját vagy létesítmény légkezelő egységét) jelöl. Amikor üzembe helyezi az előre konfigurált megoldást, egyben négy szimulált eszközt is létrehoz, amelyek egy [Azure WebJob-feladat][lnk-webjobs] részeként működnek. A szimulált eszközök segítségével könnyebben megismerheti a megoldás működését, anélkül, hogy fizikai eszközöket kellene üzembe helyeznie. Valós fizikai eszköz üzembe helyezéséhez lásd [az eszköz az előre konfigurált távoli figyelési megoldáshoz történő csatlakoztatását][lnk-connect-rm] ismertető oktatóanyagot.

### <a name="device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek

A szimulált eszközök az alábbi típusú üzeneteket küldhetik az IoT Hubnak:

| Üzenet | Leírás |
| --- | --- |
| Indítás |Amikor egy eszköz elindul, a magára az eszközre vonatkozó információkat tartalmazó **device-info** (eszközinformációs) üzenetet küld a háttérnek. Ezekbe az adatokba beletartozik az eszköz azonosítója, valamint az eszköz által támogatott parancsok és metódusok listája. |
| Jelenlét |Az eszközök rendszeres időközönként **presence** (jelenlét) típusú üzenetet küldenek, amely azt jelzi, hogy az eszköz érzékeli az érzékelő jelenlétét. |
| Telemetria |Az eszközök rendszeres időközönként küldenek **telemetry** (telemetria) típusú üzenetet is, amelyek az eszköz szimulált érzékelői által jelentett szimulált hőmérsékleti és páratartalom-értékeket jelentenek a rendszernek. |

> [!NOTE]
> A megoldás az eszköz által támogatott parancsok listáját egy Cosmos DB-adatbázisban tárolja, és nem az ikereszközben.

### <a name="properties-and-device-twins"></a>Tulajdonságok és ikereszközök

A szimulált eszközök a következő eszköztulajdonságokat küldik el az IoT Hubon lévő [ikernek][lnk-device-twins] *jelentett tulajdonságokként*. Az eszköz az indításakor és **Eszközállapot módosítása** parancsra vagy metódusra válaszul küldi el a jelentett tulajdonságokat.

| Tulajdonság | Cél |
| --- | --- |
| Config.TelemetryInterval | Az eszköz által végzett telemetriaküldés gyakorisága (s) |
| Config.TemperatureMeanValue | Meghatározza a szimulált hőmérsékleti telemetria középértékét |
| Device.DeviceID |A megadott vagy a megoldásban az eszköz létrehozásakor hozzárendelt azonosító |
| Device.DeviceState | Az eszköz által jelentett állapot |
| Device.CreatedTime |Az eszköz létrehozásának ideje a megoldásban |
| Device.StartupTime |Az eszköz elindításának ideje |
| Device.LastDesiredPropertyChange |Az utolsó kívánt tulajdonságmódosítás verziószáma |
| Device.Location.Latitude |Az eszköz szélességi helye |
| Device.Location.Longitude |Az eszköz hosszúsági helye |
| System.Manufacturer |Eszközgyártó |
| System.ModelNumber |Az eszköz modellszáma |
| System.SerialNumber |Az eszköz sorozatszáma |
| System.FirmwareVersion |Az eszközön lévő belső vezérlőprogram aktuális verziója |
| System.Platform |Az eszköz platformarchitektúrája |
| System.Processor |Az eszközt futtató processzor |
| System.InstalledRAM |Az eszközre telepített RAM mennyisége |

A szimulált értékekben a szimulátor mintaértékekkel tölti fel ezeket a tulajdonságokat. Amikor a szimulátor elindít egy szimulált eszközt, az eszköz jelentett tulajdonságokként mindig jelenti az előre definiált metaadatokat az IoT Hubnak. A jelentett tulajdonságokat csak az eszköz frissítheti. A jelentett tulajdonságok módosításához be kell állítania egy kívánt tulajdonságot a megoldásportálon. A következők az eszköz feladatai:

1. A kívánt tulajdonságok rendszeres időközönkénti lekérése az IoT Hubról.
2. A konfiguráció frissítése a kívánt tulajdonságértékkel.
3. Az új érték visszaküldése a IoT Hubra jelentett tulajdonságként.

A megoldás irányítópultján a *kívánt tulajdonságokkal* állíthatja be egy eszköz tulajdonságait az [ikereszközzel][lnk-device-twins]. Általában az eszközök beolvasnak egy kívánt tulajdonságértéket az IoT Hubról a belső állapotuk frissítéséhez, és jelentett tulajdonságként jelentik a módosítást.

> [!NOTE]
> A szimulált eszközkód csak a **Desired.Config.TemperatureMeanValue** és a **Desired.Config.TelemetryInterval** kívánt tulajdonságot használja az IoT Hubra visszaküldött jelentett tulajdonságok frissítéséhez. A rendszer figyelmen kívül hagyja az összes többi kívánt tulajdonságmódosítási kérelmet.

### <a name="methods"></a>Metódusok

A szimulált eszközök a következő, az IoT Hubon keresztül a megoldásportálról indított metódusokat ([közvetlen metódusokat][lnk-direct-methods]) tudják kezelni:

| Módszer | Leírás |
| --- | --- |
| InitiateFirmwareUpdate |A belső vezérlőprogram frissítésére utasítja az eszközt |
| Újraindítás |Újraindításra utasítja az eszközt |
| FactoryReset |A gyári beállítások visszaállítására utasítja az eszközt |

Néhány metódus jelentett tulajdonságokkal jelenti az állapotot. Az **InitiateFirmwareUpdate** metódus például az eszközön a frissítés aszinkron futtatását szimulálja. A metódus azonnal visszatér az eszközön, míg az aszinkron feladat jelentett tulajdonságokkal folyamatosan állapotfrissítéseket küld vissza a megoldás irányítópultjának.

### <a name="commands"></a>Parancsok

A szimulált eszközök a következő, az IoT Hubon keresztül a megoldásportálról küldött parancsokat (felhőből egy eszközre irányuló üzeneteket) tudják kezelni:

| Parancs | Leírás |
| --- | --- |
| PingDevice |*Pinget* küld az eszköznek, hogy ellenőrizze, működik-e |
| StartTelemetry |Elindítja a telemetriát küldő eszközt |
| StopTelemetry |Leállítja az eszköz telemetriaküldését |
| ChangeSetPointTemp |Módosítja azt a megadott pontértéket, amely körül a véletlenszerű adatok létrejönnek |
| DiagnosticTelemetry |Aktiválja az eszközszimulátort, hogy egy további telemetriaértéket küldjön (externalTemp) |
| ChangeDeviceState |Módosítja az eszköz kiterjesztett állapot tulajdonságát, és elküldi az eszközinformációs üzenetet az eszközről |

> [!NOTE]
> Ezen parancsok (felhőből egy eszközre irányuló üzenetek) összehasonlításáért lásd a [felhőből egy eszközre irányuló kommunikáció útmutatóját][lnk-c2d-guidance].

## <a name="iot-hub"></a>IoT Hub

Az [IoT hub][lnk-iothub] feltölti a felhőbe az eszközök által küldött adatokat, és elérhetővé teszi azokat Azure Stream Analytics (ASA) feladatokban való felhasználásra. Minden Stream ASA-feladat saját IoT Hub-fogyasztói csoportot használ, amelynek segítségével beolvassa az eszközök által küldött üzenetek streamjét.

A megoldásban az IoT Hub ezenkívül a következőket teszi:

- Biztosít egy, a portálhoz csatlakoztatható összes eszköz azonosítóját és hitelesítési kulcsát tároló identitásjegyzéket. Az identitásjegyzéken keresztül a felhasználó engedélyezheti és letilthatja az eszközöket.
- Parancsokat küld az eszközöknek a megoldásportál nevében.
- Metódusokat indít az eszközökön a megoldásportál nevében.
- Az összes regisztrált eszközhöz biztosítja a megfelelő ikereszközt. Az ikereszközök tárolják az eszközök által jelentett tulajdonságértékeket. Az ikereszközök a megoldásportálon beállított kívánt tulajdonságokat is tárolják, amelyeket az eszköz a következő csatlakozáskor kérhet le.
- Feladatokat ütemez, hogy több eszközön is beállíthasson tulajdonságokat vagy meghívhasson metódusokat.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

A távoli figyelési megoldásban az [Azure Stream Analytics][lnk-asa] (ASA) továbbítja az IoT Hub által fogadott eszközüzeneteket feldolgozás vagy tárolás céljából a többi háttérkomponensnek. Az üzenet tartalmától függően a különféle ASA-feladatok más-más funkciókat végeznek el.

Az **1. feladat: eszközinformáció** szűri a bejövő üzenetstreamből az eszközinformációs üzeneteket, és elküldi őket egy eseményközpont-végpontra. Az eszközök az indításukkor és a **SendDeviceInfo** parancsra adott válaszként küldenek eszközinformációs üzeneteket. Ez a feladat az alábbi lekérdezésdefiníció segítségével azonosítja a **device-info** típusú üzeneteket:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Ez a feladat egy eseményközpontba továbbítja kimenetét további feldolgozás céljából.

A **2. feladat: szabályok** kiértékeli a bejövő hőmérséklettel és páratartalommal kapcsolatos telemetriaértékeket az eszközönkénti küszöbértékekhez képest. A küszöbértékek a megoldásportálon elérhető szabályszerkesztőben vannak beállítva. A Blob-tárhelyek időbélyegző szerint rendezve tárolják az eszköz/érték párokat, amelyeket a rendszer **referenciaadatokként** olvas be a Stream Analyticsbe. A feladat összehasonlítja az összes nem nulla értéket az eszköz beállított küszöbértékével. Ha meghaladja a „>” feltételt, a feladat **riasztási** eseményt küld, amely jelzi a küszöbérték túllépését, és megadja az eszközt, az értéket és az időbélyegző értékét. Ez a feladat az alábbi lekérdezésdefiníció segítségével azonosítja azokat a telemetriai üzeneteket, amelyeknek riasztást kell atktiválniuk:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

A feladat egy eseményközpontba továbbítja kimenetét további feldolgozás céljából, majd a blobtárolóba menti a riasztásokat, ahonnan a megoldás portálja be tudja olvasni azok adatait.

A **3. feladat: telemetria** kétféleképpen működik a bejövő eszköz telemetriastreamjén. Az első az eszköz által küldött összes telemetriai üzenetet a perzisztens blobtárolóba küldi hosszú távú tárolás céljából. A második kiszámítja az átlagos, minimális és maximális páratartalom-értékeket egy ötperces csúszóablakban, majd elküldi ezeket az adatokat a blobtárolóba. A megoldásportál beolvassa a Blob Storage-ból a telemetriaadatokat a diagramok adatokkal történő feltöltéséhez. Ez a feladat a következő lekérdezésdefiníciót használja:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs

A **device info** és a **rules** ASA-feladatok az eseményközpontoknak adják át az adataikat, amelyek megbízhatóan továbbítják azokat a WebJob-feladatban futó **eseményfeldolgozónak**.

## <a name="azure-storage"></a>Azure Storage tárterület

A megoldás az Azure Blob Storage segítségével őrzi meg a megoldásba bevont eszközök által küldött nyers és összegzett telemetriai adatokat. A portál beolvassa a Blob Storage-ból a telemetriaadatokat a diagramok adatokkal történő feltöltéséhez. Riasztások megjelenítéséhez a megoldásportál beolvassa a Blob Storage-ból azon adatokat, amelyek akkor lettek rögzítve, amikor a telemetriaértékek túllépték a konfigurált küszöbértékeket. A megoldás a Blob Storage használatával rögzíti a megoldásportálon beállított küszöbértékeket is.

## <a name="webjobs"></a>WebJobs

Az eszközszimulátorok elérhetővé tételén kívül a megoldásban a WebJobs szolgáltatja a parancsválaszokat kezelő Azure WebJobban futó **Eseményfeldolgozót** is. Parancsválasz-üzeneteket használ a (Cosmos DB-adatbázisban tárolt) eszközparancs-előzmények frissítéséhez.

## <a name="cosmos-db"></a>Cosmos DB

A megoldás egy Cosmos DB-adatbázisban tárolja a megoldáshoz csatlakoztatott eszközökre vonatkozó adatokat. Ezen adatok közé tartoznak a megoldásportálról az eszközökre küldött parancsok és a megoldásportálról indított metódusok előzményei.

## <a name="solution-portal"></a>Megoldásportál

A megoldásportál egy webalkalmazás, amely az előre konfigurált megoldás részeként van üzembe helyezve. A megoldásportál kiemelt oldala az irányítópult és az eszközlista.

### <a name="dashboard"></a>Irányítópult

A webalkalmazás ezen oldala PowerBI javascript-vezérlőket használ (lásd a [PowerBI-vizualizációk tárát](https://www.github.com/Microsoft/PowerBI-visuals)) az eszközök által küldött telemetriai adatok vizualizálásához. A megoldás az ASA telemetriai feladat használatával írja a blobtárolóba a telemetriai adatokat.

### <a name="device-list"></a>Eszközlista

A megoldásportál ezen oldaláról a következőket teheti:

* Új eszközöket építhet ki. Ezzel a művelettel állíthatja be az egyedi eszközazonosítót, valamint hozhatja létre a hitelesítési kulcsot. A művelet mind az IoT Hub-identitásjegyzékébe, mind a megoldásspecifikus Cosmos DB-adatbázisba beírja az eszközadatokat.
* Felügyelheti az eszköztulajdonságokat. A művelet segítségével megtekintheti a meglévő tulajdonságokat, és új tulajdonságokat hozhat létre.
* Parancsokat küldhet eszközökre.
* Megtekintheti az eszközök parancselőzményeit.
* Engedélyezheti és letilthatja a különböző eszközöket.

## <a name="next-steps"></a>További lépések

A következő TechNet-blogbejegyzés további részleteket tartalmaz a távoli figyelési előre konfigurált megoldásról:

* [IoT Suite – Technikai részletek – Távoli figyelés](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-v1-under-the-hood-remote-monitoring.aspx)
* [IIoT Suite – Távoli figyelés – Élő és szimulált eszközök hozzáadása](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-v1-remote-monitoring-adding-live-and-simulated-devices.aspx)

Folytassa az IoT Suite megismerését az alábbi cikkek elolvasásával:

* [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
