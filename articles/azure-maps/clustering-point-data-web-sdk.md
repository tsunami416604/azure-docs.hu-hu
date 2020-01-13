---
title: A csoportosítási pontra vonatkozó adatelemek | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a fürtökkel kapcsolatos információkat, és hogyan teheti azt egy térképen a Microsoft Azure Maps web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 846abb61511ae1d5aedf77059ed2f1e9f4e5dbfb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911742"
---
# <a name="clustering-point-data"></a>Fürtözési pontra vonatkozó adatértékek

Ha a Térkép számos adatpontját jeleníti meg, a pontok átfedésben vannak egymással, a Térkép zsúfoltnak tűnik, és nehéz lesz megtekinteni és használni. Az adatpontok fürtözése a felhasználói élmény javításához használható. A fürtözési pontra vonatkozó adatgyűjtési folyamat a pontok egymáshoz közel lévő és a térképen való megjelenítését jelenti egyetlen fürtözött adatpontként. Ahogy a felhasználó nagyítja a térképet, a fürtök az egyes adatpontokon kívülre kerülnek.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Az adatforrások fürtözésének engedélyezése

A fürtözés egyszerűen engedélyezhető a `DataSource` osztályban úgy, hogy a `cluster` beállítást igaz értékre állítja. Emellett a képponthoz tartozó sugár kiválasztásával a fürthöz egyesíthető közeli pontok is megadhatók a `clusterRadius` használatával, és a nagyítási szint megadható, amikor a `clusterMaxZoom` lehetőséggel letilthatja a fürtözési logikát. Íme egy példa arra, hogyan engedélyezhető a fürtözés egy adatforrásban.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Ha két adatpont van a helyszínen, akkor lehetséges, hogy a fürt soha nem szakad meg egymástól, attól függetlenül, hogy a felhasználó milyen mértékben nagyítja a szolgáltatást. Ennek a megoldásnak a megadásához beállíthatja az adatforrás `clusterMaxZoom` lehetőségét, amely a nagyítási szinten megadja a fürtszolgáltatási logika letiltását, és egyszerűen megjeleníti a mindent.

A `DataSource` osztály a következő, fürtözéshez kapcsolódó metódusokkal is rendelkezik:

| Módszer | Visszatérési típus | Leírás |
|--------|-------------|-------------|
| getClusterChildren (clusterId: szám) | Megígéri, hogy&lt;Array&lt;&lt;geometriát, bármely&gt; \| alakzatot&gt;&gt; | A következő nagyítási szinten kéri le a megadott fürt gyermekeit. Ezek a gyerekek az alakzatok és alfürtek kombinációja lehet. Az alfürtek a ClusteredProperties megfelelő tulajdonságokkal rendelkező funkciók lesznek. |
| getClusterExpansionZoom (clusterId: szám) | Ígéret&lt;szám&gt; | Kiszámítja azt a nagyítási szintet, amelynél a fürt megkezdi a kibővítését vagy szétbontását. |
| getClusterLeaves (clusterId: szám, korlát: szám, eltolás: szám) | Megígéri, hogy&lt;Array&lt;&lt;geometriát, bármely&gt; \| alakzatot&gt;&gt; | Egy fürt összes pontjának lekérése. Állítsa be úgy a `limit`, hogy a pontok egy részhalmazát adja vissza, és használja a `offset` a pontokon keresztül. |

## <a name="display-clusters-using-a-bubble-layer"></a>Fürtök megjelenítése buborék réteg használatával

A buborékdiagram nagyszerű módja a fürtözött pontok megjelenítésének, mivel egyszerűen méretezheti a RADIUS-t, és a fürtben lévő pontok száma alapján módosíthatja a színeket a kifejezés használatával. Ha buborék réteg használatával jeleníti meg a fürtöket, külön réteget kell használnia a nem fürtözött adatpontok megjelenítéséhez. Gyakran jó, ha a buborékon felül a fürt méretét is meg szeretné tudni. Egy szöveggel rendelkező szimbólum-réteg és nem használható ikon a viselkedés eléréséhez. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű buborékdiagram-fürtözés" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>alapszintű buborék rétegének fürtözését</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Fürtök megjelenítése egy szimbólum réteg használatával

