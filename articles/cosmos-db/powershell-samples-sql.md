---
title: Azure PowerShell minták a Azure Cosmos DB-SQL (Core) API-hoz
description: Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB SQL API-fiókokban
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366189"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell minták a Azure Cosmos DB-SQL (Core) API-hoz

Az alábbi táblázat a Azure Cosmos DB for SQL (Core) API-hoz gyakran használt Azure PowerShell parancsfájlokra mutató hivatkozásokat tartalmaz. Ha szeretné összekapcsolni ezeket a PowerShell-mintákat a GitHub-adattárból Cosmos DBhoz, látogasson el [Cosmos db PowerShell-mintákat a githubon](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

További Cosmos DB PowerShell-minták az SQL (Core) API-hoz és a dokumentációhoz: [Azure Cosmos db SQL API-erőforrások kezelése a PowerShell használatával](manage-with-powershell.md). Más API-khoz Cosmos DB PowerShell-mintákat a következő témakörben talál: [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)és [Table API](powershell-samples-table.md).

> [!NOTE]
> A minták az [az. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használják. Vegye figyelembe, hogy `Az.CosmosDB` a parancsmagok még előzetes verzióban vannak, és a kiadás előtt változhatnak. Ellenőrizze, hogy rendszeresen frissülnek-e `Az.CosmosDB` a frissítések.

| | |
|---|---|
|[Fiók, adatbázis és tároló létrehozása](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, adatbázist és tárolót. |
|[Tároló létrehozása nagyméretű partíciós kulccsal](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy tárolót egy nagyméretű partíciós kulccsal. |
|[Adatbázisok vagy tárolók listázása és lekérése](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tárolók listázása vagy beolvasása. |
|[RU/s beolvasása](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy tárolóhoz tartozó RU/s beszerzése. |
|[RU/s frissítése](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy tárolóhoz tartozó RU/s frissítése. |
|[Index-szabályzat nélküli tároló létrehozása](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolót az index Policy kikapcsolva.|
|[Fiók frissítése](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók alapértelmezett konzisztencia-szintjének frissítése. |
|[Fiók régiói frissítése](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók régióinak frissítése. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos DB fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||
