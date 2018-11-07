---
title: Az Azure Maps forgalom megjelenítése |} A Microsoft Docs
description: Forgalmi adatok megjelenítését a Javascript térképen
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 532001a0cda22903d0bdf807ee868aef211336e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240086"
---
# <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

Ez a cikk bemutatja, hogyan forgalom és események információk megjelenítéséhez a térképen.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='456' scrolling='no' title='Forgalom megjelenítése térképen' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>forgalom megjelenítése térképen</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](map-create.md) útmutatást.

A második kódblokkot használ [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) függvény a térkép osztály megjelenítése a forgalom adatfolyamait és incidensek a térképen.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A teljes kódmintát a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Znaková stránka-minta](https://aka.ms/AzureMapsSamples)

A felhasználói élmény fokozása:

> [!div class="nextstepaction"]
> [Térkép interakció egérrel](./map-events.md)

> [!div class="nextstepaction"]
> [Egy elérhető térkép létrehozása](./map-accessibility.md)
