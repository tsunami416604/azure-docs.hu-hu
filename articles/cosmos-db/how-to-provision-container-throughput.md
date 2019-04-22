---
title: Tároló átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan oszthatja ki az átviteli sebességet tárolói szinten az Azure Cosmos DB-ben
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d092844fbd75fc2307cc13ec9b7779ae9237535d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679704"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló átviteli sebességet kiépítése

Ez a cikk bemutatja, hogyan helyezhet üzembe egy tárolót az Azure Cosmos DB (gyűjtemény, graph vagy tábla) átviteli sebességet. Akkor is kioszthatja az átviteli sebességet egy egyetlen tároló vagy [az adatbázisok kiépítése átviteli](how-to-provision-database-throughput.md) , és ossza meg az adatbázisban lévő tárolók között. Akkor is kioszthatja az átviteli sebességet egy tárolót az Azure portal, Azure CLI-vel vagy az Azure Cosmos DB SDK-k használatával.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure-Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új gyűjtemény**. Következő lépésként adja meg a következő adatokat:

   * Adja meg e létrehozásakor egy új adatbázist és egy meglévő használatával.
   * Adjon meg egy tároló (vagy táblázat vagy gráf).
   * Adja meg a partíciókulcs-értékkel (például `/userid`).
   * Adja meg a kívánt átviteli sebesség (például 1000 ru-k) üzembe helyezése.
   * Kattintson az **OK** gombra.

![Képernyőfelvétel az adatkezelő, kiemelve új gyűjteménnyel](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Átviteli sebesség kiosztása az Azure CLI használatával

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

Ha készül üzembe helyezni egy tárolót egy Azure Cosmos-fiókot az Azure Cosmos DB API a mongodb-hez konfigurálva az átviteli sebességet, `/myShardKey` a partíciós kulcs elérési útja. Ha készül üzembe helyezni egy tárolót a Cassandra API-val konfigurált egy Azure Cosmos-fiókot az átviteli sebességet, `/myPrimaryKey` a partíciós kulcs elérési útja.

## <a name="provision-throughput-by-using-net-sdk"></a>Kiépítés átviteli .NET SDK-val

> [!Note]
> A Cosmos SDK-kat használja SQL API-hoz való kiépítése átviteli sebesség az összes Cosmos DB API-k, kivéve a Cassandra API.

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

* [Átviteli sebesség az adatbázisok kiépítése](how-to-provision-database-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
