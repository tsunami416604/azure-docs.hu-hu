---
title: SQL API Python-példák Azure Cosmos DB
description: A GitHubon található Python-példákat a Azure Cosmos DB gyakori feladataihoz, beleértve a szifilisz-műveleteket.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1e7c3e06c8aa49f9a23d3cc600ee6f60218c2fbd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72170136"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-példák

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [Node. js-példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [Azure-documentdb-Python](https://github.com/Azure/azure-documentdb-python) GitHub-adattár tartalmazza a szifiliszi műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos egyéb gyakori műveleteket. Ez a cikk a következő információkat tartalmazza:

* A például szolgáló Python-projektfájlokban található feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A [Python SDK](sql-api-sdk-python.md)-ra is szüksége lesz. 
   
   > [!NOTE]
   > Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást adnak ki a [CosmosClient. CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-). Minden alkalommal, amikor ez bekövetkezik, az előfizetése egy órányi használattal lesz terhelve. További információ az Azure Cosmos DB kiszámlázott tételeiről: [Azure Cosmos DB – díjszabás](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Adatbázis-példák
A [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) -projekt [program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) -fájlja bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient. CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createdatabase-database--options-none-) |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabase-database-link--options-none-) |
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabases-options-none-) |
| [Adatbázis törlése](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient. Deletedatabase metódust](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Gyűjtemény-példák
A [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) -projekt [program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) -fájlja bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient. CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) |
| [Az egy adatbázisban lévő összes gyűjtemény listájának olvasása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainers-database-link--options-none-) |
| [Gyűjtemény beolvasása azonosító alapján](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainer-collection-link--options-none-) |
| [Gyűjtemény átviteli sebességének megváltoztatása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceoffer-offer-link--offer-)|
| [Gyűjtemény törlése](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient. DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Dokumentumpéldák
A [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) -projekt [program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) -fájlja bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Dokumentumgyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Dokumentum olvasása ID (azonosító) alapján](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitem-document-link--options-none-) |
| [Egy gyűjteményben lévő összes dokumentum beolvasása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitems-collection-link--feed-options-none-) |
| [Dokumentum cseréje feltételes ETag-ellenőrzéssel](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Indexelési példák
A [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) -projekt [program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) -fájlja bemutatja, hogyan végezheti el a következő feladatokat.  A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-types.md)és az [indexelési útvonalak](index-paths.md) fogalmi cikkei. 

| Tevékenység | API-referencia |
| --- | --- |
| [Manuális indexelés használata (automatikus helyett)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Automatikus indexelési házirend |
| [Adott dokumentum-útvonalak kizárása az indexből](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Házirend indexelése kizárt elérési utakkal|
| [Dokumentum kizárása az indexből](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Indexelési mód beállítása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Tartományindexek használata sztringeken](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Az indexelési házirend belefoglalt elérési utakkal|
| [Indexátalakítás végrehajtása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Lekérdezéspéldák
A minta projektek a következő lekérdezési feladatokat is mutatják be. Ha a következő minták futtatása előtt szeretne többet megtudni a Azure Cosmos DB SQL-lekérdezési referenciáról, tekintse meg az [SQL-lekérdezési példák](how-to-sql-query.md) fogalmi cikkét. Ha a következő minták futtatása előtt szeretne többet megtudni a Azure Cosmos DB SQL-lekérdezési referenciáról, tekintse meg az [SQL-lekérdezési példák](how-to-sql-query.md) fogalmi cikkét. 


| Tevékenység | API-referencia |
| --- | --- |
| [Fiók lekérdezése egy adatbázishoz](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#querydatabases-query--options-none-) |
| [Dokumentumok lekérdezése](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Tartományvizsgálati művelet kikényszerítése kivonatolt indexelt elérési útvonalon](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

