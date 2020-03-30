---
title: Adatforrás létrehozása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan hozhat létre adatforrást, és hogyan adhat hozzá egy térképhez a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190236"
---
# <a name="create-a-data-source"></a>Adatforrás létrehozása

Az Azure Maps Web SDK adatforrásokban tárolja az adatokat. Az adatforrások használata optimalizálja az adatműveleteket lekérdezésre és renderelésre. Jelenleg kétféle adatforrás létezik:

**GeoJSON adatforrás**

GeoJSON alapú adatforrás-betöltés, és az `DataSource` osztály használatával helyileg tárolja az adatokat. A GeoJSON-adatok manuálisan is létrehozhatók vagy létrehozhatók az [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) névtér segítő osztályai használatával. Az `DataSource` osztály helyi vagy távoli GeoJSON-fájlok importálásához biztosít funkciókat. A távoli GeoJSON-fájlokat egy CORs-kompatibilis végponton kell üzemeltetni. Az `DataSource` osztály a fürtözési pont adatainak funkcióit biztosítja. Az adatok könnyen hozzáadhatók, eltávolíthatók és `DataSource` frissíthetők az osztállyal.


> [!TIP]
> Tegyük fel, hogy felül szeretné `DataSource`írni az összes adatot a ban. Ha az `clear` akkori `add` függvényeket hívja, előfordulhat, hogy a térkép kétszer újra renderel, ami némi késést okozhat. Ehelyett használja `setShapes` a funkciót, amely eltávolítja és lecseréli az adatforrás összes adatát, és csak a térkép egyetlen újrarenderelését indítja el.

**Vektormozaik forrása**

A vektormozaik-forrás azt írja le, hogyan lehet hozzáférni egy vektormozaik-réteghez. A [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) osztály segítségével példányosítani egy vektoros csempe forrás. A vektormozaik-rétegek hasonlóak a mozaikrétegekhez, de nem azonosak. A mozaikréteg raszteres kép. A vektormozaik-rétegek tömörített fájlok, PBF formátumban. Ez a tömörített fájl vektoros térképadatokat és egy vagy több réteget tartalmaz. A fájl renderelhető és stilizált az ügyfélen, az egyes rétegek stílusa alapján. A vektormozaikban lévő adatok pontok, vonalak és sokszögek formájában tartalmaznak földrajzi jellemzőket. A raszteres mozaikrétegek helyett számos előnye van a vektormozaik-rétegek használatának:

 - A vektorcsempe fájlmérete általában sokkal kisebb, mint egy ezzel egyenértékű raszteres csempe. Mint ilyen, kevesebb sávszélességet használnak. Ez alacsonyabb késleltetést, gyorsabb térképet és jobb felhasználói élményt jelent.
 - Mivel a vektorcsempék megjelennek az ügyfélen, alkalmazkodnak annak az eszköznek a felbontásához, amelyen megjelennek. Ennek eredményeképpen a renderelt térképek jobban meghatározottnak tűnnek, kristálytiszta címkékkel.
 - A vektortérképekben lévő adatok stílusának módosítása nem igényel újra az adatok letöltéséhez, mivel az új stílus alkalmazható az ügyfélre. Ezzel szemben a rasztermozaik-réteg stílusának módosítása általában a csempék kiszolgálóról való betöltését igényli, majd az új stílus alkalmazását.
 - Mivel az adatok vektor formában történnek, kevesebb kiszolgálóoldali feldolgozás szükséges az adatok előkészítéséhez. Ennek eredményeképpen az újabb adatok gyorsabban elérhetők.

Minden vektorforrást használó `sourceLayer` rétegnek meg kell adnia egy értéket.

A létrehozás után adatforrások adhatók `map.sources` hozzá a térképhez a tulajdonságon keresztül, amely egy [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). A következő kód bemutatja, hogyan hozhat létre egy `DataSource` és adhat hozzá a térképhez.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Az Azure Maps megfelel a [Mapbox Vector tile specification,](https://github.com/mapbox/vector-tile-spec)egy nyitott szabvány.

## <a name="connecting-a-data-source-to-a-layer"></a>Adatforrás csatlakoztatása réteghez

Az adatok renderelési rétegek használatával jelennek meg a térképen. Egyetlen adatforrásra egy vagy több renderelési réteg hivatkozhat. A következő renderelési rétegek adatforrást igényelnek:

- [Buborékréteg](map-add-bubble-layer.md) - a pontadatokat méretezett körökként jeleníti meg a térképen.
- [Szimbólumréteg](map-add-pin.md) – a pontadatokat ikonokként vagy szövegként jeleníti meg.
- [Hőtérkép-réteg](map-add-heat-map-layer.md) - a pontadatokat sűrűségi hőtérképként jeleníti meg.
- [Vonalréteg](map-add-shape.md) - egy vonal renderelése és a sokszögek körvonalának renderelése. 
- [Sokszögréteg](map-add-shape.md) - egyszínű vagy képmintázattal tölti ki a sokszög et.

A következő kód bemutatja, hogyan hozhat létre adatforrást, hogyan veheti fel a térképre, és hogyan csatlakoztathatja buborékréteghez. Ezután importálja a GeoJSON-pontadatokat egy távoli helyről az adatforrásba. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Vannak további renderelési rétegek, amelyek nem kapcsolódnak ezekhez az adatforrásokhoz, de közvetlenül betöltik az adatokat a rendereléshez. 

- [Képréteg](map-add-image-layer.md) – egyetlen képet fed le a térkép tetején, és a sarkokat egy megadott koordinátakészlethez köti.
- [Mozaikréteg](map-add-tile-layer.md) - raszteres csemperéteget helyez a térkép tetejére.

## <a name="one-data-source-with-multiple-layers"></a>Egy több rétegű adatforrás

Egyetlen adatforráshoz több réteg is csatlakoztatható. Számos különböző forgatókönyv van, amelyben ez a beállítás hasznos. Vegyük például azt a forgatókönyvet, amelyben a felhasználó sokszöget rajzol. Meg kell tetsolni, és töltse ki a sokszög terület, mint a felhasználó hozzáteszi, pontokat a térképhez. Ha egy stílusú vonalat ad hozzá a sokszög körvonalazásához, könnyebben láthatja a sokszög széleit, ahogy a felhasználó rajzol. A sokszög egyedi pozíciójának kényelmes szerkesztéséhez minden pozíció fölé hozzáadhatunk egy fogantyút, például egy tűt vagy egy jelölőt.

![Több rétegből származó adatok megjelenítése egyetlen adatforrásból](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

A legtöbb leképezési platformon sokszögobjektumra, vonalobjektumra és a sokszög minden egyes pozíciójához egy tűre van szükség. A sokszög módosításakor manuálisan kell frissítenie a vonalat és a tűket, amelyek gyorsan bonyolulttá válhatnak.

Az Azure Maps segítségével mindössze egyetlen sokszögre van szüksége egy adatforrásban, ahogy az az alábbi kódban látható.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileForrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Hőtérkép hozzáadása](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)