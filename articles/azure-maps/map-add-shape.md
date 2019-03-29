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
ms.openlocfilehash: 2582b277238bbfbda29156c857e7bd91cf6fe059
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579291"
---
# <a name="add-a-shape-to-a-map"></a>Alakzat hozzáadása a térkép

Ez a cikk bemutatja, hogyan adhat hozzá egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) egy meglévő alakzatot a térképen térkép és a frissítés tulajdonságait.

<a id="addALine"></a>

## <a name="add-a-line"></a>Adjon hozzá egy sort

<iframe height='500' scrolling='no' title='Adjon hozzá egy sort egy térképre' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adjon hozzá egy sort egy térképhez</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objektum létrehozása és az adatforrás hozzá.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). A legutóbbi kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a vonalréteg létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a sor után teljes betölti a térkép jelenik meg.

## <a name="add-symbols-along-a-line"></a>Adjon hozzá egy vonal mentén szimbólumok

Ez a példa bemutatja, hogyan adhat hozzá egy vonal mentén nyíl ikon a térképen. Egy szimbólum réteg használatával állítsa be a "line" "elhelyezési" lehetőséget, ha ezzel a szimbólumokat a vonal mentén jelennek meg, és az ikonok elforgatása (0 fok = jobbra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vonal mentén nyíl megjelenítése" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show nyíl vonal mentén</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Egy vonalréteg testreszabása

A sor réteg számos stílusának beállítása. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='Vonalbeállítások réteg' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>rétegbeli kapcsolók</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adjon hozzá egy kört

<iframe height='500' scrolling='no' title='Adjon hozzá egy kört térképre' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adjon hozzá egy kört térképre</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. Kör van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) , és egy `subType` tulajdonsága "kör" és a egy `radius` tulajdonság értéke mérőszámok. Kör altípusa pont szolgáltatás adatforrásként való felvételekor, átalakítja a térkép belül körkörös sokszög.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A legutóbbi kódblokkot hoz létre, és a egy sokszögréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a sokszögréteg létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvény használatával győződjön meg arról, hogy a kör megjelenik-e után teljes betölti a térképen.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adjon hozzá egy sokszög

A térkép is hozzáadhat egy sokszög két különböző módja van. Mindkét mutatjuk be az alábbi példák.

### <a name="use-polygon-layer"></a>Sokszögréteg használata 

<iframe height='500' scrolling='no' title='Adjon hozzá egy sokszög térképre ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög felvétele a térképre </a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) létrehozott koordinátáinak gyűjteményét, és hozzáadja az adatforráshoz. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A legutóbbi kódblokkot hoz létre, és a egy sokszögréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a sokszögréteg létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy megjelenik-e a sokszög, teljes mértékben betölti a térkép után.

### <a name="use-polygon-and-line-layer"></a>Sokszög- és réteg használata

<iframe height='500' scrolling='no' title='Sokszög- és réteg sokszög hozzáadása' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>sokszög- és réteg hozzáadása a sokszög</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) létrehozott koordinátáinak gyűjteményét, és hozzáadja az adatforráshoz. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) sorok tömbje. Megtekintheti a tulajdonságait, egy sor réteg [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A harmadik kódblokkot sokszög- és vonalrétegek hoz létre.

A legutóbbi kódblokkot a sokszög- és vonalrétegek ad hozzá a térképen. Az adatforrás és a Rétegek létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy megjelenik-e a sokszög, teljes mértékben betölti a térkép után.

## <a name="fill-a-polygon-with-a-pattern"></a>Töltse ki a sokszög egy mintával

Sokszög kitöltés színe mellett egy kép minta is használható. Egy rendszerkép minta betöltheti a maps képerőforrások sprite, és ezután hivatkozhat a rendszerképet a `fillPattern` a sokszögréteg tulajdonságát.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési minta" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>sokszög kitöltési minta</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-layer"></a>Egy sokszögréteg testreszabása

A sokszögréteg csak néhány stílusának lehetőség van. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-a-shape"></a>Egy alakzat frissítése

Egy alakzat osztály becsomagolja egy [geometriai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) és megkönnyíti a frissítendő és karbantartandó őket.
`new Shape(data: Feature<data.Geometry, any>)` egy alakzat objektumot hoz létre, és végül inicializálja a megadott szolgáltatással.

<br/>

<iframe height='500' scrolling='no' title='Alakzat tulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>alakzat tulajdonságainak frissítése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A pont egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) az osztály. A második kódblokkot inicializálja a csúszka HTML-elem radius értékét, és hoz majd létre, és egy pont objektum burkolja egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályobjektumban.

A harmadik kódblokk létrehoz egy függvényt, amely a HTML-tartomány csúszka elem értéket veszi fel, és módosítja a radius-értékét az alakzat osztály használatával [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metódust.

A negyedik kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A pont adatforráshoz kerül.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A harmadik kódblokkot egy sokszögréteg hoz létre. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás az kattintson eseményfigyelő és a sokszögréteg létrehoz és a térkép belül hozzáadja a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a pont után teljes betölti a térkép jelenik meg.

## <a name="next-steps"></a>További lépések

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [HTML Markers](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Egy megadott hőtérképrészlet térképréteg hozzáadása](./map-add-heat-map-layer.md)
