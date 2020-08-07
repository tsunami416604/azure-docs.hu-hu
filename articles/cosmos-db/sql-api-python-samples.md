---
title: SQL API Python-példák Azure Cosmos DB
description: A GitHubon található Python-példákat a Azure Cosmos DB gyakori feladataihoz, beleértve a szifilisz-műveleteket.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: devx-track-python
ms.openlocfilehash: 6602d757e210837aadadd431b27bb7cf76f06715
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873347"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-példák

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-példák](sql-api-java-sdk-samples.md)
> * [Node.js példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Az [Azure-documentdb-Python](https://github.com/Azure/azure-documentdb-python) GitHub-adattár tartalmazza a szifiliszi műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos egyéb gyakori műveleteket. Ez a cikk a következő információkat tartalmazza:

* A például szolgáló Python-projektfájlokban található feladatok hivatkozásai.
* A kapcsolódó API-referenciatartalmak hivatkozásai.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A [Python SDK](sql-api-sdk-python.md)-ra is szüksége lesz.

   > [!NOTE]
   > Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást adnak ki `CosmosClient.CreateContainer` . Minden alkalommal, amikor ez bekövetkezik, az előfizetése egy órányi használattal lesz terhelve. További információ az Azure Cosmos DB kiszámlázott tételeiről: [Azure Cosmos DB – díjszabás](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Adatbázis-példák

A [database_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata.

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient. create_database|
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient. get_database_client|
| [Adatbázisok lekérdezése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient. query_databases|
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient. list_databases|
| [Adatbázis törlése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient. delete_database|

## <a name="container-examples"></a>Példák tárolókra

A [container_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata.

| Tevékenység | API-referencia |
| --- | --- |
| [Tároló lekérdezése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |adatbázis. query_containers |
| [Tároló létrehozása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |adatbázis. create_container |
| [Egy adatbázis összes tárolójának listázása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |adatbázis. list_containers |
| [Tároló beszerzése azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |adatbázis. get_container_client |
| [Tároló kiépített átviteli sebességének kezelése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |Container. read_offer, tároló. replace_throughput|
| [Tároló törlése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |adatbázis. delete_container |

## <a name="item-examples"></a>Példák elemekre

A [item_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata.

| Tevékenység | API-referencia |
| --- | --- |
| [Elemek létrehozása egy tárolóban](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |tároló. create_item |
| [Elemek olvasása azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |tároló. read_item |
| [Egy tároló összes elemének olvasása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |tároló. read_all_items |
| [Egy tétel lekérdezése az azonosító alapján](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |tároló. query_items |
| [Elem cseréje](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |tároló. replace_items |
| [Upsert egy elemmel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |tároló. upsert_item |
| [Elem törlése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |tároló. delete_item |
| [Tárolóban lévő elemek módosítási csatornájának beolvasása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |tároló. query_items_change_feed |

## <a name="indexing-examples"></a>Indexelési példák

A [index_management.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) a Python-minta bemutatja, hogyan végezheti el a következő feladatokat. A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-types.md)és az [indexelési útvonalak](index-paths.md) fogalmi cikkei.

| Tevékenység | API-referencia |
| --- | --- |
| [Adott elem kizárása az indexelésből](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Manuális indexelés használata adott elemek indexeléséhez](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | dokumentumok. IndexingDirective. include |
| [Elérési utak kizárása az indexelésből](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |A tulajdonságban kizárandó elérési utak megadása `IndexingPolicy` |
| [Tartományindexek használata sztringeken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Az indexelési szabályzat definiálása a karakterlánc adattípusú tartomány-indexekkel. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Indexátalakítás végrehajtása](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |Database. replace_container (a frissített indexelési házirend használata)|
| [Vizsgálatok használata, ha csak a kivonatoló index létezik az elérési úton](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | a `enable_scan_in_query=True` és az `enable_cross_partition_query=True` elemek lekérdezésének beállítása |
