---
title: Földrajzi kerítés és térinformatikai összesítés az Azure Stream Analytics segítségével
description: Ez a cikk ismerteti, hogyan használhatja az Azure Stream Analytics a geofencing és a térinformatikai összesítés.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443637"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Földrajzi kerítés- és térösszesítési forgatókönyvek az Azure Stream Analytics segítségével

A beépített térinformatikai funkciókkal az Azure Stream Analytics segítségével alkalmazásokat hozhat létre olyan forgatókönyvekhez, mint a flottakezelés, a fuvarmegosztás, az összekapcsolt autók és az eszközök nyomon követése.

## <a name="geofencing"></a>Geokerítések kezelése

Az Azure Stream Analytics támogatja az alacsony késleltetésű valós idejű geofencing számítások a felhőben és az IoT Edge futásidejű.

### <a name="geofencing-scenario"></a>Geofencing forgatókönyv

Egy gyártó cégnek nyomon kell követnie az épületeit. Minden eszközt GPS-szel szereltek fel, és értesítést szeretnének kapni, ha egy eszköz elhagyja az adott területet.

Az ebben a példában használt referenciaadatok az egyes épületekben engedélyezett épületek és eszközök geokerítés-információit tartalmazják. Ne feledje, hogy a referenciaadatok lehetnek statikusak vagy lassúak. Ehhez a forgatókönyvhöz statikus referenciaadatokat használ. Az adatfolyam folyamatosan kibocsátja az eszköz azonosítóját és aktuális helyzetét.

### <a name="define-geofences-in-reference-data"></a>Geokerítések definiálása referenciaadatokban

Geokerítés geoJSON-objektum használatával definiálható. Az 1.2-es vagy újabb verziójú feladatok esetében a geokerítések jól ismert szöveg `NVARCHAR(MAX)`(WKT) használatával is definiálhatók a következőképpen: . A WKT egy Nyílt Térinformatikai Konzorcium (OGC) szabvány, amely a térbeli adatok szöveges formátumban történő ábrázolására szolgál.

A beépített térinformatikai függvények meghatározott geokerítések segítségével megtudhatják, hogy egy elem egy adott geokerítés-sokszögben van-e vagy sem.

Az alábbi táblázat egy példa a geokerítés referenciaadatok, amelyek tárolhatók az Azure blob storage vagy egy Azure SQL-tábla. Minden helyet egy térinformatikai sokszög jelöl, és minden eszköz egy engedélyezett helyazonosítóhoz van társítva.

