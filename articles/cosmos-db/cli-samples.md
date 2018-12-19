---
title: Az Azure CLI-minták az Azure Cosmos DB használatával
description: Azure CLI-minták – Azure Cosmos DB-fiókok, -adatbázisok, -tárolók, -régiók és -tűzfalak létrehozása és kezelése.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 97518f1121628d045cb4b252a10b06d8c1c5f830
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582223"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-minták az Azure Cosmos DB-hez

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](/cli/azure/cosmosdb) között érheti el.

| |  |
|---|---|
|**Azure Cosmos DB-fiók, -adatbázis és -tárolók létrehozása**||
| [Hozzon létre egy Azure Cosmos DB-fiók: Az SQL API-val](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Egyetlen Azure Cosmos DB SQL API-fiókot, -adatbázist és -tárolót hoz létre. |
| [Hozzon létre egy Azure Cosmos DB-fiók: API a mongodb-hez](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egy egyetlen Azure Cosmos DB API-fiókot hoz létre MongoDB, egy adatbázist és gyűjteményt. |
| [Hozzon létre egy Azure Cosmos DB-fiók: Gremlin API-val](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB Gremlin API-fiókot, -adatbázist és -grafikont hoz létre. |
| [Hozzon létre egy Azure Cosmos DB-fiók: Cassandra API-val](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB Cassandra API-fiókot és -adatbázist hoz létre. |
| [Hozzon létre egy Azure Cosmos DB-fiók: Tábla API](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB Table API-fiókot, -adatbázist és -táblázatot hoz létre. |
|**Az Azure Cosmos DB méretezése**||
| [A tároló átviteli sebességének méretezése](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Módosítja egy tároló kiosztott átviteli sebességét.|
| [Azure Cosmos DB-adatbázisfiók replikálása több régióban és a feladatátvételi prioritások konfigurálása](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globálisan és meghatározott feladatátvételi prioritás szerint replikál fiókadatokat több régióba.|
|**Az Azure Cosmos DB védelme**||
| [Fiókkulcsok lekérése](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekéri a fiók elsődleges és a másodlagos írási mesterkulcsát és elsődleges és másodlagos írásvédett kulcsát.|
| [Az Azure Cosmos DB API kapcsolati karakterláncának lekérése MongoDB-hez](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Beolvassa a kapcsolati karakterláncot a MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-fiókot.|
| [Fiókkulcsok ismételt létrehozása](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Újból létrehozza a fiók kulcsait.|
| [Tűzfal létrehozása](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy bemeneti IP-címekre vonatkozó hozzáférés-vezérlési szabályzatot, hogy a fiók hozzáférését a jóváhagyott gépekre és/vagy felhőszolgáltatásokra korlátozza.|
|**Magas rendelkezésre állás, vészhelyreállítás, biztonsági mentés és visszaállítás**||
| [Feladatátvételi szabályzat konfigurálása](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Beállítja azon régiók feladatátvételi prioritását, amelyekbe a fiók replikálva van.|
|**Az Azure Cosmos DB csatlakoztatása erőforrásokhoz**||
| [Webalkalmazás csatlakoztatása az Azure Cosmos DB-hez](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB-adatbázis létrehozása és csatlakozása egy Azure-webalkalmazáshoz.|
|||
