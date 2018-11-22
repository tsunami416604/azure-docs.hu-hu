---
title: Adja hozzá a szimbólumok és jelölőket küldhet az Azure Maps |} A Microsoft Docs
description: Szimbólumok és jelölők hozzáadása egy Javascript-térkép
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c56ac35f49c364b7b0f2ad26b82b178411419414
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282685"
---
# <a name="add-symbols-and-markers-to-a-map"></a>Adja hozzá a szimbólumok és jelölők térképre

Ez a cikk bemutatja, hogyan szimbólumok és jelölők hozzáadása egy térkép adatforrást használ.

## <a name="add-a-symbol-marker"></a>Egy szimbólum mutató hozzáadása

<iframe height='500' scrolling='no' title='Kapcsoló PIN-kód helyét' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>kapcsoló PIN-kód helyét</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, egy adatforrás-objektum létrejött, használja a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály. Ezután létrehozott és az adatforráshoz hozzáadandó egy pont. A pont egy [funkció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) , [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest).

A harmadik kódblokkot létrehoz egy [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) és a frissítések, egér, a pont koordináták kattintson az alakzat osztállyal [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) metódust.

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen.  Az adatforrás az kattintson eseményfigyelő és a szimbólum réteg létrehozása és belül a térképhez hozzáadni a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy a pont után teljes betölti a térkép jelenik meg.

## <a name="add-a-custom-symbol"></a>Adjon hozzá egy egyéni szimbólum

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hozzáad egy [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) a térkép történő a [jelölők](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) tulajdonságát a [térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) osztály. A térkép belül a HtmlMarker bekerül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) győződjön meg arról, hogy teljes mértékben betölti a térkép után megjelenik a függvény.

## <a name="add-bubble-markers"></a>Buborékdiagramok jelölői hozzáadása

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

Egy beosztásokhoz tömbjét van definiálva a második kódblokkot, és a egy [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) objektum létrehozása. Egy adatforrás-objektum majd jön létre használatával a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztály és a MultiPoint objektumot adnak hozzá az adatforrást.

A [buborék réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) csomagolni szoftverfrissítésipont-alapú adatok rendereli a [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) , a térképen kör alakú. A legutóbbi kódblokkot egy buborék réteget hoz létre, és hozzáadja azt a térképen. Tekintse meg a buborék réteg tulajdonságainak [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Létrehozza és belül a térképhez hozzáadni a MultiPoint-objektumot, az adatforrás és a buborékok réteg a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvény használatával győződjön meg arról, hogy a kör megjelenik-e után teljes betölti a térképen.

## <a name="add-bubble-markers-with-label"></a>A címkével ellátott buborékdiagramok jelölői hozzáadása

<iframe height='500' scrolling='no' title='Többrétegű adatforrás' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>többrétegű DataSource</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód bemutatja, hogyan jelenítheti meg és címke adatait a térképen. A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot, létrehoz egy [pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektum. Ezután létrehoz egy objektum használhatja a [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) osztályt, és a pontot ad hozzá az adatforrást.

A [buborék réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) csomagolni szoftverfrissítésipont-alapú adatok rendereli a [adatforrás](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) , a térképen kör alakú. A harmadik kódblokkot egy buborék réteget hoz létre, és hozzáadja azt a térképen. Tekintse meg a buborék réteg tulajdonságainak [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A [szimbólum réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) használja a szöveges vagy ikonjai csomagolni szoftverfrissítésipont-alapú adatok megjelenítése a [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) szimbólumra a térképen. A legutóbbi kódblokkot hoz létre, és hozzáadja egy szimbólum réteg jeleníti meg az a szöveg címkéje a buborék a térképen. Tekintse meg a szimbólum réteg tulajdonságainak [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Az adatforrás és a Rétegek létrehozásakor és a térkép belül a [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosítására, hogy az adatok után teljes betölti a térkép megjelenik-e.


## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták a maps hozzá az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Egy előugró ablak hozzáadása](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](./map-add-shape.md)