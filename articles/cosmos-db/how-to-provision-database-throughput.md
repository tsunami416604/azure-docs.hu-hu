---
title: Adatbázis átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Tudnivalók az adatbázisszinten kiosztható átviteli sebességről az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: c648522e689c64de8e7e09b85ca3b6eb26b6945b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477199"
---
# <a name="provision-throughput-on-an-azure-cosmos-database"></a>Egy Azure Cosmos-adatbázis kiépítése átviteli sebességet

Ez a cikk azt ismerteti, hogy hogyan oszthatja ki az átviteli sebességet egy adatbázishoz az Azure Cosmos DB-ben. Az átviteli sebességet kioszthatja egyetlen [tárolóhoz](how-to-provision-container-throughput.md), vagy kioszthatja egy adatbázishoz, majd megoszthatja a benne található tárolók között. Az átviteli sebességet az Azure Portal vagy a Cosmos DB SDK használatával oszthatja ki adatbázisszinten.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

### <a id="portal-sql"></a>SQL (Core) API

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. [Hozzon létre egy új Cosmos DB-fiókot](create-sql-api-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új adatbázis** lehetőséget. Töltse ki az űrlapot a következő értékekkel:

   * Adjon meg egy adatbázis-azonosítót. 
   * Válassza ki az Átviteli sebesség kiosztása elemet.
   * Adjon meg egy átviteli sebességet, például 1000 kérelemegységet.
   * Kattintson az **OK** gombra.

![SQL API – adatbázis átviteli sebességének kiosztása](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

> [!Note]
> Az SQL API használatával minden API-hoz kioszthat átviteli sebességet. Az alábbi példát igény szerint a Cassandra API esetében is használhatja.

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

Az átviteli sebesség Cosmos DB-ben történő kiosztásáról a következő cikkekben talál további információt:

* [Átviteli sebesség kiosztása tárolókhoz](how-to-provision-container-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
