---
title: Azure-alkalmazás konfigurációs időpontjának pillanatképe
description: Áttekintés arról, hogy az adott időponthoz tartozó pillanatkép hogyan működik az Azure app Configuration szolgáltatásban
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4db52ce1897aa5a2b809cb7044b9764baffd0767
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495253"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure-alkalmazás konfigurációja rögzíti a pontos időpontokat, amikor létrejön egy új kulcs-érték pár, majd módosul. Ezek a rekordok teljes idővonalat alkotnak a kulcs-érték változásaiban. Az alkalmazás-konfigurációs tárolók újraállíthatják a kulcsok előzményeit, és egy adott pillanatban újra visszajátszják a korábbi értékeket, akár a jelenre. Ezzel a funkcióval a "Time-Travel" visszafelé, a régi kulcs értékét pedig lekérheti. A tegnapi konfigurációs beállításokat például közvetlenül a legutóbbi üzembe helyezés előtt is beolvashatja, hogy helyreállítsa az előző konfigurációt, és visszaállítsa az alkalmazást.

## <a name="key-value-retrieval"></a>Kulcs-érték lekérése

A korábbi kulcsok értékének lekéréséhez adja meg azt az időpontot, amikor a kulcs értéke pillanatkép egy REST API hívás HTTP-fejlécében. Példa:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

Az alkalmazás konfigurációja jelenleg hét nappal korábbi változási előzményeket tart fenn.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core Webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
