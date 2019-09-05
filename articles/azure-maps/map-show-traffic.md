---
title: Forgalom megjelenítése a Azure Mapskal | Microsoft Docs
description: Forgalmi adatok megjelenítése JavaScript-térképen
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638629"
---
# <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

Ez a cikk bemutatja, hogyan jelenítheti meg a forgalom és az incidensek adatait a térképen.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='456' scrolling='no' title='Térkép forgalmának megjelenítése' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on található Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti az adatforgalom <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>megjelenítése a térképen</a> .
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. Ehhez útmutatást a [Térkép létrehozása](map-create.md) című témakörben találhat.

A kód második blokkja a Térkép [esemény-figyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) függvényében a [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvényt használja a forgalmi folyamatok és incidensek megjelenítéséhez a térképen.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A következő cikkekből megtekintheti a kódok teljes mintáit:

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)

Fejlessze felhasználói élményeit:

> [!div class="nextstepaction"]
> [Térkép interakciója az egér eseményeivel](./map-events.md)

> [!div class="nextstepaction"]
> [Akadálymentes Térkép létrehozása](./map-accessibility.md)
