---
title: Heat Map-réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Heat Map-réteg hozzáadása a JavaScript-térképhez
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 85f184603cdcadce6bf750db5765f32a0735453d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639024"
---
# <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

A Heat Maps, más néven a pont sűrűsége térképek olyan adatvizualizációk, amelyek az adatsűrűséget jelölik az adatmennyiség színskálájának használatával. Gyakran használják a "gyors elérésű pontok" megjelenítésére a térképen, és nagyszerű módszer a nagyméretű adathalmazok megjelenítésére.  Például a Térkép nézet több tízezer pontján szimbólumként jeleníti meg a térképi terület nagy részét, és számos szimbólumot eredményezhet egymással, így megnehezíti az adatelemzést. Ugyanakkor az azonos adatkészletet is láthatóvá teheti egy hő-hozzárendeléssel, így könnyen megtekinthető, hogy hol találhatók a legsűrűbbek és a relatív sűrűségek a többi területhez képest. Sok olyan helyzet van, amelyben a Heat Maps használatban van. Íme néhány példa;

* A hőmérsékleti információk általában hő-térképként jelennek meg, mivel a két adatpont közötti hőmérséklet-közelítést biztosítja.
* A zajszint-érzékelőkre vonatkozó adatmegjelenítés nem csupán azt mutatja meg, hogy az érzékelő milyen zajt tartalmaz, de a távolságon keresztül is biztosíthatja az adatelemzést. Előfordulhat, hogy a zajszint egy adott helyen nem magas, de ha a zaj lefedettségi területe több érzékelőnél átfedésben van, lehetséges, hogy ez az átfedési terület nagyobb zajszintet eredményezhet, és így láthatóvá válik a Heat térképen.
* Egy olyan GPS-nyomkövetés megjelenítése, amely magában foglalja a sebességét súlyozott magasságú térképként, ahol az egyes adatpontok intenzitása a sebességen alapul, így megtekintheti a jármű sebességének helyét.

> [!TIP]
> A Heat Map-rétegek alapértelmezés szerint az adatforrásban lévő geometriák koordinátáit fogják megjeleníteni. Ha úgy szeretné korlátozni a réteget, hogy az csak a pont geometriájának funkcióit jelenítse meg, `['==', ['geometry-type'], 'Point']` állítsa `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` a `filter` réteg tulajdonságát a (vagy) értékre, ha a multipoint-szolgáltatásokat is fel szeretné venni.

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Ha a pontok egy adatforrását szeretné megjeleníteni hő-hozzárendelésként, adja át az adatforrást az `HeatMapLayer` osztály egy példányának, és adja hozzá a térképhez az itt látható módon.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű Heat Map-réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>egyszerű Heat Térkép réteget</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ebben a példában minden egyes hőforrás 10 képpont sugarú, minden nagyítási szinten. Ha a térképhez hozzáadja a Heat Map réteget, ez a minta beszúrja azt a címke réteg alá, hogy jobb felhasználói élményt hozzon létre, mivel a címkék jól láthatók a Heat Térkép felett. Az ebben a példában szereplő adatok forrása az [USGS földrengés](https://earthquake.usgs.gov/) -veszélyforrások programja, és az elmúlt 30 napban bekövetkezett jelentős földrengéseket jelöli.

## <a name="customizing-the-heat-map-layer"></a>A Heat Térkép rétegének testreszabása

Az előző példában a sugár-és opacitási beállítások megadásával testre szabta a Heat térképet. A Heat Map réteg számos lehetőséget kínál a testreszabásra;

* `radius`: Meghatározza az egyes adatpontok megjelenítéséhez használandó képpont sugarat. A sugár rögzített számként vagy kifejezésként állítható be. Egy kifejezés használatával lehetséges a sugár méretezése a nagyítási szint alapján, amely úgy tűnik, hogy egy konzisztens térbeli terület a térképen (például 5 mérföldes RADIUS).
* `color`: Meghatározza a hő térképének színét. Egy színátmenetet gyakran használnak a Heat Maps esetében, és egy `interpolate` kifejezéssel is elérhetők. A hő-Térkép színezésére szolgáló kifejezéshasználatávalasűrűségvizuálisanolyantartományokrakerül,amelyeknagyobbmértékbenhasonlítanakegykontúrosvagyradarstílusútérképre.`step` Ezek a színpalettaek a minimális és a maximális sűrűség értékének színét határozzák meg. A Heat Maps színértékei kifejezésként vannak megadva az `heatmap-density` értéken. Egy interpolációs kifejezésben vagy a Step kifejezés alapértelmezett színében a 0 indexnél megadott szín határozza meg annak a területnek a színét, ahol nincs adatmennyiség, és a háttérszín definiálására használható. Számos előnyben részesítette ezt az értéket áttetszőre vagy félig átlátszó feketére állítani. Példa színkifejezésekre:

| Interpoláció színe kifejezés | Lépcsőzetes szín kifejezése | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpoláció",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineáris"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hő-sűrűség"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Navy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zöld",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "piros"<br/>\] | 

