---
title: Azure-alkalmazás konfigurációs időponthoz tartozó pillanatképe | Microsoft Docs
description: Áttekintés arról, hogy az adott időponthoz tartozó pillanatkép hogyan működik az Azure app Configuration szolgáltatásban
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: fdbe4846700c690261dbc734063f4420478666a8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185202"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure-alkalmazás konfigurációja rögzíti a pontos időpontokat, amikor létrejön egy új kulcs-érték pár, majd módosul. Ezek a rekordok teljes idővonalat alkotnak a kulcs-érték változásaiban. Az alkalmazás-konfigurációs tárolók újraállíthatják a kulcsok előzményeit, és egy adott pillanatban újra visszajátszják a korábbi értékeket, akár a jelenre. Ezzel a funkcióval a "Time-Travel" visszafelé, a régi kulcs értékét pedig lekérheti. A tegnapi konfigurációs beállításokat például közvetlenül a legutóbbi üzembe helyezés előtt is beolvashatja, hogy helyreállítsa az előző konfigurációt, és visszaállítsa az alkalmazást.

## <a name="key-value-retrieval"></a>Kulcs-érték lekérése

A korábbi kulcsok értékének lekéréséhez adja meg azt az időpontot, amikor a kulcs értéke pillanatkép egy REST API hívás HTTP-fejlécében. Például:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Az alkalmazás konfigurációja jelenleg hét nappal korábbi változási előzményeket tart fenn.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core Webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
