---
title: Fürtözési pont adatai a térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan fürtözheti a pontadatokat, és hogyan jelenítheti meg azokat a Microsoft Azure Maps Web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804571"
---
# <a name="clustering-point-data"></a>Fürtözési pont adatai

A térkép en számos adatpontjának megjelenítésekor az adatpontok átfedhetik egymást. Az átfedés miatt a térkép olvashatatlanná és nehezen használhatóvá válhat. A fürtözési pont adatai az egymáshoz közeli pontadatok egyesítésének és a térképen egyetlen fürtözött adatpontként való megjelenítésének folyamata. Ahogy a felhasználó ráközelít a térképre, a fürtök szétválnak az egyes adatpontjaikra. Ha nagy számú adatponttal dolgozik, használja a fürtözési folyamatokat a felhasználói élmény javítása érdekében.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Fürtözés engedélyezése adatforráson

Engedélyezze a `DataSource` fürtözést `cluster` az osztályban úgy, hogy a beállítást igaz értékre állítja. Állítsa `ClusterRadius` be a közeli pontok kijelöléséhez, és fürtbe egyesítve azokat. Az érték `ClusterRadius` képpontban van. Itt `clusterMaxZoom` adhatja meg azt a nagyítási szintet, amelyen letilthatja a fürtözési logikát. Íme egy példa arra, hogyan engedélyezheti a fürtözést egy adatforrásban.

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
> Ha két adatpont közel van egymáshoz a földön, lehetséges, hogy a fürt soha nem szakad szét, függetlenül attól, hogy milyen közel van a felhasználó nagyítása. Ennek megoldásához beállíthatja `clusterMaxZoom` a fürtözési logika letiltásának lehetőségét, és egyszerűen megjeleníthet mindent.

Az osztály által `DataSource` a fürtözéshez biztosított további módszerek:

| Módszer | Visszatérés típusa | Leírás |
|--------|-------------|-------------|
| getClusterChildren(clusterId: szám) | Ígéret&lt;&lt;tömbjellemző&lt;geometriája, bármilyen&gt; \| alakzat&gt;&gt; | Az adott fürt gyermekeinek lekérése a következő nagyítási szinten. Ezek a gyerekek lehetnek a formák és alklaszterek kombinációja. Az alfürtök fürtözött tulajdonságokkal rendelkező szolgáltatások lesznek. |
| getClusterExpansionZoom(clusterId: szám) | Ígéret&lt;száma&gt; | Kiszámítja azt a nagyítási szintet, amelyen a fürt kitágul vagy széttörik. |
| getClusterLeaves(clusterId: szám, limit: szám, eltolás: szám) | Ígéret&lt;&lt;tömbjellemző&lt;geometriája, bármilyen&gt; \| alakzat&gt;&gt; | A fürt összes pontjának beolvasása. Állítsa `limit` be a pontok egy részhalmazának `offset` visszaadására, és használja a lapozáshoz a pontokat. |

## <a name="display-clusters-using-a-bubble-layer"></a>Fürtök megjelenítése buborékréteg használatával

A buborékréteg nagyszerű módja a csoportosított pontok megjelenítésének. A kifejezések segítségével méretezheti a sugarat, és módosíthatja a színt a fürtben lévő pontok száma alapján. Ha a fürtöket buborékréteggel jeleníti meg, akkor külön réteget kell használnia a nem fürtözött adatpontok megjelenítéséhez.

Ha meg szeretné jeleníteni a fürt méretét a buborék tetején, használjon szöveget tartalmazó szimbólumréteget, és ne használjon ikont.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Egyszerű buborékréteg-fürtözés" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic buborékréteg fürtözését</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Fürtök megjelenítése szimbólumréteg használatával

Az adatpontok megjelenítésekor a szimbólumréteg automatikusan elrejti az egymást átfedő szimbólumokat, hogy tisztább felhasználói felületet biztosítson. Ez az alapértelmezett viselkedés nem kívánatos, ha meg szeretné jelenlennie az adatpontok sűrűsége a térképen. Ezek a beállítások azonban módosíthatók. Az összes szimbólum megjelenítéséhez `allowOverlap` állítsa a `iconOptions` Szimbólumrétegek `true`tulajdonság beállítását a parancsra. 

