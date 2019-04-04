---
title: 'Az Azure Cosmos DB: Az SQL API aszinkron Java-példák'
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő aszinkron Java-példákat.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 06/18/2018
ms.author: sngun
ms.openlocfilehash: 34551dcc64e6add2baa1ec94067037c98de67abe
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917940"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Az Azure Cosmos DB: Az SQL API aszinkron Java-példák

> [!div class="op_single_selector"]
> * [.NET-példáit](sql-api-dotnet-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [NODE.js-példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Az Azure-kód Mintakatalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) GitHub-adattárban megtalálhatók a legújabb mintaalkalmazások, amelyek CRUD műveleteket és más általános műveleteket végeznek az Azure Cosmos DB erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* Az egyes aszinkron Java-projektfájlokban lévő feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Is [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetésében kreditek minden hónapban biztosít Önnek, hogy a fizetős Azure-szolgáltatásokat is használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A mintaalkalmazás futtatásához a következőkre lesz szüksége:

* Java fejlesztői készlet 8-as verziója
* Microsoft Azure Cosmos DB Java SDK

A Maven segítségével beszerezheti a legújabb Microsoft Azure Cosmos DB Java SDK bináris fájlokat a projekthez. [Itt](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) választhatja ki a legújabb verziót. A Maven automatikusan hozzáadja az összes szükséges függőséget. Egyéb esetben közvetlenül letöltheti a pom.xml fájlban szereplő függőségeket, és az elérési úthoz adhatja őket.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**A mintaalkalmazások futtatása**

A mintaadattár klónozása:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

A mintákat az Eclipse használatával vagy a parancssorból futtathatja a Maven segítségével.

Futtatás az Eclipse-ből:
* Töltse be a fő szülő projekt pom.xml fájlját az Eclipse-be; automatikusan be kell töltenie az azure-cosmosdb-examples modult.
* A minták futtatásához érvényes Azure Cosmos DB-végpontra van szüksége. A végpontok beolvasása az `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java` mappából történik.
* A végpont hitelesítő adatait VM-argumentumként illesztheti be az Eclipse JUnit futtatási konfigurációba, vagy a végpont hitelesítő adatait a TestConfigurations.java fájlban helyezheti el.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* Most már JUnit tesztként futtathatja a mintákat az Eclipse-ben.

Futtatás a parancssorból:
* A minták futtatásának másik módja a Maven használata:
* Futtassa a Mavent, és illessze be az Azure Cosmos DB-végpont hitelesítő adatait:
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást intéznek a [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) felé. Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott gyűjtemény teljesítményszintjének megfelelően. 
   > 
   > 

## <a name="database-examples"></a>Adatbázispéldák
A [DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat. További információ az Azure Cosmos-adatbázisok a következő minták futtatása előtt,: [-adatbázisok, tárolók és elemek használata](databases-containers-items.md) fogalmi cikk. 

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [Nem sikerült létrehozni egy adatbázist, amely már létezik](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Hozzon létre és olvasható adatbázis](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [Hozzon létre és töröl egy adatbázist](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [Egy adatbázis létrehozása és lekérdezése](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>Gyűjteménypéldák
A [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat. További információ az Azure Cosmos-gyűjtemények a következő minták futtatása előtt,: [-adatbázisok, tárolók és elemek használata](databases-containers-items.md) fogalmi cikk. 

| Tevékenység | API-referencia |
| --- | --- |
| [Egypartíciós gyűjtemény létrehozása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [Egy egyéni több partíció-gyűjtemény létrehozása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [Nem sikerült létrehozni a gyűjteményt, amelyik már létezik](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Hozzon létre, és a egy gyűjtemény olvasása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [Hozzon létre, és a egy gyűjtemény törlése](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [Hozzon létre, és a gyűjtemény lekérdezése](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>Dokumentumpéldák
A [DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat. További információ az Azure Cosmos-dokumentumok, a következő minták futtatása előtt,: [-adatbázisok, tárolók és elemek használata](databases-containers-items.md) fogalmi cikk. 

| Tevékenység | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [Hozzon létre egy dokumentumot egy programozható dokumentum-definícióval](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Dokumentum](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [Dokumentumok létrehozása, és keresse meg a teljes RU költség](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [Nem sikerült létrehozni egy dokumentumot, amely már létezik](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Hozzon létre, és a dokumentum cseréje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [Hozzon létre és a egy dokumentumot upsert](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [Hozzon létre vagy töröljön egy dokumentumot](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [Hozzon létre, és olvassa el a dokumentumot](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>Indexelési példák
A [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat.  A következő minták futtatása előtt az Azure Cosmos DB indexelése kapcsolatos további információkért lásd: [indexelési szabályzataihoz](index-policy.md), [adattípusok indexelése](index-types.md), és [elérési utak indexelő](index-paths.md) elméleti cikkeket. 

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre egy index és indexelő szabályzat beállítása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[indexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

További információ az indexelésről: [Azure Cosmos DB indexelési szabályzat](index-policy.md).

## <a name="query-examples"></a>Lekérdezéspéldák
A [DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat. További információ az SQL-lekérdezés referencia az Azure Cosmos DB a következő minták futtatása előtt,: [SQL-lekérdezés példák](how-to-sql-query.md) fogalmi cikk. 

| Tevékenység | API-referencia |
| --- | --- |
| [A dokumentum egyszerű lekérdezés végrehajtása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [A dokumentum egyszerű lekérdezés végrehajtása, és keresse meg a teljes RU költség](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [A dokumentum egyszerű lekérdezés végrehajtása, olvassa el a egy oldallal, és mondja le a visszaadott rendszernek megfigyelhetőnek](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [A dokumentum egyszerű lekérdezés végrehajtása, és az eredmények szűréséhez](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Az order by dokumentum partícióra kiterjedő lekérdezés végrehajtása](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

További információ a lekérdezések írásáról: [SQL-lekérdezés az Azure Cosmos DB-ben](how-to-sql-query.md).

## <a name="offer-examples"></a>Ajánlatpéldák
Az [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) fájl mutatja a következő feladatok elvégzési módját:

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre egy gyűjteményt, és állítsa be az átviteli sebesség](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [Olvassa el a kapcsolódó ajánlat található gyűjtemény](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [Egy gyűjtemény átviteli sebességét frissítheti az ajánlat cseréje](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Tárolt eljárások példái
A [StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat. A következő minták futtatása előtt az Azure Cosmos DB kiszolgálóoldali programozása kapcsolatos további információkért lásd: [tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md) fogalmi cikk. 

| Tevékenység | API-referencia |
| --- | --- |
| [Hozhat létre és futtathat tárolt eljárás](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [Hozhat létre és futtathat tárolt eljárás argumentumokkal](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Hozhat létre és futtathat tárolt eljárás objektum argumentummal](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Egyedi kulcs
A [UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) fájl bemutatja, hogyan hajthat végre a következő feladatokat. További információ az Azure Cosmos DB-ben egyedi kulcsaival, a következő minták futtatása előtt,: [egyedi kulcsra vonatkozó megkötések](unique-keys.md) fogalmi cikk. 

| Tevékenység | API-referencia |
| --- | --- |
| [Hozzon létre gyűjteményt egy egyedi kulcsot](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[uniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
