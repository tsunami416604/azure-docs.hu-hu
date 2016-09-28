<properties
 pageTitle="A távoli figyelési előre konfigurált megoldás bemutatója | Microsoft Azure"
 description="Az Azure IoT távoli figyelési előre konfigurált megoldás és architektúrájának leírása."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>


# A távoli figyelési előre konfigurált megoldás bemutatója

## Bevezetés

Az IoT Suite távoli figyelési [előre konfigurált megoldás][lnk-preconfigured-solutions] végpontok közötti figyelési megoldást kínál, amely egyszerre akár több, távoli helyeken futó géphez is használható. A megoldás alapvető Azure-szolgáltatások együttes használatával biztosítja az üzleti forgatókönyv általános megvalósítását, amelyet Ön kiindulópontként használhat saját egyedi megoldásának kialakításához. A megoldást [testre szabhatja][lnk-customize], hogy az minél pontosabban illeszkedjen az Ön konkrét üzleti igényeihez.

Ebben a cikkben bemutatjuk a távoli figyelési megoldás néhány fontos elemét, hogy jobban megismerhesse a szolgáltatás működését. Ezeknek az ismereteknek a birtokában:

- Elháríthatja a megoldásban felmerülő hibákat.
- Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek. 
- Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

## Logikai architektúra

A következő diagram az előre konfigurált megoldás logikai összetevőit vázolja fel:

