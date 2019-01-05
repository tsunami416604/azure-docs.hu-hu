---
title: Az Azure CLI-minták az Azure Cosmos DB használatával
description: Azure CLI-minták – Azure Cosmos DB-fiókok, -adatbázisok, -tárolók, -régiók és -tűzfalak létrehozása és kezelése.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a6348024d4e84c27610f1294f916cca9a851b6b9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034199"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-minták az Azure Cosmos DB-hez

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](/cli/azure/cosmosdb) között érheti el.

| |  |
|---|---|
|**Azure Cosmos DB-fiók, -adatbázis és -tárolók létrehozása**||
| [Hozzon létre egy Azure Cosmos DB-fiókot az SQL API használatával](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy egyetlen Azure Cosmos DB fiókot, adatbázist és tárolót. |
| [Hozzon létre egy Azure Cosmos DB-fiókot Cosmos DB API a mongodb-hez](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy egyetlen Azure Cosmos DB-fiókot, egy adatbázist és gyűjteményt. |
| [Hozzon létre egy Azure Cosmos DB-fiók Gremlin API használata](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy egyetlen Azure Cosmos DB fiókot, adatbázist és gráfot. |
| [Hozzon létre egy Azure Cosmos DB-fiókot, Cassandra API-val](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy egyetlen Azure Cosmos DB-fiókot és az adatbázis. |
| [Hozzon létre egy Azure Cosmos DB-fiók Table API-val](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egy egyetlen Azure Cosmos DB fiókot, adatbázist és táblát hoz létre. |
|**Az Azure Cosmos DB méretezése**||
| [A tároló átviteli sebességének méretezése](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Módosítja egy tároló kiosztott átviteli sebességét.|
| [Azure Cosmos DB-adatbázisfiók replikálása több régióban és a feladatátvételi prioritások konfigurálása](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globálisan és meghatározott feladatátvételi prioritás szerint replikál fiókadatokat több régióba.|
|**Az Azure Cosmos DB védelme**||
| [Fiókkulcsok lekérése](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekéri a fiók elsődleges és a másodlagos írási mesterkulcsát és elsődleges és másodlagos írásvédett kulcsát.|
| [A kapcsolati sztring lekérése mongodb-hez készült Azure Cosmos DB API-val konfigurált Cosmos-fiók](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Beolvassa a kapcsolati karakterláncot a MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-fiókot.|
| [Fiókkulcsok ismételt létrehozása](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Újból létrehozza a fiók kulcsait.|
| [Tűzfal létrehozása](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy bemeneti IP-címekre vonatkozó hozzáférés-vezérlési szabályzatot, hogy a fiók hozzáférését a jóváhagyott gépekre és/vagy felhőszolgáltatásokra korlátozza.|
|**Magas rendelkezésre állás, vészhelyreállítás, biztonsági mentés és visszaállítás**||
| [Feladatátvételi szabályzat konfigurálása](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Beállítja azon régiók feladatátvételi prioritását, amelyekbe a fiók replikálva van.|
|**Az Azure Cosmos DB csatlakoztatása erőforrásokhoz**||
| [Webalkalmazás csatlakoztatása az Azure Cosmos DB-hez](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB-adatbázis létrehozása és csatlakozása egy Azure-webalkalmazáshoz.|
|||
