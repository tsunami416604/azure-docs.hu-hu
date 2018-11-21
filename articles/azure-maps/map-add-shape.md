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
ms.openlocfilehash: 505514d836c7b3244464ac53d268cc346a9321ad
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263334"
---
# <a name="add-a-shape-to-a-map"></a>Alakzat hozzáadása a térkép

Ez a cikk bemutatja, hogyan adhat hozzá egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) egy meglévő alakzatot a térképen térkép és a frissítés tulajdonságait.

<a id="addALine"></a>

## <a name="add-a-line"></a>Adjon hozzá egy sort

<iframe height='500' scrolling='no' title='Adjon hozzá egy sort egy térképre' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adjon hozzá egy sort egy térképhez</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. Egy sor egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) LineString. Ezután hoz létre, és a sor burkolja az [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályú objektum használatával `new atlas.Shape(new atlas.data.Feature((new atlas.data.LineString()))` , és hozzáadja az adatforrást.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) rendereket sor objektumok csomagolni a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). A legutóbbi kódblokkot hoz létre, és a egy vonalréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, egy sor réteg [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a vonalréteg létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a sor után teljes betölti a térkép jelenik meg.

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adjon hozzá egy kört

<iframe height='500' scrolling='no' title='Adjon hozzá egy kört térképre' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adjon hozzá egy kört térképre</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. Kör van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Ezután hoz létre, és a kör burkolja az [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályú objektum használatával `new atlas.Shape(new atlas.data.Feature(new atlas.data.Point()))` , és hozzáadja az adatforrást.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A legutóbbi kódblokkot hoz létre, és a egy sokszögréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a sokszögréteg létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvény használatával győződjön meg arról, hogy a kör megjelenik-e után teljes betölti a térképen.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adjon hozzá egy sokszög

A térkép is hozzáadhat egy sokszög két különböző módja van. Mindkét mutatjuk be az alábbi példák.

### <a name="use-polygon-layer"></a>Sokszögréteg használata 

<iframe height='500' scrolling='no' title='Adjon hozzá egy sokszög térképre ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög felvétele a térképre </a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , a [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) osztály. `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` Hozzon létre egy sokszög rendezett szolgál koordináták burkolt be, egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályobjektumban. Az alakzat objektum kerül az adatforráshoz.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A legutóbbi kódblokkot hoz létre, és a egy sokszögréteg hozzáadása a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás és a sokszögréteg létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy megjelenik-e a sokszög, teljes mértékben betölti a térkép után.

### <a name="use-polygon-and-line-layer"></a>Sokszög- és réteg használata

<iframe height='500' scrolling='no' title='Sokszög- és réteg sokszög hozzáadása' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>sokszög- és réteg hozzáadása a sokszög</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest). `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` Hozzon létre egy sokszög rendezett szolgál koordináták burkolt be, egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályobjektumban. Az alakzat objektum kerül az adatforráshoz.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) sorok tömbje. Megtekintheti a tulajdonságait, egy sor réteg [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest). A harmadik kódblokkot sokszög- és vonalrétegek hoz létre.

A legutóbbi kódblokkot a sokszög- és vonalrétegek ad hozzá a térképen. Az adatforrás és a Rétegek létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy megjelenik-e a sokszög, teljes mértékben betölti a térkép után.

## <a name="update-a-shape"></a>Egy alakzat frissítése

Egy alakzat osztály becsomagolja egy [geometriai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) vagy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) és megkönnyíti a frissítendő és karbantartandó őket.
`new Shape(data: Feature<data.Geometry, any>)` egy alakzat objektumot hoz létre, és végül inicializálja a megadott szolgáltatással.

<br>

<iframe height='500' scrolling='no' title='Alakzat tulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>alakzat tulajdonságainak frissítése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A pont egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) az osztály. A második kódblokkot inicializálja a csúszka HTML-elem radius értékét, és hoz majd létre, és egy pont objektum burkolja egy [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályobjektumban.

A harmadik kódblokk létrehoz egy függvényt, amely a HTML-tartomány csúszka elem értéket veszi fel, és módosítja a radius-értékét az alakzat osztály használatával [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) metódust.

A negyedik kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. A pont adatforráshoz kerül.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) csomagolni adatok rendereli a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a térképen. A harmadik kódblokkot egy sokszögréteg hoz létre. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Az adatforrás az kattintson eseményfigyelő és a sokszögréteg létrehoz és a térkép belül hozzáadja a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a pont után teljes betölti a térkép jelenik meg.

## <a name="next-steps"></a>További lépések

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Egyéni HTML hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Keresési eredmények megjelenítése](./map-search-location.md)
