---
title: Tároló létrehozása az Azure Cosmos DB-ben
description: Tudnivalók a tárolók Azure Cosmos DB-ben történő létrehozásáról
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 9805ff9aa4932c262db13c47fd2e442b3d3d676f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811729"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos-tároló létrehozása

Ez a cikk az Azure Cosmos-tárolók (gyűjtemény, tábla vagy gráf) létrehozásának különböző módszereit ismerteti. Ehhez használhatja a Azure Portal, az Azure CLI vagy a támogatott SDK-kat is. Ez a cikk bemutatja, hogyan hozhat létre tárolót, hogyan adhatja meg a partíciós kulcsot, és hogyan lehet kiépíteni az átviteli sebességet.

## <a name="create-a-container-using-azure-portal"></a>Tároló létrehozása az Azure Portal használatával

### <a id="portal-sql"></a>SQL API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló**elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adjon meg egy partíciós kulcsot.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a Adatkezelő panelről, új tároló kiemelve](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>MongoDB Azure Cosmos DB API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló**elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adja meg a szegmens kulcsát.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a MongoDB Azure Cosmos DB API-ról, tároló hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-cassandra-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla**lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új lemezterületet hoz létre, vagy egy meglévőt használ.
   * Adja meg egy tábla nevét.
   * Adja meg a tulajdonságokat, és adjon meg egy elsődleges kulcsot.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a Cassandra APIről, tábla hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> A Cassandra API esetében a rendszer az elsődleges kulcsot használja partíciókulcsként.

### <a id="portal-gremlin"></a>Gremlin API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-graph-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új gráf**lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a gráf AZONOSÍTÓját.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adja meg a csúcspontok partíciós kulcsát.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a Gremlin API-ról, gráf hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-table-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla**lehetőséget. Ezután adja meg a következő adatokat:

   * Adja meg a tábla AZONOSÍTÓját.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    ![Képernyőkép a Table APIről, tábla hozzáadása párbeszédpanel](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> A Table API esetében a rendszer minden új sor hozzáadásakor megad egy partíciókulcsot.

## Tároló létrehozása az Azure CLI-vel<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Az alábbi hivatkozások bemutatják, hogyan hozhat létre Azure Cosmos DB tároló-erőforrásokat az Azure CLI használatával.

Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [SQL API](cli-samples.md), [CASSANDRA API](cli-samples-cassandra.md), [MongoDB api](cli-samples-mongodb.md), [Gremlin API](cli-samples-gremlin.md)és [Table API](cli-samples-table.md)

* [Tároló létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-container)
* [Azure Cosmos DB gyűjtemény létrehozása a MongoDB API-hoz az Azure CLI-vel](/scripts/cli/mongodb/create.md)
* [Cassandra-tábla létrehozása az Azure CLI-vel](/scripts/cli/cassandra/create.md)
* [Gremlin gráf létrehozása az Azure CLI-vel](/scripts/cli/gremlin/create.md)
* [Table API tábla létrehozása az Azure CLI-vel](/scripts/cli/table/create.md)

## Tároló létrehozása a PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"> használatával<a id="ps-table"></a>

Az alábbi hivatkozások bemutatják, hogyan hozhat létre Azure Cosmos DB tároló-erőforrásokat a PowerShell használatával.

Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [SQL API](powershell-samples-sql.md), [CASSANDRA API](powershell-samples-cassandra.md), [MongoDB api](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)és [Table API](powershell-samples-table.md)

* [Tároló létrehozása a PowerShell-lel](manage-with-powershell.md#create-container)
* [Azure Cosmos DB gyűjtemény létrehozása a MongoDB API-hoz a PowerShell-lel](/scripts/powershell/mongodb/ps-mongodb-create.md)
* [Cassandra-tábla létrehozása a PowerShell-lel](/scripts/powershell/cassandra/ps-cassandra-create.md)
* [Gremlin gráf létrehozása a PowerShell-lel](/scripts/powershell/gremlin/ps-gremlin-create.md)
* [Table API tábla létrehozása a PowerShell-lel](/scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Tároló létrehozása .NET SDK használatával

### <a id="dotnet-sql-graph"></a>SQL API és Gremlin API

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

### <a id="dotnet-mongodb"></a>MongoDB Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> A MongoDB huzal protokoll nem ismeri a [kérelmek egységének](request-units.md)fogalmát. Ha olyan új gyűjteményt szeretne létrehozni, amely számára kiépített átviteli sebességgel rendelkezik, használja a Azure Portal vagy Cosmos DB SDK-kat az SQL API-hoz.

### <a id="dotnet-cassandra"></a>Cassandra API

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
* [Adatforgalom kiépítése a tárolók és adatbázisok számára](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)
