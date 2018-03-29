---
title: 'Az Azure Cosmos DB: Java-alapú példa az SQL API-hoz |} Microsoft Docs'
description: Java-alapú példa a Githubon található gyakori feladatok a Azure Cosmos DB SQL API-t, beleértve a CRUD műveletek.
keywords: NoSQL-példa
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: java
ms.assetid: d824d517-903e-4d82-ab0a-09fc3b984c84
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 992451018baeea15bf63906c71ad72faccb0dbe4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Az Azure Cosmos DB: Java példák az SQL API-hoz.

> [!div class="op_single_selector"]
> * [.NET-példák](sql-api-dotnet-samples.md)
> * [Java-alapú példa](sql-api-java-samples.md)
> * [NODE.js-példák](sql-api-nodejs-samples.md)
> * [Python példák](sql-api-python-samples.md)
> * [A minta Azure Kódgalériából.](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

A legújabb alkalmazásokat CRUD műveletek és más olyan gyakori műveleteket Azure Cosmos DB erőforrások végző szerepelnek a [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub-tárházban. Ez a cikk a következő információkat tartalmazza:

* A példa Java mindegyikében feladatokra mutató hivatkozásokat projektfájlok. 
* A kapcsolódó API mutató hivatkozások tartalom hivatkozik.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Is [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetéssel biztosít Önnek krediteket minden hónapban, fizetős Azure-szolgáltatásokat is használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Futtassa a mintaalkalmazást a következőkre lesz szüksége:

* Java fejlesztői készlet 7
* Microsoft Azure DocumentDB Java SDK

Maven segítségével a Microsoft Azure DocumentDB Java SDK legújabb bináris fájlok parancsok használja a projekt nem kötelező. Maven automatikusan hozzáadja a szükséges függőségek. Ellenkező esetben közvetlenül letöltheti a pom.xml fájlban felsorolt függőségei és adja hozzá a build elérési útját.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**A minta-alkalmazások futtatása**

A minta-tárház klónozása:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Futtathatja a minták segítségével Eclipse vagy Maven használatával a parancssorból.

Eclipse-ről futtatva:
* Töltse be a fő szülő projekt pom.xml fájlt az eclipse-ben; az automatikusan töltse be a documentdb-példák.
* A minta futtatásához szüksége van egy érvényes Azure Cosmos DB végpontot. A végpontok pedig olvassa a `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Eclipse JUnit futtatása config VM argumentumként átadhatók a végpont hitelesítő adatokat, vagy a végpont hitelesítő adatok AccountCredentials.java helyezhet el.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Most már a mintákat is futhat, JUnit vizsgálatok az eclipse-ben.

Futtatása a parancssorból:
* A minta futtatásához a más módon, hogy maven használja:
* Futtassa a Maven, és az Azure-beli Cosmos DB végpont hitelesítő adatok továbbítása:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > A mintákat az önálló; Beállítja magát, és a szükségtelenné vált maga után. A minták ki több hívások [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection). Minden alkalommal, amikor ez történik, az előfizetés számlázása számára létrehozott gyűjtemény teljesítményszint használati 1 órában. 
   > 
   > 

## <a name="database-examples"></a>Adatbázis-példák
A [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre, és olvassa el az adatbázis](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Hozzon létre vagy töröljön egy adatbázis](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Hozzon létre, és az adatbázis lekérdezése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Gyűjtemény példák
A [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Az egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Egyéni több partíció-gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._request_options) |
| [A gyűjtemény törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>A dokumentum példák
A [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre, olvassa el és dokumentum törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Programozható dokumentum definícióval dokumentum létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [A dokumentum](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Az indexelő példák
A [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre egy index és indexelő házirend beállítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._indexing_policy) |

Az indexelő kapcsolatos további információkért lásd: [Azure Cosmos DB házirendek indexelő](indexing-policies.md).

## <a name="query-examples"></a>lekérdezés példák
A [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Egy egyszerű kereszt-partíció dokumentum lekérdezés végrehajtása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Lekérdezés szerinti rendezés](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

További információ a lekérdezések írásáról: [belül Azure Cosmos adatbázis SQL-lekérdezés](sql-api-sql-query.md).

## <a name="offer-examples"></a>Az ajánlat példák
A [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre egy gyűjteményt, és állítsa be az átviteli sebesség](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Olvassa el a társított ajánlat található gyűjtemény](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Partíciós kulcs példák
A [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Az egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Módosítsa az egypartíciós gyűjtemény átviteli vonatkozó ajánlatot](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Tárolt eljárás példák
A [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) fájlt mutatja be a következő feladatok végezhetők el:

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre egy tárolt eljárás](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [A tárolt eljárás futtatására a következő argumentumokkal](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [A következő tárolt eljárás futtatására objektum argumentummal](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
