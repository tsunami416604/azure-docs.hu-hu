---
title: Szimbólum hozzáadása a Azure Mapshoz | Microsoft Docs
description: Szimbólumok hozzáadása a JavaScript-térképhez
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ba5d5d3aaa6a83dbcc5e5072872bca0fcd22bbf9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638720"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Szimbólum réteg hozzáadása térképhez

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból a Térkép szimbólum rétegének használatával. A szimbólumok rétegei a WebGL használatával jelennek meg, és sokkal nagyobb számú pontot támogatnak, mint a HTML-jelölők, de nem támogatják a hagyományos CSS-és HTML-elemeket a stílushoz.  

> [!TIP]
> A szimbólumok alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha korlátozni szeretné a réteget úgy, hogy az csak a pont geometriai `filter` funkcióit jelenítse meg `['==', ['geometry-type'], 'Point']` , `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` állítsa a réteg tulajdonságát a (vagy) értékre, ha a multipoint-szolgáltatásokat is szeretné használni.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

<iframe height='500' scrolling='no' title='PIN-kód helyének váltása' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>switch PIN-kód helyét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkjában létrejön egy adatforrás-objektum az [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -osztály használatával. A [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometriáját tartalmazó [funkció] a [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztályba van csomagolva, hogy könnyebb legyen a frissítés, majd az adatforrás létrehozása és hozzáadása.

A kód harmadik blokkja egy [esemény](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) -figyelőt hoz létre, és a pont koordinátáit az egér gombra kattintva a SHAPE Class [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metódus használatával frissíti.

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az adatforrásban szimbólumként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)  Az adatforrást, a Click Event Listener (esemény-figyelő) és a szimbólum réteget hozza létre és adja hozzá `ready` a térképhez az [Event Listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvényben, hogy a pont megjelenjen a betöltés után, és készen álljon a hozzáférésre.

> [!TIP]
> A teljesítmény érdekében a szimbólum-rétegek alapértelmezés szerint az átfedésben lévő szimbólumok elrejtésével optimalizálja a szimbólumok megjelenítését. Ahogy a rejtett szimbólumok nagyítása láthatóvá válik. A funkció letiltásához és az összes szimbólum megjelenítéséhez állítsa be a `allowOverlap` `iconOptions` beállítások `true`tulajdonságát a következőre:.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egyéni ikon hozzáadása egy szimbólum réteghez

A szimbólumok rétegei a WebGL használatával jelennek meg. Ennek megfelelően az összes erőforrást, például ikonokat be kell tölteni a WebGL-környezetbe. Ez a minta bemutatja, hogyan adhat hozzá egyéni ikont a Térkép erőforrásaihoz, majd használhatja azt a Térkép egyéni szimbólumának megjelenítéséhez. A szimbólum réteg tulajdonságának meg kell adni egy kifejezést. `textField` Ebben az esetben a hőmérséklet tulajdonságot szeretnénk megjeleníteni, de mivel ez egy szám, karakterlánccá kell alakítani. Emellett szeretnénk hozzáfűzni a "°F"-t is. Ezt a kifejezést használhatja; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Egyéni szimbólum képe ikon' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Egyéni szimbólumának</a> ikonját<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Szimbólum réteg testreszabása 

A szimbólum rétegben számos lehetőség áll rendelkezésre. Az alábbi eszköz segítségével tesztelheti ezeket a különböző stílusokat.

<br/>

<iframe height='700' scrolling='no' title='Szimbólum réteg beállításai' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll szimbólumának rétegbeli<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/PxVXje/'>beállításait</a> Azure Maps () alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Felugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)
