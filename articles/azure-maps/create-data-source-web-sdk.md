---
title: Adatforrás létrehozása Azure Mapsban | Microsoft Docs
description: Adatforrás létrehozása és használata Azure Maps web SDK-val.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c225aa5f821925247d27890e9ee75f3eac4d9eb9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977351"
---
# <a name="create-a-data-source"></a>Adatforrás létrehozása

A Azure Maps web SDK olyan adatforrásokban tárolja az adatforrásokat, amelyek a lekérdezéshez és megjelenítéshez optimalizálja az adatforrásokat. Jelenleg két típusú adatforrás létezik:

**GeoJSON-adatforrás**

A GeoJSON-alapú adatforrások helyileg is betölthetik és `DataSource` tárolhatják az adataikat a osztály használatával. A GeoJSON adatai manuálisan hozhatók létre vagy hozhatók létre az [Atlas.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) adatnévtérben található segítő osztályok használatával. Az `DataSource` osztály a helyi vagy távoli GeoJSON-fájlok importálására szolgáló funkciókat biztosít. A távoli GeoJSON-fájlokat egy CORs-kompatibilis végponton kell tárolni. Az `DataSource` osztály a fürtszolgáltatási pontokra vonatkozó adatgyűjtési funkciókat biztosít. 

**Vektoros csempe forrása**

A vektoros csempék forrása azt ismerteti, hogyan lehet hozzáférni a vektoros csempék rétegéhez, és a [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) osztály használatával hozható létre. A Azure Maps a [Mapbox Vector csempe specifikációjának](https://github.com/mapbox/vector-tile-spec)megfelelően illeszkedik, amely egy nyílt szabvány. A vektoros csempe rétegei hasonlóak a csempék rétegeihez, azonban az egyes csempék nem egy raszteres képek, hanem tömörített fájlok (PBF-formátum), amelyek vektoros leképezési adatokkal rendelkeznek, és egy vagy több olyan réteget, amely az egyes rétegek stílusa alapján jeleníthető meg és rendezhető az ügyfélen. A vektoros csempén lévő információk pontok, vonalak és sokszögek formájában található földrajzi funkciókat tartalmaznak. A vektoros csempék rétegeinek számos előnye van a raszteres csempén.

 - A vektoros csempék fájlmérete általában sokkal kisebb, mint egy egyenértékű raszteres csempe. Így kevesebb sávszélesség van használatban, ami alacsonyabb késést és gyorsabb leképezést jelent. Ez jobb felhasználói élményt eredményez.
 - Mivel a vektoros csempék a-ügyfélen jelennek meg, alkalmazkodnak az eszköz által megjelenített megoldáshoz. Ez lehetővé teszi, hogy a megjelenített térképek sokkal pontosabban legyenek meghatározva, és a Crystal Clear-címkékkel jelenjenek meg. 
 - A vektoros leképezésekben lévő adatstílus módosítása nem igényli újra az adatletöltést, mivel az új stílust alkalmazni lehet az ügyfélen. Ezzel szemben a raszteres csempék stílusának módosítása általában olyan csempék betöltését igényli a kiszolgálóról, amelyeken alkalmazva van az új stílus.
 - Mivel az adat vektoros formában lett továbbítva, az adatelőkészítéshez kevesebb kiszolgálóoldali feldolgozás szükséges, ami azt jelenti, hogy az újabb adat gyorsabban elérhetővé tehető.

A vektoros forrást használó összes rétegnek meg `sourceLayer` kell adnia egy értéket. 

A létrehozás után az adatforrások hozzáadhatók a térképhez a `map.sources` tulajdonságon keresztül, amely egy [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). A következő kód bemutatja, hogyan hozhat létre `DataSource` és adhat hozzá a térképhez.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Adatforrás csatlakoztatása réteghez

Az adatmegjelenítés a térképen renderelési rétegek használatával történik. Egyetlen adatforrás hivatkozhat egy vagy több renderelési rétegre. A következő megjelenítési rétegekhez az adatforrás szükséges:

- [Buborék réteg](map-add-bubble-layer.md) – a térképre méretezett körökként jeleníti meg a pontok mennyiségét.
- [Szimbólum réteg](map-add-pin.md) – megjelenítheti az adatpontokat ikonként és/vagy szövegként.
- [Hő-Térkép réteg](map-add-heat-map-layer.md) – megjeleníti az adatpontok sűrűségének hő-térképét.
- [Vonal réteg](map-add-shape.md) – a vonal és a sokszög körvonalainak megjelenítéséhez használható. 
- [Sokszög réteg](map-add-shape.md) – egy sokszög területének kitöltése folytonos színnel vagy képmintázattal.

A következő kód bemutatja, hogyan hozhat létre egy adatforrást, hogyan adhatja hozzá a térképhez, és hogyan csatlakoztathatja azt egy buborék réteghez, majd importálhatja a GeoJSON pont adatait egy távoli helyről. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

További renderelési rétegek is vannak, amelyek nem csatlakoznak ezekhez az adatforrásokhoz, hanem közvetlenül az általuk megjelenített adathalmazokat töltik be. 

- [Képréteg](map-add-image-layer.md) – egyetlen rendszerképet fedi le a térképen, és a sarkokat megadott koordináták halmazához köti.
- [Csempe réteg](map-add-tile-layer.md) – a Térkép tetején lévő raszteres csempe réteget rendeli.

## <a name="one-data-source-with-multiple-layers"></a>Egy adatforrás több réteggel

Több réteg is csatlakoztatható egyetlen adatforráshoz. Ez furcsán hangzik, de számos különböző forgatókönyv hasznos lehet. Tegyük fel például, hogy létrehoz egy sokszög rajzolási élményt. Amikor egy felhasználó megrajzol egy sokszöget, a kitöltési sokszög területét kell megjelenítenie, amikor a felhasználó hozzáadja a pontokat a térképhez. A sokszöget körvonalazó stílusú sorok hozzáadásával könnyebben megtekinthető a sokszög szélei. Végül a sokszögben lévő egyes helyzetek, például a PIN-kód vagy a jelölő hozzáadásával könnyebben szerkesztheti az egyes pozíciókat. Itt látható egy olyan rendszerkép, amely bemutatja ezt a forgatókönyvet.

![Több réteget ábrázoló Térkép egyetlen adatforrásból származó adatok megjelenítéséhez](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Ahhoz, hogy ez a forgatókönyv a legtöbb leképezési platformon elérhető legyen, létre kell hoznia egy sokszög objektumot, egy sor objektumot és egy PIN-kódot a sokszög minden egyes helyére. Ahogy a sokszög módosítva lett, manuálisan kell frissítenie a vonalat és a PIN-ket, ami összetett gyorsan elvégezhető.

Azure Maps mindössze annyit kell tennie, hogy egy adatforrásban egyetlen sokszög van, ahogy az alábbi kódban is látható.

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Felugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Hő-Térkép hozzáadása](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)