---
title: Azure Cosmos DB szolgáltatási kérelem időtúllépési kivételének megoldása
description: Cosmos DB szolgáltatási kérelem időtúllépési kivételének diagnosztizálása és javítása
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294276"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Azure Cosmos DB kérelmek időtúllépésének diagnosztizálása és megoldása
A Azure Cosmos DB HTTP 408 kérelem időtúllépését adta vissza

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Az alábbi lista a kérelmek időtúllépési kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="1-check-the-sla"></a>1. Az SLA ellenõrzése
Az ügyfélnek ellenőriznie kell a [Azure Cosmos db figyelését](monitor-cosmos-db.md) annak ellenőrzéséhez, hogy az 408-es számú kivétel sérti-e a Cosmos db SLA-t.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>1. megoldás: nem sértette meg a Cosmos DB SLA-t
Az alkalmazásnak kezelnie kell ezt a forgatókönyvet, és újra kell próbálkoznia ezekkel az átmeneti hibákkal.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>2. megoldás: megszegte a Cosmos DB SLA-t
Forduljon az Azure ügyfélszolgálatához:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. gyors partíciós kulcs
A Azure Cosmos DB a teljes kiosztott átviteli sebességet egyenletesen osztja el a fizikai partíciók között. Ha van egy gyakori partíció, a fizikai partíciók egy vagy több logikai partíciója a fizikai partíció RU/s-t használja fel, míg az egyéb fizikai partíciókon lévő RU/s nem lesz használatban. Ez azt eredményezi, hogy a felhasznált RU/s mennyisége kevesebb lesz, mint az adatbázis vagy a tároló összesített kiépített RU/s-száma, de továbbra is megtekintheti a szabályozást (429s) a gyors logikai partíciós kulcsra vonatkozó kérelmeknél. A [normalizált ru](monitor-normalized-request-units.md) -használati metrika használatával ellenőrizze, hogy a számítási feladatok egy gyors partícióval találkoznak-e. 

#### <a name="solution"></a>Megoldás:
Válasszon egy jó partíciós kulcsot, amely egyenletesen osztja el a kérelmek mennyiségét és tárolását. Megtudhatja, hogyan [módosíthatja a partíciós kulcsot](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése