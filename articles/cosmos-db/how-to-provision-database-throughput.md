---
title: Adatbázis átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Tudnivalók az adatbázisszinten kiosztható átviteli sebességről az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: c0a947c9cf2c1d4a11d310b9bab0774a70534a1e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259431"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Átviteli sebesség kiosztása adatbázisokhoz az Azure Cosmos DB-ben

Ez a cikk azt ismerteti, hogy hogyan oszthatja ki az átviteli sebességet egy adatbázishoz az Azure Cosmos DB-ben. Az átviteli sebességet kioszthatja egyetlen [tárolóhoz](how-to-provision-container-throughput.md), vagy kioszthatja egy adatbázishoz, majd megoszthatja a benne található tárolók között. Ismerje meg, mikor érdemes használni a tároló-szintű és az adatbázisszintű átviteli sebesség, tekintse meg a [eseteinek átviteli sebesség, a tárolók és adatbázisok kiépítése](set-throughput.md) cikk. Adatbázis-szintű átviteli telepíthet az Azure portal vagy az Azure Cosmos DB SDK-k használatával.

## <a name="provision-throughput-by-using-azure-portal"></a>Kiépítés átviteli sebesség az Azure portal használatával

### <a id="portal-sql"></a>SQL (Core) API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos DB-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** panelre, és válassza **új adatbázis**. Adja meg a következő adatokat:

   * Adjon meg egy adatbázist. 
   * Válassza ki **kiépítése átviteli**.
   * Adjon meg egy átviteli sebesség (például 1000 ru-k).
   * Kattintson az **OK** gombra.

![Képernyőfelvétel az új adatbázis párbeszédpanel](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>Kiépítés átviteli .NET SDK-val

> [!Note]
> Az SQL API használatával minden API-hoz kioszthat átviteli sebességet. Igény szerint használhatja az alábbi példa a Cassandra API-hoz is.

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

További információ a kiépítése az Azure Cosmos DB átviteli sebesség a következő cikkekben talál:

* [Átviteli sebesség kiosztása tárolókhoz](how-to-provision-container-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
