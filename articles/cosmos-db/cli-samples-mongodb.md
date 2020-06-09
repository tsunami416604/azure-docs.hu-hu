---
title: Azure CLI-minták a Azure Cosmos DB MongoDB API-hoz
description: Azure CLI-minták a Azure Cosmos DB MongoDB API-hoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: af510e5d29e494e96f6df6143fe012db20fb6ca4
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509457"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure CLI-minták a Azure Cosmos DB MongoDB API-hoz

Az alábbi táblázat a Azure Cosmos DB MongoDB API-hoz készült Azure CLI-szkriptekre mutató hivatkozásokat tartalmaz. Az összes Azure Cosmos DB CLI-parancsra vonatkozó hivatkozási lapok az [Azure CLI-referenciában](/cli/azure/cosmosdb)érhetők el. A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.

> [!NOTE]
> A PowerShell, a CLI és a Resource Manager-sablonok használatával jelenleg csak a 3,2-es verziót hozhatja létre (azaz a a végpontot használó fiókokat `*.documents.azure.com` Azure Cosmos db) a MongoDB-fiókokhoz. A fiókok 3,6-es verziójának létrehozásához használja a Azure Portal helyet.

| |  |
|---|---|
| [Azure Cosmos-fiók, adatbázis és gyűjtemény létrehozása](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és gyűjteményt a MongoDB API-hoz. |
| [Átviteli sebesség módosítása](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Az RU/s frissítése egy adatbázison és gyűjteményen.|
| [Hozzáadási vagy feladatátvételi régiók](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Régió hozzáadása, feladatátvételi prioritás módosítása, manuális feladatátvétel indítása.|
| [A fiókok kulcsai és a kapcsolatok karakterláncai](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Fiókok kulcsai, csak olvasható kulcsok listázása, kulcsok újragenerálása és a kapcsolatok listázása.|
| [Biztonságos IP-tűzfallal](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy olyan Cosmos-fiókot, amely konfigurálva van az IP-tűzfallal.|
| [Új fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy Cosmos-fiókot, és gondoskodjon a szolgáltatás-végpontok biztonságáról.|
| [Meglévő fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Cosmos-fiók frissítése a szolgáltatás-végpontok biztonságossá tételéhez, ha az alhálózat végül konfigurálva van.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||
