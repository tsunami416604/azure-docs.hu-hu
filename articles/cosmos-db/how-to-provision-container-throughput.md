---
title: Tároló átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan oszthatja ki az átviteli sebességet tárolói szinten az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 28060637db47b42db66f706815066d498032ec11
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258710"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló átviteli sebességet kiépítése

Ez a cikk bemutatja, hogyan helyezhet üzembe egy tárolót (gyűjteményt, graph vagy tábla) az Azure Cosmos DB az átviteli sebességet. Is kioszthatja az átviteli sebesség az egyetlen tároló, vagy [adatbázis kiépítése](how-to-provision-database-throughput.md) , és ossza meg az adatbázisban lévő tárolók között. Is kioszthatja az átviteli sebességet egy tárolóhoz az Azure Portalon, az Azure CLI-vel vagy az Azure Cosmos DB SDK-k használatával.

## <a name="provision-throughput-by-using-azure-portal"></a>Kiépítés átviteli sebesség az Azure portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos DB-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új gyűjtemény**. Következő lépésként adja meg a következő adatokat:

   * Adja meg e létrehozásakor egy új adatbázist és egy meglévő használatával.
   * Adjon meg egy gyűjtemény azonosítója (vagy táblázat vagy gráf).
   * Adja meg a partíciókulcs-értékkel (például `/userid`).
   * Adjon meg egy átviteli sebesség (például 1000 ru-k).
   * Kattintson az **OK** gombra.

![Képernyőfelvétel az adatkezelő, kiemelve új gyűjteménnyel](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Azure CLI-vel üzembe helyezése átviteli

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Ha szeretne kiépíteni az Azure Cosmos DB-fiókot, az Azure Cosmos DB API a mongodb-hez konfigurálva az átviteli sebesség, `/myShardKey` a partíciós kulcs elérési útja. Ha szeretne kiépíteni az Azure Cosmos DB-fiókot, Cassandra API-hoz konfigurált átviteli sebesség, `/myPrimaryKey` a partíciós kulcs elérési útja.

## <a name="provision-throughput-by-using-net-sdk"></a>Kiépítés átviteli .NET SDK-val

> [!Note]
> Az SQL API használatával a Cassandra API kivételével minden API-hoz kioszthat átviteli sebességet.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin és Table API

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>További lépések

További információ az átviteli sebesség kiépítése az Azure Cosmos DB a következő cikkekben talál:

* [Átviteli sebesség kiosztása adatbázisokhoz](how-to-provision-database-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
