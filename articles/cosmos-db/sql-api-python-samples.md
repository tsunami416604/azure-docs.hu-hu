---
title: SQL API Python-példák az Azure Cosmos DB-ben | Microsoft Docs
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Python-példákat.
keywords: python-példák
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 2eb585876f91526633fb2844e67ba734648b38e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389536"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-példák

> [!div class="op_single_selector"]
> * [.NET-példák](sql-api-dotnet-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [Node.js-példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure-kódminták katalógusa](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub-adattárban találhatók olyan mintamegoldások, amelyek CRUD- és egyéb általános műveleteket végeznek Azure Cosmos DB-erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* A például szolgáló Python-projektfájlokban található feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek jóváírást, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A [Python SDK](sql-api-sdk-python.md)-ra is szüksége lesz. 
   
   > [!NOTE]
   > Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást intéznek a [document_client.CreateCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient#createcollection) felé. Minden alkalommal, amikor ez bekövetkezik, az előfizetése egy órányi használattal lesz terhelve. További információ az Azure Cosmos DB kiszámlázott tételeiről: [Azure Cosmos DB – díjszabás](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Adatbázispéldák
A [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) projekt [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) fájlja bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createdatabase) |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdatabase) |
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdatabases) |
| [Adatbázis törlése](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#deletedatabase) |

## <a name="collection-examples"></a>Gyűjteménypéldák
A [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) projekt [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) fájlja bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createcollection) |
| [Egy adatbázisban lévő összes gyűjtemény listájának beolvasása](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readcollections) |
| [Gyűjtemény lekérése azonosító alapján](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readcollection) |
| [Gyűjtemény átviteli sebességének megváltoztatása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#replaceoffer)|
| [Gyűjtemény törlése](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#deletecollection) |

## <a name="document-examples"></a>Dokumentumpéldák
A [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) projekt [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) fájlja bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createdocument) |
| [Dokumentumgyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python) |
| [Dokumentum beolvasása azonosító alapján](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdocument) |
| [Egy gyűjteményben lévő összes dokumentum beolvasása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdocuments) |

## <a name="indexing-examples"></a>Indexelési példák
Az [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) projekt [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) fájlja bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Manuális indexelés használata (automatikus helyett)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](https://docs.microsoft.com/python/api/pydocumentdb.documents?view=azure-python) |
| [Adott dokumentum-útvonalak kizárása az indexből](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Dokumentum kizárása az indexből](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Indexelési mód beállítása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingmode?view=azure-python) |
| [Tartományindexek használata sztringeken](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Indexátalakítás végrehajtása](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#replacecollection) |

## <a name="query-examples"></a>Lekérdezéspéldák
A mintaprojektek azt is bemutatják, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázisfiók lekérdezése](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#querydatabases) |
| [Dokumentumok lekérdezése](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#querydocuments) |
| [Tartományvizsgálati művelet kikényszerítése kivonatolt indexelt elérési útvonalon](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](https://docs.microsoft.com/python/api/pydocumentdb.http_constants.httpheaders?view=azure-python#enablescaninquery) |
