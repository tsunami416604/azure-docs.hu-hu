---
title: A buborék réteg hozzáadása az Azure Maps |} A Microsoft Docs
description: A Javascript-térkép buborékméret réteg hozzáadása
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281488"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Vegyen fel egy buborék réteget a térkép

Ez a cikk bemutatja, hogyan renderelni a pont adatokat egy adatforrásból egy buborék a térképen rétegként. Buborékdiagram rétegek pontok rögzített képpontos radius-szal a térképen a körök jelennek meg. 

> [!TIP]
> Alapértelmezés szerint a buborék rétegek egy adatforrásban lévő összes geometriája koordinátáit jelenik meg. A réteg korlátozása úgy, hogy a vártak csak pont geometriai szolgáltatások beállítása az `filter` tulajdonság a réteg `['==', ['geometry-type'], 'Point']` vagy `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Ha fel szeretne venni, valamint a MultiPoint szolgáltatások.

## <a name="add-a-bubble-layer"></a>Buborékréteg hozzáadása

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, tömbjét a [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektumok van definiálva, és hozzáadja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektum.

A [buborék réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) csomagolni szoftverfrissítésipont-alapú adatok rendereli a [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) , a térképen kör alakú. A legutóbbi kódblokkot egy buborék réteget hoz létre, és hozzáadja azt a térképen. Tekintse meg a buborék réteg tulajdonságainak [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Pont objektumok tömbje, az adatforrás és a buborékok réteg létrehozása és belül a térképhez hozzáadni a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a kör jelenik meg a térkép rendelkezik teljes betöltése után.

## <a name="show-labels-with-a-bubble-layer"></a>Egy buborék réteggel feliratok megjelenítése

<iframe height='500' scrolling='no' title='Többrétegű adatforrás' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>többrétegű DataSource</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód bemutatja, hogyan jelenítheti meg és címke adatait a térképen. A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, létrehoz egy [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektum. Ezután létrehoz egy objektum használhatja a [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztályt, és a pontot ad hozzá az adatforrást.

A [buborék réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) csomagolni szoftverfrissítésipont-alapú adatok rendereli a [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) , a térképen kör alakú. A harmadik kódblokkot egy buborék réteget hoz létre, és hozzáadja azt a térképen. Tekintse meg a buborék réteg tulajdonságainak [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. A legutóbbi kódblokkot hoz létre, és hozzáadja egy szimbólum réteg jeleníti meg az a szöveg címkéje a buborék a térképen. Tekintse meg a szimbólum réteg tulajdonságainak [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Az adatforrás és a Rétegek létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy az adatok a térkép rendelkezik teljes betöltése után megjelenik-e.

## <a name="customize-a-bubble-layer"></a>A buborék réteg testreszabása

A buborék réteg csak akkor van néhány stílusának beállítása. Itt egy olyan eszköz, próbálja ki őket.

<br/>

<iframe height='700' scrolling='no' title='Buborékdiagram réteg beállításai' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Rétegbeállítások Buborékdiagramot</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták a maps hozzá az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Egy szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Az adatvezérelt stílus kifejezések használata](data-driven-style-expressions-web-sdk.md)