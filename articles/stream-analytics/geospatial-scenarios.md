---
title: A Geokerítések és térinformatikai összesítési forgatókönyvek az Azure Stream Analytics
description: Ez a cikk ismerteti, hogyan használható az Azure Stream Analytics geokerítések és térinformatikai összesítés.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: cc301855e4cdcb8eb687e753835577399cfe72b0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58807170"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>A Geokerítések és térinformatikai összesítési forgatókönyvek az Azure Stream Analytics

Beépített térinformatikai függvényekkel, az Azure Stream Analytics használatával flottakezelés hasonló forgatókönyvek esetén alkalmazásokat hozhat létre, ledolgozni, megosztás, a csatlakoztatott autók és nyomon követése.

## <a name="geofencing"></a>Geokerítések kezelése

Az Azure Stream Analytics közel valós idejű geokerítések számítások a felhőben és az IoT Edge-futtatókörnyezet támogatja.

### <a name="geofencing-scenario"></a>A Geokerítések forgatókönyv

Egy gyártóvállalat cég nyomon kell követni az eszközök az épületek. Ezeket a GPS használó összes eszköz felszerelt, és szeretne értesítéseket kapni, ha egy eszköz egy bizonyos területen hagyja.

Ebben a példában használt referenciaadatok az épületek és az eszközöket, amelyek használata engedélyezett az épületek mindegyike geokerítésen információkat tartalmaz. Ne feledje, hogy a referenciaadatok vagy lehet statikus vagy lassan módosítása. Ebben a forgatókönyvben használható statikus referenciaadatokra vonatkoznak. Egy adatfolyam folyamatosan bocsát ki, az eszköz Azonosítójával és aktuális pozícióját.

### <a name="define-geofences-in-reference-data"></a>Referenciaadatok a geokerítések definiálása

A GeoJSON-objektumot a geokerítésen lehet definiálni. Az 1.2-es vagy magasabb kompatibilitási verzió feladatokhoz geokerítések is lehetőség van a használatával Well Known Text (wkt) formátumú, `NVARCHAR(MAX)`. Wkt formátumú egy nyissa meg a földrajzi Consortium (OGC) standard szintű, amely a térbeli adatok egy szöveges formátumú megjelenítésére szolgál.

A beépített térinformatikai függvényekkel meghatározott geokerítések segítségével ismerje meg, ha egy elem vagy egy adott geokerítésen sokszög ki van-e.

Az alábbi táblázat példázza geokerítésen referenciaadatok, amely az Azure blob storage-bA vagy egy Azure SQL-táblát tárolható. Minden hely egy térinformatikai sokszög képviseli, és minden eszköz társítva egy webhely.

|SiteID|Webhely neve:|Geokerítés|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"SOKSZÖG ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)) "|"B"|
|2|"Redmond Building 40"|"SOKSZÖG ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)) "|"A"|
|3|"Redmond Building 22-es"|"SOKSZÖG ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)) "|"C"|

### <a name="generate-alerts-with-geofence"></a>Riasztások generálására lehetőséget nyújtó geokerítésen

Eszközök kibocsátható azonosítója és a hely keresztül nevű adatfolyam percenként `DeviceStreamInput`. Az alábbi táblázat a bemeneti adatfolyam.

|Eszközazonosító|GeoPosition|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475554553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Írhat egy lekérdezést, amely az eszköz adatfolyam a geokerítésen referenciaadatok csatlakozik, és riasztást küld minden alkalommal, amikor egy eszköz egy engedélyezett épületen kívül esik.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Az alábbi képen a geokerítések jelöli. Láthatja, ahol az eszközök vannak-e a bemeneti adatok összhangban.

![Épület geokerítések](./media/geospatial-scenarios/building-geofences.png)

"C" eszköz létrehozásához azonosítója 2, ami nem engedélyezett a referencia-adatok alapján belül található. Ez az eszköz belső azonosítója 3 létrehozásához kell elhelyezni. Ez a feladat fut az adott megsértése riasztást hoz létre.

### <a name="site-with-multiple-allowed-devices"></a>Hely több engedélyezett eszközzel

Ha egy hely lehetővé teszi a több eszközre, eszközazonosítókat álló tömb lehet definiálni `AllowedDeviceID` és a egy felhasználó által megadott függvény is használható a `WHERE` záradék ellenőrizheti, ha az adatfolyam eszköz azonosítója megegyezik-e ebben a listában szereplő bármely eszköz Azonosítóját. További információkért tekintse meg a [Javascript UDF](stream-analytics-javascript-user-defined-functions.md) oktatóanyag felhőbeli feladatok és a [ C# UDF](stream-analytics-edge-csharp-udf.md) edge-feladatok oktatóanyagot.

## <a name="geospatial-aggregation"></a>Térinformatikai összesítés

Az Azure Stream Analytics közel valós idejű térinformatikai összesítés a felhőben és az IoT Edge-futtatókörnyezet támogatja.

### <a name="geospatial-aggregation-scenario"></a>Térinformatikai összesítési forgatókönyv

Egy cab vállalat szeretne létrehozni egy valós idejű alkalmazást is a cab illesztőprogramok felé a várost, magasabb igény szerint jelenleg tapasztaló területeit indításáról keres.

A vállalat az városa logikai területekre referenciaadatok tárolja. Minden egyes régió egy RegionID RegionName és Geokerítésen határozzák meg.

### <a name="define-the-geofences"></a>A geokerítések definiálása

Az alábbi táblázat példázza geokerítésen referenciaadatok, amely az Azure blob storage-bA vagy egy Azure SQL-táblát tárolható. Minden régióhoz, a streamelési adatok érkező kérések korrelációját használt térinformatikai sokszög képviseli.

Ezek poligonok csak referenciaként szolgálnak, és nem felelnek meg a logikai és fizikai elkülönülés tényleges város.

|RegionID|RegionName|Geokerítés|
|--------|----------|--------|
|1|"SoHo"|"SOKSZÖG ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"SOKSZÖG ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)) "|
|3|"Tribeca"|"SOKSZÖG ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Összesített adatok ideig keresztül

Az alábbi táblázat tartalmazza a streamelési adatok a "fel."

|Felhasználói azonosító|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

A következő lekérdezés az eszköz adatfolyam a geokerítésen referenciaadatok csatlakozik, és kiszámítja és a egy olyan időkeretet, 15 perces percenként régiónként kérések száma.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Ez a lekérdezés jelenít meg a kérelmek száma az elmúlt 15 percben percenkénti minden régióban az városa szerint. Ezeket az adatokat a Power BI-irányítópulton egyszerűen megjeleníthető, vagy az összes illesztőprogram-integráció révén a szolgáltatások, például az Azure functions SMS szöveges üzenetekként elküldhetők.

Az alábbi képen a Power BI-irányítópultra a lekérdezés kimenetét mutatja be. 

![Eredmény kimeneti a Power BI-irányítópult](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>További lépések

* [Bevezetés a Stream Analytics a térinformatikai függvények](stream-analytics-geospatial-functions.md)
* [A térinformatikai függvények (az Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
