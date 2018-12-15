---
title: Tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata az Azure Cosmos DB SDK-k meghívása
description: Ismerje meg, hogyan kell regisztrálni, és tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata az Azure Cosmos DB SDK-k hívása
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/08/2018
ms.author: mjbrown
ms.openlocfilehash: d06a1efd1b706d242cbc5af9e93b3d08c84c575e
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410917"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Regisztrálása és használata tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB-ben

Az Azure Cosmos DB SQL API támogatja a regisztráció és a tárolt eljárások, eseményindítók és felhasználó által definiált függvények (UDF) javascriptben írt meghívása. Használhatja az SQL API [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) vagy [Python](sql-api-sdk-python.md) SDK-k regisztrálása és a tárolt eljárások hívása. Miután beállított egy vagy több tárolt eljárások, eseményindítók és felhasználó által definiált függvények, betölteni, és megtekintheti azokat a [az Azure portal](https://portal.azure.com/) az adatkezelő használatával.

## <a id="stored-procedures"></a>Tárolt eljárások futtatása

Tárolt eljárások a JavaScript használatával írt. Azok, frissíteni, olvassa el a lekérdezést, törölhetnek és hozhatnak létre egy Azure Cosmos-tárolóban lévő elemek. További tájékoztatást lásd az Azure Cosmos DB, a tárolt eljárások írásával [írásával tárolt eljárások az Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) cikk.

Az alábbi példák bemutatják, hogyan regisztrálja, és a egy tárolt eljárást hívja az Azure Cosmos DB SDK-k használatával. Tekintse meg [hozzon létre egy dokumentumot](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) tárolt eljárás végrehajtásához a forrás mentésekor `spCreateToDoItem.js`.

> [!NOTE]
> A particionált tárolók, a tárolt eljárás végrehajtása közben partíciókulcs-értékkel kell adni a kérés beállításokat. Tárolt eljárások mindig hatóköre egy partíciókulcsot. Egy másik partíciókulcs-értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárás. Ez is alkalmazandó, valamint eseményindítók.

### <a name="stored-procedures---net-sdk"></a>Tárolt eljárások – .NET SDK-val

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a .NET SDK-val:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a .NET SDK-val:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Tárolt eljárások – Java SDK-val

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a Java SDK-val:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a Java SDK-val:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Tárolt eljárások – JavaScript SDK

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a JavaScript SDK-val

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a JavaScript SDK-val:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Tárolt eljárások – Python SDK-t

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a Python SDK-val

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
            'id': 'spCreateToDoItem',
            'serverScript': file_contents,
        }
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a Python SDK-val

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Futtatása előtti eseményindítók

Az alábbi példák bemutatják, hogyan regisztrálja és előtti trigger meghívása az Azure Cosmos DB SDK-k használatával. Tekintse meg a [előtti eseményindító példa](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) a forrás az üzem előtti eseményindító mentésekor `trgPreValidateToDoItemTimestamp.js`.

Végrehajtásakor, előtti eseményindítók adja át a rendszer a RequestOptions objektum megadásával `PreTriggerInclude` , az eseményindító nevét majd egy lista objektumban.

> [!NOTE]
> Annak ellenére, hogy az eseményindító neve átadott listaként, műveletenként csak egy eseményindító továbbra is hajthat végre.

### <a name="pre-triggers---net-sdk"></a>Üzem előtti triggerek – a .NET SDK-val

A következő kód bemutatja, hogyan regisztrálhat egy üzem előtti eseményindítót, a .NET SDK használatával:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

A következő kód bemutatja, hogyan hívhat meg egy üzem előtti eseményindítót, a .NET SDK használatával:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Üzem előtti triggerek – Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy üzem előtti eseményindítót, a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy üzem előtti eseményindítót, a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Üzem előtti triggerek – JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy üzem előtti eseményindítót a JavaScript SDK-val:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

A következő kód bemutatja, hogyan hívhat meg egy üzem előtti eseményindítót a JavaScript SDK-val:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Üzem előtti triggerek – Python SDK-t

A következő kód bemutatja, hogyan regisztrálhat egy üzem előtti eseményindítót, a Python SDK-val:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPreValidateToDoItemTimestamp',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Pre,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy üzem előtti eseményindítót, a Python SDK-val:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'category': 'Personal', 'name': 'Groceries', 'description':'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, { 'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Futtatása utáni eseményindítók

Az alábbi példák bemutatják, hogyan utáni eseményindító regisztrálása az Azure Cosmos DB SDK-k használatával. Tekintse meg a [utáni eseményindító példa](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) a forrás, az alkalmazásnak eseményindító mentésekor `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Utáni és eseményindítók – .NET SDK-val

A következő kód bemutatja, hogyan regisztrálhat egy utáni eseményindítót, a .NET SDK használatával:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js");,
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

A következő kód bemutatja, hogyan hívhat meg egy utáni eseményindítót, a .NET SDK használatával:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

var options = { postTriggerInclude: "trgPostUpdateMetadata" };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Utáni és eseményindítók – Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy utáni eseményindítót, a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy utáni eseményindítót, a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Utáni és eseményindítók – JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat a JavaScript SDK-val utáni eseményindító:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

A következő kód bemutatja, hogyan hívhat meg egy utáni eseményindítót a JavaScript SDK-val:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Utáni és eseményindítók – Python SDK-t

A következő kód bemutatja, hogyan kell regisztrálni egy utáni eseményindítót, a Python SDK-val:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Post,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy utáni eseményindítót, a Python SDK-val:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'name': 'artist_profile_1023', 'artist': 'The Band', 'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, { 'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Felhasználó által definiált függvények használata

Az alábbi példák bemutatják, hogyan regisztrálja a felhasználó által definiált függvény az Azure Cosmos DB SDK-k használatával. Ebben [felhasználó által definiált függvény példa](how-to-write-stored-procedures-triggers-udfs.md#udfs) a forrás, az alkalmazásnak eseményindító mentésekor `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Felhasználó által definiált függvények – .NET SDK-val

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvény a .NET SDK használatával:

```csharp
string udfId = "udfTax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvény a .NET SDK használatával:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Felhasználó által definiált függvények – Java SDK-val

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvény a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "udfTax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvény a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Felhasználó által definiált függvények – JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvény JavaScript SDK-val:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "udfTax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvény JavaScript SDK-val:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Felhasználó által definiált függvények – Python SDK-t

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvény a Python SDK-val:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
        }
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvény a Python SDK-val:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(container_link, 'SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000'))
```

## <a name="next-steps"></a>További lépések

További fogalmakat és útmutató írási vagy tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata az Azure Cosmos DB:

- [Azure Cosmos DB-vel működő tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB-ben](stored-procedures-triggers-udfs.md)
- [Működő JavaScript nyelvvel integrált lekérdezési API-t az Azure Cosmos DB-ben](javascript-query-api.md)
- [Az Azure Cosmos DB-ben tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md)
- [Hogyan írhat a tárolt eljárások és eseményindítók az Azure Cosmos DB a Javascript Query API használatával](how-to-write-javascript-query-api.md)
