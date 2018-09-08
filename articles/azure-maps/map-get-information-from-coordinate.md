---
title: Az Azure Maps koordináta információkat jelenít meg |} A Microsoft Docs
description: Hogyan egy címet kapcsolatos információk megjelenítéséhez a térképen, amikor a felhasználó kiválaszt egy koordinátája
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d1baa4adc555e65c4a25928d19f201dba6109142
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157684"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ez a cikk bemutatja, hogyan készítheti el egy fordított cím keresése, és kattintásra esetén címe jelenik meg a való kattintás helyét egy előugró ablakban.

Két módon, hogy egy fordított cím keresési, lekérdezési által a [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) szolgáltatáson keresztül modul és a egy másik program azáltal, hogy egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) a lekérdezésben az API-t a cím. Bemutatjuk, mind az alábbi.

## <a name="use-the-service-module-to-make-a-reverse-address-search"></a>A modul egy fordított cím keresés használata

### <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='(Szolgáltatásmodul) koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>(Szolgáltatásmodul) koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A sort a második kódblokkot a szolgáltatási ügyfél példányosítja.

A harmadik kódblokkot mutató frissíti az egérmutatót stílusát.

A negyedik kódblokk létrehoz egy előugró ablak. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

A legutóbbi kódblokkot hozzáad egy Eseményfigyelőt, a kattintások. Egy kattintásra esetén, hoz létre egy keresési lekérdezést az való kattintás pont fordítani. Akkor használja a térkép [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) végpont lekérdezni a fordítani a címet.

Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) függvény az előugró osztály.

## <a name="use-xmlhttprequest-to-make-a-reverse-address-search"></a>XMLHTTPRequest fordított cím keresés használata

### <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot mutató frissíti az egérmutatót stílusát.

A harmadik kódblokkot létrehoz egy előugró ablak. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

Az utolsó kódblokkot hozzáad egy Eseményfigyelőt, a kattintások. A kattintásra esetén küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) függvény az előugró osztály

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja: 
* [Fordított cím keresése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Zárja be](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

A maps hozzá további kódot példák a következő cikkekben talál:
* [Útvonal megjelenítése a-b](./map-route.md)
* [Forgalom megjelenítése](./map-show-traffic.md)
