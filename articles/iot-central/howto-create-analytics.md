---
title: Hozzon létre egyéni analytics az Azure IoT központi alkalmazáshoz |} Microsoft Docs
description: Kezelőként egyéni analytics az Azure IoT központi alkalmazás létrehozása.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5facdf3f02b71e154a23d8f26c7bcc40b5c71e35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629303"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Az eszköz adatok analytics használata

A Microsoft Azure IoT központi képességeket biztosít a gazdag analytics való célszerű nagy mennyiségű adatok az eszközökről. Analytics segítségével megtekintheti és elemezheti az adatokat egy [eszköz set](howto-use-device-sets.md) az alkalmazásban. Egy eszköz be kapcsolva az eszközök felhasználói csoportja. Az elemzési eszközök kis csoportja vagy egyetlen eszközt megadásával szűkíthető.

Kezelőként válasszon **Analytics** a bal oldali navigációs menüben válasszon olyan eszköz, és válassza a diagramon megjelenítendő mérések. Az alábbiakban néhány eszközök segítségével az adatok további szelet:

* **Mérések:** válassza ki a mérések, például a hőmérséklet és a páratartalom megjelenítéséhez.
* **Összesítés:** méréshez összesítő függvény kiválasztása. Például **minimális** vagy **átlagos**.
* **Vegyes által:** nyissa ki az adatok eszköztulajdonságok vagy eszköz neve szerint rendezze. Például felosztása eszköz helye szerint:

     ![Elemzés főoldala](media\howto-create-analytics\analytics-main.png)

* **Időtartomány:** időtartomány az előre definiált időtartományhoz közül lehet választani, vagy hozzon létre egy egyéni időtartomány: ![Analytics időtartománynak](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>A képi megjelenítések módosítása

A diagramokon a képi megjelenítés követelményeinek a három módjai közül választva bármikor módosíthatja:

* **Halmozott:** egy grafikonon minden mérték halmozott, és minden a diagramokon rendelkezik, saját y tengely. A halmozott diagramok hasznosak, ha a kiválasztott több mérések, és a mérési adatok különböző képet szeretné.
* **Trendvonalat:** egy grafikonon minden mértékcsoport egy y tengely kell vinni, de az y értékek megváltoztak a kijelölt mértéken alapul. Trendvonalat diagramjait hasznosak, ha több mértéket átfedő és minták között ezeket a mértékeket a ugyanazon időtartományra megtekintéséhez.
* **Megosztott y tengely:** a diagramokon azonos tengely megosztani, és ne változtassa meg a tengely értékei. Megosztott y tengely diagramjait hasznosak, ha egyetlen mértéket nézze meg az adatokat a felosztás által felosztás során szeretné.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan hozzon létre egyéni analytics az Azure IoT központi alkalmazáshoz, itt a javasolt következő lépésre van:

> [!div class="nextstepaction"]
> [Készítse elő, és csatlakozzon a Node.js-alkalmazás](howto-connect-nodejs.md)