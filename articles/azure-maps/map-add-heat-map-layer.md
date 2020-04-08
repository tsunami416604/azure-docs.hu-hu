---
title: Hőtérkép-réteg hozzáadása a térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá hőtérkép-réteget egy térképhez a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804554"
---
# <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

A hőtérképek, más néven pontsűrűségi térképek, az adatvizualizáció egy típusa. Az adatok sűrűségét egy színtartomány segítségével jelenítik meg, és a "forró pontok" adatait jelenítik meg a térképen. A hőtérképek nagyszerű módja annak, hogy nagy számú pontot számláló adatkészleteket jelenítsen meg. 

Több tízezer pont szimbólumként való renderelése lefedheti a térképterület nagy részét. Ez az eset valószínűleg sok egymást átfedő szimbólumot eredményez. Megnehezíti az adatok jobb megértését. Ha azonban ugyanazt az adatkészletet hőtérképként jelenítheti meg, könnyen láthatja az egyes adatpontok sűrűségét és relatív sűrűségét.

A hőtérképeket számos különböző forgatókönyvben használhatja, többek között a következőkben:

- **Hőmérsékleti adatok**: Közelítést biztosít a hőmérséklet két adatpont közötti hőmérséklethez.
- **Zajérzékelők adatai**: Nem csak a zaj intenzitását mutatja ott, ahol az érzékelő van, hanem betekintést nyújt a távolsági disszipációba is. Előfordulhat, hogy a zajszint bármelyik helyen nem magas. Ha több érzékelő zajlefedettségi területe átfedi egymást, lehetséges, hogy ez az egymást átfedő terület magasabb zajszintet tapasztalhat. Mint ilyen, az átlapolt terület látható lenne a hőtérképen.
- **GPS-nyomkövetés**: A sebességet súlyozott magasságtérképként tartalmazza, ahol az egyes adatpontok intenzitása a sebességen alapul. Ez a funkció például lehetővé teszi annak megtekintését, hogy a jármű hol hajtott.

> [!TIP]
> A hőtérkép-rétegek alapértelmezés szerint egy adatforrás összes geometriájának koordinátáit jelenítik meg. Ha úgy szeretné korlátozni a réteget, hogy az `filter` csak a `['==', ['geometry-type'], 'Point']`pontgeometria idomait jelenítse meg, állítsa a réteg tulajdonságát a értékre. Ha multipoint-szolgáltatásokat is szeretne felvenni, `filter` állítsa a `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`réteg tulajdonságát a-ra.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Ha a pontok adatforrását hőtérképként szeretné megjeleníteni, adja át `HeatMapLayer` az adatforrást az osztály egy példányának, és adja hozzá a térképhez.