![Logikai architektúra](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## Szimulált eszközök

Az előre konfigurált megoldásban a szimulált eszköz egy hűtőeszközt (például egy épület légkondicionálóját vagy létesítmény légkezelő egységét) jelöl. Amikor üzembe helyezi az előre konfigurált megoldást, egyben négy szimulált eszközt is létrehoz, amelyek egy [Azure WebJob-feladat][lnk-webjobs] részeként működnek. A szimulált eszközök segítségével könnyebben megismerheti a megoldás működését, anélkül, hogy fizikai eszközöket kellene üzembe helyeznie. Tényleges fizikai eszközök üzembe helyezésével kapcsolatban olvassa el a [Connect your device to the remote monitoring preconfigured solution][lnk-connect-rm] (Eszközök csatlakoztatása a távoli figyelési előre konfigurált megoldáshoz) című oktatóanyagot.

A szimulált eszközök az alábbi típusú üzeneteket küldhetik az IoT Hubnak:

| Üzenet  | Leírás |
|----------|-------------|
| Indítás  | Amikor egy eszköz elindul, a magára az eszközre vonatkozó információkat tartalmazó **device-info** (eszközinformációs) üzenetet küld a háttérnek. Ide tartozik az eszköz azonosítója, az eszköz metaadatai, az eszköz által támogatott parancsok listája, és az eszköz aktuális konfigurációja. |
| Jelenlét | Az eszközök rendszeres időközönként **presence** (jelenlét) típusú üzenetet küldenek, amely azt jelzi, hogy az eszköz érzékeli az érzékelő jelenlétét. |
| Telemetria | Az eszközök rendszeres időközönként küldenek **telemetry** (telemetria) típusú üzenetet is, amelyek az eszköz szimulált érzékelői által jelentett szimulált hőmérsékleti és páratartalom-értékeket jelentenek a rendszernek. |


A szimulált eszközök a következő eszköztulajdonságokat küldik el a **device-info** üzenet részeként:

| Tulajdonság               |  Cél |
|------------------------|--------- |
| Eszközazonosító              | A megadott vagy a megoldásban az eszköz létrehozásakor hozzárendelt azonosító. |
| Gyártó           | Eszközgyártó |
| Modellszám           | Az eszköz modellszáma |
| Sorozatszám          | Az eszköz sorozatszáma |
| Belső vezérlőprogram               | Az eszközön lévő belső vezérlőprogram aktuális verziója |
| Platform               | Az eszköz platformarchitektúrája |
| Processzor              | Az eszközt futtató processzor |
| Telepített RAM          | Az eszközre telepített RAM mennyisége |
| Hub engedélyezett állapota      | Az eszköz IoT Hub állapotának tulajdonsága |
| Létrehozás ideje           | Az eszköz létrehozásának ideje a megoldásban |
| Frissítés ideje           | Az eszközhöz tulajdonságainak legutóbbi frissítése |
| Szélesség               | Az eszköz szélességi helye |
| Hosszúság              | Az eszköz hosszúsági helye |

A szimulált értékekben a szimulátor mintaértékekkel tölti fel ezeket a tulajdonságokat.  Amikor a szimulátor elindít egy szimulált eszközt, az eszköz mindig közzéteszi az előre definiált metaadatokat az IoT Hubra. Vegye figyelembe, hogy ez felülírja az eszközportálon végzett összes metaadat-frissítést.


A szimulált eszközök a következő parancsokat képesek fogadni a megoldás irányítópultjáról az IoT Hubon keresztül:

| Parancs                | Leírás                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | _Pinget_ küld az eszköznek, hogy ellenőrizze, működik-e   |
| StartTelemetry         | Elindítja a telemetriát küldő eszközt                 |
| StopTelemetry          | Leállítja az eszköz telemetriaküldését             |
| ChangeSetPointTemp     | Módosítja azt a megadott pontértéket, amely körül a véletlenszerű adatok létrejönnek |
| DiagnosticTelemetry    | Aktiválja az eszközszimulátort, hogy egy további telemetriaértéket küldjön (externalTemp) |
| ChangeDeviceState      | Módosítja az eszköz kiterjesztett állapot tulajdonságát, és elküldi az eszközinformációs üzenetet az eszközről |

Az eszközparancs a megoldás hátterének címzett nyugtázását az IoT Hub küldi.

## IoT Hub

Az [IoT hub][lnk-iothub] feltölti a felhőbe az eszközök által küldött adatokat, és elérhetővé teszi azokat Azure Stream Analytics (ASA) feladatokban való felhasználásra. Az IoT Hub ezenfelül parancsokat küld az eszköznek az eszközportálról. Minden Stream ASA-feladat saját IoT Hub-fogyasztói csoportot használ, amelynek segítségével beolvassa az eszközök által küldött üzenetek streamjét.

## Azure Stream Analytics

A távoli figyelési megoldásban az [Azure Stream Analytics][lnk-asa] (ASA) továbbítja az IoT Hub által fogadott eszközüzeneteket feldolgozás vagy tárolás céljából a többi háttérkomponensnek. Az üzenet tartalmától függően a különféle ASA-feladatok más-más funkciókat végeznek el.

Az **1. feladat: eszközinformáció** szűri a bejövő üzenetstreamből az eszközinformációs üzeneteket, és elküldi őket egy eseményközpont-végpontra. Az eszközök az indításukkor és a **SendDeviceInfo** parancsra adott válaszként küldenek eszközinformációs üzeneteket. Ez a feladat az alábbi lekérdezésdefiníció segítségével azonosítja a **device-info** típusú üzeneteket:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Ez a feladat egy eseményközpontba továbbítja kimenetét további feldolgozás céljából.

A **2. feladat: szabályok** kiértékeli a bejövő hőmérséklettel és páratartalommal kapcsolatos telemetriaértékeket az eszközönkénti küszöbértékekhez képest. A küszöbértékeket a megoldás irányítópultján elérhető szabályszerkesztőben lehet beállítani. A Blob-tárhelyek időbélyegző szerint rendezve tárolják az eszköz/érték párokat, amelyeket a rendszer **referenciaadatokként** olvas be a Stream Analyticsbe. A feladat összehasonlítja az összes nem nulla értéket az eszköz beállított küszöbértékével. Ha meghaladja a „>” feltételt, a feladat **riasztási** eseményt küld, amely jelzi a küszöbérték túllépését, és megadja az eszközt, az értéket és az időbélyegző értékét. Ez a feladat az alábbi lekérdezésdefiníció segítségével azonosítja azokat a telemetriai üzeneteket, amelyeknek riasztást kell atktiválniuk:

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

A feladat egy eseményközpontba továbbítja kimenetét további feldolgozás céljából, majd a blobtárolóba menti a riasztásokat, ahonnan a megoldás irányítópultja be tudja olvasni azok adatait.

A **3. feladat: telemetria** kétféleképpen működik a bejövő eszköz telemetriastreamjén. Az első az eszköz által küldött összes telemetriai üzenetet a perzisztens blobtárolóba küldi hosszú távú tárolás céljából. A második kiszámítja az átlagos, minimális és maximális páratartalom-értékeket egy ötperces csúszóablakban, majd elküldi ezeket az adatokat a blobtárolóba. A megoldás irányítópultja beolvassa a telemetriai adatokat a blobtárolóból, és ezek alapján létrehozza a diagramokat. Ez a feladat a következő lekérdezésdefiníciót használja:

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

## Event Hubs

A **device info** és a **rules** ASA-feladatok az eseményközpontoknak adják át az adataikat, amelyek megbízhatóan továbbítják azokat a WebJob-feladatban futó **eseményfeldolgozónak**.

## Azure Storage

A megoldás az Azure Blob Storage segítségével őrzi meg a megoldásba bevont eszközök által küldött nyers és összegzett telemetriai adatokat. Az irányítópult beolvassa a telemetriai adatokat a blobtárolóból, és ezek alapján létrehozza a diagramokat. A riasztások megjelenítéséhez az irányítópult a blobtárolóból olvassa be az adatokat, amely a küszöböt meghaladó telemetriai értékeket rögzíti. A megoldás az irányítópultban beállított küszöbértékek rögzítésére is a blobtárolót használja.

## WebJobs

Az eszközszimulátorok futtatása mellett egy WebJob-feladat működteti az eszközinformációs üzeneteket és a parancsválaszokat kezelő **eseményfeldolgozót** is. A következőket használja:

- Eszközinformációs üzenetek a (DocumentDB-adatbázisban tárolt) eszközjegyzék frissítéséhez az aktuális eszközinformációkkal.
- Parancsválasz-üzenetek a (DocumentDB-adatbázisban tárolt) eszközparancs-előzmények frissítéséhez.

## DocumentDB

A megoldás egy DocumentDB-adatbázisban tárolja a megoldáshoz csatlakoztatott eszközökre vonatkozó adatokat. Az adatok közé tartoznak az eszköz metaadatai, valamint az eszköznek az irányítópultról küldött parancsok előzményei.

## Webalkalmazások

### Távoli figyelési irányítópult
A webalkalmazás ezen oldala PowerBI javascript-vezérlőket használ (lásd a [PowerBI-vizualizációk tárát](https://www.github.com/Microsoft/PowerBI-visuals)) az eszközök által küldött telemetriai adatok vizualizálásához. A megoldás az ASA telemetriai feladat használatával írja a blobtárolóba a telemetriai adatokat.


### Eszközfelügyeleti portál

Ezzel a webalkalmazással a következőket teheti:

- Új eszközöket építhet ki. Ezzel a művelettel állíthatja be az egyedi eszközazonosítót, valamint hozhatja létre a hitelesítési kulcsot. A művelet mind az IoT Hub-identitásjegyzékébe, mind a megoldásspecifikus DocumentDB-adatbázisba beírja az eszközadatokat.
- Felügyelheti az eszköztulajdonságokat. A művelet segítségével megtekintheti a meglévő tulajdonságokat, és új tulajdonságokat hozhat létre.
- Parancsokat küldhet eszközökre.
- Megtekintheti az eszközök parancselőzményeit.
- Engedélyezheti és letilthatja a különböző eszközöket.

## Következő lépések

A következő TechNet-blogbejegyzés további részleteket tartalmaz a távoli figyelési előre konfigurált megoldásról:

- [IoT Suite - Under The Hood - Remote Monitoring (IoT Suite – Technikai részletek – Távoli figyelés)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (IoT Suite – Távoli figyelés – Élő és szimulált eszközök hozzáadása)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Folytassa az IoT Suite megismerését az alábbi cikkek elolvasásával:

- [Eszközök csatlakoztatása a távoli figyelési előre konfigurált megoldáshoz][lnk-connect-rm]
- [Engedélyek az azureiotsuite.com oldalon][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md


<!--HONumber=Sep16_HO4-->


