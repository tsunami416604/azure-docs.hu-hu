---
title: Forgalom megjelenítése a térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a forgalmi adatokat a térképen a Microsoft Azure Maps Web SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534814"
---
# <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

Az Azure Mapsben kétféle forgalmi adat érhető el:

- Incidensadatok - pont- és vonalalapú adatokból állnak, például az építkezésre, az útlezárásokra és a balesetekre vonatkozóan.
- Áramlási adatok - mérőszámokat biztosít az utak forgalmának folyamatáról. Gyakran előfordul, hogy a forgalom adatokat használnak, hogy színes az utak. A színek attól függnek, hogy mekkora forgalom lassítja a folyamatot a sebességhatárhoz vagy egy másik mérőszámhoz képest. Az Azure Maps forgalmi adatait három különböző mérési metrika rendelkezik:
    - `relative`- az út szabad áramlási sebességéhez viszonyítva.
    - `absolute`- az összes közúti jármű abszolút sebessége.
    - `relative-delay`- azokat a területeket jeleníti meg, amelyek lassabbak az átlagos várható késleltetésnél.

A következő kód bemutatja, hogyan jeleníthető meg a forgalmi adatok a térképen.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Forgalom megjelenítése a térképen' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>megjelenítése a forgalmat az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a <a href='https://codepen.io'>CodePen</a>térképen .
</iframe>

## <a name="traffic-overlay-options"></a>Forgalmi átfedési beállítások

A következő eszköz lehetővé teszi a különböző forgalmi átfedési beállítások közötti váltást a renderelés változásainak megtekintéséhez. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Forgalmi átfedési beállítások" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Tollforgalmi átfedési beállításait</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions (TrafficOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Fokozza felhasználói élményét:

> [!div class="nextstepaction"]
> [Egéreseményekkel való térképezési interakció](map-events.md)

> [!div class="nextstepaction"]
> [Akadálymentes térkép létrehozása](map-accessibility.md)

> [!div class="nextstepaction"]
> [Kódmintalap](https://aka.ms/AzureMapsSamples)
