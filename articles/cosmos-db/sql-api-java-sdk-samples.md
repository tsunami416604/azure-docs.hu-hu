---
title: 'Azure Cosmos DB SQL API: Java SDK v4 példák'
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Java-példákat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727161"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4 példák

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-példák](sql-api-java-sdk-samples.md)
> * [Node. js-példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Ha többet szeretne megtudni a Java SDK v4-ről, tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos), a Azure Cosmos db Java SDK v4 [teljesítménnyel kapcsolatos TIPPEKET](performance-tips-java-sdk-v4-sql.md), valamint Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatót](troubleshoot-java-sdk-v4-sql.md) . Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Az [Azure-Cosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) GitHub-adattár tartalmazza a legújabb, a szifilisz-műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos gyakori műveleteket végrehajtó alkalmazásokat. Ez a cikk a következő információkat tartalmazza:

* Az egyes Java-projektfájlokban lévő feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

A mintaalkalmazás futtatásához a következőkre lesz szüksége:

* Java fejlesztői készlet 8-as verziója
* Azure Cosmos DB Java SDK v4

Igény szerint a Maven használatával beszerezheti a legújabb Azure Cosmos DB Java SDK v4 bináris fájlokat a projektben való használathoz. A Maven automatikusan hozzáadja az összes szükséges függőséget. Egyéb esetben közvetlenül letöltheti a pom.xml fájlban szereplő függőségeket, és az elérési úthoz adhatja őket.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**A mintaalkalmazások futtatása**

A mintaadattár klónozása:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

A mintákat IDE (Eclipse, IntelliJ vagy VSCODE) vagy a parancssorból is futtathatja a Maven használatával.

Ezeket a környezeti változókat be kell állítani

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

a minták olvasási/írási hozzáférésének biztosítása a fiókhoz.

A minta futtatásához adja meg a fő osztályt 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

ahol a *sample. szinkron. MainClass* lehet
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. aszinkron. SampleCRUDQuickstartAsync
* indexmanagement. Sync. SampleIndexManagement
* indexmanagement. aszinkron. SampleIndexManagementAsync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. aszinkron. SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(a Changefeed csak aszinkron mintát tartalmaz, nincs szinkronizálási minta.)* ... stb.

> [!NOTE]
> Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást adnak ki a létrehozásához `CosmosContainer` . Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott gyűjtemény teljesítményszintjének megfelelően. 
> 
> 

