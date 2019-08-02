---
title: Buborék réteg hozzáadása a Azure Mapshoz | Microsoft Docs
description: Buborék réteg hozzáadása a JavaScript-térképhez
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639043"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Buborék réteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból egy Térkép buborék rétegének használatával. A buborék rétegek a térképbeli körökként jelenítik meg a rögzített képpont sugarú pontokat. 

> [!TIP]
> A buborék rétegek alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha korlátozni szeretné a réteget úgy, hogy az csak a pont geometriai `filter` funkcióit jelenítse meg `['==', ['geometry-type'], 'Point']` , `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` állítsa a réteg tulajdonságát a (vagy) értékre, ha a multipoint-szolgáltatásokat is szeretné használni.

## <a name="add-a-bubble-layer"></a>Buborékréteg hozzáadása

<iframe height='500' scrolling='no' title='BubbleLayer adatforrása' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer</a> adatforrását Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektumok tömbje van definiálva, és a rendszer hozzáadja az [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -objektumhoz.

A [buborék réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) az adatforrásban lévő, pont-alapú adatsorokat jeleníti meg körökként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód utolsó blokkja egy buborék réteget hoz létre, és hozzáadja azt a térképhez. Tekintse meg a buborék réteg tulajdonságait a következő helyen: [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A pont objektumok, az adatforrás és a buborék réteget a rendszer létrehozza és hozzáadja a térképhez az [esemény-figyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvényben annak érdekében, hogy a Térkép teljes betöltését követően megjelenjen a kör.

## <a name="show-labels-with-a-bubble-layer"></a>Címkék megjelenítése buborék réteggel

<iframe height='500' scrolling='no' title='Többrétegű adatforrás' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/rqbQXy/'></a> a toll többrétegű<a href='https://codepen.io/azuremaps'>@azuremaps</a>adatforrást Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód bemutatja, hogyan jelenítheti meg és címkézheti az adatleképezést. A fenti kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja egy [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektumot hoz létre. Ezután létrehoz egy adatforrás-objektumot az [adatforrás osztály használatával](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) , és hozzáadja a pontot az adatforráshoz.

A [buborék réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) az adatforrásban lévő, pont-alapú adatsorokat jeleníti meg körökként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód harmadik blokkja egy buborék réteget hoz létre, és hozzáadja azt a térképhez. Tekintse meg a buborék réteg tulajdonságait a következő helyen: [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az adatforrásban szimbólumként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód utolsó blokkja létrehoz és felhelyez egy szimbólum réteget a térképhez, amely megjeleníti a buborékhoz tartozó szöveg címkéjét. Tekintse meg a szimbólumok rétegének tulajdonságait a következő helyen: [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Az adatforrást és a rétegeket a rendszer létrehozza és hozzáadja a térképhez az [esemény-figyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvényben annak érdekében, hogy a Térkép teljes betöltését követően megjelenjen az adat.

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
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)