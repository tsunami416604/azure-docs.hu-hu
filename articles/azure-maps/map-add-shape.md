---
title: Alakzat hozzáadása Azure Maps | Microsoft Docs
description: Alakzat hozzáadása JavaScript-térképhez
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0696eba4f3cca7beedc2efcda0182ab82b3d69d9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638697"
---
# <a name="add-a-shape-to-a-map"></a>Alakzat hozzáadása térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg a geometriákat a térképen vonal-és sokszög-rétegek használatával. A Azure Maps web SDK támogatja a kör alakú geometriák létrehozását is a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)definiált módon. Az összes funkció geometriája könnyen frissíthető, ha az [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályba van csomagolva.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Sorok hozzáadása a térképhez

`LineString`a `MultiLineString` és a funkciók segítségével a Térkép útvonalait és körvonalait jelölheti.

### <a name="add-a-line"></a>Vonal hozzáadása

<iframe height='500' scrolling='no' title='Vonal hozzáadása térképhez' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on lévő Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() <a href='https://codepen.io/azuremaps/pen/qomaKv/'>, és adjon hozzá egy sort</a> .
</iframe>

A fenti kódban szereplő kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában létrejön egy adatforrás-objektum az [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -osztály használatával. Létrejön egy [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objektum, amely hozzáadódik az adatforráshoz.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) az adatforrásba burkolt vonalas objektumokat jeleníti meg. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód utolsó blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Az adatforrást és a sor réteget a rendszer létrehozta és hozzáadja a térképhez az [eseménykezelőn](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) belül, így biztosítva, hogy a Térkép teljes betöltése után megjelenjen a sor.

### <a name="add-symbols-along-a-line"></a>Szimbólumok felvétele a vonal mentén

Ez a minta bemutatja, hogyan adhat hozzá nyíl ikonokat a Térkép egyik sorához. Ha szimbólum réteget használ, állítsa a "elhelyezés" lehetőséget a "line" értékre, ez a szimbólum a vonal mentén jelenik meg, és elforgatja az ikonokat (0 fok = jobbra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nyíl megjelenítése a vonal mentén" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat mutató nyilat a<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/drBJwX/'>vonal mentén</a> Azure Maps () alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a>Körvonal átmenetének hozzáadása sorba

Amellett, hogy egyetlen ecsetvonási színt is alkalmazhat egy sorba, egy színátmenetes vonalat is kitölthet az egyik vonalszakasz és a következő közötti átmenet megjelenítéséhez. A vonalak közötti átmenetek például az idő és a távolság, illetve a csatlakoztatott objektumok különböző hőmérsékletei közötti változások ábrázolására használhatók. Ahhoz, hogy a funkciót egy sorba lehessen alkalmazni, az adatforrásnak `lineMetrics` igaz értékűnek kell lennie, majd egy színátmenetes kifejezés is átadható `strokeColor` a sor kapcsolójának. A stroke Gradient kifejezésnek hivatkoznia kell `['line-progress']` arra az adatkifejezésre, amely a számított vonal metrikáit a kifejezésre teszi elérhetővé.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ecsetvonás-átmenetes vonal" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>- <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'></a> on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollvonási átmenetet.
</iframe>

### <a name="customize-a-line-layer"></a>Vonal rétegének testreszabása

A vonal rétegének több stílusa is van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Vonal rétegének beállításai' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>parancssori rétegbeli beállításait</a> a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Sokszög hozzáadása a térképhez

`Polygon`a `MultiPolygon` és a funkciók gyakran a Térkép területének ábrázolására használatosak. 

### <a name="use-a-polygon-layer"></a>Sokszög réteg használata 

A sokszögek rétege egy sokszög területét jeleníti meg. 

<iframe height='500' scrolling='no' title='Sokszög hozzáadása térképhez ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög hozzáadása</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában létrejön egy adatforrás-objektum az [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -osztály használatával. A [sokszögek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) egy tömbből jönnek létre, és hozzáadódnak az adatforráshoz. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) a térképen lévő adatforrásba burkolt [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) adatfeliratokat jeleníti meg. A kód utolsó blokkja létrehoz egy sokszög réteget a térképhez, és hozzáadja azt. Tekintse meg a sokszögek rétegének tulajdonságait a következő helyen: [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrást és a sokszög réteget a rendszer létrehozza és hozzáadja a térképhez [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) az eseménykezelőn belül annak érdekében, hogy a Térkép teljes terhelése után megjelenjen a sokszög.

### <a name="use-a-polygon-and-line-layer-together"></a>Sokszög és vonal rétegek együttes használata

A sokszög körvonalainak megjelenítéséhez egy vonal réteget lehet használni. 

<iframe height='500' scrolling='no' title='Sokszög és vonal réteg a sokszög hozzáadásához' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával vegye fel a sokszöget a toll <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>és a vonal rétegben</a> .
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában létrejön egy adatforrás-objektum az [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -osztály használatával. A [sokszögek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) egy tömbből jönnek létre, és hozzáadódnak az adatforráshoz. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) a térképen lévő adatforrásba burkolt [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) adatfeliratokat jeleníti meg. Tekintse meg a sokszögek rétegének tulajdonságait a következő helyen: [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vonalak tömbje. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A kód harmadik blokkja a sokszög és a vonal réteget hozza létre.

A kód utolsó blokkja hozzáadja a sokszög és a vonal réteget a térképhez. Az adatforrást és a rétegeket a rendszer létrehozta és hozzáadja a [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) térképhez az eseménykezelőn belül annak érdekében, hogy a Térkép teljes terhelése után megjelenjen a sokszög.

> [!TIP]
> A sorok alapértelmezés szerint a sokszögek koordinátáit, valamint egy adatforrás sorait jelenítik meg. Ha korlátozni szeretné a réteget úgy, hogy az csak az LineString funkciókat `filter` jelenítse meg, állítsa `['==', ['geometry-type'], 'LineString']` be `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` a réteg tulajdonságát a (vagy) értékre, ha MultiLineString funkciókat is szeretne használni.

### <a name="fill-a-polygon-with-a-pattern"></a>Sokszög kitöltése mintázattal

A sokszögek színnel való kitöltése mellett a képmintázat is használható. Helyezzen be egy képmintázatot a Maps-rendszerkép sprite-erőforrásaiba, majd hivatkozzon `fillPattern` erre a képre a sokszög rétegének tulajdonságával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési mintája" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll sokszögének kitöltésére szolgáló <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>mintát</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Sokszög-réteg testreszabása

A sokszög rétegnek csak néhány stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/LXvxpg/'></a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által LXvxpg tollat a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely az [itt](extend-geojson.md#circle)feljegyzett körök definícióját adja meg. Egy kör megjeleníthető a térképen egy `Point` olyan szolgáltatás létrehozásával, amely egy értékkel rendelkező `subType` `"Circle"` tulajdonságot tartalmaz, valamint egy `radius` olyan tulajdonságot, amely egy szám, amely a sugarat jelöli a mérőórákban. Példa:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

A Azure Maps web SDK ezeket `Point` a `Polygon` funkciókat a borítók alatt lévő funkciókba konvertálja, és a térképen a sokszög és a vonal rétegek használatával jeleníthető meg, ahogy az itt látható.

<iframe height='500' scrolling='no' title='Kör hozzáadása térképhez' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on lévő Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>, és adjon hozzá egy kört egy térképhez</a> .
</iframe>

A fenti kódban szereplő kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában létrejön egy adatforrás-objektum az [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -osztály használatával. A kör a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) egyik `subType` [funkciója](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , és `"Circle"` `radius` a tulajdonság értéke méter. Ha egy adatforráshoz hozzáad `subType` egy `"Circle"` pont-szolgáltatást, a rendszer egy körkörös sokszögbe konvertálja azt a térképen belül.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) a térképen lévő adatforrásba burkolt [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) adatfeliratokat jeleníti meg. A kód utolsó blokkja létrehoz egy sokszög réteget a térképhez, és hozzáadja azt. Tekintse meg a sokszögek rétegének tulajdonságait a következő helyen: [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrást és a sokszög réteget a rendszer létrehozza és hozzáadja a térképhez [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) az eseménykezelőn belül annak érdekében, hogy a kör a Térkép teljes betöltése után megjelenjen.

## <a name="make-a-geometry-easy-to-update"></a>Geometria egyszerű frissítése

Egy `Shape` osztály egy geometriát [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [szolgáltatást](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) csomagol, és megkönnyíti a frissítését és karbantartását.
`new Shape(data: Feature<data.Geometry, any>)`létrehozza az alakzat objektumát, és inicializálja azt a megadott szolgáltatással.

<br/>

<iframe height='500' scrolling='no' title='Alakzat tulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>frissítési alakzatának tulajdonságait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

Az a [pont az osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) egyik [funkciója](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) . A kód második blokkja inicializálja a HTML-csúszka elem RADIUS-értékét, majd egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztály objektumban rajzolja és csomagolja ki a pont objektumot.

A harmadik kód blokk olyan függvényt hoz létre, amely a HTML-tartomány csúszka elemének értékét veszi át, és a sugár értékét a SHAPE osztály [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metódusával módosítja.

A negyedik blokkban az adatforrás-objektum az [adatforrás osztály használatával](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jön létre. A rendszer Ezután hozzáadja a pontot az adatforráshoz.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) a térképen lévő adatforrásba burkolt [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) adatfeliratokat jeleníti meg. A kód harmadik blokkja egy sokszög réteget hoz létre. Tekintse meg a sokszögek rétegének tulajdonságait a következő helyen: [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrást, a Click eseménykezelőt és a sokszög réteget hozza létre és adja hozzá a térképhez az [eseménykezelőn](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) belül, hogy a pont megjelenjen a teljes terhelés után.

## <a name="next-steps"></a>További lépések

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)
