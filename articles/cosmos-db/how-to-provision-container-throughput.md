---
title: Tároló átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan oszthatja ki az átviteli sebességet tárolói szinten az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 550201e692bb79197d50c2f44017c43ab9ea2016
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477328"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló átviteli sebességet kiépítése

Ez a cikk ismerteti, hogyan oszthatja ki az átviteli sebességet egy (gyűjtemény, gráf vagy táblázat típusú) tároló számára az Azure Cosmos DB-ben. Az átviteli sebességet kioszthatja egyetlen tárolóhoz, vagy [kioszthatja egy adatbázishoz](how-to-provision-database-throughput.md), majd megoszthatja az adatbázisban található tárolók között. A tárolók átviteli sebességét az Azure Portal, az Azure CLI vagy Cosmos DB SDK-k használatával is kioszthatja.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-sql-api-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új gyűjtemény** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Hozzon létre egy új adatbázist, vagy használjon egy meglévőt.
   * Adja meg egy gyűjtemény azonosítóját (illetve egy táblázatét vagy gráfét).
   * Adja meg például a partíciókulcs-értékkel, `/userid`.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![SQL API – tároló átviteli sebességének kiosztása](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

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

Átviteli sebességet egy Cosmos-fiókot konfigurálni az Azure Cosmos DB API a mongodb-hez szeretne kiépíteni, ha használja a "/ myShardKey" a partíciós kulcs elérési útja és átviteli sebességet egy Cosmos-fiókot, Cassandra API-hoz konfigurált üzembe helyezésekor, használja a(z) / myPrimaryKey "az a partíciós kulcs elérési útja.

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

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

Az átviteli sebesség Cosmos DB-ben történő kiosztásáról a következő cikkekben talál további információt:

* [Átviteli sebesség kiosztása adatbázisokhoz](how-to-provision-database-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
