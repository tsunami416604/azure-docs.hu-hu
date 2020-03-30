---
title: Tároló átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan építheti ki az átviteli teljesítményt a tároló szintjén az Azure Cosmos DB-ban az Azure Portal, a CLI, a PowerShell és számos más SDK használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273298"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Átviteli átbocsátás kiépítése egy Azure Cosmos-tárolón

Ez a cikk ismerteti, hogyan építheti ki az átviteli egy tároló (gyűjtemény, grafikon vagy tábla) az Azure Cosmos DB-ben. Átviteli szintet egyetlen tárolón, vagy [egy adatbázis-átviteli és](how-to-provision-database-throughput.md) az adatbázis tárolói között megosztását is kiépítheti. Az Azure Portal, az Azure CLI vagy az Azure Cosmos DB SDK-k használatával átviteli átbocsátást építhet ki egy tárolón.

## <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-sql-api-dotnet.md#create-account)vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új gyűjtemény**lehetőséget. Ezután adja meg a következő részleteket:

   * Adja meg, hogy új adatbázist hoz-e létre, vagy meglévőt használ.
   * Adjon meg egy tároló (vagy táblázat vagy grafikon) azonosítót.
   * Adjon meg egy partíciókulcs `/userid`értékét (például ) .
   * Adja meg a kiépíteni kívánt átviteli értéket (például 1000 További információit).
   * Válassza **az OK gombot.**

    ![Képernyőkép az Adatkezelőről, kiemelve az Új gyűjtemény](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI vagy PowerShell

Ha dedikált átviteli fokkal rendelkező tárolót szeretne létrehozni,

* [Tároló létrehozása az Azure CLI használatával](manage-with-cli.md#create-a-container)
* [Tároló létrehozása a PowerShell használatával](manage-with-powershell.md#create-container)

> [!Note]
> Ha egy Azure Cosmos-fiók ban egy Azure Cosmos-fiók a MongoDB-hoz `/myShardKey` konfigurált tárolóátviteli rendszer kiépítése, használja a partíciókulcs elérési útját. Ha egy Cassandra API-val konfigurált Azure Cosmos-fiók ban `/myPrimaryKey` egy tárolón létesít átviteli szintet, használja a partíciókulcs elérési útját.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> A Cosmos SDK-k az SQL API-hoz átviteli átviteli az összes Cosmos DB API-k, kivéve a Cassandra API-k.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL, MongoDB, Gremlin és Table API
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

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

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

Hasonló parancsok bármely CQL-kompatibilis illesztőprogramon keresztül kiadhatók.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Cassandra-tábla átviteli állapotának módosítása vagy módosítása

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogy miként építheti ki az átviteli erőket az Azure Cosmos DB-ben:

* [Az adatbázis átviteli átviteli sorának kiépítése](how-to-provision-database-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
