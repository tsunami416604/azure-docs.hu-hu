---
title: Adatbázis átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan építhet ki adatátviteli sebességet Azure Cosmos DB adatbázis szintjén a Azure Portal, a CLI, a PowerShell és a különböző egyéb SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: cc89c0052097ba9250370334386943ec4107140c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655325"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db"></a>Standard (manuális) átviteli sebesség kiépítése a Azure Cosmos DB található adatbázison

Ez a cikk azt ismerteti, hogyan lehet szabványos (manuális) átviteli sebességet kiépíteni egy Azure Cosmos DB található adatbázison. Az átviteli sebességet egyetlen [tárolóhoz](how-to-provision-container-throughput.md)vagy egy adatbázishoz is kiépítheti, és megoszthatja az átviteli sebességet a tárolóban lévő tárolók között. A tárolók szintjének és az adatbázis szintű átviteli sebesség használatának megismeréséhez tekintse [meg a tárolók és adatbázisok átviteli sebességének használati esetei](set-throughput.md) című cikket. Az adatbázis-szint átviteli sebességét az Azure Portal vagy a Azure Cosmos DB SDK-k használatával is kiépítheti.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

### <a name="sql-core-api"></a><a id="portal-sql"></a>SQL (Core) API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új adatbázis**lehetőséget. Adja meg a következő adatokat:

   * Adja meg az adatbázis AZONOSÍTÓját.
   * Válassza ki az **átviteli sebesség**lehetőséget.
   * Adja meg az átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    ![Képernyőkép az új adatbázis párbeszédpanelről](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Átviteli sebesség kiépítése az Azure CLI vagy a PowerShell használatával

Megosztott átviteli sebességgel rendelkező adatbázis létrehozása:

* [Adatbázis létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Adatbázis létrehozása a PowerShell használatával](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

> [!Note]
> A Cosmos SDK-k használatával az SQL API-hoz kiépítheti az átviteli sebességet az összes API-hoz. Az alábbi példát is használhatja Cassandra APIhoz is.

### <a name="all-apis"></a><a id="dotnet-all"></a>Minden API

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

Hasonló parancs bármely CQL-kompatibilis illesztőprogrammal végrehajtható.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet Azure Cosmos DBban:

* [Kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [A standard (manuális) átviteli sebesség kiépítése egy tárolóhoz](how-to-provision-container-throughput.md)
* [Az autoscale átviteli sebességének kiépítése egy tárolóra](how-to-provision-autoscale-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
