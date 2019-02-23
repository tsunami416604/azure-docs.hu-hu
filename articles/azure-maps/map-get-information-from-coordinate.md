---
title: Az Azure Maps koordináta információkat jelenít meg |} A Microsoft Docs
description: Hogyan egy címet kapcsolatos információk megjelenítéséhez a térképen, amikor a felhasználó kiválaszt egy koordinátája
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fdf2e884ca896be1dcb8eea810958076b8b8eb7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668667"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ez a cikk bemutatja, hogyan javíthatja egy fordított cím keresését, amely megjeleníti a cím való kattintás előugró hely.

Győződjön meg arról, fordított cím keresés két módon lehet. Egyik módszer, a lekérdezés a [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) egy modul segítségével. A másik lehetőség, győződjön meg arról, hogy egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) található cím az API-hoz. Mindkét irányban keresztszűréssel alatt vannak felügyelt.

## <a name="make-a-reverse-search-request-via-service-module"></a>Indítson egy fordított keresési szolgáltatás modulon keresztül

<iframe height='500' scrolling='no' title='(Szolgáltatásmodul) koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>(Szolgáltatásmodul) koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A vonal jelzi a második kódblokkot egy ügyfél szolgáltatás példányosítja.

A harmadik kódblokkot mutató frissíti az egérmutatót a stílus és [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektum. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

A negyedik kódblokkot hozzáad egy [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) az egérrel rákattint. Egy kattintásra esetén azt a koordináták a való kattintás pont egy keresési lekérdezést hoz létre. Ezután használja a térkép [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchaddressreverseresponse?view=azure-iot-typescript-latest) lekérdezni a koordináták a cím végponthoz.

Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) függvény az előugró osztály.

A módosítás a kurzor, egy előugró objektum és a kattintás esemény összes jönnek létre a térkép [terhelés eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosításához térkép terhelések teljes előtt a koordináták adatait.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Fordított keresési keresztül XMLHttpRequest kérést

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot mutató frissíti az egérmutatót a stílus és [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektum. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

A harmadik kódblokkot hozzáad egy Eseményfigyelőt, a kattintások. A kattintásra esetén küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -lekérdezéshez való kattintás koordinátái címét. Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) függvény az előugró osztály.

A módosítás a kurzor, egy előugró objektum és a kattintás esemény összes jönnek létre a térkép [terhelés eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) biztosításához térkép terhelések teljes előtt a koordináták adatait.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a-b](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)
