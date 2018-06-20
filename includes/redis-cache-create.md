---
title: fájl belefoglalása
description: fájl belefoglalása
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 6a96bd7c3d7d02f181a7d7513edb3bb39881274f
ms.sourcegitcommit: 7de1432648c4ff3bcd09530c079418477d9f4d00
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35719430"
---
1. Gyorsítótár létrehozásához először jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ezután válassza az **Erőforrás létrehozása** > **Adatbázisok** > **Redis Cache** lehetőséget.

    ![Új gyorsítótár](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. Az **Új Redis Cache-gyorsítótár** lapon adja meg az új gyorsítótár beállításait.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-név** | Globálisan egyedi név | A gyorsítótár neve. A gyorsítótár nevének 1–63 karakter hosszúságú sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A gyorsítótár neve nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben az új Azure Redis Cache-példány létrejön. | 
    | **Erőforráscsoport** |  *TestResources* | Az új erőforráscsoport neve, amelyben létrehozza a gyorsítótárat. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlése például az alkalmazáshoz társított összes erőforrást törli. | 
    | **Hely** | USA keleti régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a gyorsítótárat használó más szolgáltatások közelében. |
    | **[Tarifacsomag](https://azure.microsoft.com/pricing/details/cache/)** |  Alapszintű C0 (250 MB-os gyorsítótár) |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ az [Azure Redis Cache áttekintése](../articles/redis-cache/cache-overview.md) lapon található. |
    | **Rögzítés az irányítópulton** |  Kiválasztva | Rögzítheti az új gyorsítótárat az irányítópulton, hogy könnyen megtalálható legyen. |

    ![Gyorsítótár létrehozása](media/redis-cache-create/redis-cache-cache-create.png) 

3. Az új gyorsítótár beállításainak konfigurálása után válassza a **Létrehozás** lehetőséget. 

    A gyorsítótár létrehozása eltarthat néhány percig. A folyamat előrehaladásának állapotát az irányítópulton ellenőrizheti. Miután a gyorsítótár létrejött, az állapota **Fut**, és használatra kész.

    ![A gyorsítótár létrejött](media/redis-cache-create/redis-cache-cache-created.png)

