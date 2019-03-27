---
title: Az Azure App konfigurációs időponthoz pillanatkép |} A Microsoft Docs
description: Hogyan-időponthoz pillanatkép áttekintést működéséről az Azure-alkalmazások konfigurálása
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
ms.openlocfilehash: 6238f96c9e8df0431e42caa5e5846af3fc60e681
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484015"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure App konfigurálása a pontos alkalommal, amikor egy új kulcs-érték pár létrehozott, és ezután a módosított nyilvántartást vezet. Ezeket a rekordokat egy teljes ütemterv alkotnak, kulcs-érték megváltozik. Egy alkalmazás a konfigurációs adattároló is hozza az összes kulcsérték előzményeit, és az elmúlt értékét visszajátszani bármely adott pillanatban, akár a jelenlegi. Ezzel a funkcióval is "idő utazási" hátra, és egy régi kulcs értékét a vizualizációhoz. Tegnapi konfigurációs beállításokat, a legutóbbi telepítés előtt helyreállítása egy korábbi konfigurációt, és állítsa vissza az alkalmazás például kaphat.

## <a name="key-value-retrieval"></a>Kulcs-érték lekéréséhez

Elmúlt kulcsérték lekéréséhez időpontnak a megadása kulcsérték pillanatképet HTTP REST API-hívás fejlécében. Példa:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Jelenleg az alkalmazáskonfiguráció hét nap, az előzmények megőrzi.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: ASP.NET-webalkalmazás létrehozása](quickstart-aspnet-core-app.md)  
