---
title: Azure PowerShell minták a Azure Cosmos DB-MongoDB API-hoz
description: A Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB API-MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: ca93b4fa85f5ef3ab87d108242e9cbfbd12036ab
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521863"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell minták a Azure Cosmos DB MongoDB API-hoz

A következő táblázat a MongoDB API-hoz Azure Cosmos DB Azure PowerShell parancsfájlokra mutató hivatkozásokat tartalmaz.

> [!NOTE]
> Jelenleg a PowerShell, a CLI és a Resource Manager-sablonok használatával csak a 3,2-es verziót hozhatja létre (azaz a `*.documents.azure.com`) Azure Cosmos DB API-ját használó MongoDB. A fiókok 3,6-es verziójának létrehozásához használja a Azure Portal helyet.

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
