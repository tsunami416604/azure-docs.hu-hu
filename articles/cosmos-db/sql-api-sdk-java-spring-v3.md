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
ms.openlocfilehash: f0cb3d5f9184bacef42a0258add6dd2461a71dd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326660"
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

# <a name="explore"></a>[Ismerkedés](#tab/explore)

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

---

## <a name="resources"></a>Források

* **Közreműködés az SDK-** ban: [Spring-adatAzure Cosmos db-tárház a githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Oktatóanyag**: [Spring-adatAzure Cosmos db oktatóanyag a githubon](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Cosmos DBról](https://azure.microsoft.com/services/cosmos-db/).

További információ a [Spring Framework](https://spring.io/projects/spring-framework)-ről.

További információ a [Spring boot](https://spring.io/projects/spring-boot)-ről.

További információ a [Spring-adatforrásokról](https://spring.io/projects/spring-data).