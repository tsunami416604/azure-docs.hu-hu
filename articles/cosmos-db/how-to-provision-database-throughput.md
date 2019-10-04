---
title: Adatbázis átviteli sebességének kiosztása Azure Cosmos DB-ben
description: Tudnivalók az adatbázisszinten kiosztható átviteli sebességről az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 93961b44f1c0d063774395ab384cb84b1aa05d99
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812491"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Átviteli sebesség kiépítése egy adatbázisba Azure Cosmos DB

Ez a cikk azt ismerteti, hogyan lehet adatátvitelt kiépíteni egy adatbázison Azure Cosmos DB. Az átviteli sebességet kioszthatja egyetlen [tárolóhoz](how-to-provision-container-throughput.md), vagy kioszthatja egy adatbázishoz, majd megoszthatja a benne található tárolók között. Ha meg szeretné tudni, hogy mikor kell használni a tároló-és az adatbázis-szintű átviteli sebességet, tekintse meg az [átviteli sebesség használata a tárolókban és az adatbázisokban](set-throughput.md) című cikket. Az adatbázis-szint átviteli sebességét az Azure Portal vagy a Azure Cosmos DB SDK-k használatával is kiépítheti.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

### <a id="portal-sql"></a>SQL (Core) API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új adatbázis**lehetőséget. Adja meg a következő adatokat:

   * Adja meg az adatbázis AZONOSÍTÓját.
   * Válassza ki az **átviteli sebesség**lehetőséget.
   * Adja meg az átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép az új adatbázis párbeszédpanelről](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Átviteli sebesség kiépítése az Azure CLI vagy a PowerShell használatával

Megosztott átviteli sebességgel rendelkező adatbázis létrehozása:

* [Adatbázis létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Adatbázis létrehozása a PowerShell használatával](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

> [!Note]
> A Cosmos SDK-k használatával az SQL API-hoz kiépítheti az átviteli sebességet az összes API-hoz. Az alábbi példát is használhatja Cassandra APIhoz is.

### <a id="dotnet-all"></a>Minden API

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

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet Azure Cosmos DBban:

* [Kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Adatforgalom kiépítése a tárolók és adatbázisok számára](set-throughput.md)
* [Átviteli sebesség kiosztása tárolókhoz](how-to-provision-container-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
