---
title: Azure PowerShell minták a Azure Cosmos DB Core (SQL) API-hoz
description: Azure PowerShell minták beszerzése általános feladatok végrehajtásához Azure Cosmos DB for Core (SQL) API-ban
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 95b3bdf7c0f232655b4f64f3700579f3672eb3b8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341977"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Azure PowerShell minták a Azure Cosmos DB Core (SQL) API-hoz
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az alábbi táblázat a Azure Cosmos DB leggyakrabban használt Azure PowerShell parancsfájljaira mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB PowerShell-parancsmagra vonatkozó hivatkozási lapok a [Azure PowerShell-hivatkozásban](/powershell/module/az.cosmosdb)érhetők el. Ellenőrizze, hogy rendszeresen frissülnek-e a frissítések `Az.CosmosDB` . Ezeket a PowerShell-mintákat a GitHub-adattárból is letölthetik Cosmos DBhoz, [Cosmos db PowerShell-mintákat a githubon](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Más API-khoz készült PowerShell-parancsmagok esetében lásd: [PowerShell-minták a Cassandra](powershell-samples-cassandra.md), PowerShell-minták a [MongoDB API](powershell-samples-mongodb.md)-hoz, PowerShell-minták a [Gremlin](powershell-samples-gremlin.md), PowerShell-minták [táblázathoz](powershell-samples-table.md)

## <a name="common-samples"></a>Gyakori minták

|Feladat | Leírás |
|---|---|
|[Fiók frissítése](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók alapértelmezett konzisztencia-szintjének frissítése. |
|[Fiók régiói frissítése](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók régióinak frissítése. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos DB fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API-minták

|Feladat | Leírás |
|---|---|
|[Fiók, adatbázis és tároló létrehozása](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, adatbázist és tárolót. |
|[Hozzon létre egy fiókot, egy adatbázist és egy tárolót az autoscale](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, adatbázist és tárolót az autoscale paranccsal. |
|[Tároló létrehozása nagyméretű partíciós kulccsal](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy tárolót egy nagyméretű partíciós kulccsal. |
|[Index-szabályzat nélküli tároló létrehozása](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolót az index Policy kikapcsolva.|
|[Adatbázisok vagy tárolók listázása és lekérése](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tárolók listázása vagy beolvasása. |
|[Átviteli sebességi műveletek](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy tároló átviteli műveletei, beleértve az autoscale és a standard átviteli sebesség közötti Get, Update és Migrálás folyamatát. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
