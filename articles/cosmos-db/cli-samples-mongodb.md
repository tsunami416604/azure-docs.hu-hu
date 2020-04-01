---
title: Azure CLI-minták az Azure Cosmos DB MongoDB API-hoz
description: Azure CLI-minták az Azure Cosmos DB MongoDB API-hoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77524563"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure CLI-minták az Azure Cosmos DB MongoDB API-hoz

Az alábbi táblázat az Azure Cosmos DB MongoDB API-hoz az Azure CLI-parancsfájlok mintájára mutató hivatkozásokat tartalmaz. Az Azure Cosmos DB CLI összes parancsának referencialapjai az [Azure CLI-referenciadokumentumban](/cli/azure/cosmosdb)érhetők el. Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)

> [!NOTE]
> Jelenleg csak 3.2-es verziót (azaz a végpontot `*.documents.azure.com`használó, formátumú fiókokat) hozhat létre az Azure Cosmos DB MongoDB-fiókokapi-jának PowerShell-, CLI- és Erőforrás-kezelő-sablonok használatával. A fiókok 3.6-os verziójának létrehozásához használja inkább az Azure Portalt.

| |  |
|---|---|
| [Hozzon létre egy Azure Cosmos-fiókot, -adatbázist és -gyűjteményt](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure Cosmos DB-fiókot, adatbázist és gyűjteményt a MongoDB API-hoz. |
| [Átviteli átmenő legapróbábban](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | A RU/ok frissítése egy adatbázisban és gyűjteményben.|
| [Feladatátvételi területek hozzáadása vagy átvétele](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adjon hozzá egy régiót, módosítsa a feladatátvételi prioritást, manuális feladatátvételt kezdeményez.|
| [Fiókkulcsok és kapcsolati karakterláncok](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | List fiókkulcsok, írásvédett kulcsok, újragenerálja a kulcsokat és a lista kapcsolati karakterláncok.|
| [Biztonságos IP-tűzfallal](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy Cosmos-fiókot az IP-tűzfal konfigurálásával.|
| [Új fiók biztonságossá tétele szolgáltatásvégekkel](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Hozzon létre egy Cosmos-fiókot, és a szolgáltatás-végpontok biztonságos.|
| [Meglévő fiók biztonságossá tétele szolgáltatásvégpontokkal](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Frissítsen egy Cosmos-fiókot a szolgáltatás-végpontok biztonságossá tétele érdekében, ha az alhálózat végül konfigurálva van.|
|||
