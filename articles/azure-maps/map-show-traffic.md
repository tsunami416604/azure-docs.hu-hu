---
title: Térkép forgalmának megjelenítése | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a forgalmi adatokat egy térképen a Microsoft Azure Maps web SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534814"
---
# <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

A Azure Mapsban kétféle forgalmi adatok érhetők el:

- Incidensek – olyan pont-és vonal-alapú adatmennyiségből állnak, mint például az építőipar, a közúti bezárások és a balesetek.
- Flow-adatok – mérőszámokat biztosít a forgalom forgalmáról az utakon. Gyakran a forgalmi forgalomra vonatkozó adatokat használják az utak színezésére. A színek attól függnek, hogy mekkora forgalom lassul le a folyamaton, a sebességkorlátozás vagy más metrika alapján. Azure Maps adatforgalmának adatai három különböző mérőszámtal rendelkeznek:
    - `relative`-az út szabad áramlási sebességéhez viszonyítva.
    - `absolute`– az összes jármű teljes sebessége az úton.
    - `relative-delay`– a vártnál lassabban megjelenő területeket jeleníti meg.

A következő kód bemutatja, hogyan jelenítheti meg a forgalmi adatokat a térképen.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Térkép forgalmának megjelenítése' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on található Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti az <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>adatforgalom megjelenítése a térképen</a> .
</iframe>

## <a name="traffic-overlay-options"></a>Forgalom átfedési beállításai

A következő eszköz lehetővé teszi a különböző forgalom átfedési beállításainak közötti váltást, hogy megtekintse a renderelés változásait. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Forgalom átfedési beállításai" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>forgalom átfedési beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Fejlessze felhasználói élményeit:

> [!div class="nextstepaction"]
> [Térkép interakciója az egér eseményeivel](map-events.md)

> [!div class="nextstepaction"]
> [Akadálymentes Térkép létrehozása](map-accessibility.md)

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)
