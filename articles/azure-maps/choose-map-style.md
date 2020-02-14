---
title: Térkép stílusa funkciói | Microsoft Azure térképek
description: Ebben a cikkben megismerheti a Microsoft Azure Maps web SDK-ban elérhető stílusokkal kapcsolatos funkciókat.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cabf39f017afe440c883a63db57643c5c5367128
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189743"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Válasszon egy térképi stílust Azure Maps

[Azure Maps számos támogatott térképi stílusa](./supported-map-styles.md) elérhető a web SDK-ban. Ez a cikk bemutatja, hogyan használhatja a stílussal kapcsolatos funkciókat. Megtudhatja, hogyan állíthat be egy stílust a Térkép betöltésekor, és megtudhatja, hogyan állíthat be új leképezési stílust a Style Picker vezérlőelem használatával.

## <a name="set-style-on-map-load"></a>Stílus beállítása a Térkép betöltéséhez

A következő kódban a Térkép `style` beállításának értéke `grayscale_dark` az inicializáláskor.

<br/>

<iframe height='500' scrolling='no' title='A stílus beállítása a Térkép betöltéséhez' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) beállítással megtekintheti a <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Térkép terhelésének stílusát</a> .
</iframe>

## <a name="update-the-style"></a>A stílus frissítése

A következő kódban a térképi példány betöltését követően a Térkép stílusa `road`ról `satellite`ra frissül a [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvény használatával.

<br/>

<iframe height='500' scrolling='no' title='A stílus frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stílus frissítése</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>A Style Picker hozzáadása

A következő kód egy [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) helyez el a térképhez, így a felhasználó könnyedén válthat a különböző térképi stílusok között. A Térkép stílusa a jobb felső sarokban látható Térkép stílusa vezérlőelem használatával válthat.

<br/>

<iframe height='500' scrolling='no' title='A Style Picker hozzáadása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>a Style Picker hozzáadásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Alapértelmezés szerint a Azure Maps S0 díjszabási szintje használatakor a Style Picker vezérlő felsorolja az összes elérhető stílust. Ha csökkenteni szeretné a listában szereplő stílusok számát, adja át a listában megjeleníteni kívánt stílusok tömbjét a Style Picker `mapStyle` lehetőségével. Ha S1-et használ, és az összes rendelkezésre álló stílust meg szeretné jeleníteni, állítsa `"all"`re a Style Picker `mapStyles` lehetőségét.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Vezérlőelemek hozzáadása a térképekhez:

> [!div class="nextstepaction"]
> [Leképezési vezérlők hozzáadása](map-add-controls.md)

> [!div class="nextstepaction"]
> [PIN-kód hozzáadása](map-add-pin.md)
