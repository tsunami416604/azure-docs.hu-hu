---
title: Az Azure Maps alakzat hozzáadása |} A Microsoft Docs
description: Alakzat hozzáadása egy Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6b78c7f5d7d1b5ad4db9401bf2138502c9bbd397
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121906"
---
# <a name="add-a-shape-to-a-map"></a>Alakzat hozzáadása a térkép

Ez a cikk bemutatja, hogyan adhat hozzá egy sort, a kör és a egy sokszög a térkép. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Adjon hozzá egy sort

<iframe height='500' scrolling='no' title='Adjon hozzá egy sort egy térképre' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adjon hozzá egy sort egy térképhez</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, az létrehoz egy sort. Egy sor egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , LineString, az a funkció tulajdonsága mint LineStringProperties. Használat `new atlas.data.Feature(new atlas.data.LineString())` hozzon létre egy sort, és adja meg a tulajdonságait.

Egy vonalréteg vonalak egy tömb. A legutóbbi kódblokkot használ [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) függvény a térkép osztály a vonalréteg hozzáadása a térképen, és a sor réteg tulajdonságainak definiálásához. Megtekintheti a tulajdonságait, egy sor réteg [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adjon hozzá egy kört

<iframe height='500' scrolling='no' title='Adjon hozzá egy kört térképre' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adjon hozzá egy kört térképre</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, a kör jön létre. Kör van egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circleproperties?view=azure-iot-typescript-latest) , a szolgáltatás tulajdonsága. Használat `new atlas.data.Feature(new atlas.data.Point())` kör létrehozása és a hozzá tartozó tulajdonságok meghatározása.

A kör réteg körök tömbjét. A legutóbbi kódblokkot használ [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcircles) függvény a térkép osztály vegyen fel a kör réteget a térkép és a kör réteg tulajdonságainak definiálásához. Lásd: a kör réteg tulajdonságainak [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adjon hozzá egy sokszög

<iframe height='500' scrolling='no' title='Adjon hozzá egy sokszög térképre ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög felvétele a térképre </a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, a sokszög jön létre. A poligon egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) a [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonproperties?view=azure-iot-typescript-latest) , a szolgáltatás tulajdonsága. Használat `new atlas.data.Feature(new atlas.data.Polygon())` sokszög létrehozni és meghatározni a tulajdonságait. Sokszög elérési útjában a sokszög konstruktor rendezett koordinátáit adja meg.

Egy sokszögréteg poligonok tömbjét. A legutóbbi kódblokkot használ [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpolygons) függvény a térkép osztály a sokszögréteg hozzáadása a térképen, és adja meg a tulajdonságait. Megtekintheti a tulajdonságait, polygon réteg [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>További lépések

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Egyéni HTML hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Keresési eredmények megjelenítése](./map-search-location.md)
