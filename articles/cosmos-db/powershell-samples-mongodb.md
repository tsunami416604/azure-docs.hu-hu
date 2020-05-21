---
title: Azure PowerShell minták a Azure Cosmos DB-MongoDB API-hoz
description: A Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB API-MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: c8e0a7a60a3512d19a1dfdfdb07b20e523ce7b92
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649713"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell minták a Azure Cosmos DB MongoDB API-hoz

A következő táblázat a MongoDB API-hoz Azure Cosmos DB Azure PowerShell parancsfájlokra mutató hivatkozásokat tartalmaz.

> [!NOTE]
> A PowerShell, a CLI és a Resource Manager-sablonok használatával jelenleg csak a 3,2-es verziót hozhatja létre (azaz a a végpontot használó fiókokat `*.documents.azure.com` Azure Cosmos db) a MongoDB-fiókokhoz. A fiókok 3,6-es verziójának létrehozásához használja a Azure Portal helyet.

> [!NOTE]
> A minták az [az. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használják. Ellenőrizze, hogy rendszeresen frissülnek-e a frissítések `Az.CosmosDB` .

| | |
|---|---|
|[Fiók, adatbázis és gyűjtemény létrehozása](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot,-adatbázist és-gyűjteményt. |
|[Adatbázisok vagy gyűjtemények listázása és lekérése](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gyűjtemény listázása vagy beolvasása. |
|[RU/s beolvasása](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy gyűjteményhez tartozó RU/s beszerzése. |
|[RU/s frissítése](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az RU/s frissítése egy adatbázishoz vagy gyűjteményhez. |
|[Fiók frissítése vagy régió hozzáadása](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Régió hozzáadása Cosmos-fiókhoz. A más fiók tulajdonságainak módosítására is használható, de a régiókban lévő módosításoktól el kell különíteni őket. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos-fiókhoz tartozó fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||
