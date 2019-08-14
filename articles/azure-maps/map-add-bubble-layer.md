---
title: Buborék réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Buborék réteg hozzáadása a Azure Maps web SDK-hoz.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5cc5dbdc89f629c09d47ef683b7ff7fff61d2f49
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976579"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Buborék réteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból egy Térkép buborék rétegének használatával. A buborék rétegek a térképbeli körökként jelenítik meg a rögzített képpont sugarú pontokat. 

> [!TIP]
> A buborék rétegek alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha korlátozni szeretné a réteget úgy, hogy az csak a pont geometriai `filter` funkcióit jelenítse meg `['==', ['geometry-type'], 'Point']` , `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` állítsa a réteg tulajdonságát a (vagy) értékre, ha a multipoint-szolgáltatásokat is szeretné használni.

## <a name="add-a-bubble-layer"></a>Buborékréteg hozzáadása

A következő kód egy tömböt tölt be egy adatforrásba, és csatlakoztatja egy [buborékdiagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)-réteghez. A buborék réteg az egyes buborékok sugarának megjelenítésére szolgál öt képpontban, a fehér szín kitöltési színe, a kék színű körvonal színe, valamint a 6 képpont vastagsága. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer adatforrása' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer</a> adatforrását Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Címkék megjelenítése buborék réteggel

A következő kód azt mutatja be, hogyan lehet egy buborék réteggel megjeleníteni egy pontot a térképen és egy szimbólum réteget a címke megjelenítéséhez. A szimbólum réteg ikonjának elrejtéséhez állítsa az `image` ikon `'none'`beállításainak tulajdonságát a következőre:.

<br/>

<iframe height='500' scrolling='no' title='Többrétegű adatforrás' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/rqbQXy/'></a> a toll többrétegű<a href='https://codepen.io/azuremaps'>@azuremaps</a>adatforrást Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Buborék-réteg testreszabása

A buborék rétegnek csak néhány stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Buborék réteg beállításai' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a tollas <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>buborék rétegének beállításait</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)