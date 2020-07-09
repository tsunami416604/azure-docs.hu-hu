---
title: Azure PowerShell minták a Azure Cosmos DB-Gremlin API-hoz
description: Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB Gremlin API-fiókokban
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 62036a507061a956febb1eb63e6607abcce7467c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563882"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Azure PowerShell minták a Azure Cosmos DB Gremlin API-hoz

A következő táblázat a Gremlin API-hoz Azure Cosmos DB Azure PowerShell parancsfájlokra mutató hivatkozásokat tartalmaz.

> [!NOTE]
> A minták az [az. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használják. Ellenőrizze, hogy rendszeresen frissülnek-e a frissítések `Az.CosmosDB` .

|Tevékenység | Leírás |
|---|---|
|[Fiók, adatbázis és gráf létrehozása](scripts/powershell/gremlin/ps-gremlin-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, adatbázist és gráfot. |
|[Adatbázisok vagy diagramok listázása és lekérése](scripts/powershell/gremlin/ps-gremlin-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gráf listázása vagy beolvasása. |
|[RU/s beolvasása](scripts/powershell/gremlin/ps-gremlin-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy gráfhoz tartozó RU/s beszerzése. |
|[RU/s frissítése](scripts/powershell/gremlin/ps-gremlin-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az RU/s frissítése egy adatbázishoz vagy gráfhoz. |
|[Fiók frissítése vagy régió hozzáadása](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Régió hozzáadása Cosmos-fiókhoz. A más fiók tulajdonságainak módosítására is használható, de a régiókban lévő módosításoktól el kell különíteni őket. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos-fiókhoz tartozó fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot, amelyen engedélyezve van az IP-tűzfal. |
|[Erőforrások zárolásának törlése a törlésből](scripts/powershell/gremlin/powershell-gremlin-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal. |
|||
