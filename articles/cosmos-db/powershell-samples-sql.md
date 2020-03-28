---
title: Azure PowerShell-minták az Azure Cosmos DB - SQL (Core) API-hoz
description: Az Azure PowerShell-minták beszereznie a különböző gyakori feladatok elvégzéséhez az Azure Cosmos DB SQL API-fiókokban
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366189"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell-minták az Azure Cosmos DB - SQL (Core) API-hoz

Az alábbi táblázat az Azure Cosmos DB SQL (Core) API-hoz készült, általánosan használt Azure PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz. Ha szeretné elválasztani ezeket a PowerShell-mintákat a Cosmos DB-hoz a GitHub-tárházunkból, keresse fel [a Cosmos DB PowerShell-mintákat a GitHubon.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)

További Cosmos DB PowerShell-minták at SQL (Core) API és dokumentáció, [az Azure Cosmos DB SQL API-erőforrások kezelése a PowerShell használatával.](manage-with-powershell.md) Cosmos DB PowerShell-mintákat más API-khoz, lásd: [Cassandra API,](powershell-samples-cassandra.md) [MongoDB API,](powershell-samples-mongodb.md) [Gremlin API](powershell-samples-gremlin.md)és [Table API.](powershell-samples-table.md)

> [!NOTE]
> A minták [az Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használnak. Kérjük, `Az.CosmosDB` vegye figyelembe, hogy a parancsmagok továbbra is előzetes verzióban vannak, és a kiadás előtt változhatnak. Kérjük, rendszeresen ellenőrizze a frissítéseket. `Az.CosmosDB`

| | |
|---|---|
|[Fiók, adatbázis és tároló létrehozása](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB-fiókot, adatbázist és tárolót. |
|[Tároló létrehozása nagy partíciókulccsal](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy nagy partíciós kulccsal rendelkező tárolót. |
|[Adatbázisok vagy tárolók listázása vagy beírása](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tárolók listázása vagy beírása. |
|[Ru/s beszerezni](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ru/s beszerezni e-műveleteket egy adatbázishoz vagy tárolóhoz. |
|[Ru/s frissítése](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tároló RU/s frissítése. |
|[Indexházirend nélküli tároló létrehozása](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolót, amelynek indexszabályzata ki van kapcsolva.|
|[Fiók frissítése](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Frissítse a Cosmos DB-fiók alapértelmezett konzisztenciaszintjét. |
|[Fiók régióinak frissítése](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Frissítse a Cosmos DB-fiók régióit. |
|[Feladatátvételi prioritás módosítása vagy eseményindító feladatátvétel](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Módosítsa az Azure Cosmos-fiók területi feladatátvételi prioritását, vagy indítson el egy manuális feladatátvételt. |
|[Fiókkulcsok vagy kapcsolati karakterláncok](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, kapcsolati karakterláncok vagy egy Azure Cosmos DB-fiók fiókkulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB-fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||
