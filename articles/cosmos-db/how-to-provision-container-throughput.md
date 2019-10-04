---
title: Tároló átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan oszthatja ki az átviteli sebességet tárolói szinten az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 8da27773cc74324c1dde5a95de1abef3256c1f1c
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811679"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Átviteli sebesség kiépítése Azure Cosmos-tárolón

Ez a cikk azt ismerteti, hogyan lehet átviteli sebességet kiépíteni egy tárolón (gyűjtemény, gráf vagy tábla) Azure Cosmos DBban. Az átviteli sebességet egyetlen tárolón is kiépítheti, vagy az [átviteli sebességet kiépítheti egy adatbázisba](how-to-provision-database-throughput.md) , és megoszthatja azt az adatbázisban lévő tárolók között. Az átviteli sebességet Azure Portal, Azure CLI vagy Azure Cosmos DB SDK-k használatával is kiépítheti egy tárolóba.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új gyűjtemény**lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló (vagy a tábla vagy a gráf) AZONOSÍTÓját.
   * Adja meg a partíciós kulcs értékét (például `/userid`).
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a Adatkezelőről, új gyűjtemény kiemelve](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Átviteli sebesség kiépítése az Azure CLI vagy a PowerShell használatával

Tároló létrehozása dedikált átviteli sebességgel:

* [Tároló létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-container)
* [Tároló létrehozása a PowerShell használatával](manage-with-powershell.md#create-container)

> [!Note]
> Ha a MongoDB Azure Cosmos DB API-val konfigurált Azure Cosmos-fiókban lévő tárolón való kiépítési átviteli sebességre van konfigurálva, használja a `/myShardKey` értéket a partíciós kulcs elérési útjához. Ha az átviteli sebességet egy Cassandra API konfigurált Azure Cosmos-fiókban lévő tárolóban szeretné kiépíteni, használja a `/myPrimaryKey` értéket a partíciós kulcs elérési útjához.

## <a name="provision-throughput-by-using-net-sdk"></a>Átviteli sebesség kiépítése a .NET SDK használatával

> [!Note]
> Az SQL API-hoz készült Cosmos SDK-k segítségével az összes Cosmos DB API-t kiépítheti az Cassandra API kivételével.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin és Table API
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

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>További lépések

A következő cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet a Azure Cosmos DBban:

* [Adatátviteli kapacitás kiépítése egy adatbázison](how-to-provision-database-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
