---
title: 'Azure Cosmos DB: Java-példák az SQL API-hoz'
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Java-példákat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2019
ms.author: sngun
ms.openlocfilehash: 8b133f0044bdf8f99fdee657177d561ef5bb406b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238481"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Java-példák az SQL API-hoz

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

Az [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub-adattárban megtalálhatók a legújabb mintaalkalmazások, amelyek CRUD műveleteket és más általános műveleteket végeznek az Azure Cosmos DB erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* Az egyes Java-projektfájlokban lévő feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A mintaalkalmazás futtatásához a következőkre lesz szüksége:

* Java fejlesztői készlet 7-as verziója
* Microsoft Azure DocumentDB Java SDK

Igény szerint a Maven segítségével is beszerezheti a legújabb Microsoft Azure DocumentDB Java SDK bináris fájlokat a projekthez. A Maven automatikusan hozzáadja az összes szükséges függőséget. Egyéb esetben közvetlenül letöltheti a pom.xml fájlban szereplő függőségeket, és az elérési úthoz adhatja őket.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**A mintaalkalmazások futtatása**

A mintaadattár klónozása:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

A mintákat az Eclipse használatával vagy a parancssorból futtathatja a Maven segítségével.

Futtatás az Eclipse-ből:
* Töltse be a fő szülő projekt pom.xml fájlját az Eclipse-be; automatikusan be kell töltenie a documentdb-examples modult.
* A minták futtatásához érvényes Azure Cosmos DB-végpontra van szüksége. A végpontok beolvasása az `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java` mappából történik.
* A végpont hitelesítő adatait VM-argumentumként illesztheti be az Eclipse JUnit futtatási konfigurációba, vagy a végpont hitelesítő adatait az AccountCredentials.java fájlban helyezheti el.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Most már JUnit tesztként futtathatja a mintákat az Eclipse-ben.

Futtatás a parancssorból:
* A minták futtatásának másik módja a Maven használata:
* Futtassa a Mavent, és illessze be az Azure Cosmos DB-végpont hitelesítő adatait:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást adnak ki a [DocumentClient.createCollection fájlba.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott gyűjtemény teljesítményszintjének megfelelően. 
   > 
   > 

## <a name="database-examples"></a>Adatbázis-példák
A [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. Az Azure Cosmos-adatbázisok futtatása előtt a következő mintákfuttatásáról az Adatbázisok, tárolók és elemek fogalmi [cikkének munkáért.](databases-containers-items.md) 

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása és olvasása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase fájl](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readAdatbázis](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [Adatbázis létrehozása és törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteAdatbázis](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [Adatbázis létrehozása és lekérdezése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryadatbázisok](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>Gyűjtemény-példák
A [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. Az Azure Cosmos-gyűjtemények futtatása előtt a következő mintákat, az [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikk kidolgozása című témakörben olvashat.

| Tevékenység | API-referencia |
| --- | --- |
| [Egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createGyűjtemény](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Egyéni többpartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [Gyűjtemény törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteGyűjtemény](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>Dokumentumpéldák
A [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. Az Azure Cosmos-dokumentumok futtatása előtt a következő mintákfuttatásáról az Adatbázisok, tárolók és elemek fogalmi [cikkének munkáért.](databases-containers-items.md)

| Tevékenység | API-referencia |
| --- | --- |
| [Dokumentum létrehozása, olvasása és törlése](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [Dokumentum létrehozása programozható dokumentumdefinícióval](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokumentum](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>Indexelési példák
A [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. Ha többet szeretne tudni az Indexelés az Azure Cosmos DB futtatása előtt a következő mintákat, olvassa el az [indexelési szabályzatok](index-policy.md), [indexelési típusok](index-types.md)és [indexelési útvonalak fogalmi](index-paths.md) cikkek. 

| Tevékenység | API-referencia |
| --- | --- |
| [Index létrehozása és indexelési szabályzat beállítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

További információ az indexelésről: [Azure Cosmos DB indexelési szabályzat](index-policy.md).

## <a name="query-examples"></a>Lekérdezéspéldák
A [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. Ha többet szeretne megtudni az SQL-lekérdezési hivatkozás az Azure Cosmos DB futtatása előtt a következő mintákat, tekintse meg az [SQL-lekérdezési példák](how-to-sql-query.md) fogalmi cikk. 


| Tevékenység | API-referencia |
| --- | --- |
| [Partíciók közötti egyszerű dokumentumlekérdezés végrehajtása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [Lekérdezés szerinti rendezés](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse\<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

További információ a lekérdezések írásáról: [SQL-lekérdezés az Azure Cosmos DB-ben](how-to-sql-query.md).

## <a name="offer-examples"></a>Ajánlatpéldák
Az [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fájl bemutatja, hogyan végezheti el a következő feladatokat:

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása és a teljesítmény beállítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createGyűjtemény](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [Gyűjtemény olvasása a társított ajánlat megkereséséhez](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>Partíciókulcs-példák
A [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. A particionálásés a partíciókulcsok az Azure Cosmos DB-ben a következő minták futtatása előtt, [particionálás](partitioning-overview.md) irt. 


| Tevékenység | API-referencia |
| --- | --- |
| [Egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createGyűjtemény](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Egypartíciós gyűjtemény teljesítményajánlatának módosítása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Tárolt eljárások példái
A [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) fájl bemutatja, hogyan hajthatja végre a következő feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos DB kiszolgálóoldali programozásáról, olvassa el a [Tárolt eljárások, eseményindítók és a felhasználó által definiált függvények](stored-procedures-triggers-udfs.md) fogalmi cikkét. 


| Tevékenység | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [Tárolt eljárás futtatása argumentumokkal](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [Tárolt eljárás futtatása objektumargumentummal](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
