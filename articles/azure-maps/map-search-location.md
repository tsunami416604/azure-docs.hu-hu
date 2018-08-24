---
title: Az Azure Maps a keresési eredmények megjelenítése |} A Microsoft Docs
description: Hogyan hajthat végre keresést az Azure Maps-kérést, majd az eredményeket megjeleníteni a Javascrip térképen
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b0ab271eab45a6f4b05d01713e2e2ddd22a22ea3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746603"
---
# <a name="show-search-results-on-the-map"></a>A térképen a keresési eredmények megjelenítése

Ez a cikk bemutatja, hogyan készítheti el egy keresési kérelmet, és a keresési eredmények megjelenítése a térképen. 

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Keresési eredmény megjelenítése térképen' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show keresési eredmények a térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot hoz létre, és hozzáadja a keresési réteget rögzíti a térképen. Látható [PIN-kód hozzáadása a térképen](./map-add-pin.md) útmutatást.

A harmadik kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps intelligens keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

A legutóbbi kódblokkot elemzi a bejövő válasz. A sikeres válasz gyűjti a szélességi és hosszúsági adatokat az egyes visszaadott helyek. Ez a helypont hozzáadja a térkép gombostűk formájában, és beállítja a térkép összes a PIN-kódok előállítására határain.


## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja: 

* [Azure Maps-intelligens keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
A maps hozzá további kódot példák a következő cikkekben talál: 
* [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)
* [Útvonal megjelenítése a-b](./map-route.md)
