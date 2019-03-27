---
title: Az Azure Maps egér események kezeléséhez |} A Microsoft Docs
description: A térkép események Javascript interaktív térkép biztosítása
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499190"
---
# <a name="interact-with-the-map---mouse-events"></a>Használhatja a térképet - egér események

Ez a cikk bemutatja, hogyan használható [osztály leképezése](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [események](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tulajdonság a térképen, és a különböző rétegeket, a térkép események kiemeléséhez. Azt is bemutatja, hogyan jelölje ki az eseményeket, amikor egy HTML-jelölő dolgozhat a térkép osztálytulajdonság-események használatával.

## <a name="interact-with-the-map"></a>Használhatja a térképet

<iframe height='600' scrolling='no' title='A térképek – egér események' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>jelentések kezelése a térképen – egér események</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti térkép kísérletezhet, és tekintse meg a megfelelő egér eseményeket, a jobb oldalon kiemelve. Kattintson a a **JS lapon** megtekintése és szerkesztése a JavaScript-kódot. Is kattinthat a **Szerkesztés a CodePen** gombra, és a kódot a CodePen szerkesztése.

## <a name="interact-with-map-layers"></a>A térképrétegek kezelése

<iframe height='600' scrolling='no' title='A térképek – réteg események' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>a térképek – réteg események</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód emeli ki, hogy az első aktivált fel a szimbólum réteg használata közben az események neve. Az összes jel, buborékdiagram, vonal és sokszög réteg támogatja ugyanazokat az eseményeket. A csempe réteg nem támogatja az események bármelyikét.

## <a name="interact-with-html-marker"></a>HTML jelölő kezelése

<iframe height='500' scrolling='no' title='A térképek – HTML jelölő események' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>a térképek – HTML jelölő események</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód hozzáadja egy HTML-jelölő Javascript térkép eseményeket. Emellett a név az eseményeket, hogy az első aktivált fel, a HTML-jelölő használata közben emeli ki.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Az Azure Maps Services modullal](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Znaková stránka-minta](https://aka.ms/AzureMapsSamples)
