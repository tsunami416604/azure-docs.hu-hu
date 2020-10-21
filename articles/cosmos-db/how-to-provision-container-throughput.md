---
title: Tároló átviteli sebességének kiépítése Azure Cosmos DB SQL API-ban
description: Megtudhatja, hogyan építhet ki átviteli sebességet Azure Cosmos DB SQL API tároló szintjén a Azure Portal, a CLI, a PowerShell és a különböző egyéb SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a6855a1c730c33a835e5033041ee7978be28fc6b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278666"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Standard (manuális) átviteli sebesség kiépítése egy Azure Cosmos-tárolón – SQL API

Ez a cikk azt ismerteti, hogyan lehet szabványos (manuális) átviteli sebességet kiépíteni egy Azure Cosmos DB SQL API-beli tárolón. Az átviteli sebességet egyetlen tárolón is kiépítheti, vagy az [átviteli sebességet kiépítheti egy adatbázisba](how-to-provision-database-throughput.md) , és megoszthatja azt az adatbázisban lévő tárolók között. Az átviteli sebességet Azure Portal, Azure CLI vagy Azure Cosmos DB SDK-k használatával is kiépítheti egy tárolóba.

Ha más API-t használ, tekintse meg a [MongoDB API](how-to-provision-throughput-mongodb.md)-t, [CASSANDRA API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -cikkeket az átviteli sebesség kiépítéséhez.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló**elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adja meg a partíciós kulcs értékét (például: `/ItemID` ).
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Képernyőkép a Adatkezelőről, új gyűjtemény kiemelve":::

## <a name="azure-cli-or-powershell"></a>Azure CLI vagy PowerShell

Tároló létrehozása dedikált átviteli sebességgel:

* [Tároló létrehozása az Azure CLI használatával](manage-with-cli.md#create-a-container)
* [Tároló létrehozása a PowerShell használatával](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Az SQL API-hoz készült Cosmos SDK-k segítségével az összes Cosmos DB API-ra kiépítheti az átviteli sebességet, a Cassandra és a MongoDB API kivételével.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

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

## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet a Azure Cosmos DBban:

* [A standard (manuális) átviteli sebesség kiépítése egy adatbázison](how-to-provision-database-throughput.md)
* [Az autoskálázási teljesítmény kiépítése egy adatbázison](how-to-provision-autoscale-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
