---
title: fájlbefoglalás
description: fájlbefoglalás
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829182"
---
1. Gyorsítótár létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása**lehetőséget.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Válassza ki az Azure cache-t a Redis.":::

   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Válassza ki az Azure cache-t a Redis.":::
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a * \<DNS name> . Redis.cache.Windows.net*lesz. | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Tarifacsomag** | Legördülő menüből válassza ki a [díjszabási szintet](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](../articles/azure-cache-for-redis/cache-overview.md). |

1. Válassza a **hálózatkezelés** fület, vagy kattintson a lap alján található **hálózatkezelés** gombra.

1. A **hálózatkezelés** lapon válassza ki a kapcsolati módszert.

1. Válassza a **Next (speciális** ) lapot, vagy kattintson a lap alján található **Tovább: speciális** gombra.

1. Az alapszintű vagy standard gyorsítótár-példány **speciális** lapján jelölje be a váltás engedélyezése jelölőnégyzetet, ha engedélyezni szeretné a nem TLS portot.

1. A Premium cache-példány **speciális** lapján konfigurálja a nem TLS port, a fürtözés és az adatmegőrzés beállításait.

1. Válassza a **Next: Tags (tovább** ) lapot, vagy kattintson a **Next: Tags (tovább** ) gombra a lap alján.

1. Szükség esetén a **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné. 

1. Válassza a **felülvizsgálat + létrehozás**lehetőséget. A felülvizsgálat + létrehozás lapon az Azure ellenőrzi a konfigurációt.

1. Ha megjelenik az átadott zöld érvényesítés üzenet, válassza a **Létrehozás**lehetőséget.

Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés**   oldalon. Ha az **állapot**    **futásra**mutat, a gyorsítótár készen áll a használatra. 