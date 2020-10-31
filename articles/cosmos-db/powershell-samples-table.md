---
title: Azure PowerShell minták Azure Cosmos DB Table API
description: A Azure PowerShell minták beszerzése általános feladatok végrehajtásához Azure Cosmos DB Table API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 4594f1d3f33f3fc20bfc5582f58945e5382a7db8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096172"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Azure PowerShell minták Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Az alábbi táblázat a Azure Cosmos DB leggyakrabban használt Azure PowerShell parancsfájljaira mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB PowerShell-parancsmagra vonatkozó hivatkozási lapok a [Azure PowerShell-hivatkozásban](/powershell/module/az.cosmosdb)érhetők el. Ellenőrizze, hogy rendszeresen frissülnek-e a frissítések `Az.CosmosDB` . Ezeket a PowerShell-mintákat a GitHub-adattárból is letölthetik Cosmos DBhoz, [Cosmos db PowerShell-mintákat a githubon](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Gyakori minták

|Feladat | Leírás |
|---|---|
|[Fiók frissítése](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók alapértelmezett konzisztencia-szintjének frissítése. |
|[Fiók régiói frissítése](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB fiók régióinak frissítése. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos DB fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos DB fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||

## <a name="table-api-samples"></a>Table API minták

|Feladat | Leírás |
|---|---|
|[Fiók és tábla létrehozása](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és-táblát. |
|[Fiók és tábla létrehozása az autoscale paranccsal](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és egy tábla-méretezést. |
|[Táblázatok listázása és lekérése](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Táblázatok listázása vagy beolvasása. |
|[Átviteli sebességi műveletek](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy táblázat átviteli műveletei, beleértve a Get, a Update és az áttelepítés az autoscale és a standard átviteli sebesség között. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
