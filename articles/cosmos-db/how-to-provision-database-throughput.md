---
title: Adatbázis átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Tudnivalók az adatbázisszinten kiosztható átviteli sebességről az Azure Cosmos DB-ben
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: da56c06e215e02ee3eefe3d0552c962a8c59011e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683477"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Az Azure Cosmos DB-adatbázis kiépítése átviteli sebességet

Ez a cikk bemutatja, hogyan építheti ki az Azure Cosmos DB adatbázis átviteli sebességet. Az átviteli sebességet kioszthatja egyetlen [tárolóhoz](how-to-provision-container-throughput.md), vagy kioszthatja egy adatbázishoz, majd megoszthatja a benne található tárolók között. Ismerje meg, mikor érdemes használni a tároló-szintű és az adatbázisszintű átviteli sebesség, tekintse meg a [eseteinek átviteli sebesség, a tárolók és adatbázisok kiépítése](set-throughput.md) cikk. Adatbázis-szintű átviteli telepíthet az Azure portal vagy az Azure Cosmos DB SDK-k használatával.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

### <a id="portal-sql"></a>SQL (Core) API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure-Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új adatbázis**. Adja meg a következő adatokat:

   * Adjon meg egy adatbázist. 
   * Válassza ki **kiépítése átviteli**.
   * Adjon meg egy átviteli sebesség (például 1000 ru-k).
   * Kattintson az **OK** gombra.

![Képernyőfelvétel az új adatbázis párbeszédpanel](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

> [!Note]
> Segítségével Cosmos SDK-k SQL API üzembe helyezése átviteli sebesség az összes API-k. Igény szerint használhatja az alábbi példa a Cassandra API-hoz is.

### <a id="dotnet-all"></a>Minden API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>További lépések

További információ az Azure Cosmos DB kiosztott átviteli sebesség a következő cikkekben talál:

* [Globális skálázása a kiosztott átviteli sebesség](scaling-throughput.md)
* [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Átviteli sebesség kiosztása tárolókhoz](how-to-provision-container-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)