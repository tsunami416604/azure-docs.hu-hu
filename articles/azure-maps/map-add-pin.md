---
title: A szimbólum réteg hozzáadása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép szimbólumok hozzáadása
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 718a679418790a6bf1207a96e5c204f7962de239
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411254"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Vegyen fel egy szimbólum réteget a térkép

Ez a cikk bemutatja, hogyan renderelni a pont adatokat egy adatforrásból egy szimbólum rétegként a térképen. Szimbólum rétegek vagy a Web GL vannak leképezve, és HTML jelölők, mint jóval több adatpont támogatja, de nem támogatja a hagyományos CSS- és HTML-elemek a Stílusszerkesztő.  

> [!TIP]
> Symbol rétegek alapértelmezés szerint egy adatforrásban lévő összes geometriája koordinátáit jelenik meg. A réteg korlátozása úgy, hogy a vártak csak pont geometriai szolgáltatások beállítása az `filter` tulajdonság a réteg `['==', '$type', 'Point']`

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

<iframe height='500' scrolling='no' title='Kapcsoló PIN-kód helyét' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>kapcsoló PIN-kód helyét</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. [A szolgáltatás], amely tartalmazza a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometriai van burkolt be, a [alakzat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) osztály könnyebb frissíteni, majd létrehozott és az adatforrás hozzá.

A harmadik kódblokkot létrehoz egy [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) és a frissítések, egér, a pont koordináták kattintson az alakzat osztállyal [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metódust.

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen.  Az adatforrás az kattintson eseményfigyelő és a szimbólum réteg létrehozása és belül a térképhez hozzáadni a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a pont után teljes betölti a térkép jelenik meg.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egy szimbólum réteget ad hozzá egy egyéni ikon

Symbol rétegek használatával vagy a Web GL vannak leképezve. Az ilyen összes erőforrások, például képek ikonra kell lennie tölti be a vagy a Web GL környezetet. Ez a minta bemutatja a térkép-erőforrások ad hozzá egy egyéni szimbólum ikon és, amellyel jelennek meg a térképen egy egyéni szimbólummal pont adatait. A `textField` a szimbólum réteg tulajdonság szükséges egy kifejezést lehet megadni. Ebben az esetben szeretnénk a hőmérséklet-tulajdonság a szöveges értéket a pont szolgáltatás jelennek meg. Ez érhető el a következő kifejezést: `['get', 'temperature']`. 

<br/>

<iframe height='500' scrolling='no' title='Egyéni szimbólum lemezkép ikon' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>egyéni szimbólum lemezkép ikon</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>A szimbólum réteg testreszabása 

A szimbólum réteg rendelkezésre álló számos stílusának lehetőséggel rendelkezik. Itt egy olyan eszköz teszteléséhez ezek különböző stílusának lehetőségeit.

<br/>

<iframe height='700' scrolling='no' title='Symbol réteg beállításai' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/PxVXje/'>réteg beállításai</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták a maps hozzá az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Egy előugró ablak hozzáadása](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Egy buborék réteg hozzáadása](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adja hozzá a HTML-számára](./map-add-bubble-layer.md)
