---
title: Azure Cosmos DB-Table API Azure PowerShell minták
description: Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB Table API-fiókokban
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 751fa9daabaaef05abb5df8229e84975b87cffc1
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563824"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---table-api"></a>Azure Cosmos DB-Table API Azure PowerShell minták

A következő táblázat hivatkozásokat tartalmaz a Table API Azure Cosmos DBhoz tartozó Azure PowerShell parancsfájlokra.

> [!NOTE]
> A minták az [az. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használják. Ellenőrizze, hogy rendszeresen frissülnek-e a frissítések `Az.CosmosDB` .

|Tevékenység | Leírás |
|---|---|
|[Fiók és tábla létrehozása](scripts/powershell/table/ps-table-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot és-táblát. |
|[Táblázatok listázása és lekérése](scripts/powershell/table/ps-table-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Táblázatok listázása vagy beolvasása. |
|[RU/s beolvasása](scripts/powershell/table/ps-table-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A táblához tartozó RU/s beolvasása. |
|[RU/s frissítése](scripts/powershell/table/ps-table-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy táblához tartozó RU/s frissítése. |
|[Fiók frissítése vagy régió hozzáadása](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Régió hozzáadása Cosmos-fiókhoz. A más fiók tulajdonságainak módosítására is használható, de a régiókban lévő módosításoktól el kell különíteni őket. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos-fiókhoz tartozó fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot, amelyen engedélyezve van az IP-tűzfal. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/table/powershell-table-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
