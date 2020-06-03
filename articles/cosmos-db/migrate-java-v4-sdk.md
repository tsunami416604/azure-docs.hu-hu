---
title: Az alkalmazás migrálása a Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos) használatára
description: Ismerje meg, hogyan frissítheti meglévő Java-alkalmazásait a régebbi Azure Cosmos DB Java SDK-k használatával az újabb Java SDK 4,0 (com. Azure. Cosmos csomag) for Core (SQL) API-hoz.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/26/2020
ms.reviewer: sngun
ms.openlocfilehash: b1ad7b44b34b678eac3348ba3b3ec54fcc508fcf
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310665"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Az alkalmazás migrálása a Azure Cosmos DB Java SDK v4 használatára

> [!IMPORTANT]  
> Az SDK-val kapcsolatos további információkért tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos), a Azure Cosmos db Java SDK v4 [teljesítménnyel kapcsolatos TIPPEKET](performance-tips-java-sdk-v4-sql.md), valamint Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatót](troubleshoot-java-sdk-v4-sql.md).
>

Ez a cikk azt ismerteti, hogyan frissítheti a régebbi Azure Cosmos DB Java SDK-t használó meglévő Java-alkalmazást az újabb Azure Cosmos DB Java SDK 4,0 for Core (SQL) API-hoz. Azure Cosmos DB Java SDK v4 megfelel a `com.azure.cosmos` csomagnak. Ha az alkalmazást a következő Azure Cosmos DB Java SDK-k bármelyikéről telepíti át, akkor használhatja az ebben a dokumentumban található utasításokat: 

* Java SDK szinkronizálása 2. x. x
* Aszinkron Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>A Java SDK és a csomag-hozzárendelések Azure Cosmos DB

A következő táblázat felsorolja a különböző Azure Cosmos DB Java SDK-kat, a csomag nevét és a kiadási információkat:

