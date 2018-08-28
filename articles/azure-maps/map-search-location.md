---
title: Az Azure Maps a keresési eredmények megjelenítése |} A Microsoft Docs
description: Hogyan hajthat végre keresést az Azure Maps-kérést, majd az eredményeket megjeleníteni a Javascrip térképen
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 540faa04573f7fa66b574f6743d42adfe855c571
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095255"
---
# <a name="show-search-results-on-the-map"></a>A térképen a keresési eredmények megjelenítése

Ez a cikk bemutatja, hogyan keresse meg a lényeges helyét, és a keresési eredmények megjelenítése a térképen. 

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='305' scrolling='no' title='Keresési eredmény megjelenítése térképen (szolgáltatás modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>találatok megjelenítése a térképen (Szolgáltatásmodul)</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódrészletben az első kódblokkot egy térkép-objektumot hoz létre, és a egy ügyfél szolgáltatást példányosítja. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot használ az intelligens keresés [Azure Maps intelligens keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) hasznos helyekre vezető útvonalak keresése. Intelligens keresést API intelligens bemenetek bármilyen kombinációját képes kezelni. Az intelligens keresés szolgáltatástól kapott válasz majd szűrőtulajdonság GeoJSON formátumú, és a PIN-kód hozzáadja a hasznos helyekhez a térképen megjeleníthető leképezés. 

A legutóbbi kódblokkot kamera esik a térkép hozzáadja a térkép segítségével [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) tulajdonság.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja: 

* [Azure Maps-intelligens keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
A maps hozzá további kódot példák a következő cikkekben talál: 
* [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)
* [Útvonal megjelenítése a-b](./map-route.md)
