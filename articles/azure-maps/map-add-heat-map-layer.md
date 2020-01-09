---
title: Heat Map-réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Heat Map-réteg hozzáadása a Azure Maps web SDK-hoz.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b0d07f18682bf39558180753de38a9c5ff106ee3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408524"
---
# <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

A Heat Maps (más néven a pont sűrűsége) olyan adatvizualizációk, amelyek az adatsűrűséget jelölik. Gyakran használják a "gyors elérésű pontok" megjelenítésére a térképen, és nagyszerű módszer a nagyméretű adatkészletek megjelenítésére. 

Például a Térkép nézet több tízezer pontját jeleníti meg, mivel a szimbólumok a legtöbb ábrázolási területhez tartoznak. Ez valószínűleg sok szimbólumot eredményez egymással átfedésben, így megnehezíti az adatelemzést. Ha azonban ugyanazt az adatkészletet tekinti meg, mint egy hő-Térkép, könnyen megtekintheti, hogy a pontok hol találhatók a legsűrűbben, és hogy a relatív sűrűség más területekre is vonatkozik-e.

A Heat Maps számos különböző forgatókönyvben használható, beleértve a következőket:

- **Hőmérsékleti információ**: a két adatpont közötti hőmérséklet-érték közelítését biztosítja.
- A **zaj-érzékelőkre vonatkozó adat**: nem csak az érzékelőt tartalmazó zaj erősségét mutatja, de a távolságon keresztül betekintést nyújt az elszóródásba is. Előfordulhat, hogy a zajszint egy adott helyen nem magas. Ha azonban a zaj lefedettségi területe több érzékelő átfedésben van, lehetséges, hogy ez az átfedési terület nagyobb zajszintet tapasztal, és így láthatóvá válik a Heat térképen.
- **GPS-nyomkövetés**: magában foglalja a sebességét súlyozott magassági térképként, ahol az egyes adatpontok intenzitása a sebességen alapul. Így például megtekintheti a jármű sebességének helyét.

> [!TIP]
> Az adatforrásban lévő összes geometriá koordinátáit alapértelmezés szerint a térképi rétegek rétege jeleníti meg. Ha úgy szeretné korlátozni a réteget, hogy az csak a pont geometriájának funkcióit jelenítse meg, állítsa `['==', ['geometry-type'], 'Point']`értékre a réteg `filter` tulajdonságát. Ha azt szeretné, hogy a multipoint-funkciók is szerepeljenek, állítsa `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`re a réteg `filter` tulajdonságát.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Ha a pontok adatforrását szeretné megjeleníteni hő-hozzárendelésként, adja át az adatforrást a `HeatMapLayer` osztály egy példányának, és adja hozzá a térképhez.

