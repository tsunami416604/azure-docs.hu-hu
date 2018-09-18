---
title: Az Azure Maps koordináta információkat jelenít meg |} A Microsoft Docs
description: Hogyan egy címet kapcsolatos információk megjelenítéséhez a térképen, amikor a felhasználó kiválaszt egy koordinátája
author: jingjing-z
ms.author: jinzh
ms.date: 09/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: bf44437f4d0b60a5d56c2be29418b7132346da2e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732294"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ez a cikk bemutatja, hogyan javíthatja egy fordított cím keresését, amely megjeleníti a cím való kattintás előugró hely.

Győződjön meg arról, fordított cím keresés két módon lehet. Egyik módszer, a lekérdezés a [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) egy modul segítségével. A másik lehetőség, győződjön meg arról, hogy egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) található cím az API-hoz. Mindkét irányban keresztszűréssel alatt vannak felügyelt.

## <a name="make-a-reverse-search-request-via-service-module"></a>Indítson egy fordított keresési szolgáltatás modulon keresztül

<iframe height='500' scrolling='no' title='(Szolgáltatásmodul) koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>(Szolgáltatásmodul) koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A sort a második kódblokkot a szolgáltatási ügyfél példányosítja.

A harmadik kódblokkot mutató frissíti az egérmutatót stílusát.

A negyedik kódblokk létrehoz egy előugró ablak az [nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

A legutóbbi kódblokkot [hozzáad egy Eseményfigyelőt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) az egérrel rákattint. Egy kattintásra esetén, hoz létre egy keresési lekérdezést az való kattintás pont fordítani. Akkor használja a térkép [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) végpont lekérdezni a fordítani a címet.

Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) függvény az előugró osztály.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Fordított keresési keresztül XMLHttpRequest kérést

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordináta információinak lekérése</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot mutató frissíti az egérmutatót stílusát.

A harmadik kódblokkot létrehoz egy előugró ablak az [nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) útmutatást.

Az utolsó kódblokkot hozzáad egy Eseményfigyelőt, a kattintások. A kattintásra esetén küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Sikeres válasz, a cím való kattintás helyéhez gyűjti, és meghatározza az előugró ablak tartalmát és pozícióját keresztül [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) függvény az előugró osztály

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

A teljes hitelesítésikód-példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a-b](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)
