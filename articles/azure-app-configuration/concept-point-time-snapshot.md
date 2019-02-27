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
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884867"
---
# <a name="point-in-time-snapshot"></a>Időponthoz pillanatkép

Az Azure App konfigurálása a pontos alkalommal, amikor egy új kulcs-érték pár létrehozott, és ezt követően módosította nyilvántartást vezet. Ezeket a rekordokat egy teljes ütemterv alkotnak, kulcs-érték megváltozik. Egy alkalmazás a konfigurációs adattároló is hozza az összes kulcs-érték előzményeit, és visszajátszani az elmúlt értékét bármely adott pillanatban, akár a jelenlegi. Ez a funkció lehetővé teszi, hogy "idő utazási" visszafelé és a egy régi kulcs-érték lekéréséhez. Például kaphat a konfigurációs beállításokat a tegnap, csak a legutóbbi üzembe helyezés előtt, hogy helyre tudja állítani az esetben szükséges, állítsa vissza az alkalmazás egy korábbi konfigurációt.

## <a name="key-value-retrieval"></a>Kulcs-érték lekéréséhez

Lekérdezheti az elmúlt kulcs-értékeket, kulcs-értékeket pillanatképet időpontnak a megadása, a REST API-hívás a HTTP-fejlécben kell. Példa:

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Jelenleg a Alkalmazáskonfiguráció 7 nap az előzmények megőrzi.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: ASP.NET-webalkalmazás létrehozása](quickstart-aspnet-core-app.md)  