|SiteID azonosító|SiteName|Geokerítés|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond épület 41"|"SOKSZÖG((-122.1337357922017 47.63782998329432,-122.13373042778369 47.6376347932573746 2022530954,-122.132.13348902897235 47.637508280806806,-122.13361777500506 47.6375082806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Redmond épület 40"|"SOKSZÖG((-122.1336154507967 47.63667459447009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636477 9400347675,-122.13349743360004 47.63636372927573,-122.133728103575732 47,63633637372933 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Redmond épület 22"|"SOKSZÖG((-122.1361160248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.1362389084293 47.6373 3603619712,-122.13622389084293 47.63717179101473,-122.1358161950726 47,63692757827657,-122.1355962534 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Riasztások létrehozása geokerítéssel

Az eszközök percenként bocsáthatják ki azonosítójukat `DeviceStreamInput`és helyüket a nevű adatfolyamon keresztül. Az alábbi táblázat egy bemeneti adatfolyam.

|Deviceid|Geopozíció|
|--------|-----------|
|"A"|"PONT(-122.1329234159497 47,636318374032726)"|
|"B"|"PONT(-122.133384755545553 47.63743531308874)"|
|"C"|"PONT(-122.13354001095752 47.63627622505007)"|

Írhat egy lekérdezést, amely összefűzi az eszköz adatfolyamot a geokerítés referenciaadatokkal, és riasztást hoz létre minden alkalommal, amikor egy eszköz egy engedélyezett épületen kívül van.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Az alábbi képen a geokerítések láthatók. Láthatja, hogy az eszközök hol vannak az adatfolyam-adatbevitelnek megfelelően.

![Geokerítések építése](./media/geospatial-scenarios/building-geofences.png)

A "C" eszköz a 2-es azonosítóval működő épületen belül található, amely a referenciaadatok szerint nem engedélyezett. Ennek az eszköznek a 3-as azonosítón belül kell lennie. A feladat futtatása riasztást hoz létre az adott megsértésről.

### <a name="site-with-multiple-allowed-devices"></a>Több engedélyezett eszközzel rendelkező webhely

Ha egy hely több eszközt engedélyez, eszközazonosítók tömbje `AllowedDeviceID` definiálható, és a `WHERE` záradékban egy felhasználó által definiált függvény használható annak ellenőrzésére, hogy az adatfolyam-eszköz azonosítója megfelel-e a listában szereplő bármely eszközazonosítónak. További információkért tekintse meg a [Javascript UDF](stream-analytics-javascript-user-defined-functions.md) oktatóanyag felhőfeladatok és a [C# UDF](stream-analytics-edge-csharp-udf.md) oktatóanyag peremhálózati feladatok.

## <a name="geospatial-aggregation"></a>Térinformatikai összesítés

Az Azure Stream Analytics támogatja az alacsony késleltetésű valós idejű térinformatikai összesítést a felhőben és az IoT Edge futásidőben.

### <a name="geospatial-aggregation-scenario"></a>Térinformatikai összesítési forgatókönyv

Egy taxitársaság valós idejű alkalmazást szeretne építeni, hogy a taxisok a jelenleg nagyobb keresletet tapasztaló városok felé vezessék az utazást.

A vállalat referenciaadatként tárolja a város logikai régióit. Minden régiót egy RegionID, RegionName és Geofence határoz meg.

### <a name="define-the-geofences"></a>A geokerítések meghatározása

Az alábbi táblázat egy példa a geokerítés referenciaadatok, amelyek tárolhatók az Azure blob storage vagy egy Azure SQL-tábla. Minden régiót egy térinformatikai sokszög képvisel, amely a streamelési adatokból érkező kérésekkel korrelál.

Ezek a sokszögek csak tájékoztató jellegűek, és nem jelentenek tényleges városlogikai vagy fizikai elválasztást.

|Régióazonosító|Régióneve|Geokerítés|
|--------|----------|--------|
|1|"SoHo"|"SOKSZÖG((-74.00279525078275 40,72833625216264,-74.00547744597 9765 40,7219291586324,-74.00125029839018 40,71893680218 994,-73,995778591998 40,7252140907576,-73,9972377137039 40,72557184584898,-74,00279525078275 40,728336252162644) )"|
|2|"Kínai negyed"|"SOKSZÖG((-73.9971236714876 40.71281582267133,-73.9901070123658 40,71336881907936,-73.9902357539851 40.71452 359088633,-73,98976368961189 40,71554823078944,-73.99551434573982 40.71733724678735,-73.994806255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"SOKSZÖG((-74.01091641815208 40.725831200006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.7136 17702123415,-74.008620447235333 40.711308107057235,-74.00194711120628 40,7194438654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Adatok összesítése időablakon keresztül

Az alábbi táblázat a "fuvarok" streamelési adatait tartalmazza.

|UserID|FromLocation (Hely)|Tolocation (hely)|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"PONT(-74.00726861389182 40.71610611981975)"|"PONT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"PONT(-74.00249841021645 40.723827238895666)"|"PONT(-74.01160699942085 40.7137884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"PONT(-73.99680120565864 40.716439898624024)"|"PONT(-73.9828963412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"PONT(-74.00741090068288 40.71615626755086)"|"PONT(-73.979999843120539 40,73477895807408)"|"2019-03-12T07:03:00Z"|

A következő lekérdezés az eszközstreamet a geokerítés referenciaadataival egyesíti, és percenként 15 perces időablakban kiszámítja a kérelmek számát régiónként.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Ez a lekérdezés a városon belüli egyes régiók minden egyes régiójában percenként adja ki a kérelmek számát. Ez az információ könnyen megjeleníthető a Power BI irányítópultján, vagy az összes illesztőprogram nak SMS-ben is sugározható a szolgáltatásokkal, például az Azure-funkciókkal való integráció révén.

Az alábbi kép a lekérdezés power BI irányítópultjára való kimenetét mutatja be. 

![Eredménykimenet a Power BI irányítópultján](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>További lépések

* [Bevezetés a Stream Analytics térinformatikai funkcióiba](stream-analytics-geospatial-functions.md)
* [Térinformatikai függvények (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