A következő kódban minden egyes hőforrás 10 képpont sugarú, minden nagyítási szinten. Amikor hozzáadja a Heat Map réteget a térképhez, ez a minta beszúrja azt a címke réteg alá, hogy jobb felhasználói élményt hozzon létre. A feliratok jól láthatók a Heat Térkép felett. Az ebben a példában szereplő adatok az [USGS földrengés veszélyforrások programból](https://earthquake.usgs.gov/)származnak, és az elmúlt 30 napban bekövetkezett jelentős földrengéseket jelölik.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Itt látható az előző kód teljes futtatási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű Heat Map-réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>egyszerű Heat Térkép réteget</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>A Heat Map-réteg testreszabása

Az előző példában a sugár-és opacitási beállítások megadásával testre szabta a Heat térképet. A Heat Map réteg számos testreszabási lehetőséget kínál, többek között a következőket:

* `radius`: meghatározza az egyes adatpontok megjelenítéséhez használandó képpont sugarat. A sugarat rögzített számként vagy kifejezésként is beállíthatja. Egy kifejezés használatával méretezheti a sugarat a nagyítási szint alapján, és a térképen egy konzisztens térbeli terület (például egy 5 mérföldes sugár) látható.
* `color`: azt határozza meg, hogy a Heat Map hogyan legyen színezetve. A színátmenetek a Heat Maps gyakori funkciója, amely `interpolate` kifejezéssel is megvalósítható. `step` kifejezést is használhat a Heat Térkép színezéséhez, a sűrűséget pedig vizuálisan kibontva olyan tartományokra, amelyek egy kontúr vagy egy radar stílusú térképhez hasonlítanak. Ezek a színpalettaek a minimális és a maximális sűrűség értékének színét határozzák meg. 

  A Heat Maps színértékeit kifejezésként kell megadni a `heatmap-density` értéknél. Egy interpolációs kifejezésben a 0 indexű szín, vagy a Step kifejezés alapértelmezett színe határozza meg annak a területnek a színét, ahol nincs adatmennyiség. Ezzel megadhatja a háttér színét. Ez az érték gyakran átlátszó vagy félig átlátszó feketére van állítva. 
   
  Példák a színkifejezésekre:

  | Interpoláció színe kifejezés | Lépcsőzetes szín kifejezése | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpoláció",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineáris"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"átlátszó",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Navy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zöld",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "Red"<br/>\] |   

- `opacity`: azt határozza meg, hogy a Heat Térkép rétege milyen átlátszatlan vagy átlátszó legyen.
- `intensity`: szorzót alkalmaz az egyes adatpontok súlyozására a hő teljes intenzitásának növeléséhez. Ez megkönnyíti az adatpontok súlyozásának kis mértékű különbségeit.
- `weight`: alapértelmezés szerint az összes adatponthoz 1 érték tartozik, és a súlyozása egyenlő. A súlyozási beállítás szorzóként funkcionál, és beállíthatja számként vagy kifejezésként. Ha egy szám (például 2) a súlyozásként van beállítva, akkor a rendszer minden adatpontot kétszer helyez el a térképen, így megduplázva a sűrűséget. Ha a súlyozási beállítást egy számra állítja, akkor a rendszer hasonló módon jeleníti meg a hő-hozzárendelést az intenzitás lehetőség használatával. 

  Ha azonban egy kifejezést használ, az egyes adatpontok súlyozása az egyes adatpontok tulajdonságai alapján lehetséges. Tegyük fel például, hogy az egyes adatpontok földrengést jelentenek. Fontos mérőszám, hogy minden egyes földrengés adatpontja egy magnitúdó érték. A földrengések minden alkalommal történnek, de a legtöbb esetben alacsony a jelentősége, és nem is érezhető. Ha egy kifejezésben a magnitúdó értékkel rendeli a súlyozást az egyes adatpontokhoz, a földrengések jelentőségét jobban áttekintheti a meleg térképen.
- `source` és `source-layer`: lehetővé teszi az adatforrás frissítését.

Itt talál egy eszközt a különböző Heat Térkép réteg beállításainak teszteléséhez.

<br/>

<iframe height='700' scrolling='no' title='Hő-Térkép réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Térkép réteg beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzisztens nagyítású Heat-Térkép

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. A Térkép nagyítása után az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. 

Egy `zoom` kifejezéssel méretezheti a sugarat az egyes nagyítási szintekhez, így az egyes adatpontok a Térkép fizikai területére is kiterjednek. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. 

A sugár skálázása úgy, hogy az minden nagyítási szinten megduplázódik, létrehoz egy hő-térképet, amely minden nagyítási szinten konzisztensnek tűnik. Ehhez használja a `zoom`t a 2. alap`exponential interpolation` kifejezéssel, ahogy az a következő mintában látható. A Térkép nagyításával megtekintheti, hogy a Heat Térkép hogyan méretezhető a nagyítási szinttel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzisztens nagyítású Heat-Térkép" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintse meg a tollat, amely <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konzisztens, kinagyítható hőt</a> biztosít.
</iframe>

> [!TIP]
> Ha engedélyezi a fürtözést az adatforráson, akkor a egymáshoz közeledő pontok fürtözött pontként vannak csoportosítva. Az egyes fürtökhöz tartozó pontok számát a Heat Map súlyozási kifejezése használhatja, és jelentősen csökkentheti a megjelenítendő pontok számát. A fürt pontjának számát a pont funkció `point_count` tulajdonsága tárolja: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Ha a fürtözési sugár csak néhány képpont, a renderelésben kevés a vizualizáció. A nagyobb RADIUS-csoportok több pontot mutatnak az egyes fürtökre, és javítják a hő teljesítményét.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)
