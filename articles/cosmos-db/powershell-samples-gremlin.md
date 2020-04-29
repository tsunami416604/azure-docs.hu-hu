---
title: Azure PowerShell minták a Azure Cosmos DB-Gremlin API-hoz
description: Azure PowerShell minták beszerzése különböző gyakori feladatok végrehajtásához Azure Cosmos DB Gremlin API-fiókokban
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: d0deef4df6cfdcfdb2d549cee35892620cf6b791
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80365727"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Azure PowerShell minták a Azure Cosmos DB Gremlin API-hoz

A következő táblázat a Gremlin API-hoz Azure Cosmos DB Azure PowerShell parancsfájlokra mutató hivatkozásokat tartalmaz.

> [!NOTE]
> A minták az [az. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használják. Vegye figyelembe, hogy `Az.CosmosDB` a parancsmagok még előzetes verzióban vannak, és a kiadás előtt változhatnak. Ellenőrizze, hogy rendszeresen frissülnek-e `Az.CosmosDB` a frissítések.

| | |
|---|---|
|[Fiók, adatbázis és gráf létrehozása](scripts/powershell/gremlin/ps-gremlin-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, adatbázist és gráfot. |
|[Adatbázisok vagy diagramok listázása és lekérése](scripts/powershell/gremlin/ps-gremlin-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gráf listázása vagy beolvasása. |
|[RU/s beolvasása](scripts/powershell/gremlin/ps-gremlin-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy adatbázishoz vagy gráfhoz tartozó RU/s beszerzése. |
|[RU/s frissítése](scripts/powershell/gremlin/ps-gremlin-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az RU/s frissítése egy adatbázishoz vagy gráfhoz. |
|[Fiók frissítése vagy régió hozzáadása](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Régió hozzáadása Cosmos-fiókhoz. A más fiók tulajdonságainak módosítására is használható, de a régiókban lévő módosításoktól el kell különíteni őket. |
|[Feladatátvételi prioritás módosítása vagy feladatátvételi trigger](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók regionális feladatátvételi prioritásának módosítása vagy manuális feladatátvétel indítása. |
|[Fiók kulcsai vagy a kapcsolatok karakterláncai](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, a kapcsolatok karakterláncának beolvasása vagy egy Azure Cosmos-fiókhoz tartozó fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP-tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot, amelyen engedélyezve van az IP-tűzfal. |
|||
