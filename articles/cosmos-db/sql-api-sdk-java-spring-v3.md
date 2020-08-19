---
title: Spring-adatAzure Cosmos DB v3 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerje meg az SQL API-hoz készült Spring-adatok Azure Cosmos DB v3-as verzióját, beleértve a kiadási dátumokat, a kivonási dátumokat és a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590637"
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

A Spring-adatAzure Cosmos DB v3 for Core (SQL) lehetővé teszi a fejlesztők számára, hogy Azure Cosmos DB használjanak a Spring Applications szolgáltatásban. A Spring-adatAzure Cosmos DB teszi elérhetővé a Spring-adatfelületet az adatbázisok és gyűjtemények, a dokumentumok kezelése és a lekérdezések kiállítása során. A szinkronizálási és aszinkron (reaktív) API-k ugyanabban a Maven-összetevőben támogatottak. 

A Spring-adatAzure Cosmos DB a Spring adatkeretrendszertől függ. Azure Cosmos DB SDK-csapat kiad Maven-összetevőket a Spring-adatv 2.2-es és a v 2.3-as verzióhoz.

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely egyszerűbbé teszi a Java-alkalmazások fejlesztését. A szervezet webhelyének felidézéséhez a Spring leegyszerűsíti a függőségi befecskendezést használó alkalmazások "vízvonalát". Számos fejlesztő, mint például a Spring, az alkalmazások fejlesztése és tesztelése is egyszerűbb lesz. A [Spring boot](https://spring.io/projects/spring-boot) kiterjeszti ezt a gondolatot a vízvezetékek kezelésére a webalkalmazások és a szolgáltatások fejlesztésének szem előtt tartásával. A [Spring-adatok](https://spring.io/projects/spring-data) egy programozási modell és keretrendszer az adattárolók, Azure Cosmos db például a Spring vagy Spring rendszerindítási alkalmazás környezetében való hozzáféréshez. 

Az [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) -alkalmazásaiban a Spring-alapú adatAzure Cosmos DBek is használhatók.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring-adatAzure Cosmos DBek v3 verziójára vonatkoznak. A v2 kibocsátási megjegyzéseit [itt](sql-api-sdk-java-spring-v2.md)találja. 
>
> A Spring-adatAzure Cosmos DB csak az SQL API-t támogatja.
>
> A következő útmutatók támogatják a rugós adatAzure Cosmos DB API-k használatát:
> * [Spring-adatforrások az Apache Cassandra és a Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring-adatMongoDB Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring-adatGremlin Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Itt érdemes kezdenie

# <a name="explore"></a>[Böngészés](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>A fenti lapokon navigáljon az alapszintű Spring-adatok Azure Cosmos DB mintákhoz.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Függőségek konfigurálása

Két rugós adatAzure Cosmos DB v3 Maven-összetevő érhető el.

A Spring adatkeretrendszer v 2.2-es verziójának függvénye:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

A Spring adatkeretrendszer v 2.3-tól függő összetevő:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[Kapcsolódás](#tab/connect)

### <a name="connect"></a>Kapcsolódás

Azure Cosmos DB fiók és tároló adatainak megadása. A Spring-alapú adatAzure Cosmos DB automatikusan létrehozza az ügyfelet, és csatlakozik a tárolóhoz.

[alkalmazás. properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
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

[Létrehozás](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Törlés](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Lekérdezés](#tab/queries)

### <a name="query"></a>Lekérdezés

[Lekérdezés](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Hasznos tartalom

| Tartalom | Spring adatkeretrendszer v 2.2 | Spring adatkeretrendszer v 2.3 |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Közreműködés az SDK-val** | [Spring-adatAzure Cosmos DB-tárház a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Spring-adatAzure Cosmos DB-tárház a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Oktatóanyag**| [Spring-adatAzure Cosmos DB oktatóanyag a GitHubon](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Spring-adatAzure Cosmos DB oktatóanyag a GitHubon](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Kiadási előzmények

### <a name="300-beta1-2020-08-17"></a>3.0.0-Beta. 1 (2020-08-17)
#### <a name="new-features"></a>Új funkciók
* A csoport AZONOSÍTÓjának frissítése a következőre: `com.azure` .
* Az összetevő-azonosító frissítve a következőre: `azure-spring-data-2-3-cosmos` .
* Az Azure-Cosmos SDK függőségének frissítése megtörtént `4.3.2-beta.2` .
* Naplózási entitások támogatása – a createdBy, a createdDate, a lastModifiedBy és a lastModifiedDate megjegyzésekkel ellátott mezőinek automatikus kezelése.
* `@GeneratedValue` Megjegyzések támogatása a típusú azonosító mezőkhöz az automatikus azonosító létrehozásához `String` .
* Többadatbázisos konfiguráció támogatása egyetlen Cosmos-fiókhoz több adatbázissal és több adatbázissal rendelkező több Cosmos-fiókkal.
* A `@Version` jegyzetek támogatása bármely karakterlánc-mezőben.
* A frissített szinkronizálási API-k `Iterable` a típus helyett típusokba térnek vissza `List` .
* `CosmosClientBuilder`A Cosmos SDK-ból a Spring Bean-ként van kitéve az `@Configuration` osztálynak.
* Frissítve `CosmosConfig` , hogy tartalmazza a lekérdezési metrikákat és a válasz diagnosztikai processzor implementációját.
* Az `Optional` egyértékű lekérdezések adattípusának támogatása.
#### <a name="renames"></a>Átnevezi
* `CosmosDbFactory` a következőre: `CosmosFactory` .
* `CosmosDBConfig` a következőre: `CosmosConfig` .
* `CosmosDBAccessException` a következőre: `CosmosAccessException` .
* `Document` Jegyzet a jegyzethez `Container` .
* `DocumentIndexingPolicy` Jegyzet a jegyzethez `CosmosIndexingPolicy` .
* `DocumentQuery` a következőre: `CosmosQuery` .
* az Application. properties jelzője a következőre: `populateQueryMetrics` `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Diagnosztikai naplózási feladat ütemezése a `Parallel` szálak számára a nagymennyiségű I/O-szálak blokkolásának elkerülése érdekében.
* A törlési művelet rögzített optimista zárolása.
* Kijavítva a záradékkal kapcsolatos lekérdezések Escape-hibáját `IN` .
* Kijavítva a hiba, amely lehetővé teszi `long` az adattípust `@Id` .
* Kijavítottuk a problémát azáltal, hogy a, a, `boolean` `long` `int` `double` as típusú adattípusokat a `@PartitionKey` jegyzetekhez.
* `IgnoreCase`  &  `AllIgnoreCase` A Case-lekérdezések kihagyására szolgáló rögzített kulcsszavak
* A rendszer az alapértelmezett 4000-es értéket törölte a tárolók automatikus létrehozásakor.

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>További lépések
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.

A Spring Framework szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [projekt kezdőlapját](https://spring.io/projects/spring-framework).

A Spring boot szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [projekt kezdőlapját](https://spring.io/projects/spring-boot).

Ha többet szeretne megtudni a Spring-információkról, tekintse meg a [projekt kezdőlapját](https://spring.io/projects/spring-data).