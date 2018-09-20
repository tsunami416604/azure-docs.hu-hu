---
title: Térkép vezérlők hozzáadása az Azure Maps |} A Microsoft Docs
description: Hogyan lehet hozzáadni egy térkép az Azure Maps-nagyítási vezérlő, a lényegét ellenőrzési, a rotálása vezérlő és a egy stílus kiválasztása.
author: walsehgal
ms.author: v-musehg
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4410c2ec5851ff210ca8a5fb4f482e5e12d0b8e8
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367797"
---
# <a name="add-map-controls-to-azure-maps"></a>Térkép vezérlők hozzáadása az Azure Maps

Ez a cikk bemutatja, hogyan térképvezérlőket hozzáadása egy térképen. Azt is megtudhatja, hogyan térkép létrehozásához az összes vezérlő és a egy [stílus kiválasztása](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>A nagyítási vezérlő hozzáadása

<iframe height='500' scrolling='no' title='A nagyítási vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>nagyítási vezérlő hozzáadása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokk objektumot hoz létre nagyítási vezérlő használatával a atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest).

A nagyítási vezérlő leehetővé a térkép adataikkal nagyítás. A harmadik blokk nagyítási vezérlő hozzáadja a térkép segítségével térkép [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

## <a name="add-pitch-control"></a>Lényegét vezérlőelem felvétele

<iframe height='500' scrolling='no' title='A terv lényegét vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>felébresztve vezérlőelem hozzáadása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre a stílus be van állítva, szürkeárnyalatos. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokk objektumot hoz létre Felébresztve vezérlőelem használatával a atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest).

Lényegét vezérlő leehetővé a térkép a térköz módosítása. A harmadik blokk felébresztve vezérlőelemet vesz fel a térkép segítségével térkép [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

## <a name="add-compass-control"></a>Iránytűn vezérlőelem felvétele

<iframe height='500' scrolling='no' title='Forgatás vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>rotálása vezérlőelem hozzáadása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokkot objektumot hoz létre Compass vezérlőelem használatával a atlas [Compass vezérlő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol). Hozzáadja a iránytűn vezérlő a térkép segítségével térképhez [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

## <a name="a-map-with-all-controls"></a>A térkép összes vezérlőkkel

<iframe height='500' scrolling='no' title='Az összes vezérlőjével térkép' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>egy térképen az összes vezérlőjével</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód egy térkép-objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokk objektumot hoz létre Compass vezérlőelem használatával a atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) , és hozzáadja a térkép segítségével térkép [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

A harmadik kódblokkot az atlas használatával nagyítás objektumot hoz létre [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) , és hozzáadja a térkép segítségével térkép [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

A negyedik kódblokk objektumot hoz létre Felébresztve vezérlőelem használatával a atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) , és hozzáadja a térkép segítségével térkép [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

A legutóbbi kódblokkot objektumot hoz létre stílus kiválasztása a atlas használatával [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) , és hozzáadja a térkép segítségével térkép [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metódust.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

A teljes kódja a következő cikkekben talál:

> [!div class="nextstepaction"]
> [A PIN-kód hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Egy előugró ablak hozzáadása](./map-add-popup.md)