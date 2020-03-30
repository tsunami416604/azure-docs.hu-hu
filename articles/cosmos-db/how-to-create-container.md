---
title: Tároló létrehozása az Azure Cosmos DB-ben
description: Megtudhatja, hogyan hozhat létre tárolót az Azure Cosmos DB-ben az Azure Portal, a .Net, a Java, a Python, a Node.js és más SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873709"
---
# <a name="create-an-azure-cosmos-container"></a>Hozzon létre egy Azure Cosmos-tárolót

Ez a cikk ismerteti az Azure Cosmos-tároló (gyűjtemény, tábla vagy grafikon) létrehozásának különböző módjait. Ehhez használhatja az Azure Portalt, az Azure CLI-t vagy a támogatott SDK-kat. Ez a cikk bemutatja, hogyan hozhat létre egy tárolót, adja meg a partíciókulcsot, és a kiépítési átviteli.

## <a name="create-a-container-using-azure-portal"></a>Tároló létrehozása az Azure Portal használatával

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-sql-api-dotnet.md#create-account)vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új tároló**lehetőséget. Ezután adja meg a következő részleteket:

   * Adja meg, hogy új adatbázist hoz-e létre, vagy meglévőt használ.
   * Adja meg a tároló azonosítóját.
   * Adja meg a partíciókulcsot.
   * Adjon meg egy kiépítendő átviteli értéket (például 1000 További 1000.1000.U.).
   * Válassza **az OK gombot.**

    ![Képernyőkép az Adatkezelő ablaktábláról, kiemelve az Új tároló](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-mongodb-dotnet.md#create-a-database-account)vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új tároló**lehetőséget. Ezután adja meg a következő részleteket:

   * Adja meg, hogy új adatbázist hoz-e létre, vagy meglévőt használ.
   * Adja meg a tároló azonosítóját.
   * Adja meg a szegmenskulcsot.
   * Adjon meg egy kiépítendő átviteli értéket (például 1000 További 1000.1000.U.).
   * Válassza **az OK gombot.**

    ![Képernyőkép a MongoDB Azure Cosmos DB API-járól, a Tároló hozzáadása párbeszédpanelről](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-cassandra-dotnet.md#create-a-database-account)vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új tábla**lehetőséget. Ezután adja meg a következő részleteket:

   * Adja meg, hogy új kulcsteret hoz-e létre, vagy meglévőt használ.
   * Adja meg egy tábla nevét.
   * Adja meg a tulajdonságokat, és adjon meg egy elsődleges kulcsot.
   * Adjon meg egy kiépítendő átviteli értéket (például 1000 További 1000.1000.U.).
   * Válassza **az OK gombot.**

    ![Képernyőkép a Cassandra API, Tábla hozzáadása párbeszédpanelről](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> A Cassandra API esetében a rendszer az elsődleges kulcsot használja partíciókulcsként.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin API

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-graph-dotnet.md#create-a-database-account)vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új diagram**lehetőséget. Ezután adja meg a következő részleteket:

   * Adja meg, hogy új adatbázist hoz-e létre, vagy meglévőt használ.
   * Adja meg a grafikonazonosítót.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adja meg a csúcspontok partíciókulcsát.
   * Adjon meg egy kiépítendő átviteli értéket (például 1000 További 1000.1000.U.).
   * Válassza **az OK gombot.**

    ![Képernyőkép a Gremlin API,Diagram hozzáadása párbeszédpanelről](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>Tábla API

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-table-dotnet.md#create-a-database-account)vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és válassza az **Új tábla**lehetőséget. Ezután adja meg a következő részleteket:

   * Adja meg a táblaazonosítót.
   * Adjon meg egy kiépítendő átviteli értéket (például 1000 További 1000.1000.U.).
   * Válassza **az OK gombot.**

    ![Képernyőkép: Tábla API, Tábla hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> A Table API esetében a rendszer minden új sor hozzáadásakor megad egy partíciókulcsot.

## <a name="create-a-container-using-azure-cli"></a>Tároló létrehozása az Azure CLI használatával<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Az alábbi hivatkozások bemutatják, hogyan hozhat létre tárolóerőforrásokat az Azure Cosmos DB számára az Azure CLI használatával.

Az Azure Cosmos db API-k összes Azure CLI-mintalistájának listázását az [SQL API,](cli-samples.md) [a Cassandra API,](cli-samples-cassandra.md)a [MongoDB API,](cli-samples-mongodb.md)a [Gremlin API](cli-samples-gremlin.md)és a Table API [tartalmazza.](cli-samples-table.md)

* [Tároló létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-container)
* [Gyűjtemény létrehozása az Azure Cosmos DB-hez a MongoDB API-hoz az Azure CLI-vel](./scripts/cli/mongodb/create.md)
* [Cassandra-tábla létrehozása az Azure CLI-vel](./scripts/cli/cassandra/create.md)
* [Gremlin-grafikon létrehozása az Azure CLI-vel](./scripts/cli/gremlin/create.md)
* [Table API-tábla létrehozása az Azure CLI-vel](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Tároló létrehozása a PowerShell használatával<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Az alábbi hivatkozások bemutatják, hogyan hozhat létre tárolóerőforrásokat az Azure Cosmos DB-hez a PowerShell használatával.

Az Azure Cosmos db API-k összes Azure CLI-mintalistájának listázását az [SQL API,](powershell-samples-sql.md) [a Cassandra API,](powershell-samples-cassandra.md)a [MongoDB API,](powershell-samples-mongodb.md)a [Gremlin API](powershell-samples-gremlin.md)és a Table API [tartalmazza.](powershell-samples-table.md)

* [Tároló létrehozása a Powershell használatával](manage-with-powershell.md#create-container)
* [Gyűjtemény létrehozása az Azure Cosmos DB-hez a MongoDB API-hoz a Powershell segítségével](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Cassandra-tábla létrehozása powershellnel](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Gremlin-grafikon létrehozása a Powershell segítségével](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Table API-tábla létrehozása powershellnel](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Tároló létrehozása .NET SDK használatával

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL API és Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> A MongoDB vezetékes protokoll jattot nem érti a [kérésegységek fogalmát.](request-units.md) Új gyűjtemény létrehozásához a kiépített átviteli rajta, használja az Azure Portalon vagy a Cosmos DB SDK-k az SQL API-hoz.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)
