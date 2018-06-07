---
title: Az Azure-leképezések alakzat |} Microsoft Docs
description: A Javascript-leképezés egy alakzat hozzáadása
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: dec9b7289927365faa9c58522df2571db99f0494
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599371"
---
# <a name="add-a-shape-to-a-map"></a>Olyan térképet alakzat hozzáadása

Ez a cikk bemutatja, hogyan egy vonal, kör és sokszög hozzáadása a térképen. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Adja hozzá a sort

<iframe height='500' scrolling='no' title='Vonal felvétele térképre' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/qomaKv/'>szúrjon be hozzá egy sort a térkép</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot, az létrehoz egy sort. Egy sor olyan [szolgáltatás](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) , LineString, a szolgáltatás tulajdonságként LineStringProperties. Használjon `new atlas.data.Feature(new atlas.data.LineString())` hozzon létre egy sort, és a tulajdonságok meghatározásához. 

Egy sor réteg sorok tömbjét. Az utolsó kódblokkot használ [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) függvény a térkép osztály a sor réteg tulajdonságainak meghatározásához és a sor réteg hozzáadása a térképen. A tulajdonságok között, sor réteg [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Kör hozzáadása

<iframe height='500' scrolling='no' title='Kör felvétele a térképre' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>kör felvétele a térképre</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot, a kör jön létre. Kör van egy [szolgáltatás](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) a [pont](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) rendelkező [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) , a szolgáltatás tulajdonsága. Használjon `new atlas.data.Feature(new atlas.data.Point())` kör létrehozása, és a tulajdonságok meghatározásához.

A kör réteg körök tömbjét. Az utolsó kódblokkot használ [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) függvény a térkép osztály a kör réteg hozzáadása a térkép, illetve a kör réteg tulajdonságainak megadása. A tulajdonságok között egy kör réteg: [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>A sokszög hozzáadása
<iframe height='500' scrolling='no' title='A sokszög felvétele a térképre ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög felvétele a térképre </a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot, a sokszög jön létre. A poligon egy [szolgáltatás](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) a [sokszög](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) rendelkező [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) , a szolgáltatás tulajdonsága. Használjon `new atlas.data.Feature(new atlas.data.Polygon())` Sokszög létrehozásához, és adja meg a tulajdonságait. Adja meg a sokszög elérési útja a sokszög konstruktorban rendezett koordinátáját.

A sokszög réteg sokszögek tömbjét. Az utolsó kódblokkot használ [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) függvény a térkép osztály a sokszög réteg hozzáadása a térkép, és adja meg a tulajdonságait. A tulajdonságok a sokszög réteg [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>További lépések
További kód hozzáadása a maps, tekintse meg a következő cikkeket:
* [Adja hozzá az egyéni HTML](./map-add-custom-html.md)
* [Keresési eredmények megjelenítése](./map-search-location.md)


