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
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484306"
---
# <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

A Heat Maps, más néven a pont sűrűsége térképek olyan adatvizualizációk, amelyek az adatsűrűséget jelölik az adatmennyiség színskálájának használatával. Gyakran használják a "gyors elérésű pontok" megjelenítésére a térképen, és nagyszerű módszer a nagyméretű adathalmazok megjelenítésére.  Például a Térkép nézet több tízezer pontján szimbólumként jeleníti meg a térképi terület nagy részét, és számos szimbólumot eredményezhet egymással, így megnehezíti az adatelemzést. Ugyanakkor az azonos adatkészletet is láthatóvá teheti egy hő-hozzárendeléssel, így könnyen megtekinthető, hogy hol találhatók a legsűrűbbek és a relatív sűrűségek a többi területhez képest. Sok olyan helyzet van, amelyben a Heat Maps használatban van. Íme néhány példa;

- A hőmérsékleti információk általában hő-térképként jelennek meg, mivel a két adatpont közötti hőmérséklet-közelítést biztosítja.
- A zajszint-érzékelőkre vonatkozó adatmegjelenítés nem csupán azt mutatja meg, hogy az érzékelő milyen zajt tartalmaz, de a távolságon keresztül is biztosíthatja az adatelemzést. Előfordulhat, hogy a zajszint egy adott helyen nem magas, de ha a zaj lefedettségi területe több érzékelőnél átfedésben van, lehetséges, hogy ez az átfedési terület nagyobb zajszintet eredményezhet, és így láthatóvá válik a Heat térképen.
- Egy olyan GPS-nyomkövetés megjelenítése, amely magában foglalja a sebességét súlyozott magasságú térképként, ahol az egyes adatpontok intenzitása a sebességen alapul, így megtekintheti a jármű sebességének helyét.

> [!TIP]
> A Heat Map-rétegek alapértelmezés szerint az adatforrásban lévő geometriák koordinátáit fogják megjeleníteni. Ha úgy szeretné korlátozni a réteget, hogy az csak a pont geometriai funkcióit jelenítse meg, állítsa `['==', ['geometry-type'], 'Point']` vagy `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`re a réteg `filter` tulajdonságát, ha a multipoint-szolgáltatásokat is fel szeretné venni.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Ha a pontok adatforrását szeretné megjeleníteni hő-hozzárendelésként, adja át az adatforrást a `HeatMapLayer` osztály egy példányának, és adja hozzá a térképhez az itt látható módon.

