---
title: Adatbázis átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Megtudhatja, hogyan építheti ki az átviteli teljesítményt az Azure Cosmos DB adatbázisszintjén az Azure Portal, a CLI, a PowerShell és számos más SDK használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933769"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Átviteli adatszolgáltatás kiépítése egy adatbázison az Azure Cosmos DB-ben

Ez a cikk ismerteti, hogyan építheti ki az átviteli áteresztőátviteli egy adatbázis az Azure Cosmos DB-ben. Egy [tárolóvagy](how-to-provision-container-throughput.md)egy adatbázis átviteli átalódása, és megoszthatja az átviteli szintet a tárolók között belül. Ha meg szeretné tudni, hogy mikor kell használni a tárolók szintje és az adatbázis szintű átviteli, tekintse meg a [Használati esetek a tárolók és adatbázisok átviteli keresztülezési](set-throughput.md) cikket. Az Azure Portal vagy az Azure Cosmos DB SDK-k használatával adatbázisszintű átviteli.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

### <a name="sql-core-api"></a><a id="portal-sql"></a>SQL (Core) API

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-sql-api-dotnet.md#create-account)vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új adatbázis**lehetőséget. Adja meg a következő adatokat:

   * Adja meg az adatbázis azonosítóját.
   * Válassza **a Kiépítési átviteli átbocsátás lehetőséget.**
   * Adjon meg egy átviteli értéket (például 1000 További 1000-et).
   * Válassza **az OK gombot.**

    ![Az Új adatbázis párbeszédpanel képernyőképe](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Átviteli teljesítmény kiépítése az Azure CLI vagy a PowerShell használatával

Ha megosztott átviteli hatós adatbázist szeretne létrehozni, lásd:

* [Adatbázis létrehozása az Azure CLI használatával](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Adatbázis létrehozása a Powershell használatával](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

> [!Note]
> A Cosmos SDK-k sql API-k az összes API-k átviteli. A Cassandra API-hoz a következő példát is használhatja.

### <a name="all-apis"></a><a id="dotnet-all"></a>Minden API

### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API
Hasonló parancs bármely CQL-kompatibilis illesztőprogramon keresztül végrehajtható. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogy miként készült átviteli a kitágított átviteli mertét az Azure Cosmos DB-ben:

* [Kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Átviteli sebesség kiosztása tárolókhoz](how-to-provision-container-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