A fürtözés segítségével megjelenítheti az adatpontok sűrűségét, miközben tiszta felhasználói felületet tart fenn. Az alábbi minta bemutatja, hogyan adhat hozzá egyéni szimbólumokat, és hogyan jelölheti a fürtöket és az egyes adatpontokat a szimbólumréteg használatával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürtözött szimbólumréteg" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Fürtözött szimbólum réteget</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Klaszterek és a hőtérképek réteg

A hőtérképek nagyszerű módja annak, hogy megjelenítse az adatok sűrűségét a térképen. Ez a vizualizációs módszer önmagában nagy számú adatpontot képes kezelni. Ha az adatpontok csoportosítva vannak, és a fürtméretet használja a hőtérkép súlyaként, akkor a hőtérkép még több adatot képes kezelni. Ennek a beállításnak `weight` az eléréséhez állítsa `['get', 'point_count']`be a hőtérkép-réteg beállítását a értékre. Ha a fürt sugara kicsi, a hőtérkép majdnem megegyezik a nem fürtözött adatpontokat használó hőtérképpel, de sokkal jobban fog teljesíteni. Azonban minél kisebb a fürt sugara, annál pontosabb lesz a hőtérkép, de kevesebb teljesítményelőnnyel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt súlyozott hőtérképe" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Tollfürt súlyozott hőtérképét</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) szerint a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Egéresemények csoportosított adatpontokon

Ha egéresemények lépnek fel fürtözött adatpontokat tartalmazó rétegen, a fürtözött adatpont GeoJSON-pont szolgáltatásobjektumként tér vissza az eseményhez. Ez a pontszolgáltatás a következő tulajdonságokkal rendelkezik:

| Tulajdonság neve             | Típus    | Leírás   |
|---------------------------|---------|---------------|
| `cluster`                 | logikai | Azt jelzi, hogy a szolgáltatás fürtöt jelöl-e. |
| `cluster_id`              | sztring  | A fürt egyedi azonosítója, amely használható a `getClusterExpansionZoom` `getClusterChildren`DataSource `getClusterLeaves` , és metódusok. |
| `point_count`             | szám  | A fürt által tartalmazott pontok száma.  |
| `point_count_abbreviated` | sztring  | Olyan karakterlánc, amely rövidíti `point_count` az értéket, ha hosszú. (például 4000 lesz 4K)  |

Ez a példa egy buborékréteget vesz igénybe, amely fürtpontokat jelenít meg, és kattintási eseményt ad hozzá. Amikor a kattintási esemény eseményindítók, a kód kiszámítja és nagyítja a térképet a következő nagyítási szintre, amelyen a fürt szétesik. Ez a funkció az `getClusterExpansionZoom` `DataSource` osztály metódusával `cluster_id` és a kattintott fürtözött adatpont tulajdonságával valósítja meg.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="display-cluster-area"></a>Fürtterület megjelenítése 

A fürt által képviselt pontadatok egy területen vannak elosztva. Ebben a mintában, amikor az egér egy fürt fölé kerül, két fő viselkedés fordul elő. Először is, a klaszterben található egyedi adatpontokat a konvex hajótest kiszámítására használják. Ezután a konvex hajótest jelenik meg a térképen, hogy megjelenjen egy terület.  A konvex hajótest egy sokszög, amely körülöleli egy sor pontot, mint egy rugalmas sáv, és lehet számítani a `atlas.math.getConvexHull` módszerrel. A fürtben lévő összes pont a `getClusterLeaves` metódus sal beolvasható az adatforrásból.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Klaszter terület konvex hajótest" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>Tollfürt terület konvex hajótest</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Adatok összesítése fürtökben

A fürtöket gyakran egy szimbólum mal jelölik, amely a fürtön belüli pontok számát használja. De néha kívánatos, hogy testre szabhatja a fürtök stílusát további mérőszámokkal. A fürtösszesítések segítségével egyéni tulajdonságok hozhatók létre és tölthetők fel [egy összesített kifejezésszámítással.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  A fürtaggregátumok `clusterProperties` a `DataSource`beállításában határozhatók meg.

A következő minta egy összesítő kifejezést használ. A kód kiszámítja a fürt egyes adatpontjainak entitástípus-tulajdonsága alapján a számlálót. Amikor a felhasználó egy fürtre kattint, egy előugró ablak jelenik meg a fürtről további információkkal.

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürtaggregátumok" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>Tollfürt összesítéseit</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [DataSource osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math névtér](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Tekintse meg a kódpéldákat, hogy funkciókat adjon az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Hőtérképréteg hozzáadása](map-add-heat-map-layer.md)
