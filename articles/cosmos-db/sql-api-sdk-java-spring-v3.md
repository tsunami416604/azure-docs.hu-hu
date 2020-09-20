---
title: Spring-adatAzure Cosmos DB v3 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerje meg az SQL API-hoz készült Spring-adatok Azure Cosmos DB v3-as verzióját, beleértve a kiadási dátumokat, a kivonulási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817870"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring-adatAzure Cosmos DB v3 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring-adatv2](sql-api-sdk-java-spring-v2.md)
> * [Spring-adatforrások v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

A Spring-adatAzure Cosmos DB a Core (SQL) 3-as verziójának köszönhetően a fejlesztők a Azure Cosmos DBt a Spring Applications szolgáltatásban használhatják. A Spring-adatAzure Cosmos DB teszi elérhetővé a Spring-adatfelületet az adatbázisok és gyűjtemények, a dokumentumok kezelése és a lekérdezések kiállítása során. A szinkronizálási és aszinkron (reaktív) API-k ugyanabban a Maven-összetevőben támogatottak. 

A Spring-adatAzure Cosmos DB a Spring adatkeretrendszertől függ. A Azure Cosmos DB SDK csapata a 2,2-es és a 2,3-os tavaszi adatverziók Maven-összetevőit bocsátja ki.

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely megkönnyíti a Java-alkalmazások fejlesztését. A Spring leegyszerűsíti az alkalmazások "vízmennyiségét" a függőségi befecskendezés használatával. Számos fejlesztő, mint például a Spring, egyszerűbbvé teszi az alkalmazások létrehozását és tesztelését. A [Spring boot](https://spring.io/projects/spring-boot) kiterjeszti a vízvezetékek kezelését a webalkalmazások és a szolgáltatások fejlesztésének szem előtt tartásával. A [Spring-adatok](https://spring.io/projects/spring-data) egy programozási modell és keretrendszer az adattárolók eléréséhez, például a Azure Cosmos db egy Spring vagy Spring rendszerindítási alkalmazás kontextusában. 

Az [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) -alkalmazásaiban a Spring-alapú adatAzure Cosmos DBek is használhatók.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring-adatmennyiségek Azure Cosmos DB 3. verziójára vonatkoznak. [A 2. verzió kibocsátási megjegyzéseit itt](sql-api-sdk-java-spring-v2.md)találja. 
>
> A Spring-alapú adatAzure Cosmos DB csak az SQL API-t támogatja.
>
> Tekintse meg ezeket a cikkeket a más Azure Cosmos DB API-k tavaszi adataival kapcsolatban:
> * [Spring-adatforrások az Apache Cassandra és a Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring-adatMongoDB Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring-adatGremlin Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Itt érdemes kezdenie

# <a name="explore"></a>[Böngészés](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Ezek a lapok alapszintű Spring-adatok Azure Cosmos DB mintákat tartalmaznak.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Függőségek konfigurálása

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Kapcsolódás](#tab/connect)

### <a name="connect"></a>Kapcsolódás

Azure Cosmos DB fiók és tároló adatainak megadása. A Spring-alapú adatAzure Cosmos DB automatikusan létrehozza az ügyfelet, és csatlakozik a tárolóhoz.

[alkalmazás. properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc-Ops](#tab/docs)

### <a name="document-operations"></a>Dokumentumok műveletei

Létrehozása
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

Törlés:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[Lekérdezés](#tab/queries)

Lekérdezés:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>További források

* **Közreműködés az SDK-** ban: [Spring-adatAzure Cosmos db-tárház a githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Oktatóanyag**: [Spring-adatAzure Cosmos db oktatóanyag a githubon](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>Kiadási előzmények

### <a name="300-beta2-september-17-2020"></a>3.0.0-Beta. 2 (szeptember 17, 2020)

#### <a name="new-features"></a>Új funkciók

* Az összetevő-azonosító frissítve a következőre: `azure-spring-data-cosmos` .
* Frissítve az Azure-Cosmos-függőséggel `4.5.0` .
* `Query Annotation` natív lekérdezések támogatása.
* A Java 11 támogatása.
* Beágyazott partíciós kulcs támogatása a megjegyzések mező kiírásával `partitionKeyPath` `@Container` .
* A `limit` lekérdezési típus támogatása, amely lehetővé teszi `top` , `first` hogy az adattár API-k meghatározásakor használható legyen.

#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

* Rögzített beágyazott partíciós hiba a jegyzettel való használatkor `@GeneratedValue` .

### <a name="300-beta1-august-17-2020"></a>3.0.0-Beta. 1 (augusztus 17, 2020)

#### <a name="new-features"></a>Új funkciók

* Frissíti a csoport AZONOSÍTÓját a következőre: `com.azure` .
* Frissíti az összetevő AZONOSÍTÓját a következőre: `azure-spring-data-2-3-cosmos` .
* Frissíti az Azure-Cosmos SDK-függőségét `4.3.2-beta.2` .
* Támogatást nyújt a naplózási entitások számára: automatikus felügyelet,,, `createdBy` `createdDate` `lastModifiedBy` és `lastModifiedDate` jegyzetekkel ellátott mezők.
* Hozzáadja a `@GeneratedValue` jegyzetek támogatását az automatikus azonosító létrehozásához a típusú azonosítók mezőihez `String` .
* Többadatbázisos konfiguráció támogatása több adatbázissal rendelkező önálló Azure Cosmos DB-fiókokhoz és több Azure Cosmos DB-fiókhoz több adatbázissal.
* A jegyzet támogatását `@Version` bármely karakterlánc-mezőhöz hozzáadja.
* A szinkronizált API-k visszatérési típusai a következő `Iterable` helyett típusok: `List` .
* `CosmosClientBuilder`A Azure Cosmos db SDK-ból Spring Bean-ként teszi elérhetővé a `@Configuration` osztályt.
* `CosmosConfig`A frissítések tartalmazzák a lekérdezési metrikákat és a válasz diagnosztikai processzor implementációját.
* A támogatja az `Optional` egyértékű lekérdezések adattípusának visszaadását.

#### <a name="renames"></a>Átnevezi

* `CosmosDbFactory` a következőre: `CosmosFactory` .
* `CosmosDBConfig` a következőre: `CosmosConfig` .
* `CosmosDBAccessException` a következőre: `CosmosAccessException` .
* `Document` Jegyzet a jegyzethez `Container` .
* `DocumentIndexingPolicy` Jegyzet a jegyzethez `CosmosIndexingPolicy` .
* `DocumentQuery` a következőre: `CosmosQuery` .
* az Application. properties jelzője a következőre: `populateQueryMetrics` `queryMetricsEnabled` .

#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

* Diagnosztikai naplózási feladat ütemezése a `Parallel` szálak számára a nagyszámú I/O-szálak blokkolásának elkerülése érdekében.
* Megjavítja a törlési művelet optimista zárolását.
* Kijavítja a záradékhoz tartozó lekérdezések Escape-problémáját `IN` .
* Kijavítja a problémát azáltal, hogy engedélyezi `long` az adattípust `@Id` .
* Kijavítja a problémát azáltal, hogy lehetővé teszi a,, `boolean` `long` `int` és `double` adattípusokat a `@PartitionKey` jegyzethez.
* Javítások `IgnoreCase` és `AllIgnoreCase` kulcsszavak a kis-és nagybetűk lekérdezésének mellőzéséhez.
* Eltávolítja a 4 000-es alapértelmezett értéket, ha a tárolók automatikusan létrejönnek.

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>További lépések

További információ a [Azure Cosmos DBról](https://azure.microsoft.com/services/cosmos-db/).

További információ a [Spring Framework](https://spring.io/projects/spring-framework)-ről.

További információ a [Spring boot](https://spring.io/projects/spring-boot)-ről.

További információ a [Spring-adatforrásokról](https://spring.io/projects/spring-data).