A következő kódban minden hőpont sugara 10 képpont minden nagyítási szinten. A jobb felhasználói élmény érdekében a hőtérkép a címkeréteg alatt van. A címkék jól láthatóak maradnak. Az adatok ebben a mintában a [USGS Földrengés veszélyek Program](https://earthquake.usgs.gov/). Ez a jelentős földrengések történtek az elmúlt 30 napban.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Itt van a teljes futó kód minta az előző kód.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű hőtérkép-réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen Simple Heat<a href='https://codepen.io/azuremaps'>@azuremaps</a>Map <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Layer</a> by Azure Maps () című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="customize-the-heat-map-layer"></a>A hőtérkép-réteg testreszabása

Az előző példa a sugár és az opacitás beállításainak beállításával szabta testre a hőtérképet. A hőtérkép-réteg számos testreszabási lehetőséget kínál, többek között:

* `radius`: Meghatározza azt a képpontsugarat, amelyben az egyes adatpontok at szeretné megjeleníteni. A sugarat beállíthatja rögzített számként vagy kifejezésként. Egy kifejezés használatával a sugarat a nagyítási szint alapján méretezheti, és a térképen egységes térbeli területet (például 5 mérföldes sugarat) jelölhet.
* `color`: Megadja a hőtérkép színezésének módját. A színátmenet a hőtérképek gyakori jellemzője. A hatást egy `interpolate` kifejezéssel érheti el. A hőtérkép `step` színezéséhez kifejezéssel is színezheti a sűrűséget, és a sűrűséget vizuálisan olyan tartományokra bonthatja, amelyek hasonlítanak egy kontúr- vagy sugárstílus-térképre. Ezek a színpaletták határozzák meg a színeket a minimumtól a maximális sűrűségig. 

  A hőtérképek színértékeit az `heatmap-density` érték kifejezéseként adhatja meg. Az adatokat nem mutató terület színe az "Interpoláció" kifejezés 0. Ezt az értéket használhatja háttérszín definiálására. Gyakran előfordul, hogy ez az érték átlátszó, vagy félig átlátszó fekete. 
   
  Íme néhány példa a színkifejezésekre:

  | Interpoláció színkifejezése | Lépcsős színkifejezés | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpoláció",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineáris",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hőtérkép-sűrűség",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "átlátszó",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"lépés",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hőtérkép-sűrűség",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;"átlátható",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "haditengerészet",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zöld",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "piros"<br/>\] |   

- `opacity`: Megadja, hogy a hőtérkép-réteg mennyire átlátszatlan vagy átlátszó.
- `intensity`: A hőtérkép teljes intenzitásának növelése érdekében szorzót alkalmaz az egyes adatpontok súlyára. Különbséget okoz az adatpontok súlyában, így könnyebben láthatóvá válik.
- `weight`: Alapértelmezés szerint minden adatpont súlya 1, és a súlyozás a következő képpen történik. A súlybeállítás szorzóként működik, és beállíthatja számként vagy kifejezésként. Ha egy szám van beállítva súlyként, akkor az egyenértékűség, hogy az egyes adatpontot kétszer helyezzük a térképre. Például, ha a súly 2, akkor a sűrűség megduplázódik. Ha a weight opciót számra állítja, a hőtérkép hasonló módon jelenik meg, mint az intenzitási beállítás. 

  Ha azonban kifejezést használ, az egyes adatpontok vastagsága az egyes adatpont-tulajdonságokon alapulhat. Tegyük fel például, hogy minden adatpont földrengést jelöl. A magnitúdó értéke volt egy fontos metrika minden földrengés adatpont. Földrengések történnek minden alkalommal, de a legtöbb egy alacsony nagyságrendű, és nem veszik észre. Használja a kifejezés magnitúdóértékét, hogy a súlyt az egyes adatpontokkal rendelje hozzá. Segítségével a nagysága értéket rendelni a súlyt, akkor kap egy jobb képviseletét a jelentősége földrengések a hőtérképen.
- `source`és `source-layer`: Az adatforrás frissítésének lehetővé tétele.

Itt egy eszköz, hogy teszteljék a különböző hőtérkép réteg lehetőségeket.

<br/>

<iframe height='700' scrolling='no' title='Hőtérkép réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll heat map<a href='https://codepen.io/azuremaps'>@azuremaps</a>layer <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>options</a> by Azure Maps ( ) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzisztens nagyítható hőtérkép

Alapértelmezés szerint a hőtérkép-rétegben megjelenített adatpontok sugara rögzített képpontsugarat mutat az összes nagyítási szinthez. A térkép nagyításakor az adatok összeaggregálódnak, és a hőtérkép-réteg máshogy néz ki. 

Használjon `zoom` kifejezést az egyes nagyítási magasságok sugarának méretezéséhez úgy, hogy minden adatpont a térkép ugyanazon fizikai felületét fedje le. Ez a kifejezés teszi a hőtérkép réteg meg statikus és következetes. A térkép minden egyes nagyítási szintje kétszer annyi képponttal rendelkezik függőlegesen és vízszintesen, mint az előző nagyítási szint. 

A sugár méretezése úgy, hogy minden nagyítási szinttel megduplázódjon, olyan hőtérképet hoz létre, amely minden nagyítási szinten egységesnek tűnik. A méretezés alkalmazásához `zoom` használja a `exponential interpolation` 2. `2 * Math.pow(2, minZoom - maxZoom)` Nagyítsa a térképet, hogy lássa, hogyan skálázódik a hőtérkép a nagyítási szinttel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzisztens nagyítható hőtérkép" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Konzisztens, nagyítható hőtérképét</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

> [!TIP]
> Ha engedélyezi a fürtözést az adatforráson, az egymáshoz közeli pontok fürtözött pontként lesznek csoportosítva. Az egyes fürtök pontszámát használhatja a hőtérkép súlykifejezéseként. Ez jelentősen csökkentheti a renderelendő pontok számát. A fürt pontszáma a pontszolgáltatás `point_count` egyik tulajdonságában van tárolva: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Ha a fürtözési sugár csak néhány képpont, akkor a renderelés ben kis vizuális különbség van. A nagyobb sugár több pontot csoportosít az egyes fürtökbe, és javítja a hőtérkép teljesítményét.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HeatMapLayer (HeatMapLayer)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOpciók](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

A térképekhez hozzáadni rakandó további kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)
