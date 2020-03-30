---
title: fájl belefoglalása
description: fájl belefoglalása
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73719113"
---
1. Gyorsítótár létrehozásához jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és válassza **az Erőforrás létrehozása lehetőséget.** 
   
   ![Erőforrás létrehozása lehetőséget.](media/redis-cache-create/create-a-resource.png)
   
1. Az **Új** lapon válassza az **Adatbázisok** lehetőséget, majd válassza **az Azure Cache for Redis**lehetőséget.
   
   ![Válassza ki a Redis Azure-gyorsítótárát](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Az **Új Redis-gyorsítótár** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti karakterláncnak kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* * \<a>.redis.cache.windows.net .* | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetést. | Az előfizetés, amely alatt az új Azure-gyorsítótár redis példány létrehozása. | 
   | **Erőforráscsoport** | Legördülő menü válasszon egy erőforráscsoportot, vagy válassza **az Új létrehozása** lehetőséget, és adjon meg egy új erőforráscsoport nevet. | Annak az erőforráscsoportnak a neve, amelyben a gyorsítótárat és más erőforrásokat létre lehet hozni. Ha az összes alkalmazáserőforrást egyetlen erőforráscsoportba helyezi, egyszerűen kezelheti vagy törölheti őket együtt. | 
   | **Helyen** | Legördülő menüt, és válasszon ki egy helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a gyorsítótárat használó egyéb szolgáltatások közelében. |
   | **Tarifacsomag** | Legördülő menüt, és válasszon egy [tarifacsomagot.](https://azure.microsoft.com/pricing/details/cache/) |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure Cache for Redis Overview](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Kattintson a **Létrehozás** gombra. 
   
   ![Azure-gyorsítótár létrehozása a Redis számára](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Eltart egy ideig, amíg a gyorsítótár létrehoz. Az Azure Cache for Redis **áttekintése** lapon nyomon követheti a folyamatot. Ha **az állapot** **futóként**jelenik meg, a gyorsítótár készen áll a használatra.
   
   ![Azure Cache for Redis létrehozva](media/redis-cache-create/redis-cache-cache-created.png)

