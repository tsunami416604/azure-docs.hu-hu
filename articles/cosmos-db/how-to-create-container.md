---
title: Tároló létrehozása az Azure Cosmos DB-ben
description: Tudnivalók a tárolók Azure Cosmos DB-ben történő létrehozásáról
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/17/2019
ms.author: rimman
ms.openlocfilehash: c075a801a877309709258dd6466e68e46d802eff
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680434"
---
# <a name="create-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló létrehozása

Ez a cikk ismerteti a különböző módon hozhat létre egy Azure Cosmos-tárolóhoz (gyűjtemény, táblázat vagy gráf). Azure Portallal, Azure CLI-vel, vagy a támogatott SDK-k a. Ez a cikk bemutatja, hogyan hozzon létre egy tárolót, adja meg a partíciókulcs és oszthatnak ki átviteli kapacitásokat.

## <a name="create-a-container-using-azure-portal"></a>Tároló létrehozása az Azure Portal használatával

### <a id="portal-sql"></a>SQL API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új gyűjtemény**. Következő lépésként adja meg a következő adatokat:

   * Adja meg e létrehozásakor egy új adatbázist és egy meglévő használatával.
   * Adjon meg egy gyűjteményt.
   * Adjon meg egy partíciókulcsot.
   * Adjon meg egy átviteli (például 1000 kérelemegység) ki kell építeni.
   * Kattintson az **OK** gombra.

![Képernyőfelvétel az adatkezelő panelen az új gyűjtemény kiemelésével](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Az Azure Cosmos DB API a mongodb-hez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új gyűjtemény**. Következő lépésként adja meg a következő adatokat:

   * Adja meg e létrehozásakor egy új adatbázist és egy meglévő használatával.
   * Adjon meg egy gyűjteményt.
   * Adja meg a szegmenskulccsal.
   * Adjon meg egy átviteli (például 1000 kérelemegység) ki kell építeni.
   * Kattintson az **OK** gombra.

![Képernyőkép az Azure Cosmos DB API a mongodb-hez, gyűjtemény hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-cassandra-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új tábla**. Következő lépésként adja meg a következő adatokat:

   * Azt jelzik, hogy létrehozásakor egy új kulcsteret, és egy meglévő használatával.
   * Adja meg egy tábla nevét.
   * Adja meg a tulajdonságokat, és adja meg az elsődleges kulcsot.
   * Adjon meg egy átviteli (például 1000 kérelemegység) ki kell építeni.
   * Kattintson az **OK** gombra.

![Képernyőkép a Cassandra API, táblázat hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> A Cassandra API esetében a rendszer az elsődleges kulcsot használja partíciókulcsként.

### <a id="portal-gremlin"></a>Gremlin API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-graph-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új gráf**. Következő lépésként adja meg a következő adatokat:

   * Adja meg, hogy létrehozásakor egy új adatbázist, és egy meglévő használatával.
   * Adja meg a gráf azonosítóját.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adja meg a csúcspontok partíciókulcsot.
   * Adjon meg egy átviteli (például 1000 kérelemegység) ki kell építeni.
   * Kattintson az **OK** gombra.

![Képernyőkép a Gremlin API, gráf hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-table-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új tábla**. Következő lépésként adja meg a következő adatokat:

   * Adjon meg egy táblát.
   * Adjon meg egy átviteli (például 1000 kérelemegység) ki kell építeni.
   * Kattintson az **OK** gombra.

![Képernyőfelvétel: a Table API, táblázat hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> A Table API esetében a rendszer minden új sor hozzáadásakor megad egy partíciókulcsot.

## <a name="create-a-container-using-azure-cli"></a>Tároló létrehozása az Azure CLI használatával

### <a id="cli-sql"></a>SQL API

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

### <a id="cli-cassandra"></a>Cassandra API

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

### <a id="cli-gremlin"></a>Gremlin API

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

### <a id="cli-table"></a>Table API

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

### <a id="dotnet-sql-graph"></a>SQL API és Gremlin API

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
> MongoDB átviteli protokoll nem tudja értelmezni a fogalmát [kérelemegység](request-units.md). Hozzon létre új gyűjteményt, a kiosztott átviteli sebesség, használja az Azure portal vagy a Cosmos DB SDK-k SQL API-hoz.

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>További lépések

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Az Azure Cosmos DB-kérésegységeiről](request-units.md)
- [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
- [Az Azure Cosmos-fiók használata](account-overview.md)
