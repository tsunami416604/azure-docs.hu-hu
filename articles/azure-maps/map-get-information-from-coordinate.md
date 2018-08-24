---
title: Az Azure Maps koordináta információkat jelenít meg |} A Microsoft Docs
description: Hogyan egy címet kapcsolatos információk megjelenítéséhez a térképen, amikor a felhasználó kiválaszt egy koordinátája
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e1cbed8995c0efbfb6010daaca5cd97ebec92dc6
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746342"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ez a cikk bemutatja, hogyan készítheti el egy fordított cím keresése, és kattintásra esetén címe jelenik meg a való kattintás helyét egy előugró ablakban. 

## <a name="understand-the-code"></a>A kód értelmezése

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