Ha a pont adatai a szimbólum réteggel jelennek meg, az alapértelmezés szerint automatikusan elrejti az egymással átfedésben lévő szimbólumokat a tisztább felhasználói élmény létrehozásához, azonban ez nem a kívánt élmény, ha meg szeretné tekinteni az adatpontok sűrűségét a térképen. A Symbol Layers `iconOptions` tulajdonság `allowOverlap` beállításának beállítása `true` letiltja ezt a folyamatot, de a rendszer az összes megjelenített szimbólumot eredményezi. A fürtözés lehetővé teszi, hogy megtekintse az összes információ sűrűségét, miközben szép tiszta felhasználói élményt hoz létre. Ebben a példában egyéni szimbólumokat fog használni a fürtök és az egyes adatpontok ábrázolásához.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürtözött szimbólum réteg" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>fürtözött szimbólum réteget</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Fürtözés és a Heat Maps réteg

A Heat Maps nagyszerű lehetőséget mutat a térképen lévő adatsűrűség megjelenítésére. Ez a vizualizáció nagy számú adatpontot tud kezelni saját maga, de még több adattal is képes kezelni, ha az adatpontok fürtözöttek, és a fürt mérete a hőenergia-Térkép súlyozására szolgál. Ennek eléréséhez állítsa be a Heat Térkép rétegének `weight` lehetőségét, hogy `['get', 'point_count']`. Ha a fürt sugara kicsi, a Heat Térkép a nem fürtözött adatpontok használatával nagyjából megegyezően fog megjelenni, de sokkal jobb teljesítményt tesz elérhetővé. Azonban minél kisebb a fürt sugara, annál pontosabbak lesznek a hőenergia-leképezések, a teljesítményük pedig kisebb.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A fürt súlyozott felmelegedési térképe" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>fürt súlyozott Heat térképét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Az egér eseményei a fürtözött adatpontokon

Ha az egér eseményei olyan rétegen történnek, amely fürtözött adatpontokat tartalmaz, a rendszer a fürtözött adatpontot GeoJSON pont szolgáltatás objektumként adja vissza az eseménynek. Ennek a pontnak a funkciója a következő tulajdonságokkal fog rendelkezni:

| Tulajdonság neve             | Type (Típus)    | Leírás   |
|---------------------------|---------|---------------|
| `cluster`                 | logikai | Azt jelzi, hogy a szolgáltatás egy fürtöt jelöl-e. |
| `cluster_id`              | sztring  | A fürt egyedi azonosítója, amely a DataSource `getClusterExpansionZoom`, `getClusterChildren`és `getClusterLeaves` metódusokkal használható. |
| `point_count`             | szám  | A fürt által tartalmazott pontok száma.  |
| `point_count_abbreviated` | sztring  | Egy karakterlánc, amely a `point_count` értéket rövidíti, ha hosszú. (például 4 000-es lesz 4K)  |

Ez a példa egy olyan buborék réteget tesz elérhetővé, amely a fürt pontjait jeleníti meg, és Egy kattintásos eseményt ad hozzá, amely a leképezés aktiválásakor, kiszámításakor és nagyításakor a következő nagyítási szintre kerül, ahol a fürt a `DataSource` osztály `getClusterExpansionZoom` metódusával és a fürtözött adatpontra rákattintott `cluster_id` tulajdonsággal fog kitörni. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/moZWeV/'>fürt getClusterExpansionZoom</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>A fürt környékének megjelenítése 

A fürt által reprezentált pont-adat egy adott terület felett van elosztva. Ebben a példában, amikor az egérmutatót egy fürt fölé viszi, a rendszer a benne található egyedi adatpontokat (levelek) fogja használni a domború hajótest kiszámításához, és megjeleníti a térképen a terület megjelenítéséhez. A fürtben található összes pont a `getClusterLeaves` metódus használatával kérhető le az adatforrásból. A domború hajótest egy olyan sokszög, amely egy rugalmas szalaghoz hasonló pontokat helyez el, és a `atlas.math.getConvexHull` metódussal számítható ki.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Fürt domború hajótest" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>fürt domború hajótestét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Fürtökben lévő adatösszesítés

A fürtök gyakran a fürtön belüli pontok számát jelző szimbólumot jelképeznek, azonban esetenként érdemes lehet a fürtök stílusát egy adott metrika alapján módosítani, például a fürtön belüli összes pont teljes bevételét. A fürt összesítései egyéni tulajdonságok hozhatók létre és tölthetők fel [összesítő kifejezés](data-driven-style-expressions-web-sdk.md#aggregate-expression) számításával.  A fürtök összesítései meghatározhatók a `DataSource``clusterProperties` lehetőségében.

A következő minta összesítő kifejezéssel számítja ki, hogy a fürt minden egyes adatpontjának entitás típusa tulajdonsága alapján ki van-e számítva a darabszám.

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt összesítései" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A tollas <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>fürtök összesítéseit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján tekintheti meg a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [DataSource osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. Math névtér](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Az alkalmazás funkcióinak hozzáadásához tekintse meg a kód példáit:

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md)
