---
title: Pont adatait az Azure Maps-fürtszolgáltatás |} A Microsoft Docs
description: A Web SDK-t a fürt-pont adatainak
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580823"
---
# <a name="clustering-point-data"></a>Fürtszolgáltatás pont adatait

Amikor megjelenítik a térképen sok adatpont, pontok átfedésben vannak egymással, a térkép megjelenését zsúfolt, és nehezen megtekintéséhez és használatához. Fürtszolgáltatás pont adatok segítségével a felhasználói élmény javítása érdekében. Fürtszolgáltatás pont adatait egymás közelében lévő adatok pont összevonása és őket a térképen, mint egy fürtözött adatpont képviselő során a rendszer. A felhasználó be a térkép nagyítása, mivel a fürtök felosztása egymástól azok külön adatpontok.

## <a name="enabling-clustering-on-a-data-source"></a>Egy adatforrás a fürtözés engedélyezése

Fürtszolgáltatás egyszerűen engedélyezhető a `DataSource` beállításával osztály a `cluster` igaz értékre a beállítást. Emellett a fürt egyesítendő közeli kiválasztásához képpontos radius lze nastavit pomocí vlastností a `clusterRadius` és a nagyítási szint adhatók meg, amely letiltja a fürtözési logic használatával a `clusterMaxZoom` lehetőséget. Íme egy példa, hogyan lehet engedélyezni a fürtszolgáltatás egy adatforrásban.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Ha két adatpont egymáshoz a földön, lehetőség a fürt fog soha nem újratervezéssel, függetlenül attól, hogy zárja be a felhasználó közeledik. Ennek beállíthatja a `clusterMaxZoom` beállítás az adatforrás, amely meghatározza a nagyítási szinten tiltsa le a fürtözési logikát, és egyszerűen jelenítheti meg mindent.

A `DataSource` osztálya is rendelkezik az alábbi módszerek a fürtözött kapcsolódó:

| Módszer | Visszatérési típusa | Leírás |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;funkció&lt;Geometry, bármely&gt; \| alakzat&gt; | Olvassa be a következő nagyítási szintjét az adott fürt gyermekei. Ezek a gyermekek alakzatokat és subclusters kombinációja lehet. A subclusters lesz ClusteredProperties megfelelő tulajdonságok a funkciókat. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | A nagyítási szint, amelyen a fürt indítása kibontása vagy újratervezéssel fog számítja ki. |
| getClusterLeaves (clusterId: szám, korlát: number, eltolás: száma) | Promise&lt;funkció&lt;Geometry, bármely&gt; \| alakzat&gt; | Minden pont egy fürtben kérdezi le. Állítsa be a `limit` a pontok egy részének visszaadása, és a `offset` oldalra a ponton keresztül. |

## <a name="display-clusters-using-a-bubble-layer"></a>Megjeleníti a fürtöket egy buborék réteg használatával

Egy buborék réteg kiválóan alkalmas a könnyedén méretezheti a radius és azok alapján a fürt pontok száma kifejezés használatával színének módosítása a fürtözött pontok jelennek meg. Egy buborék réteg használatával fürtök megjelenítésekor is használjon egy külön réteg a kiszolgálónevek adatpontok megjelenítése. Legtöbbször jó is elérhetik a buborékok felül a fürt méretének megjelenítéséhez. A szöveg, és nincs ikon szimbólum réteg ezt a viselkedést eléréséhez használható. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürtszolgáltatás alapszintű buborék réteg" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>alapszintű buborék réteg Fürtszolgáltatás</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Megjeleníti a fürtöket egy szimbólum réteg használatával

