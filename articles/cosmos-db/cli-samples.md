---
title: Azure CLI-minták a Azure Cosmos DBhoz
description: Azure CLI-minták – Azure Cosmos DB-fiókok, -adatbázisok, -tárolók, -régiók és -tűzfalak létrehozása és kezelése.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615570"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-minták az Azure Cosmos DB-hez

A következő táblázat az Azure Cosmos DB-hez elérhető Azure CLI-példaszkriptekre mutató hivatkozásokat tartalmaz. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](/cli/azure/cosmosdb) között érheti el.

| |  |
|---|---|
|**Azure Cosmos DB-fiók, -adatbázis és -tárolók létrehozása**||
| [Azure Cosmos DB fiók létrehozása az SQL API használatával](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Egyetlen Azure Cosmos DB fiókot, adatbázist és tárolót hoz létre. |
| [Azure Cosmos DB fiók létrehozása a MongoDB-hez készült Cosmos DB API-val](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB fiókot, adatbázist és gyűjteményt hoz létre. |
| [Azure Cosmos DB fiók létrehozása a Gremlin API-val](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB fiókot, adatbázist és gráfot hoz létre. |
| [Azure Cosmos DB fiók létrehozása a Cassandra API használatával](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB fiókot és adatbázist hoz létre. |
| [Azure Cosmos DB fiók létrehozása a Table API használatával](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Egyetlen Azure Cosmos DB fiókot, adatbázist és táblát hoz létre. |
|**Az Azure Cosmos DB méretezése**||
| [A tároló átviteli sebességének méretezése](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Módosítja egy tároló kiosztott átviteli sebességét.|
| [Azure Cosmos Database-fiók replikálása több régióban és a feladatátvételi prioritások konfigurálása](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globálisan és meghatározott feladatátvételi prioritás szerint replikál fiókadatokat több régióba.|
|**Az Azure Cosmos DB védelme**||
| [Fiókkulcsok lekérése](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekéri a fiók elsődleges és a másodlagos írási mesterkulcsát és elsődleges és másodlagos írásvédett kulcsát.|
| [A MongoDB-hez Azure Cosmos DB API-val konfigurált Cosmos-fiókhoz tartozó kapcsolatok karakterláncának beolvasása](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Lekérdezi a kapcsolati sztringet, hogy összekapcsolja a MongoDB alkalmazást a Azure Cosmos DB-fiókkal.|
| [Fiókkulcsok ismételt létrehozása](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Újból létrehozza a fiók kulcsait.|
| [Tűzfal létrehozása](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy bemeneti IP-címekre vonatkozó hozzáférés-vezérlési szabályzatot, hogy a fiók hozzáférését a jóváhagyott gépekre és/vagy felhőszolgáltatásokra korlátozza.|
|**Magas rendelkezésre állás, vészhelyreállítás, biztonsági mentés és visszaállítás**||
| [Feladatátvételi szabályzat konfigurálása](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Beállítja azon régiók feladatátvételi prioritását, amelyekbe a fiók replikálva van.|
|**Az Azure Cosmos DB csatlakoztatása erőforrásokhoz**||
| [Webalkalmazás csatlakoztatása az Azure Cosmos DB-hez](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Hozzon létre és csatlakoztasson egy Azure Cosmos-adatbázist és egy Azure-webalkalmazást.|
|||
