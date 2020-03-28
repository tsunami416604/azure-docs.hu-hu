---
title: 'Azure Cosmos DB: .NET (Microsoft.Azure.Cosmos) példák az SQL API-hoz'
description: Keresse meg a C# .NET V3 SDK példákat a GitHubon az Azure Cosmos DB SQL API használatával végzett gyakori feladatokhoz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.openlocfilehash: 2a33a59ae0184e6c41fe7121560bc5df3a69cffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888953"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Az Azure Cosmos DB.NET V3 SDK (Microsoft.Azure.Cosmos) példák az SQL API-hoz

> [!div class="op_single_selector"]
> * [.](sql-api-dotnet-samples.md)
> * [.](sql-api-dotnet-v3sdk-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [Példák a Node.js](sql-api-nodejs-samples.md)
> * [Példák python](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

Az [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) GitHub-tárház tartalmazza a legújabb .NET mintamegoldásokat a CRUD és más gyakori műveletek végrehajtásához az Azure Cosmos DB-erőforrásokon. Ha ismeri a .NET SDK korábbi verzióját, akkor a kifejezések gyűjtéséhez és dokumentumhoz használható. Mivel az Azure Cosmos DB több API-modellt támogat, a .NET SDK 3.0-s verziója a "tároló" és az "elem" általános kifejezéseket használja. A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. Ez a cikk a következő információkat tartalmazza:

* Az egyes C#-projektfájlokban lévő feladatok hivatkozásai.
* A kapcsolódó API-referenciatartalmak hivatkozásai.

## <a name="prerequisites"></a>Előfeltételek

Visual Studio 2019 az Azure fejlesztési munkafolyamatával telepítve

- Letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

   A [Microsoft.Azure.cosmos NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Azure-előfizetés vagy ingyenes Cosmos DB próbafiók

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Aktiválhatja a Visual Studio előfizetői előnyeit:](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)A Visual Studio-előfizetése havonta krediteket ad, amelyeket fizetős Azure-szolgáltatásokhoz használhat.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> A minták önállóak, és maguk után vannak beállítva és takarítva. Minden előfordulás a tároló teljesítményszintjén egy órányi használatot számláz az előfizetésnek.
> 

## <a name="database-examples"></a>Adatbázis-példák

A minta *DatabaseManagement* projekt [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat. Ha többet szeretne megtudni az Azure Cosmos-adatbázisokról, mielőtt futtatna a következő mintákat, olvassa el az [Adatbázisok, tárolók és elemek munkája című témakört.](databases-containers-items.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [Adatbázis olvasása azonosító szerint](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Adatbázis.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [Fiók összes adatbázisának olvasása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [Adatbázis törlése](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>Példák tárolókra

A minta *ContainerManagement* projekt [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat. Ha többet szeretne megtudni az Azure Cosmos-tárolókról, mielőtt futtatna a következő mintákat, olvassa el az [Adatbázisok, tárolók és elemek munkája című témakört.](databases-containers-items.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Tároló létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Tároló létrehozása egyéni indexházirenddel](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Tároló konfigurált teljesítményének módosítása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [Tároló beszerezni azonosítóval](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Tároló.ReadContainerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [Az adatbázis összes tárolójának olvasása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [Tároló törlése](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>Példák elemekre

A minta *ItemManagement* projekt [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat. Ha többet szeretne megtudni az Azure Cosmos-elemekről, mielőtt futtatna a következő mintákat, olvassa el az [Adatbázisok, tárolók és elemek munkája című témakört.](databases-containers-items.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Elem létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Elem beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[Konténer. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Elemek lekérdezése](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[Konténer. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Elem cseréje](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[Konténer. ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [Upsert egy elem](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[Konténer. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [Elem törlése](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[Konténer. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [Elem lecserélése feltételes ETag-ellenőrzéssel](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>Indexelési példák

A minta *IndexManagement* projekt [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat. Ha többet szeretne tudni az Azure Cosmos DB indexeléséről a következő minták futtatása előtt, olvassa el az [indexszabályzatokat](index-policy.md), [az indextípusokat](index-types.md)és az [indexelérési utakat.](index-paths.md) 

| Tevékenység | API-referencia |
| --- | --- |
| [Elem kizárása az indexből](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Lusta indexelés használata](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexelésPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [Megadott elemelérési utak kizárása az indexből](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>Lekérdezéspéldák

A *mintalekérdezések* projekt [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat az SQL-lekérdezés nyelvtanával, a lekérdezéssel rendelkező LINQ-szolgáltatóval és a Lambda-val. Ha meg szeretné tudni, hogy az SQL-lekérdezésreferencia az Azure Cosmos DB-ben a következő minták futtatása előtt, tekintse meg az [SQL-lekérdezéspéldák at Az Azure Cosmos DB.](how-to-sql-query.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Elemek lekérdezése egyetlen partícióról](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[Konténer. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Elemek lekérdezése több partícióról](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[Konténer. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Lekérdezés SQL utasítással](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[Konténer. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>Változáscsatornák példái

A minta *ChangeFeed* projekt [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat. Ha többet szeretne tudni az Azure Cosmos DB változáscsatornájáról, mielőtt futtatna a következő mintákat, olvassa el az [Azure Cosmos DB módosítási hírcsatornájának olvasása](read-change-feed.md) és a Csatornaprocesszor módosítása című [témakört.](change-feed-processor.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Alapvető módosítási hírcsatorna-funkciók](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Módosítási hírcsatorna olvasása egy adott időpontból](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [A módosítási hírcsatorna elolvasása az elejétől](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.withStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet) |
| [A változáscsatorna-processzortól a V3 SDK-ban történő hírcsatorna módosításához](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák

A minta *ServerSideScripts* projekt [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) metódusa bemutatja, hogyan kell elvégezni a következő feladatokat. Ha többet szeretne megtudni az Azure Cosmos DB kiszolgálóoldali programozásáról, mielőtt futtatna a következő mintákat, olvassa el a [Tárolt eljárások, eseményindítók és a felhasználó által definiált függvények című témakört.](stored-procedures-triggers-udfs.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Parancsfájlok.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [Tárolt eljárás végrehajtása](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Parancsfájlok.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [Tárolt eljárás törlése](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Parancsfájlok.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |