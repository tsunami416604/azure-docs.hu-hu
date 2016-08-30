<properties
 pageTitle="A távoli figyelési előre konfigurált megoldás bemutatója | Microsoft Azure"
 description="Az Azure IoT távoli figyelési előre konfigurált megoldás és architektúrájának leírása."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# A távoli figyelési előre konfigurált megoldás bemutatója

## Bevezetés

Az IoT Suite távoli figyelési előre konfigurált megoldás alapvető, teljes körű figyelési megoldás olyan üzleti forgatókönyvekhez, amelyek több gépet működtetnek távoli helyeken. A megoldás fontos Azure IoT Suite-szolgáltatások kombinációját kínálja az üzleti forgatókönyv általános megvalósítása érdekében, és olyan ügyfelek használhatják kiindulási pontként, akik ilyen típusú IoT-megoldást kívánnak megvalósítani a saját üzleti követelményeik kielégítése érdekében.

## Logikai architektúra

A következő diagram az előre konfigurált megoldás logikai összetevőit vázolja fel:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Szimulált eszközök

Az előre konfigurált megoldásban a szimulált eszköz egy hűtőeszközt (például egy épület légkondicionálóját vagy létesítmény légkezelő egységét) jelöl. Mindegyik szimulált eszköz a következő telemetriai üzeneteket küldi el az IoT Hubnak:


| Üzenet  | Leírás |
|----------|-------------|
| Indítás  | Az eszköz az indulásakor **device-info** üzenetet küld, amely az eszközzel kapcsolatos információkat tartalmaz, például az eszköz azonosítóját, az eszköz metaadatait, az eszköz által támogatott parancsok listáját és az eszköz aktuális konfigurációját. |


A szimulált eszközök a következő eszköztulajdonságokat küldik el metaadatokként:

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


A szimulált eszközök az IoT Hubról küldött következő parancsokat tudják kezelni:

| Parancs                | Leírás                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | _Pinget_ küld az eszköznek, hogy ellenőrizze, működik-e   |
| StartTelemetry         | Elindítja a telemetriát küldő eszközt                 |
| StopTelemetry          | Leállítja az eszköz telemetriaküldését             |
| ChangeSetPointTemp     | Módosítja azt a megadott pontértéket, amely körül a véletlenszerű adatok létrejönnek |
| DiagnosticTelemetry    | Aktiválja az eszközszimulátort, hogy egy további telemetriaértéket küldjön (externalTemp) |
| ChangeDeviceState      | Módosítja az eszköz kiterjesztett állapot tulajdonságát, és elküldi az eszközinformációs üzenetet az eszközről |


Az eszközparancs nyugtázását az IoT Hub küldi.


### Azure Stream Analytics-feladatok


Az **1. feladat: eszközinformáció** szűri a bejövő üzenetstreamből az eszközinformációs üzeneteket, és elküldi őket egy eseményközpont-végpontra. Az eszközök az indításukkor és a **SendDeviceInfo** parancsra adott válaszként küldenek eszközinformációs üzeneteket. Ez a feladat a következő lekérdezésdefiníciót használja:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

A **2. feladat: szabályok** kiértékeli a bejövő hőmérséklettel és páratartalommal kapcsolatos telemetriaértékeket az eszközönkénti küszöbértékekhez képest. A küszöbértékek a megoldáshoz mellékelt szabályszerkesztőben vannak beállítva. Minden eszköz/érték párt időbélyegző alapján tárol egy blob, amelyet a rendszer **referenciaadatokként** olvas be a Stream Analyticsbe. A feladat összehasonlítja az összes nem nulla értéket az eszköz beállított küszöbértékével. Ha meghaladja a „>” feltételt, a feladat **riasztási** eseményt küld, amely jelzi, a küszöbérték túllépését, és megadja az eszközt, az értéket és az időbélyegző értékeit. Ez a feladat a következő lekérdezésdefiníciót használja:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
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

A **3. feladat: telemetria** kétféleképpen működik a bejövő eszköz telemetriastreamjén. Az első az összes telemetriaüzenetet állandó Blob Storage tárolóba küldi az eszközökről. A második kiszámítja az átlagos, minimális és maximális páratartalom-értékeket egy ötperces csúszóablakban. Az adatokat a Blob Storage-ba is elküldi. Ez a feladat a következő lekérdezésdefiníciót használja:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### Eseményfeldolgozó

Az **Eseményfeldolgozó** kezeli az eszközinformációs üzeneteket és parancsválaszokat. A következőket használja:

- Eszközinformációs üzenetek a (DocumentDB-adatbázisban tárolt) eszközjegyzék frissítéséhez az aktuális eszközinformációkkal.
- Parancsválasz-üzenetek a (DocumentDB-adatbázisban tárolt) eszközparancs-előzmények frissítéséhez.

## Kezdjük a bemutatót

Ez a szakasz végigvezeti a megoldás összetevőin, leírja a kívánt használati esetet, és példákat is tartalmaz.

### Távoli figyelési irányítópult
A webalkalmazás ezen oldala PowerBI Javascript-vezérlőket használ (lásd a [PowerBI-vizualizációk tárát](https://www.github.com/Microsoft/PowerBI-visuals)) a Blob Storage-ban lévő Stream Analytics-feladatok munkák kimeneti adatainak megjelenítéséhez.


### Eszközfelügyeleti portál

Ezzel a webalkalmazással a következőket teheti:

- Új eszközt építhet ki, amely beállítja az egyedi eszközazonosítót, és létrehozza a hitelesítési kulcsot.
- Eszköztulajdonságokat felügyelhet, többek között a meglévő tulajdonságok megtekintésével és az új tulajdonságokkal való frissítéssel.
- Parancsokat küldhet eszközökre.
- Megtekintheti az eszközök parancselőzményeit.

### A felhőmegoldás viselkedésének megfigyelése
A kiépített erőforrások megtekintéséhez ugorjon az [Azure Portalra](https://portal.azure.com) és keresse meg a megadott megoldásnévvel rendelkező erőforráscsoportot.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

A minta első futtatásakor négy előre konfigurált, szimulált eszköz van:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Az eszközfelügyeleti portálon új szimulált eszközt adhat hozzá:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Kezdetben az új eszköz állapota az eszközfelügyeleti portálon **Függőben**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Amikor az alkalmazás végzett a szimulált eszköz üzembe helyezésével, láthatja, hogy az eszköz állapota **Fut** az eszközfelügyeleti portálon, ahogyan a következő képernyőképen láthatja. A **DeviceInfo** Stream Analytics-feladat az eszköz állapotinformációit küldi az eszközről az eszközfelügyeleti portálnak.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

A megoldás portáljával olyan parancsokat küldhet az eszközre, mint a **ChangeSetPointTemp**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Amikor az eszköz jelenti, hogy sikeresen végrehajtotta a parancsot, az állapota **Sikeres** értékre változik:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

A megoldás portáljával adott jellemzőkkel, például modellszámmal rendelkező eszközöket kereshet:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Letilthatja az eszközöket, és a letiltásuk után eltávolíthatja őket:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)


## Következő lépések

A következő TechNet-blogbejegyzés további részleteket tartalmaz a távoli figyelési előre konfigurált megoldásról:

- [IoT Suite - Under The Hood - Remote Monitoring (IoT Suite – Technikai részletek – Távoli figyelés)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (IoT Suite – Távoli figyelés – Élő és szimulált eszközök hozzáadása)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)


<!--HONumber=jun16_HO2-->