Amikor vizualizációja a pont lesz automatikusan elrejtése alapértelmezés szerint a szimbólum réteg használatával szimbólumokat adott átfedik egymást tisztító környezetet biztosít, azonban nem lehet a kívánt környezet Ha meg szeretné tekinteni a sűrűségű adatok mutat a térképen. Beállítás a `allowOverlap` lehetőség a szimbólum rétegek `iconOptions` tulajdonságot `true` letiltja a felhasználói élményt, de éppen megjelenített összes szimbólumok eredményez. Fürtszolgáltatás használatával lehetővé teszi, hogy tekintse meg az összes adat sűrűségét-ban a processzoron tiszta felhasználói élményt létrehozása során. Ebben a példában egyéni szimbólumok a fürtök és az egyes adatpontok használható.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürtözött szimbólum réteg" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>fürtözött szimbólum réteg</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Fürtszolgáltatás és a megadott hőtérképrészlet leképezi a réteg

HEAT maps nagyszerű módját a sűrűségű adatok megjelenítéséhez a térképen. Ezt a vizualizációt képes kezelni a nagy számú adatpont önállóan, de még több adatot tud kezelni, ha az adatpont fürtözöttek és hőtérkép súlyozást a fürtméret szolgál. Állítsa be a `weight` lehetőség a heat map réteg `['get', 'point_count']` ennek eléréséhez. Ha a fürt radius alacsony, hőtérkép majdnem megegyezik a egy kiszolgálóé adatponttal használatával hőtérkép fog kinézni, de sokkal jobban hajtsa végre. Azonban minél kisebb fürt radius, a pontosabb hőtérkép lesz, de kevesebb a teljesítmény előnyeit.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt súlyozott Hőtérkép" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>fürt súlyozott Hőtérkép</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>A fürtözött adatok pontokon egér-események

Egér-események előfordulásakor egy rétegen fürtözött adatok pontokat tartalmazó fürtözött adatpont küldi vissza a rendszer az eseményre egy GeoJSON-pont funkció objektumot. A pont funkció az alábbi tulajdonságokkal fog rendelkezni:

| Tulajdonság neve | Typo | Leírás |
|---------------|------|-------------|
| fürt | logikai | Azt jelzi, ha a szolgáltatás egy fürt jelöli. |
| cluster_id | sztring | Egy egyedi Azonosítót a fürt, amely használható a DataSource `getClusterExpansionZoom`, `getClusterChildren`, és `getClusterLeaves` módszereket. |
| point_count | szám | A fürt tartalmaz pontok száma. |
| point_count_abbreviated | sztring | Egy karakterlánc, amely lerövidíti a point_count értéket, ha hosszú. (például 4000 lesz 4 KB-os) |

Ebben a példában egy buborék réteg jelenik meg a fürt pontok, és hozzáad egy kattintással eseményt vesz igénybe, amely bárkinek, kiszámítása, és a térkép zoom nagyítás szintre, ahol a fürt közötti használatával megszakítja a `getClusterExpansionZoom` módszere a `DataSource` osztály és a `cluster_id` tulajdonsága az való kattintás fürtözött adatpont. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fürt getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/moZWeV/'>fürt getClusterExpansionZoom</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Fürt terület megjelenítése 

A pont adatait, amely egy fürt jelöli egy adott területre oszlik. Ebben a példában az egér rámutatott keresztül a fürt, az egyedi adatokat a rendszer domború hajótest kiszámításához használt és a terület megjelenítése a térképen megjelenített pontok (levelek) tartalmaz. Minden pont egy fürtben lévő kérhető le a forrás-használ a `getClusterLeaves` metódust. Domború hajótest egy sokszög, amely egy halmaza, például egy rugalmas sávon, és segítségével az `atlas.math.getConvexHull` metódust.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Fürt terület domború hajótest" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>fürt terület domború hajótest</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Adatforrás-osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas.Math névtér](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Funkciók hozzáadása az alkalmazáshoz való kód példa látható:

> [!div class="nextstepaction"]
> [Egy buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Egy szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Egy megadott hőtérképrészlet térképréteg hozzáadása](map-add-heat-map-layer.md)
