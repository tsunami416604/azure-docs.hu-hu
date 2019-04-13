---
title: Az Azure Maps alakzat hozzáadása |} A Microsoft Docs
description: Alakzat hozzáadása egy Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547985"
---
# <a name="add-a-shape-to-a-map"></a>Alakzat hozzáadása a térkép

Ez a cikk bemutatja, hogyan jelennek meg a térképen, vonal- és sokszög rétegek használatával geometriája. Az Azure Maps Web SDK is támogatja a kör geometriája létrehozását, ahogyan az a [GeoJSON kiterjesztett séma](extend-geojson.md#circle). Az összes funkció geometriája is könnyen frissíthető a beburkolt a [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztály.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Sorok hozzáadása a térkép

`LineString` és `MultiLineString` hamisításszűrési funkcióit használják, amelyek elérési utak és ismerteti a térképen.

### <a name="add-a-line"></a>Adjon hozzá egy sort

<iframe height='500' scrolling='no' title='Adjon hozzá egy sort egy térképre' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adjon hozzá egy sort egy térképhez</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objektum létrehozása és az adatforrás hozzá.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). A legutóbbi kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a vonalréteg létrehozásakor és a térkép belül a [eseménykezelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy a sor után teljes betölti a térkép jelenik meg.

### <a name="add-symbols-along-a-line"></a>Adjon hozzá egy vonal mentén szimbólumok

Ez a példa bemutatja, hogyan adhat hozzá egy vonal mentén nyíl ikon a térképen. Egy szimbólum réteg használatával állítsa be a "line" "elhelyezési" lehetőséget, ha ezzel a szimbólumokat a vonal mentén jelennek meg, és az ikonok elforgatása (0 fok = jobbra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vonal mentén nyíl megjelenítése" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show nyíl vonal mentén</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Ecsetvonás színátmenet hozzáadása egy sor

Nem csak a alkalmazni egy sort egy egyetlen körvonal színe is kitöltheti egy sor színskála megjelenítése a következő egy-egy vonal-szegmens való váltás. Például sor átmenetekhez használható változásait tartalmazzák az idő és a távolságot vagy különböző hőmérsékletek egy összekapcsolt vonal objektumok között. Annak érdekében, hogy a alkalmazni ezt a szolgáltatást egy sort, rendelkeznie kell az adatforrást a `lineMetrics` beállítást igaz értékre állítva, és majd átmenetes színkifejezés adható át a `strokeColor` a vonal beállítást. A körvonal átmenetes kifejezés eredménye a hivatkozás a `['line-progress']` adatokat kifejezés, amely elérhetővé teszi a számított sor metrikák kifejezésre.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A körvonal színátmenetes vonal" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>a körvonal színátmenetes vonal</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Egy vonalréteg testreszabása

A sor réteg számos stílusának beállítása. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='Vonalbeállítások réteg' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>rétegbeli kapcsolók</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>A térkép egy sokszög hozzáadása

`Polygon` és `MultiPolygon` szolgáltatások gyakran használják, amely a térkép egy területét jelöli. 

### <a name="use-a-polygon-layer"></a>Használjon egy sokszögréteg 

Egy sokszögréteg rendereli a sokszög területen. 

<iframe height='500' scrolling='no' title='Adjon hozzá egy sokszög térképre ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög felvétele a térképre </a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) létrehozott koordinátáinak gyűjteményét, és hozzáadja az adatforráshoz. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A legutóbbi kódblokkot hoz létre, és a egy sokszögréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a sokszögréteg létrehozásakor és a térkép belül a [eseménykezelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy megjelenik-e a sokszög, teljes mértékben betölti a térkép után.

### <a name="use-a-polygon-and-line-layer-together"></a>Sokszög- és réteg együttes használata

Egy vonalréteg használható a sokszög Vázlat megjelenítése. 

<iframe height='500' scrolling='no' title='Sokszög- és réteg sokszög hozzáadása' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>sokszög- és réteg hozzáadása a sokszög</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) létrehozott koordinátáinak gyűjteményét, és hozzáadja az adatforráshoz. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) sorok tömbje. Megtekintheti a tulajdonságait, egy sor réteg [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A harmadik kódblokkot sokszög- és vonalrétegek hoz létre.

A legutóbbi kódblokkot a sokszög- és vonalrétegek ad hozzá a térképen. Az adatforrás és a Rétegek létrehozásakor és a térkép belül a [eseménykezelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy megjelenik-e a sokszög, teljes mértékben betölti a térkép után.

> [!TIP]
> Sor rétegek alapértelmezés szerint a koordináták a sokszög, valamint egy adatforrásban lévő sorok jelenik meg. A réteg korlátozása úgy, hogy csak vártak LineString szolgáltatások beállítása az `filter` tulajdonság a réteg `['==', ['geometry-type'], 'LineString']` vagy `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` Ha fel szeretne venni MultiLineString szolgáltatásokhoz is.

### <a name="fill-a-polygon-with-a-pattern"></a>Töltse ki a sokszög egy mintával

Sokszög kitöltés színe mellett egy kép minta is használható. Egy rendszerkép minta betöltheti a maps képerőforrások sprite, és ezután hivatkozhat a rendszerképet a `fillPattern` a sokszögréteg tulajdonságát.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési minta" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>sokszög kitöltési minta</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Egy sokszögréteg testreszabása

A sokszögréteg csak néhány stílusának lehetőség van. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adjon hozzá egy kört a térkép

A GeoJSON-séma által biztosított körök feljegyzett definícióját egy kiterjesztett verzióját használja, az Azure Maps [Itt](extend-geojson.md#circle). Kör jeleníthetők meg a térképen létrehozása egy `Point` funkció, amely rendelkezik egy `subType` tulajdonság értéke az `"Circle"` és a egy `radius` tulajdonságot, amely rendelkezik egy szám, amely a radius méterben jelöli. Példa:

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

Az Azure Maps Web SDK alakítja át ezeket `Pooint` funkciók be `Polygon` funkcióit, a háttérben, és a térképen sokszög- és vonalrétegek használatával itt látható módon jeleníthetők meg.

<iframe height='500' scrolling='no' title='Adjon hozzá egy kört térképre' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adjon hozzá egy kört térképre</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. Kör van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) , és egy `subType` tulajdonság `"Circle"` és a egy `radius` méterben tulajdonság értéke. Ha pont szolgáltatás egy `subType` , `"Circle"` adnak hozzá egy adatforrást, a térkép belül körkörös sokszög konvertálva.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A legutóbbi kódblokkot hoz létre, és a egy sokszögréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a sokszögréteg létrehozásakor és a térkép belül a [eseménykezelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy a kör megjelenik-e után teljes betölti a térképen.

## <a name="make-a-geometry-easy-to-update"></a>Győződjön meg arról, egy geometriai könnyen lehet frissíteni

A `Shape` burkolja osztály egy [geometriai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) és megkönnyíti a frissítendő és karbantartandó őket.
`new Shape(data: Feature<data.Geometry, any>)` egy alakzat objektumot hoz létre, és végül inicializálja a megadott szolgáltatással.

<br/>

<iframe height='500' scrolling='no' title='Alakzat tulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>alakzat tulajdonságainak frissítése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A pont egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) az osztály. A második kódblokkot inicializálja a csúszka HTML-elem radius értékét, és hoz majd létre, és egy pont objektum burkolja egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályobjektumban.

A harmadik kódblokk létrehoz egy függvényt, amely a HTML-tartomány csúszka elem értéket veszi fel, és módosítja a radius-értékét az alakzat osztály használatával [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metódust.

A negyedik kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A pont adatforráshoz kerül.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A harmadik kódblokkot egy sokszögréteg hoz létre. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás, a kattintson eseménykezelő és a sokszögréteg létrehoz és a térkép belül hozzáadja a [eseménykezelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) annak érdekében, hogy a pont után teljes betölti a térkép jelenik meg.

## <a name="next-steps"></a>További lépések

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Az adatvezérelt stílus kifejezések használata](data-driven-style-expressions-web-sdk.md)
