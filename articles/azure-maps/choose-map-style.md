---
title: Stílusbeli funkciók leképezése Azure Mapsban | Microsoft Docs
description: Ismerkedjen meg Azure Maps stílussal kapcsolatos funkciókkal.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: eb667c398be0bd51e05a6b65d416d5bce54e4386
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881970"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Válasszon egy térképi stílust Azure Maps

[Azure Maps számos támogatott térképi stílusa](./supported-map-styles.md) elérhető a web SDK-ban. Ez a cikk bemutatja, hogyan használhatók a stílussal kapcsolatos funkciók a térképes betöltések stílusának beállításához, új stílus beállítása és a Style Picker vezérlőelem használata.

## <a name="set-style-on-map-load"></a>Stílus beállítása a Térkép betöltéséhez

A következő kódban `style` a Térkép `grayscale_dark` beállítása az inicializálás beállításra van beállítva.

<br/>

<iframe height='500' scrolling='no' title='A stílus beállítása a Térkép betöltéséhez' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>-on a stílus betöltésére <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>vonatkozó beállítást</a> .
</iframe>

## <a name="update-the-style"></a>A stílus frissítése

A következő kódban a térképi példány betöltését követően a Térkép stílusa a [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvényének `road` `satellite` használatával frissül.

<br/>

<iframe height='500' scrolling='no' title='A stílus frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stílus frissítése</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>A Style Picker hozzáadása

A következő kód egy [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) helyez el a térképhez, így a felhasználó könnyedén válthat a különböző térképi stílusok között. 

<br/>

<iframe height='500' scrolling='no' title='A Style Picker hozzáadása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Style Picker</a> hozzáadásával<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Alapértelmezés szerint a Style Picker vezérlő felsorolja az összes olyan stílust, amely a Azure Maps S0 díjszabási szintje alapértelmezett használatakor elérhető. Ha csökkenteni szeretné a listában szereplő stílusok számát, adja át a listában `mapStyle` megjeleníteni kívánt stílusok tömbjét a Style Picker (stílus kiválasztása) lehetőséggel. Ha S1-et használ, és az összes elérhető stílust meg szeretné jeleníteni, állítsa `mapStyles` a Style Picker `"all"`kapcsolót a következőre:.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Vezérlőelemek hozzáadása a térképekhez:

> [!div class="nextstepaction"]
> [Leképezési vezérlők hozzáadása](map-add-controls.md)

> [!div class="nextstepaction"]
> [PIN-kód hozzáadása](map-add-pin.md)
