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
ms.openlocfilehash: a00c1f916d7e8753830bf87bef6897766acbc276
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53018378"
---
1. Gyorsítótár létrehozásához először jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **erőforrás létrehozása** > **adatbázisok** > **Azure Cache redis**.

    ![Új gyorsítótár](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. A **új Azure Cache redis**, az új gyorsítótár beállításainak konfigurálásához.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-név** | Globálisan egyedi név | A gyorsítótár neve. A gyorsítótár nevének 1–63 karakter hosszúságú sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A gyorsítótár neve nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben az új Azure Azure Cache Redis-példány létrejön. | 
    | **Erőforráscsoport** |  *TestResources* | Az új erőforráscsoport neve, amelyben létrehozza a gyorsítótárat. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlése például az alkalmazáshoz társított összes erőforrást törli. | 
    | **Hely** | USA keleti régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a gyorsítótárat használó más szolgáltatások közelében. |
    | **[Tarifacsomag](https://azure.microsoft.com/pricing/details/cache/)** |  Alapszintű C0 (250 MB-os gyorsítótár) |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információkért lásd: [Azure Cache Azure redis Cache – áttekintés](../articles/azure-cache-for-redis/cache-overview.md). |
    | **Rögzítés az irányítópulton** |  Kiválasztva | Rögzítheti az új gyorsítótárat az irányítópulton, hogy könnyen megtalálható legyen. |

    ![Gyorsítótár létrehozása](media/redis-cache-create/redis-cache-cache-create.png) 

3. Az új gyorsítótár beállításainak konfigurálása után válassza a **Létrehozás** lehetőséget. 

    A gyorsítótár létrehozása eltarthat néhány percig. A folyamat előrehaladásának állapotát az irányítópulton ellenőrizheti. Miután a gyorsítótár létrejött, az állapota **Fut**, és használatra kész.

    ![A gyorsítótár létrejött](media/redis-cache-create/redis-cache-cache-created.png)

