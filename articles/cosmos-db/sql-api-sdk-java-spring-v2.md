---
title: Spring-adatAzure Cosmos DB v2 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerkedjen meg az SQL API-hoz készült Spring adatok Azure Cosmos DB v2 szolgáltatással, beleértve a kiadási dátumokat, a kivonulási dátumokat, valamint az Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590644"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring-adatAzure Cosmos DB v2 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
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

A Spring-adatAzure Cosmos DB v2 for Core (SQL) lehetővé teszi a fejlesztők számára, hogy a Spring Applications szolgáltatásban használják a Azure Cosmos DB. A Spring-adatAzure Cosmos DB teszi elérhetővé a Spring-adatfelületet az adatbázisok és gyűjtemények, a dokumentumok kezelése és a lekérdezések kiállítása során. A szinkronizálási és aszinkron (reaktív) API-k ugyanabban a Maven-összetevőben támogatottak. 

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely egyszerűbbé teszi a Java-alkalmazások fejlesztését. A szervezet webhelyének felidézéséhez a Spring leegyszerűsíti a függőségi befecskendezést használó alkalmazások "vízvonalát". Számos fejlesztő, mint például a Spring, az alkalmazások fejlesztése és tesztelése is egyszerűbb lesz. A [Spring boot](https://spring.io/projects/spring-boot) kiterjeszti ezt a gondolatot a vízvezetékek kezelésére a webalkalmazások és a szolgáltatások fejlesztésének szem előtt tartásával. A [Spring-adatok](https://spring.io/projects/spring-data) egy olyan programozási modell, amellyel az adattárolók (például a Azure Cosmos db egy Spring vagy Spring boot-alkalmazás kontextusában) férnek hozzá. 

Az [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) -alkalmazásaiban a Spring-alapú adatAzure Cosmos DBek is használhatók.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring adatAzure Cosmos DB v2-re vonatkoznak. A v3 kibocsátási megjegyzések [itt](sql-api-sdk-java-spring-v3.md)találhatók. 
>
> A Spring-adatAzure Cosmos DB csak az SQL API-t támogatja.
>
> A következő útmutatók támogatják a rugós adatAzure Cosmos DB API-k használatát:
> * [Spring-adatforrások az Apache Cassandra és a Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring-adatMongoDB Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring-adatGremlin Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Szeretne gyorsan eljutni?
> 1. Telepítse a [minimális támogatott Java-futtatókörnyezetet, a JDK 8-as verzióját,](/java/azure/jdk/?view=azure-java-stable) hogy használhassa az SDK-t.
> 2. Hozzon létre egy Spring-alapú adatAzure Cosmos DB alkalmazást a Starter használatával – [egyszerűen](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. A Spring- [adatokon keresztül Azure Cosmos db fejlesztői útmutatóból](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) megismerheti az alapszintű Azure Cosmos db kérelmeket.
>
> A Spring [inicializáló](https://start.spring.io/)segítségével gyorsan elindíthatja a Spring boot Starter-alkalmazásokat!
>

## <a name="helpful-content"></a>Hasznos tartalom

| Tartalom | Hivatkozás |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-dokumentáció** | [A Spring-adatAzure Cosmos DB dokumentációja]() |
|**Közreműködés az SDK-val** | [Spring-adatAzure Cosmos DB-tárház a GitHubon](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring boot Starter**| [A Azure Cosmos DB Spring boot Starter ügyféloldali kódtára a Javához](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Spring TODO app-minta Azure Cosmos DB**| [Teljes körű Java-élmény App Service Linux rendszerben (2. rész)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Fejlesztői útmutató** | [Spring-adatAzure Cosmos DB fejlesztői útmutató](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Útmutató az alapszintű használathoz** | [A Spring Boot Starter használata az Azure Cosmos DB SQL API-val](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-tárház az Azure Spring boot Starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Minta App Services** | [A Spring és a Cosmos DB használata a Linuxos App Service-szel](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO-alkalmazás mintája](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Kiadási előzmények

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Új funkciók
* A Spring boot verziójának frissítése a 2.3.0-be 
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Új funkciók
* Azure Cosmos DB verziójának frissítése a v 3.7.3
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Memóriavesztés-javításokat tartalmaz, és a Cosmos SDK v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Rögzített allowTelemetry jelző, amely figyelembe veszi a CosmosDbConfig
* Rögzített TTL tulajdonság a tárolón

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Új funkciók
* Új findAll hozzáadva a Partition Key API-hoz
* Az Azure-Cosmos verzió frissítése a 3.7.0-be
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Rögzített collectionName – > containerName
* Rögzített entityClass & domainClass-> domainType
* Kijavítva – a tárház által mentett visszaküldési entitás gyűjtése a bemeneti entitás helyett

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Backported-javítás a "adattár által mentett visszaküldési entitások gyűjtése a bemeneti entitás helyett"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Új funkciók
* Az Azure-Cosmos verziója frissítve a v 3.6.0
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Új funkciók
* Frissített Cosmos DB SDK-verziót a 3.5.0-re
* Hozzáadott jegyzet mező az automatikus létrehozási gyűjtemény engedélyezéséhez/letiltásához
* Jobb kivételek kezelésére, a CosmosDBAccessException-en keresztül elérhető CosmosClientException
* RequestCharge és tevékenységazonosító elérhető a ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Az SDK 3.5.0 frissítésének javítása "kivétel történt, ha Cosmos DB HTTP-válasz fejléce nagyobb, mint 8192 bájt", "a ConsistencyPolicy. defaultConsistencyLevel () függvény a kötött elavulás és az konzisztens előtag esetében meghiúsul"
* A rögzített findById API-k viselkedése nem található, és nem találhatók üresen a kivétel helyett
* Kijavítva a hiba, ha a rendezés nem lett alkalmazva a következő lapra a CosmosPageRequest használatakor

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Új funkciók
* Hozzáadott jegyzet mező az automatikus létrehozási gyűjtemény engedélyezéséhez/letiltásához
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A rögzített findById API-k viselkedése nem található, és nem találhatók üresen a kivétel helyett

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Új funkciók
* Reaktív Cosmos-tárház támogatásának befejezése
* Cosmos DB diagnosztikai karakterlánc-és lekérdezési metrikák támogatása
* Cosmos DB SDK-verzió frissítése a 3.3.1-re
* A Spring Framework verziófrissítése a 5.2.0. RELEASE verzióra
* A Spring adatcommons verziójának frissítése a 2.2.0. RELEASE csomagra
* Hozzáadott findByIdAndPartitionKey, deleteByIdAndPartitionKey API-k
* Függőség eltávolítása az Azure-doumentdb
* A DocumentDb átnevezve a Cosmos-be
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavítva a "rendezési dob kivétel, ha a pageSize kisebb, mint az adattár összes eleme"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Új funkciók
* Document DB API-k elavultak
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey API-k lettek hozzáadva.
* Optimista zárolást hozzáadott _etag alapján
* Engedélyezett SPeL kifejezés a dokumentum-gyűjtemény neveként
* A ObjectMapper fejlesztése.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Új funkciók
* Cosmos SDK v3-függőség hozzáadva
* Reaktív Cosmos-adattár hozzáadva
* A DocumentDbTemplate implementációjának frissítése a Cosmos SDK v3 használatára.
* A reaktív Cosmos adattár támogatásának egyéb konfigurációs módosításai.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* ApplicationInsights függőség eltávolítása
    * A függőségek szennyezésének lehetséges kockázata
    * Java 11 inkompatibilitás
    * A CPU és/vagy a memória lehetséges teljesítményének elkerülése.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A vezetnie eltávolítja a applicationInsights függőségét
    * A függőségek szennyezésének lehetséges kockázata
    * Java 11 inkompatibilitás
    * A CPU és/vagy a memória lehetséges teljesítményének elkerülése.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Új funkciók
* Főverzió frissítése 2.1.1-re
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Új funkciók
* A telemetria összes kivételének figyelmen kívül hagyása
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Új funkciók
* A 2.1.0 verziójának frissítése a probléma megoldásához
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Új funkciók
* A kulcsszó hozzáadása létezik, startsWith
* Readme frissítése
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítsa a "cant Call saját href közvetlenül az entitáshoz" elemet.
* Javítás: a findAll sikertelen lesz, ha a gyűjtemény nincs létrehozva.

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (előzetes verzió) (2018-08-23)
#### <a name="new-features"></a>Új funkciók
* Csomag átnevezése a documentdb és a cosmosdb között,
* A lekérdezési módszer új funkciójának hozzáadása, 16 kulcsszó az SQL API által támogatott.
* A lekérdezés új funkciójának hozzáadása a lapozással és a rendezéssel.
* Egyszerűsítse a Spring-cosmosdb konfigurációját.
* Adja hozzá a deleteCollection és a deleteAll API-t.

#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Hibajavítás és hibák javítása.

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>További lépések
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.

A Spring Framework szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [projekt kezdőlapját](https://spring.io/projects/spring-framework).

A Spring boot szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [projekt kezdőlapját](https://spring.io/projects/spring-boot).

Ha többet szeretne megtudni a Spring-információkról, tekintse meg a [projekt kezdőlapját](https://spring.io/projects/spring-data).