* `opacity`: Meghatározza, hogy a Heat Térkép rétege milyen átlátszatlan vagy átlátszó legyen.
* `intensity`: Egy szorzót alkalmaz az egyes adatpontok súlyozására, hogy növelje a hő általános intenzitását, és megkönnyítse az adatpontok súlyozásának kis mértékű különbségének megjelenítését.
* `weight`: Alapértelmezés szerint az összes adatpont súlya 1, így az összes adatpont egyenlő súlyozású. A súlyozási beállítás szorzóként funkcionál, és beállítható számként vagy kifejezésként. Ha egy szám a súlyozásként van beállítva, mondjuk 2, akkor az egyes adatpontok kétszer történő elhelyezése a térképen, így megduplázva a sűrűséget. Ha a súlyozási beállítást egy számra állítja, akkor a rendszer hasonló módon jeleníti meg a hő-hozzárendelést az intenzitás lehetőség használatával. Ha azonban egy kifejezés van használatban, az egyes adatpontok súlyozása az egyes adatpontok tulajdonságai alapján lehetséges. Az egyes adatpontok földrengést jelentenek. Az egyes földrengés-adatpontok fontos mérőszáma a magnitúdó érték. A földrengések minden alkalommal történnek, de a legtöbbjük alacsony nagyságrendű, és nem is érezhető. Ha egy kifejezésben a magnitúdó értékét használja, a súlyozást az egyes adatpontokhoz rendelheti, a nagyobb számú földrengés jobban képviselteti magát a hő-térképen.
* Az alap réteg beállításai mellett; minimális/maximális nagyítás, látható és szűrhető, akkor is `source` lehetőség van, ha frissíteni kívánja az adatforrást és `source-layer` a beállítást, ha az adatforrás egy vektoros csempe forrása.

Itt látható egy eszköz a különböző Heat Térkép rétegbeli beállítások tesztelésére.

<br/>

<iframe height='700' scrolling='no' title='Hő-Térkép réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Térkép rétegének beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzisztens nagyítású Heat-Térkép

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. Mivel a Térkép kinagyítja az adatösszesítéseket, és a Heat Map-réteg eltérőnek tűnik. Egy `zoom` kifejezés használható a sugár méretezésére az egyes nagyítási szintekhez úgy, hogy az egyes adatpontok a Térkép fizikai területére is kiterjednek. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár méretezése úgy, hogy az minden nagyítási szinten megduplázódik, egy olyan hő-térképet hoz létre, amely minden nagyítási szinten konzisztensnek tűnik. Ez az alábbi példában látható módon végezhető `zoom` el a 2 `exponential interpolation` . alap kifejezéssel. A Térkép nagyításával megtekintheti, hogy a Heat Térkép hogyan méretezhető a nagyítási szinttel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzisztens nagyítású Heat-Térkép" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az <a href='https://codepen.io'>CodePen</a>-on található, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által konzisztens, kinagyítható <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>hő</a> -hozzárendelést.
</iframe>

> [!TIP]
> Ha engedélyezi a fürtözést az adatforráson, az egymáshoz közeledő pontok fürtözött pontként vannak csoportosítva. Az egyes fürtökhöz tartozó pontok száma felhasználható a Heat Map súlyozási kifejezéséhez, és jelentősen csökkentheti a megjelenítendő pontok számát. A fürt pontok számát a pont funkció egy `point_count` tulajdonságában tárolja az alábbi ábrán látható módon. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Ha a fürtözési sugár csak néhány képponttal rendelkezik, akkor a vizualizációnak nem kell a renderelési különbsége. Egy nagyobb sugár több pontot fog csoportosítani az egyes fürtökhöz, és javítja a hő teljesítményét, de a különbségek még észrevehetőek lesznek.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)