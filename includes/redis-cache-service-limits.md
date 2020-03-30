---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71839225"
---
| Erőforrás | Korlát |
| --- | --- |
| Gyorsítótár mérete |1,2 TB |
| Adatbázisok |64 |
| Csatlakoztatott ügyfelek maximális |40,000 |
| Azure-gyorsítótár a Redis replikákhoz, magas rendelkezésre állás |1 |
| Szegmensek prémium szintű gyorsítótárban fürtözéssel |10 |

A Redis-korlátozásokhoz és -méretekhez tartozó Azure-gyorsítótár az egyes tarifacsomagok esetében eltérő. A tarifacsomagok és a hozzájuk kapcsolódó méretek megtekintéséhez tekintse meg az [Azure Cache for Redis díjszabás.](https://azure.microsoft.com/pricing/details/cache/)

Az Azure Cache for Redis konfigurációs korlátairól az [Alapértelmezett Redis-kiszolgáló konfigurációja](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration)című témakörben olvashat bővebben.

Mivel az Azure Cache for Redis-példányok konfigurálását és kezelését a Microsoft végzi, nem minden Redis-parancs támogatott az Azure Cache for Redis. További információ: [Redis parancsok nem támogatott az Azure Cache for Redis.](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)

