---
title: Tároló átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan helyezhet üzembe átviteli sebességet a Azure Cosmos DB tároló szintjén a Azure Portal, a CLI, a PowerShell és a különböző egyéb SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: bfd18a009ed9ab5edffafcd0327b1d846cae02d8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75643848"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Átviteli sebesség kiépítése Azure Cosmos-tárolón

Ez a cikk azt ismerteti, hogyan lehet átviteli sebességet kiépíteni egy tárolón (gyűjtemény, gráf vagy tábla) Azure Cosmos DBban. Az átviteli sebességet egyetlen tárolón is kiépítheti, vagy az [átviteli sebességet kiépítheti egy adatbázisba](how-to-provision-database-throughput.md) , és megoszthatja azt az adatbázisban lévő tárolók között. Az átviteli sebességet Azure Portal, Azure CLI vagy Azure Cosmos DB SDK-k használatával is kiépítheti egy tárolóba.

## <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új gyűjtemény**lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló (vagy a tábla vagy a gráf) AZONOSÍTÓját.
   * Adja meg a partíciós kulcs értékét (például `/userid`).
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a Adatkezelőről, új gyűjtemény kiemelve](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI vagy PowerShell

Tároló létrehozása dedikált átviteli sebességgel:

* [Tároló létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-container)
* [Tároló létrehozása a PowerShell használatával](manage-with-powershell.md#create-container)

> [!Note]
> Ha a MongoDB Azure Cosmos DB API-val konfigurált Azure Cosmos-fiókban lévő tárolón való kiépítési átviteli sebességre van konfigurálva, használja a `/myShardKey` a partíciós kulcs elérési útjához. Ha az átviteli sebességet egy Cassandra API konfigurált Azure Cosmos-fiókban lévő tárolóban szeretné kiépíteni, használja a `/myPrimaryKey` a partíciós kulcs elérési útjához.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Az SQL API-hoz készült Cosmos SDK-k segítségével az összes Cosmos DB API-t kiépítheti az Cassandra API kivételével.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin és Table API
### <a name="net-v2-sdk"></a>.NET v2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a id="dotnet-cassandra"></a>Cassandra API

Hasonló parancsok a CQL-kompatibilis illesztőprogramokon keresztül is kiállíthatók.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>A Cassandra Table adatátviteli sebessége módosítható vagy módosítható

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet a Azure Cosmos DBban:

* [Adatátviteli kapacitás kiépítése egy adatbázison](how-to-provision-database-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
