---
title: Azure-alkalmazás konfigurációs időpontjának pillanatképe
description: Áttekintés arról, hogy az adott időponthoz tartozó pillanatkép hogyan működik az Azure app Configuration szolgáltatásban
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4a352ba913b6ad4e3c8607677078e21070f294fd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899595"
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
