---
title: Képezze le az Azure Maps funkciói stílus |} A Microsoft Docs
description: További tudnivalók az Azure Maps stílus kapcsolatos funkciók.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 69cf0815a8418df4c8a36adf99770640df327d49
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568855"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Azure Maps-térkép stílus kiválasztása

Az Azure Maps rendelkezik négy külön térképeken stílusok közül választhat. Térkép stílusok kapcsolatos további információkért lásd: [térkép stílusok támogatott az Azure Maps](./supported-map-styles.md). Ez a cikk bemutatja, hogyan stílus a térkép betöltéskor, egy új stílusának beállítása, ha a stílus Dátumválasztó vezérlőelem a stílus kapcsolatos funkciók használatára.

## <a name="set-style-on-map-load"></a>Térkép betöltése a beállított stílus

<iframe height='500' scrolling='no' title='A térkép betöltéskor stílusának beállítása' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>stílusának beállítása a térkép betöltéskor</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódblokk beállítja az előfizetési kulcsot, és a egy térkép-objektumot hoz létre a stílus be van állítva, a grayscale_dark. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

## <a name="update-the-style"></a>Frissítés a stílus

<iframe height='500' scrolling='no' title='Styl frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>frissítése a stílus</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódblokk beállítja az előfizetési kulcsot, és előre a stílus beállítása nélkül egy térkép objektumot hoz létre. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokk használja a térkép [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódussal adja a térkép stílus műholdas.

## <a name="add-the-style-picker"></a>Adja hozzá a stílus kiválasztása

<iframe height='500' scrolling='no' title='Hozzáadás, a stílus kiválasztása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>hozzáadása a stílus kiválasztása</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot a fenti kód beállítja az előfizetési kulcsot, és a egy térkép-objektumot hoz létre, a térkép van előzetesen beállítva grayscale_dark. Lásd: [térkép létrehozásához](./map-create.md) térkép létrehozásához útmutatást.

A második kódblokk hoz létre a atlas használatával stílus selektor [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol) konstruktor.

A stílus kiválasztása lehetővé teszi, hogy a térkép stílus kiválasztása. A harmadik kódblokk hozzáadja a stílus kiválasztása a térkép a leképezéssel [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metódust. A térkép belül van a stílus választó **eseményfigyelő** annak érdekében, hogy betölti után teljes betölti a térképen.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Vezérlő hozzáadása a maps:

> [!div class="nextstepaction"]
> [Térkép vezérlők hozzáadása](./map-add-controls.md)

Adja hozzá a térkép PIN-kódot:

> [!div class="nextstepaction"]
> [A PIN-kód hozzáadása](./map-add-pin.md)