## <a name="database-examples"></a>Adatbázis-példák
Az [adatbázis szifilisz-mintáinak](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) fájlja a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient. createDatabaseIfNotExists |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient. getDatabase |
| [Az összes adatbázis olvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Adatbázis törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Gyűjtemény-példák
A [gyűjteményi szifilisz mintáinak](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájlja a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata.

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase. Createcontainerifnotexists függvényt |
| [Gyűjtemény konfigurált teljesítményének módosítása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Gyűjtemény beolvasása azonosító alapján](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase.getContainer |
| [Egy adatbázisban lévő összes gyűjtemény olvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Gyűjtemény törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Példák az autoscale-gyűjtésre

Ha többet szeretne megtudni a minták futtatása előtt, tekintse meg ezeket az utasításokat, amelyekkel engedélyezheti az autoskálázást a [fiókjában](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) , valamint az [adatbázisaiban és a tárolókban](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

Az [autoscale Database szifilisz Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) fájl az alábbi feladatok végrehajtását mutatja be.

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása a megadott autoscale Max átviteli sebességgel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient. createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

Az [autoscale Collection szifilisz-minták](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) fájlja a következő feladatok elvégzését mutatja be. 

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása a megadott autoscale Max átviteli sebességgel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase. Createcontainerifnotexists függvényt |
| [Egy gyűjtemény konfigurált autoskálázási maximális átviteli sebességének módosítása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Gyűjtemény automatikus skálázási átviteli konfigurációjának olvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Elemzési tár-gyűjtési példák

Az [analitikai tár gyűjteményének szifilisz-mintái](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) fájlja a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, olvassa el a Azure Cosmos DB szinapszis és az analitikai tároló című témakört.

| Tevékenység | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase. Createcontainerifnotexists függvényt |

## <a name="document-examples"></a>Dokumentumpéldák
A [Document szifilisz Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) fájl a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata.

| Tevékenység | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer.createItem |
| [Dokumentum olvasása ID (azonosító) alapján](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Egy gyűjteményben lévő összes dokumentum beolvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Dokumentumok lekérdezése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.queryItems |
| [Dokumentum cseréje](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Dokumentum frissítése és beszúrása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Dokumentum törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Dokumentum cseréje feltételes ETag-vizsgálattal](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition. setType<br>AccessCondition.setCondition |
| [Dokumentum olvasása csak a dokumentum módosítása esetén](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition. setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Indexelési példák
A [gyűjteményi szifilisz mintáinak](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájlja a következő feladatok elvégzését mutatja be. A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-types.md)és az [indexelési útvonalak](index-paths.md) fogalmi cikkei. 

| Tevékenység | API-referencia |
| --- | --- |
| Dokumentum kizárása az indexből | ExcludedIndex<br>IndexingPolicy |
| Lusta indexelés használata | IndexingPolicy.IndexingMode |
| [Megadott dokumentumok elérési útjának belefoglalása az indexbe](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [A megadott dokumentumok elérési útjának kizárása az indexből](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Összetett index létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Tartományvizsgálati művelet kikényszerítése kivonatolt indexelt elérési útvonalon | FeedOptions.EnableScanInQuery |
| Tartomány-indexek használata a Sztringeken | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Indexátalakítás végrehajtása | - |
| [Térinformatikai index létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

További információ az indexelésről: [Azure Cosmos DB indexelési szabályzat](index-policy.md).

## <a name="query-examples"></a>Lekérdezéspéldák
A [lekérdezési minták](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) fájl a következő feladatokat mutatja be az SQL-lekérdezési nyelvtan használatával. Ha többet szeretne megtudni a Azure Cosmos DB SQL-lekérdezési referenciáról az alábbi minták futtatása előtt, tekintse meg a [Azure Cosmos db SQL-lekérdezési példákat](how-to-sql-query.md). 

| Tevékenység | API-referencia |
| --- | --- |
| [Az összes dokumentum lekérdezése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.queryItems |
| [Egyenlőség lekérdezése == használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.queryItems |
| [Egyenlőtlenség lekérdezése != és NOT használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.queryItems |
| [Lekérdezés tartományi operátorokkal, például a >, <, >=, <= használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.queryItems |
| [Lekérdezés tartományi operátorokkal sztringekben](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.queryItems |
| [Lekérdezés RENDELÉSsel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.queryItems |
| [Lekérdezés eltérő](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.queryItems |
| [Lekérdezés összesítő függvényekkel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.queryItems |
| [Aldokumentumok használata](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.queryItems |
| [Lekérdezés dokumentumon belüli illesztésekkel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.queryItems |
| [Lekérdezés string, Math és Array operátorral](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.queryItems |
| [Lekérdezés paraméteres SQL-lel az SqlQuerySpec használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.queryItems |
| [Lekérdezés explicit lapozással](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.queryItems |
| [Particionált gyűjtemények lekérdezése párhuzamosan](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.queryItems |
| Lekérdezés az ORDER BY particionált gyűjtemények esetén | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Változáscsatornák példái 
A következő feladatok elvégzéséhez a [hírcsatorna-feldolgozó minta fájl módosítása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) látható. Ha a következő minták futtatása előtt szeretné megismerni a Azure Cosmos DB a hírcsatornát, tekintse meg az [olvasási Azure Cosmos db módosítási hírcsatorna](read-change-feed.md) és a [hírcsatorna-feldolgozó módosítása](change-feed-processor.md)című témakört.

| Tevékenység | API-referencia |
| --- | --- |
| [Alapszintű módosítási hírcsatorna funkciói](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Változási hírcsatorna olvasása adott időpontból | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Változási hírcsatorna olvasása az elejétől](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák

A [tárolt eljárási minta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) fájl bemutatja, hogyan végezheti el a következő feladatokat. Ha többet szeretne megtudni a Azure Cosmos DB kiszolgálóoldali programozásáról a következő minták futtatása előtt, tekintse meg a [tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)című témakört.

| Tevékenység | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Tárolt eljárás végrehajtása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure. Execute |
| [Tárolt eljárás törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Felhasználókezelés példái
A felhasználói felügyeleti minta fájl a következő feladatokat mutatja be:

| Tevékenység | API-referencia |
| --- | --- |
| Felhasználó létrehozása | - |
| Engedélyek beállítása gyűjteményen vagy dokumentumban | - |
| Felhasználó engedélylistájának lekérése |- |