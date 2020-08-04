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
ms.openlocfilehash: e0557f8eb2fd120baeebee96acb4ef539344e3e7
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528827"
---
1. Gyorsítótár létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása**lehetőséget. 
   
   ![Válassza az erőforrás létrehozása lehetőséget.](media/redis-cache-create/create-a-resource.png)
   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.
   
   ![Az Azure cache kiválasztása a Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a * \<DNS name> . Redis.cache.Windows.net*lesz. | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Tarifacsomag** | Legördülő menüből válassza ki a [díjszabási szintet](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Kattintson a **Létrehozás** gombra. 
   
   ![Azure cache létrehozása a Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   > [!NOTE]
   > A gyorsítótár kiépítés várhatóan 15 – 20 percet is igénybe vehet.   
   
   Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra**mutat, a gyorsítótár készen áll a használatra.
   
   ![Redis létrehozott Azure cache](media/redis-cache-create/redis-cache-cache-created.png)

