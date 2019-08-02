---
title: Leképezési vezérlők hozzáadása a Azure Mapsban | Microsoft Docs
description: Nagyítás-vezérlés, szurok-vezérlőelem hozzáadása, a vezérlés elforgatása és a stílus kiválasztása a Azure Maps térképen.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7a504b8df199a3a461d5eb4e5b7238462b4c438f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638776"
---
# <a name="add-map-controls-to-azure-maps"></a>Leképezési vezérlők hozzáadása Azure Maps

Ebből a cikkből megtudhatja, hogyan adhat hozzá Térkép vezérlőelemeket egy térképhez. Azt is megtudhatja, hogyan hozhat létre egy térképet az összes vezérlővel és egy [Style választóval](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Nagyítási vezérlő hozzáadása

<iframe height='500' scrolling='no' title='Nagyítási vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/WKOQyN/'></a> a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) nagyítási vezérlőt a <a href='https://codepen.io'>CodePen</a>-on való hozzáadását ismertető tollat.
</iframe>

Az első kód blokk létrehoz egy Térkép objektumot a névtelen hitelesítési mechanizmus használatával. A Térkép létrehozásával kapcsolatos utasításokért tekintse meg a [Térkép létrehozása](./map-create.md) című témakört.

A nagyítási vezérlővel lehetőség nyílik a Térkép nagyítására és kikapcsolására. A második kódrészlet létrehoz egy zoom Control objektumot az Atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) , és hozzáadja azt a térképhez a Térkép vezérlők használatával [. Hozzáadás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus. A nagyítási vezérlő a Térkép **esemény** -figyelőn belül gondoskodik arról, hogy a Térkép betöltése után teljesen betöltődik.

## <a name="add-pitch-control"></a>Pitch vezérlőelem hozzáadása

<iframe height='500' scrolling='no' title='Pitch vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>a tollat</a> a CodePen-on<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( <a href='https://codepen.io'></a>) használatával.
</iframe>

Az első kód blokk létrehoz egy Térkép objektumot a névtelen hitelesítési mechanizmus használatával. A Térkép létrehozásával kapcsolatos utasításokért tekintse meg a [Térkép létrehozása](./map-create.md) című témakört.

A Pitch Control lehetővé teszi a Térkép hangmagasságának módosítását. A kód második blokkja egy pitch Control objektumot hoz létre az Atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) , és hozzáadja azt a térképhez a Térkép [vezérlői segítségével. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus. A Pitch vezérlőelem a Térkép esemény- **figyelőn** belül gondoskodik arról, hogy a Térkép teljes terhelése után is betöltődik.

## <a name="add-compass-control"></a>Iránytű vezérlőelem hozzáadása

<iframe height='500' scrolling='no' title='Forgatás vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () elforgatási <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>vezérlő hozzáadásával</a> .
</iframe>

Az első kód blokk létrehoz egy Térkép objektumot a névtelen hitelesítési mechanizmus használatával. A Térkép létrehozásával kapcsolatos utasításokért tekintse meg a [Térkép létrehozása](./map-create.md) című témakört.

A kód második blokkja egy iránytű vezérlőelem objektumot hoz létre az Atlas [Compass vezérlőelem](/javascript/api/azure-maps-control/atlas.control.compasscontrol)használatával. Emellett hozzáadja az iránytű vezérlőelemet a térképhez a Térkép [vezérlők használatával. Hozzáadás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus. Az iránytű vezérlőelem a Térkép esemény- **figyelőn** belül gondoskodik arról, hogy a Térkép teljes terhelése után is betöltődik.

## <a name="a-map-with-all-controls"></a>Térkép az összes vezérlővel

<iframe height='500' scrolling='no' title='Térkép az összes vezérlővel' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a térképes tollat az <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>összes</a> vezérlővel<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kód blokk létrehoz egy Térkép objektumot a névtelen hitelesítési mechanizmus használatával. A Térkép létrehozásával kapcsolatos utasításokért tekintse meg a [Térkép létrehozása](./map-create.md) című témakört.

A második kód blokk egy Compass Control objektumot hoz létre az Atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) , és hozzáadja azt a térképhez a Térkép [vezérlői segítségével. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus.

A kód harmadik blokkja egy nagyítás-vezérlési objektumot hoz létre az Atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) , és hozzáadja azt a térképhez a Térkép vezérlői használatával [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus.

A negyedik kód blokk egy pitch Control objektumot hoz létre az Atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) , és hozzáadja azt a térképhez a Térkép [vezérlői használatával. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus.

A kód utolsó blokkja egy Style Picker objektumot hoz létre az Atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) , és hozzáadja azt a térképhez a Térkép vezérlők használatával [. Hozzáadás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódus. Az összes vezérlő objektum hozzá van adva a Térkép **esemény** -figyelőben annak biztosításához, hogy a Térkép betöltését követően a terhelés teljesen betöltődik.

A parancsfájlban lévő vezérlőelem-objektumok sorrendje azt diktálja, hogy milyen sorrendben jelenjenek meg a térképen. A térképen a vezérlőelemek sorrendjének módosításához megváltoztathatja a parancsfájlban található sorrendet.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlaszi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

A következő cikkekben talál teljes kódot:

> [!div class="nextstepaction"]
> [PIN-kód hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Felugró ablak hozzáadása](./map-add-popup.md)
