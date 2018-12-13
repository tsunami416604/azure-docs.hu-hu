---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 71d19b5d922616944c176cdda98470607c515af9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111899"
---
| Erőforrás | Korlát |
| --- | --- |
| Gyorsítótár mérete |530 GB |
| Adatbázisok |64 |
| Maximális csatlakozott ügyfelek |40,000 |
| Az Azure Cache Redis-replikák (a magas rendelkezésre állás) |1 |
| A prémium szintű gyorsítótár fürtözési szegmensek |10 |

Az Azure Cache redis korlátozza, és minden tarifacsomag különböző méretek a következők. A tarifacsomagok és azok kapcsolódó méretek megtekintéséhez lásd: [Azure Cache Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/).

A Redis konfigurálása korlátok az Azure Cache-további információ: [kiszolgálókonfiguráció Redis alapértelmezett](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Konfigurálása és felügyelete Azure Cache Redis-példány a Microsoft végzi el, mert nem mindegyik Redis parancsok támogatottak Azure Cache-ben a Redis. További információkért lásd: [redis Cache a redis Azure Cache-ben nem támogatott parancsok](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

