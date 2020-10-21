---
title: Azure CLI-minták a Azure Cosmos DB Gremlin API-hoz
description: Azure CLI-minták a Azure Cosmos DB Gremlin API-hoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8aae65c2e1913480050a237ed7f2ec38e77e33f5
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276903"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>Azure CLI-minták a Azure Cosmos DB Gremlin API-hoz

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. A jobb oldalon található hivatkozásokkal navigáljon az API-specifikus mintákhoz. Az általános minták minden API esetében azonosak. Az összes Azure Cosmos DB CLI-parancsra vonatkozó hivatkozási lapok az [Azure CLI-referenciában](/cli/azure/cosmosdb)érhetők el. A CLI-szkriptek Azure Cosmos DB is megtalálhatók a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Ezek a minták az Azure CLI 2.12.1 vagy újabb verzióját igénylik. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető témakört.

## <a name="common-samples"></a>Gyakori minták

Ezek a minták az összes Azure Cosmos DB API-ra érvényesek

|Feladat | Leírás |
|---|---|
| [Hozzáadási vagy feladatátvételi régiók](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Régió hozzáadása, feladatátvételi prioritás módosítása, manuális feladatátvétel indítása.|
| [A fiókok kulcsai és a kapcsolatok karakterláncai](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Fiókok kulcsai, csak olvasható kulcsok listázása, kulcsok újragenerálása és a kapcsolatok listázása.|
| [Biztonságos IP-tűzfallal](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy olyan Cosmos-fiókot, amely konfigurálva van az IP-tűzfallal.|
| [Új fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy Cosmos-fiókot, és gondoskodjon a szolgáltatás-végpontok biztonságáról.|
| [Meglévő fiók biztonságossá tétele szolgáltatási végpontokkal](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Cosmos-fiók frissítése a szolgáltatás-végpontok biztonságossá tételéhez, ha az alhálózat végül konfigurálva van.|
|||

## <a name="gremlin-api-samples"></a>Gremlin API-minták

|Feladat | Leírás |
|---|---|
| [Azure Cosmos-fiók, adatbázis és gráf létrehozása](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és gráfot a Gremlin API-hoz. |
| [Azure Cosmos-fiók, adatbázis és gráf létrehozása az autoscalevel](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB fiókot, adatbázist és gráfot a Gremlin API-hoz. |
| [Átviteli sebességi műveletek](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Olvashatja, frissítheti és áttelepítheti az autoscale és a standard átviteli sebességét egy adatbázison és gráfon.|
| [Erőforrások zárolásának törlése a törlésből](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Az erőforrások törlésének megakadályozása az erőforrás-zárolásokkal.|
|||
