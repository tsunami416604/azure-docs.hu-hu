---
title: Azure PowerShell minták a Azure Cosmos DB-SQL (Core) API-hoz
description: Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB SQL API-fiókokban
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 29195ce141c29248840807b978cae3ecf79ccf34
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871924"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell minták a Azure Cosmos DB-SQL (Core) API-hoz

Az alábbi táblázat a Azure Cosmos DB for SQL (Core) API-hoz gyakran használt Azure PowerShell parancsfájlokra mutató hivatkozásokat tartalmaz. Ha szeretné összekapcsolni ezeket a PowerShell-mintákat a GitHub-tárház-látogatás Cosmos DBához, [Cosmos db PowerShell-mintákat a githubon](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

További Cosmos DB PowerShell-minták az SQL (Core) API-hoz és a dokumentációhoz: [Azure Cosmos db SQL API-erőforrások kezelése a PowerShell használatával](manage-with-powershell.md). Más API-khoz Cosmos DB PowerShell-mintákat lásd: [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)és [Table API](powershell-samples-table.md).

| | |
|---|---|
|[Fiók, adatbázis és tároló létrehozása](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot,-adatbázist és-tárolót. |
|[Tároló létrehozása nagyméretű partíciós kulccsal](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy tárolót egy nagyméretű partíciós kulccsal. |
|[Adatbázisok vagy tárolók listázása és lekérése](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tárolók listázása vagy beolvasása. |
|[RU/s beolvasása](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy tárolóhoz tartozó RU/s beszerzése. |
|[RU/s frissítése](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy tárolóhoz tartozó RU/s frissítése. |
|[Index-szabályzat nélküli tároló létrehozása](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolót az index Policy kikapcsolva.|
|[Fiók frissítése vagy régió hozzáadása](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Régió hozzáadása Cosmos-fiókhoz. A más fiók tulajdonságainak módosítására is használható, de a régiókban lévő módosításoktól el kell különíteni őket. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos-fiókhoz tartozó fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||
