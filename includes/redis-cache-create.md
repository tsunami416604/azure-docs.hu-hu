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
ms.openlocfilehash: 55bc2bd4e065c301f11a5fc4d3b58aa443b83e2d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
Gyorsítótár létrehozásához először jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Redis Cache** lehetőségre.

![Új gyorsítótár](media/redis-cache-create/redis-cache-new-cache-menu.png)

Az **Új Redis Cache-gyorsítótár** lapon adja meg az új gyorsítótár beállításait.

| Beállítás      | Ajánlott érték  | Leírás |
| ------------ |  ------- | -------------------------------------------------- |
| **DNS-név** | Globálisan egyedi név | A gyorsítótár nevének 1 és 63 karakter közötti karakterláncnak kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A gyorsítótár neve nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.  | 
| **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben az új Azure Redis Cache létrejön. | 
| **Erőforráscsoport** |  *TestResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a gyorsítótárat. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlésével például az alkalmazáshoz társított összes erőforrást törli. | 
| **Hely** | USA keleti régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a gyorsítótárat használó más szolgáltatások közelében. |
| **[Tarifacsomag](https://azure.microsoft.com/pricing/details/cache/)** |  Alapszintű C0 (250 MB-os gyorsítótár) |  Az árképzési szint határozza meg a gyorsítótár méretét, teljesítményét és az elérhető funkcióit. További információ az [Azure Redis Cache áttekintése](../articles/redis-cache/cache-overview.md) lapon található. |
| **Rögzítés az irányítópulton** |  Kiválasztva | Kattintson az új gyorsítótár rögzítése az irányítópulton lehetőségre, így könnyen megtalálható lesz. |

![Gyorsítótár létrehozása](media/redis-cache-create/redis-cache-cache-create.png) 

Az új gyorsítótár beállításainak konfigurálása után kattintson a **Létrehozás** lehetőségre. 

A gyorsítótár létrehozása eltarthat néhány percig. A folyamat előrehaladásának állapotát az irányítópulton ellenőrizheti. Miután a rendszer létrehozta a gyorsítótárat, az új gyorsítótár **Futó** állapotú, és készen áll a használatra.

![A gyorsítótár létrejött](media/redis-cache-create/redis-cache-cache-created.png)

