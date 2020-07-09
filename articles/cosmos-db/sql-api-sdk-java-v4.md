---
title: Azure Cosmos DB Java SDK v4 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerkedjen meg a Azure Cosmos DB Java SDK v4 for SQL API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: aa5e741d8578253b8b3e8e5c692873c39cec8f79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85412634"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Az Azure Cosmos DB Java SDK v4 for Core (SQL) egy aszinkron API-t és egy "Sync" API-t egyesít egy Maven-összetevőbe. A v4 SDK továbbfejlesztett teljesítményt, új API-szolgáltatásokat és aszinkron támogatást biztosít a projekt-reaktor és a [nettó könyvtár](https://netty.io/)alapján. A felhasználók a Azure Cosmos DB Java SDK v4 és a [Azure Cosmos db aszinkron Java SDK v2](sql-api-sdk-async-java.md) és a [Azure Cosmos db Sync Java SDK v2](sql-api-sdk-java.md)használatával is jobb teljesítményt számíthatnak.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések csak Azure Cosmos DB Java SDK v4-re vonatkoznak. Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>
> Íme három lépés a gyors kezdéshez!
> 1. Telepítse a [minimális támogatott Java-futtatókörnyezetet, a JDK 8-as verzióját,](/java/azure/jdk/?view=azure-java-stable) hogy használhassa az SDK-t.
> 2. A [Azure Cosmos db Java SDK v4-hez készült gyors útmutatóban](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) megismerheti a Maven-összetevők elérését, és végigvezeti az alapszintű Azure Cosmos db kérelmeken.
> 3. Az alkalmazáshoz tartozó SDK optimalizálásához olvassa el a Azure Cosmos DB Java SDK v4 [teljesítményével kapcsolatos tippeket](performance-tips-java-sdk-v4-sql.md) és [hibaelhárítási](troubleshoot-java-sdk-v4-sql.md) útmutatókat.
>
> A [Azure Cosmos db Workshops és Labs](https://aka.ms/cosmosworkshop) egy másik nagyszerű erőforrás a Azure Cosmos db Java SDK v4 használatának megismeréséhez.
>

| |  |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentáció** | [Java API-referenciák dokumentációja](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Közreműködés az SDK-val** | [Azure SDK a Java központi tárházhoz a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Első lépések** | Gyors útmutató [: Java-alkalmazás létrehozása Azure Cosmos db SQL API-beli adatkezelési szolgáltatásokhoz](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) -tárház a gyors üzembe helyezési kóddal | 
|**Alapszintű kódok** | [Azure Cosmos db: Java-példák az SQL API](sql-api-java-sdk-samples.md) -ra · [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolos alkalmazás változási hírcsatornával**| [Change feed-Java SDK v4 minta](create-sql-api-java-changefeed.md) · [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Webalkalmazás mintája**| [Webalkalmazás létrehozása Java SDK v4](sql-api-java-application.md) használatával [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Teljesítménnyel kapcsolatos tippek**| [Teljesítménnyel kapcsolatos tippek Java SDK v4-hez](performance-tips-java-sdk-v4-sql.md)| 
| **Hibaelhárítás** | [Java SDK v4-hibák elhárítása](troubleshoot-java-sdk-v4-sql.md) |
| **Migrálás a v4-re egy régebbi SDK-ból** | [Migrálás Java V4 SDK-ba](migrate-java-v4-sdk.md) |
| **Minimális támogatott futtatókörnyezet**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshopok és Labs** |[Cosmos DB workshopok kezdőlapja](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Kiadási előzmények

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Új funkciók
* A lekérdezés támogatása hozzáadva `GROUP BY` .
* Megnövelte a maxConnectionsPerEndpoint alapértelmezett értékét a 130 értékre a DirectConnectionConfig-ben.
* Megnövelte a maxRequestsPerConnection alapértelmezett értékét a DirectConnectionConfig-ben.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Az Order by lekérdezés által visszaadott hibák a folytatási token használatával ismétlődő eredményeket adnak vissza. 
* Az érték lekérdezésével kapcsolatos rögzített hibák a beágyazott objektumhoz null értékeket adnak vissza.
* Rögzített null értékű kivétel a RntbdClientChannelPool-ben a Request Managerben.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Új funkciók
* Átnevezve: `QueryRequestOptions` `CosmosQueryRequestOptions` .
* Frissítve `ChangeFeedProcessorBuilder` a Builder minta.
* Frissítve `CosmosPermissionProperties` az új tároló neve és a gyermek erőforrások API-k segítségével.
* További minták lettek hozzáadva & dúsított dokumentumok a következőhöz: `CosmosClientBuilder` . 
* Frissített `CosmosDatabase`  &  `CosmosContainer` API-k az throughputProperties az autoscale/Autopilot támogatásához. 
* Átnevezve: `CosmosClientException` `CosmosException` . 
* Az API-k váltotta fel `AccessCondition`  &  `AccessConditionType` `ifMatchETag()`  &  `ifNoneMatchETag()` . 
* Az összes típus egyesítve lett `Cosmos*AsyncResponse`  &  `CosmosResponse` egyetlen `CosmosResponse` típussal.
* Átnevezve: `CosmosResponseDiagnostics` `CosmosDiagnostics` .  
* Becsomagolva `FeedResponseDiagnostics` `CosmosDiagnostics` . 
* Eltávolított `jackson` függőség az Azure-cosmos & az Azure-Core-ra támaszkodva. 
* Lecserélve `CosmosKeyCredential` `AzureKeyCredential` típussal. 
* API-k hozzáadása a következőhöz: `ProxyOptions` `GatewayConnectionConfig` . 
* Frissítette az SDK-t a `Instant` típus helyett `OffsetDateTime` . 
* Új enumerálási típus hozzáadva `OperationKind` . 
* Átnevezve: `FeedOptions` `QueryRequestOptions` . 
* `getETag()`  &  `getTimestamp()` API-k hozzáadása a `Cosmos*Properties` típusokhoz. 
* További `userAgent` információ a alkalmazásban `CosmosException`  &  `CosmosDiagnostics` . 
* Új sor karakterének frissítése az `Diagnostics` új sor karakterbe. 
* Eltávolított `readAll*` API-k, a lekérdezés használatával válassza ki az összes API-t.
* A `ChangeFeedProcessor` becsült késési API hozzáadva.   
* Az autoscale/Autopilot átviteli sebességének támogatása az SDK-ban.  
* `ConnectionPolicy`Új kapcsolatok konfigurációjának lecserélése. Elérhető API-k `DirectConnectionConfig`  &  `GatewayConnectionConfig` `CosmosClientBuilder` a közvetlen & átjáró módú kapcsolatok konfigurációjában.
* Áthelyezve `JsonSerializable`  &  `Resource` a implementációs csomagba. 
* `contentResponseOnWriteEnabled`Olyan API-t adott hozzá a CosmosClientBuilder-hez, amely letiltja a teljes válasz tartalmakat az írási műveletekben.
* Feltehetően elérhető API-k `getETag()` a válasz típusoknál.
* Áthelyezve `CosmosAuthorizationTokenResolver` a megvalósításba. 
* Az API átnevezve a következőre: `preferredLocations`  &  `multipleWriteLocations` `preferredRegions`  &  `multipleWriteRegions` . 
* Frissítve `reactor-core` a 3.3.5. Release, `reactor-netty` a 0.9.7. Release & `netty` a 4.1.49. Final verziókig. 
* Támogatás hozzáadva az `analyticalStoreTimeToLive` SDK-hoz.     
* `CosmosClientException`kiterjeszti `AzureException` . 
* Ehelyett az API-k használatával eltávolította az API-kat `maxItemCount`  &  `requestContinuationToken` `FeedOptions` `byPage()` `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* `CosmosPermissionProperties`Az API-k nyilvános felületén lett bevezetve `Permission` .
* Eltávolított `SqlParameterList` típus & lecserélve`List`
* Rögzített több memóriavesztés a közvetlen TCP-ügyfélben. 
* A lekérdezések támogatása megnövelve `DISTINCT` . 
* A külső függőségek el lettek távolítva `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Áthelyezve `CosmosPagedFlux`  &  `CosmosPagedIterable` a `utils` csomagba. 
* Frissítve a 4.1.45. Final & Project-reaktort a 3.3.3 verzióra.
* Frissített nyilvános Rest-szerződések `Final` osztályokba.
* Speciális diagnosztika támogatása a pont műveleteihez.
* Csomag frissítve`com.azure.cosmos`
* Csomag hozzáadva `models` a modell/Rest szerződések számára
* Csomag hozzáadva `utils` a `CosmosPagedFlux`  &  `CosmosPagedIterable` típusokhoz. 
* Frissített nyilvános API-k az SDK-ban való használatra `Duration` .
* Az összes Rest-szerződés hozzáadva a `models` csomaghoz.
* `RetryOptions`átnevezve: `ThrottlingRetryOptions` .
* `CosmosPagedFlux`  &  `CosmosPagedIterable` A lekérdezési API-khoz hozzáadott tördelési típusok. 
* A TransportClient megosztásának támogatása a CosmosClients több példányán keresztül egy új API-val a`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Lekérdezések optimalizálása a kettős szerializálás/deszerializálás eltávolításával. 
* A válasz fejlécének optimalizálása a szükségtelen másolás visszavonásával. 
* Optimalizált `ByteBuffer` szerializálás/deszerializálás közbenső karakterlánc-példányok eltávolításával.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Rögzített ConnectionPolicy `toString()` Null mutató kivétel.
* Kijavítottuk a lekérdezés eredményeinek elemzését a lekérdezések alapján. 
* Rögzített szoftvercsatorna-szivárgási problémák a közvetlen TCP-ügyféllel.
* `orderByQuery`A folytatási jogkivonat hibája javítva.
* `ChangeFeedProcessor`a partíciók felosztására szolgáló hibajavítás &, ha a partíció nem található.
* `ChangeFeedProcessor`hibajavítás a címbérleti frissítések különböző szálak közötti szinkronizálásakor.
* Rögzített versenyhelyzet okozza `ArrayIndexOutOfBound` a kivételt a StoreReader

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.