| Java SDK| Kiadás dátuma | Köteg API-k   | Maven jar  | Java-csomag neve  |API-hivatkozás   | Release Notes (Kibocsátási megjegyzések)  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Aszinkron 2. x. x  | 2018. június    | Aszinkron (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Kibocsátási megjegyzések](sql-api-sdk-async-java.md) |
| 2. x. x szinkronizálása     | Szeptember 2018    | Sync   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Kibocsátási megjegyzések](sql-api-sdk-java.md)  |
| 3. x. x    | 2019. július    | Aszinkron (reaktor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | 2020. április   | Aszinkron (reaktor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>Az SDK-szint implementációjának módosításai

A különböző SDK-k között a legfontosabb implementációs különbségek a következők:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>A RxJava helyére a reaktor szerepel a Azure Cosmos DB Java SDK 3. x. x és 4,0 verziójában.

Ha nem ismeri az aszinkron programozási vagy reaktív programozást, tekintse meg a következőt: a [reaktor mintájának útmutatója](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) az aszinkron programozás és a projekt-visszavezető bevezetéséhez. Ez az útmutató akkor lehet hasznos, ha Azure Cosmos DB Sync Java SDK 2. x. x vagy Azure Cosmos DB Java SDK 3. x. x szinkronizációs API-t korábban használta.

Ha Azure Cosmos DB aszinkron Java SDK 2. x. x verzióját használja, és az 4,0 SDK-ra való áttérést tervezi, tekintse meg a [reaktor vs RxJava útmutatót](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) , amely útmutatást nyújt a RxJava-kód a reaktor használatára való átalakításához.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 közvetlen kapcsolati móddal rendelkezik az aszinkron és a Sync API-kkal

Ha a Azure Cosmos DB Sync Java SDK 2. x. x verzióját használja, vegye figyelembe, hogy a TCP protokollon alapuló közvetlen kapcsolódási mód (a HTTP-vel ellentétben) az aszinkron és a Sync API-k esetében is Azure Cosmos DB Java SDK 4,0-as verzióban van implementálva.

## <a name="api-level-changes"></a>API-szintű változások

A következő API-szint módosul Azure Cosmos DB Java SDK 4. x. x verzióban az előző SDK-k (Java SDK 3. x. x, aszinkron Java SDK 2. x. x és a Sync Java SDK 2. x. x) esetében:

![A Java SDK elnevezési konvencióinak Azure Cosmos DB](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* A Azure Cosmos DB Java SDK 3. x. x és 4,0 az ügyfél erőforrásait a következőképpen tekinti át: `Cosmos<resourceName>` . Például:, `CosmosClient` `CosmosDatabase` , `CosmosContainer` . Míg a 2. x. x verzióban a Azure Cosmos DB Java SDK-k nem rendelkeznek egységes elnevezési sémával.

* Azure Cosmos DB Java SDK 3. x. x és 4,0 egyaránt kínál szinkronizálási és aszinkron API-kat.

  * **Java SDK 4,0** : az összes osztály a szinkronizálási API-hoz tartozik, kivéve, ha az osztály nevét a következővel fűzi hozzá `Async` `Cosmos` .

  * **Java SDK 3. x. x**: az összes osztály az aszinkron API-hoz tartozik, kivéve, ha az osztály nevét a következővel fűzi hozzá `Async` `Cosmos` .

  * **Aszinkron Java SDK 2. x. x**: az osztályok neve hasonló a Java SDK 2. x. x verziójának szinkronizálásához, a név azonban *aszinkron*módon kezdődik.

### <a name="hierarchical-api-structure"></a>Hierarchikus API-struktúra

Azure Cosmos DB a Java SDK 4,0-es és 3. x. x verziója egy hierarchikus API-struktúrát mutat be, amely egy beágyazott módon rendezi az ügyfeleket, az adatbázisokat és a tárolókat, ahogyan az a következő 4,0 SDK-kódrészletben látható:

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

A Azure Cosmos DB Java SDK 2. x. x verziójában az összes erőforrásra és dokumentumra vonatkozó műveletet az ügyfél-példányon keresztül hajtja végre a rendszer.

### <a name="representing-documents"></a>Dokumentumok képviselete

Azure Cosmos DB Java SDK 4,0-as verzióban az egyéni POJO, és `JsonNodes` a dokumentumok Azure Cosmos DBból való olvasására és írására szolgáló két lehetőség.

A Azure Cosmos DB Java SDK 3. x. x verziójában az `CosmosItemProperties` objektumot a nyilvános API teszi elérhetővé, és dokumentum-ábrázolásként szolgál. Ez az osztály az 4,0-es verzióban már nem nyilvánosan elérhető.

### <a name="imports"></a>Importálja

* A Azure Cosmos DB Java SDK 4,0 csomagok kezdete`com.azure.cosmos`
  * Azure Cosmos DB Java SDK 3. x. x csomag kezdete`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4,0 több osztályt helyez el egy beágyazott csomagban `com.azure.cosmos.models` . A csomagok némelyike az alábbiakat tartalmazza:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Az aszinkron API az összes fenti csomaghoz hasonlít
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... stb.

### <a name="accessors"></a>Elérőnek

Azure Cosmos DB Java SDK 4,0 elérhetővé `get` teszi `set` a példány tagjainak elérését és metódusait. Például a `CosmosContainer` példány `container.getId()` és a `container.setId()` metódusok.

Ez különbözik a Azure Cosmos DB Java SDK 3. x. x verziótól, amely egy Fluent felületet tesz elérhetővé. Például egy példány, `CosmosSyncContainer` `container.id()` amely túlterhelt az érték beolvasásához vagy beállításához `id` .

## <a name="code-snippet-comparisons"></a>Kódrészletek összehasonlítása

### <a name="create-resources"></a>Erőforrások létrehozása

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-k közötti erőforrások közötti különbségeket mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Elemek műveletei

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-k közötti műveletek különbségeit mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Indexelés

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-k közötti adatindexelési különbségeket mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Tárolt eljárások

A következő kódrészlet az 4,0 és 3. x. x aszinkron API-k közötti tárolt eljárások különbségeit mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Csatorna módosítása

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-k közötti adatváltozások közötti különbségeket mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Tárolói szint élettartama (TTL)

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-k használatával mutatja be, hogyan lehet időt létrehozni a tárolóban lévő adatai számára.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Elemek szintjének élettartama (TTL)

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-kat használó elemek élettartamának létrehozásával kapcsolatos különbségeket mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>További lépések

* [Java-alkalmazás létrehozása](create-sql-api-java.md) Azure Cosmos db SQL API-alapú adatkezeléshez a v4 SDK használatával
* Tudnivalók a [reaktor-alapú Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) -k használatáról
* Ismerje meg, hogyan konvertálhatja a RxJava aszinkron kódot a reaktorra és az aszinkron kódra a reaktor [vs RxJava útmutató](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) segítségével
