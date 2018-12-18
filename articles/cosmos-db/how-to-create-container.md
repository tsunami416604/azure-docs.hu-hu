---
title: Tároló létrehozása az Azure Cosmos DB-ben
description: Tudnivalók a tárolók Azure Cosmos DB-ben történő létrehozásáról
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: c1f78421fd431ca6a1aeab9f6147a3cca936cf9b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539755"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Tároló létrehozása az Azure Cosmos DB-ben

Ebben a cikkben a tárolók (gyűjtemény, táblázat, gráf) létrehozásának különböző módjaival ismerkedhet meg. Az Azure Portal, az Azure CLI vagy a támogatott SDK-k használatával is létrehozhat tárolókat. Ez a cikk bemutatja a tárolók létrehozásának, a partíciókulcsok megadásának és az átviteli sebesség kiosztásának módját.

## <a name="create-a-container-using-azure-portal"></a>Tároló létrehozása az Azure Portal használatával

### <a id="portal-sql"></a>Az Azure Cosmos DB API-t, SQL (mag)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-sql-api-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új gyűjtemény** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Hozzon létre egy új adatbázist, vagy használjon egy meglévőt.
   * Adja meg egy gyűjtemény azonosítóját.
   * Adjon meg egy partíciókulcsot.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![Az SQL API létrehoz egy gyűjteményt](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Az Azure Cosmos DB API a mongodb-hez

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új gyűjtemény** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Hozzon létre egy új adatbázist, vagy használjon egy meglévőt.
   * Adja meg egy gyűjtemény azonosítóját.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adjon meg egy szegmenskulcsot.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![Az Azure Cosmos DB API a MongoDB-gyűjteményt hoz létre.](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Az Azure Cosmos DB API-hoz a Cassandra

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-cassandra-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új tábla** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Hozzon létre egy új kulcsteret, vagy használjon egy meglévőt.
   * Adja meg egy tábla nevét.
   * Adja meg a tulajdonságokat és egy ELSŐDLEGES KULCSOT.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![A Cassandra API létrehoz egy gyűjteményt](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> A Cassandra API esetében a rendszer az elsődleges kulcsot használja partíciókulcsként.

### <a id="portal-gremlin"></a>A Gremlin az Azure Cosmos DB API-hoz

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-graph-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új gráf** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Hozzon létre egy új adatbázist, vagy használjon egy meglévőt.
   * Adjon meg egy gráfazonosítót.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adjon meg egy partíciókulcsot a csúcspontokhoz.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![A Gremlin API létrehoz egy gyűjteményt](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Az Azure Cosmos DB API táblához

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-table-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új tábla** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Adjon meg egy táblaazonosítót.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![A Table API létrehoz egy gyűjteményt](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> A Table API esetében a rendszer minden új sor hozzáadásakor megad egy partíciókulcsot.

## <a name="create-a-container-using-azure-cli"></a>Tároló létrehozása az Azure CLI használatával

### <a id="cli-sql"></a>Az Azure Cosmos DB API-t, SQL (mag)

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>Az Azure Cosmos DB API a mongodb-hez

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Az Azure Cosmos DB API-hoz a Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>A Gremlin az Azure Cosmos DB API-hoz

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Az Azure Cosmos DB API táblához

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Tároló létrehozása .NET SDK használatával

### <a id="dotnet-sql-graph"></a>Az SQL és Gremlin az Azure Cosmos DB API-hoz

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>Az Azure Cosmos DB API a mongodb-hez

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> A MongoDB-ben nem létezik a kérelemegységek fogalma. Egy átviteli sebességgel rendelkező új gyűjtemény létrehozásához használja az Azure Portalt vagy az SQL API-t az előző példáknak megfelelően.

### <a id="dotnet-cassandra"></a>Az Azure Cosmos DB API-hoz a Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>További lépések

A Cosmos DB-ben történő particionálásról a következő cikkekben talál további információt:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
