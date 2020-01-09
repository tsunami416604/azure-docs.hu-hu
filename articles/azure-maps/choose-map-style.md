---
title: Válasszon egy térképi stílust Azure Maps
titleSuffix: Azure Maps
description: Ismerkedjen meg Azure Maps stílussal kapcsolatos funkciókkal.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e1d3d1b5904eded9f35c5ba628bea6426e7afaf0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531547"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Válasszon egy térképi stílust Azure Maps

[Azure Maps számos támogatott térképi stílusa](./supported-map-styles.md) elérhető a web SDK-ban. Ez a cikk bemutatja, hogyan használhatók a stílussal kapcsolatos funkciók a térképes betöltések stílusának beállításához, új stílus beállítása és a Style Picker vezérlőelem használata.

## <a name="set-style-on-map-load"></a>Stílus beállítása a Térkép betöltéséhez

A következő kódban a Térkép `style` beállításának értéke `grayscale_dark` az inicializáláskor.

<br/>

<iframe height='500' scrolling='no' title='A stílus beállítása a Térkép betöltéséhez' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) beállítással megtekintheti a <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Térkép terhelésének stílusát</a> .
</iframe>

## <a name="update-the-style"></a>A stílus frissítése

A következő kódban a térképi példány betöltését követően a Térkép stílusa `road`ról `satellite`ra frissül a Térkép [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) funkciójával.

<br/>

<iframe height='500' scrolling='no' title='A stílus frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stílus frissítése</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>A Style Picker hozzáadása

A következő kód egy [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) helyez el a térképhez, így a felhasználó könnyedén válthat a különböző térképi stílusok között. 

<br/>

<iframe height='500' scrolling='no' title='A Style Picker hozzáadása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>a Style Picker hozzáadásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Alapértelmezés szerint a Style Picker vezérlő felsorolja az összes olyan stílust, amely a Azure Maps S0 díjszabási szintje alapértelmezett használatakor elérhető. Ha csökkenteni szeretné a listában szereplő stílusok számát, adja át a listában megjeleníteni kívánt stílusok tömbjét a Style Picker `mapStyle` lehetőségével. Ha S1-et használ, és az összes rendelkezésre álló stílust meg szeretné jeleníteni, állítsa `"all"`re a Style Picker `mapStyles` lehetőségét.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Vezérlőelemek hozzáadása a térképekhez:

> [!div class="nextstepaction"]
> [Leképezési vezérlők hozzáadása](map-add-controls.md)

> [!div class="nextstepaction"]
> [PIN-kód hozzáadása](map-add-pin.md)
