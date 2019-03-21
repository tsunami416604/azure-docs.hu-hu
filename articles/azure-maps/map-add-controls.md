---
title: Térkép vezérlők hozzáadása az Azure Maps |} A Microsoft Docs
description: Hogyan lehet hozzáadni egy térkép az Azure Maps-nagyítási vezérlő, a lényegét ellenőrzési, a rotálása vezérlő és a egy stílus kiválasztása.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c1f5dd329f34d64478d605c21d229d8c75a99300
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260140"
---
# <a name="add-map-controls-to-azure-maps"></a>Térkép vezérlők hozzáadása az Azure Maps

Ez a cikk bemutatja, hogyan térképvezérlőket hozzáadása egy térképen. Azt is megtudhatja, hogyan térkép létrehozásához az összes vezérlő és a egy [stílus kiválasztása](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>A nagyítási vezérlő hozzáadása

<iframe height='500' scrolling='no' title='A nagyítási vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>nagyítási vezérlő hozzáadása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot beállítja az előfizetési kulcsot, és előre a stílus beállítása nélkül egy térkép objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A nagyítási vezérlő leehetővé a térkép adataikkal nagyítás. A második kódblokk objektumot hoz létre nagyítási vezérlő használatával a atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) , és hozzáadja a térkép segítségével térkép [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust. A térkép belül van a nagyítási vezérlő **eseményfigyelő** után a térkép teljes betölti a változóból biztosításához.

## <a name="add-pitch-control"></a>Lényegét vezérlőelem felvétele

<iframe height='500' scrolling='no' title='A terv lényegét vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>felébresztve vezérlőelem hozzáadása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot beállítja az előfizetési kulcsot, és előre a stílus beállítása nélkül egy térkép objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

Lényegét vezérlő leehetővé a térkép a térköz módosítása. A második kódblokkot objektumot hoz létre Felébresztve vezérlőelem használatával a atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) , és hozzáadja a térkép segítségével térkép [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust. A térkép belül van a terv lényegét vezérlő **eseményfigyelő** után a térkép teljes betölti a változóból biztosításához.

## <a name="add-compass-control"></a>Iránytűn vezérlőelem felvétele

<iframe height='500' scrolling='no' title='Forgatás vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>rotálása vezérlőelem hozzáadása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot beállítja az előfizetési kulcsot, és előre a stílus beállítása nélkül egy térkép objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokkot objektumot hoz létre Compass vezérlőelem használatával a atlas [Compass vezérlő](/javascript/api/azure-maps-control/atlas.control.compasscontrol). Hozzáadja a iránytűn vezérlő a térkép segítségével térképhez [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust. A térkép belül van a Compass vezérlő **eseményfigyelő** után a térkép teljes betölti a változóból biztosításához.

## <a name="a-map-with-all-controls"></a>A térkép összes vezérlőkkel

<iframe height='500' scrolling='no' title='Az összes vezérlőjével térkép' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>egy térképen az összes vezérlőjével</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot beállítja az előfizetési kulcsot, és előre a stílus beállítása nélkül egy térkép objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokk objektumot hoz létre Compass vezérlőelem használatával a atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) , és hozzáadja a térkép segítségével térkép [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust.

A harmadik kódblokkot az atlas használatával nagyítás objektumot hoz létre [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) , és hozzáadja a térkép segítségével térkép [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust.

A negyedik kódblokk objektumot hoz létre Felébresztve vezérlőelem használatával a atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) , és hozzáadja a térkép segítségével térkép [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust.

A legutóbbi kódblokkot objektumot hoz létre stílus kiválasztása a atlas használatával [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) , és hozzáadja a térkép segítségével térkép [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust. Az összes vezérlő objektum kerülnek, a térkép belül **eseményfigyelő** annak érdekében, hogy betöltése után a térkép teljes tölti be.

A parancsfájl a vezérlő objektumok sorrendje előírja a sorrendben jelenjenek meg a térképen. A térkép vezérlőelem sorrendjének módosításához a szkriptben sorrendjét módosíthatja.

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
