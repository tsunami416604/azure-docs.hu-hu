---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67179523"
---
| Erőforrás | Korlát |
| --- | --- |
| Gyorsítótár mérete |530 GB |
| Adatbázisok |64 |
| Csatlakoztatott ügyfelek maximális száma |40,000 |
| Azure cache a Redis-replikák számára a magas rendelkezésre állás érdekében |1 |
| A prémium szintű gyorsítótár fürtözési szegmensek |10 |

Az Azure Cache redis korlátozza, és minden tarifacsomag különböző méretek a következők. A díjszabási szintek és a hozzájuk tartozó méretek megtekintéséhez tekintse meg az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/).

A Redis konfigurálása korlátok az Azure Cache-további információ: [kiszolgálókonfiguráció Redis alapértelmezett](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Konfigurálása és felügyelete Azure Cache Redis-példány a Microsoft végzi el, mert nem mindegyik Redis parancsok támogatottak Azure Cache-ben a Redis. További információkért lásd: [redis Cache a redis Azure Cache-ben nem támogatott parancsok](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