A következő kódban minden egyes hőforrás 10 képpont sugarú, minden nagyítási szinten. Ha a térképhez hozzáadja a Heat Map réteget, ez a minta beszúrja azt a címke réteg alá, hogy jobb felhasználói élményt hozzon létre, mivel a címkék jól láthatók a Heat Térkép felett. Az ebben a példában szereplő adatok forrása az [USGS földrengés-veszélyforrások programja](https://earthquake.usgs.gov/) , és az elmúlt 30 napban bekövetkezett jelentős földrengéseket jelöli.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű Heat Map-réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>egyszerű Heat Térkép réteget</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>A Heat Térkép rétegének testreszabása

Az előző példában a sugár-és opacitási beállítások megadásával testre szabta a Heat térképet. A Heat Map réteg számos lehetőséget kínál a testreszabásra;

* `radius`: meghatározza az egyes adatpontok megjelenítéséhez használandó képpont sugarat. A sugár rögzített számként vagy kifejezésként állítható be. Egy kifejezés használatával lehetséges a sugár méretezése a nagyítási szint alapján, amely úgy tűnik, hogy egy konzisztens térbeli terület a térképen (például 5 mérföldes RADIUS).
* `color`: azt határozza meg, hogy a Heat Map hogyan legyen színezetve. Egy színátmenetet gyakran használnak a Heat Maps számára, és `interpolate` kifejezéssel is elérhetők. Ha `step` kifejezést használ a Heat-Térkép színezésére, a sűrűség vizuálisan olyan tartományba kerül, amely jobban hasonlít egy kontúr vagy egy radar stílusú térképre. Ezek a színpalettaek a minimális és a maximális sűrűség értékének színét határozzák meg. A Heat Maps színértékei kifejezésként vannak megadva a `heatmap-density` értéknél. Egy interpolációs kifejezésben vagy a Step kifejezés alapértelmezett színében a 0 indexnél megadott szín határozza meg annak a területnek a színét, ahol nincs adatmennyiség, és a háttérszín definiálására használható. Számos előnyben részesítette ezt az értéket áttetszőre vagy félig átlátszó feketére állítani. Példa színkifejezésekre:

| Interpoláció színe kifejezés | Lépcsőzetes szín kifejezése | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpoláció",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineáris"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"átlátszó",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Navy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zöld",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "Red"<br/>\] | 

- `opacity`: azt határozza meg, hogy a Heat Térkép rétege milyen átlátszatlan vagy átlátszó legyen.
- `intensity`: szorzót alkalmaz az egyes adatpontok súlyozására, hogy növelje a hő általános intenzitását, és megkönnyíti az adatpontok súlyozásának kis mértékű eltérését.
- `weight`: alapértelmezés szerint az összes adatponthoz 1 érték tartozik, így az összes adatpont egyenlő súlyozású. A súlyozási beállítás szorzóként funkcionál, és beállítható számként vagy kifejezésként. Ha egy szám a súlyozásként van beállítva, mondjuk 2, akkor az egyes adatpontok kétszer történő elhelyezése a térképen, így megduplázva a sűrűséget. Ha a súlyozási beállítást egy számra állítja, akkor a rendszer hasonló módon jeleníti meg a hő-hozzárendelést az intenzitás lehetőség használatával. Ha azonban egy kifejezés van használatban, az egyes adatpontok súlyozása az egyes adatpontok tulajdonságai alapján lehetséges. Az egyes adatpontok földrengést jelentenek. Az egyes földrengés-adatpontok fontos mérőszáma a magnitúdó érték. A földrengések minden alkalommal történnek, de a legtöbbjük alacsony nagyságrendű, és nem is érezhető. Ha egy kifejezésben a magnitúdó értékét használja, a súlyozást az egyes adatpontokhoz rendelheti, a nagyobb számú földrengés jobban képviselteti magát a hő-térképen.
- Az alap réteg beállításai mellett; min/max nagyítás, látható és szűrő, `source` lehetőség is van, ha frissíteni kívánja az adatforrást és a `source-layer` beállítást, ha az adatforrás egy vektoros csempe-forrás.

Itt látható egy eszköz a különböző Heat Térkép rétegbeli beállítások tesztelésére.

<br/>

<iframe height='700' scrolling='no' title='Hő-Térkép réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Térkép réteg beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzisztens nagyítású Heat-Térkép

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. Ahogy a Térkép nagyítva van, az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. Egy `zoom` kifejezés használható a sugár méretezésére az egyes nagyítási szintekhez úgy, hogy az egyes adatpontok a Térkép ugyanazon fizikai területére terjednek ki. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár méretezése úgy, hogy az minden nagyítási szinten megduplázódik, egy olyan hő-térképet hoz létre, amely minden nagyítási szinten konzisztensnek tűnik. Ezt a `zoom` az alapszintű 2 `exponential interpolation` kifejezéssel hajthatja végre, ahogyan az alábbi példában is látható. A Térkép nagyításával megtekintheti, hogy a Heat Térkép hogyan méretezhető a nagyítási szinttel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzisztens nagyítású Heat-Térkép" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintse meg a tollat, amely <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konzisztens, kinagyítható hőt</a> biztosít.
</iframe>

> [!TIP]
> Ha engedélyezi a fürtözést az adatforráson, az egymáshoz közeledő pontok fürtözött pontként vannak csoportosítva. Az egyes fürtökhöz tartozó pontok száma felhasználható a Heat Map súlyozási kifejezéséhez, és jelentősen csökkentheti a megjelenítendő pontok számát. A fürt pontjának számát a pont funkció `point_count` tulajdonsága tárolja az alább látható módon. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Ha a fürtözési sugár csak néhány képponttal rendelkezik, akkor a vizualizációnak nem kell a renderelési különbsége. Egy nagyobb sugár több pontot fog csoportosítani az egyes fürtökhöz, és javítja a hő teljesítményét, de a különbségek még észrevehetőek lesznek.

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