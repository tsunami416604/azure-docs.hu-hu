---
title: Tárolt eljárások, eseményindítók és felhasználó által definiált függvények regisztrálása és használata az Azure Cosmos DB SDK-kban
description: Ismerje meg, hogyan regisztrálhatja és hívhatja meg a tárolt eljárásokat, eseményindítókat és felhasználó által definiált függvényeket az Azure Cosmos DB SDK-k használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: mjbrown
ms.openlocfilehash: 00740bc2255962089789682e3227ce414fd0ce64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582500"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Tárolt eljárások, eseményindítók és felhasználói függvények regisztrálása és használata az Azure Cosmos DB-ben

Az Azure Cosmos DB-ben lévő SQL API támogatja a JavaScript nyelven írt tárolt eljárások, eseményindítók és felhasználói függvények regisztrálását és meghívását. Az SQL API [.NET,](sql-api-sdk-dotnet.md) [.NET Core,](sql-api-sdk-dotnet-core.md) [Java,](sql-api-sdk-java.md) [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md)vagy [Python](sql-api-sdk-python.md) SDK-k segítségével regisztrálhatja és meghívhatja a tárolt eljárásokat. Miután definiált egy vagy több tárolt eljárást, eseményindítókat és a felhasználó által definiált függvényeket, betöltheti és megtekintheti azokat az [Azure Portalon](https://portal.azure.com/) az Adatkezelő használatával.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>A tárolt eljárások futtatása

A tárolt eljárások javascript használatával történnek. Az Azure Cosmos-tárolóban létrehozhatnak, frissíthetnek, olvashatnak, kérdezhetnek és törölhetnek elemeket. A tárolt eljárások Azure Cosmos DB-ben történő írásáról a [Tárolt eljárások írása az Azure Cosmos DB-ben](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) című cikkben című témakörben olvashat bővebben.

Az alábbi példák bemutatják, hogyan regisztrálhat és hívhat meg egy tárolt eljárást az Azure Cosmos DB SDK-k használatával. Lásd: [Dokumentum létrehozása](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) a tárolt eljárás forrásaként. `spCreateToDoItem.js`

> [!NOTE]
> Particionált tárolók esetén a tárolt eljárás végrehajtásakor meg kell adni egy partíciókulcs-értéket a kérelembeállításokban. A tárolt eljárások hatóköre mindig partíciókulcsra terjed ki. A más partíciókulcs-értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárás számára. Ez az eseményindítókra is vonatkozott.

### <a name="stored-procedures---net-sdk-v2"></a>Tárolt eljárások - .NET SDK V2

A következő példa bemutatja, hogyan regisztrálhat tárolt eljárást a .NET SDK V2 használatával:

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

A következő kód bemutatja, hogyan hívható meg egy tárolt eljárás a .NET SDK V2 használatával:

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
```

### <a name="stored-procedures---net-sdk-v3"></a>Tárolt eljárások - .NET SDK V3

A következő példa bemutatja, hogyan regisztrálhat tárolt eljárást a .NET SDK V3 használatával:

```csharp
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("database", "container").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = "spCreateToDoItem",
    Body = File.ReadAllText(@"..\js\spCreateToDoItem.js")
});
```

A következő kód bemutatja, hogyan hívható meg egy tárolt eljárás a .NET SDK V3 használatával:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), newItems);
```

### <a name="stored-procedures---java-sdk"></a>Tárolt eljárások - Java SDK

A következő példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a Java SDK használatával:

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

A következő kód bemutatja, hogyan hívható meg egy tárolt eljárás a Java SDK használatával:

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
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Tárolt eljárások - JavaScript SDK

A következő példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a JavaScript SDK használatával

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

A következő kód bemutatja, hogyan hívható meg egy tárolt eljárás a JavaScript SDK használatával:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Tárolt eljárások - Python SDK

A következő példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a Python SDK használatával

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

A következő kód bemutatja, hogyan hívható meg egy tárolt eljárás a Python SDK használatával

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

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>Az előindítók futtatása

Az alábbi példák bemutatják, hogyan regisztrálhat és hívhat meg egy előindítót az Azure Cosmos DB SDK-k használatával. Tekintse meg az [eseményindító előtti példát,](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) mint az `trgPreValidateToDoItemTimestamp.js`előesemény-eseményindító forrását a mentés a néven.

Végrehajtáskor az előeseményindítók a RequestOptions objektumban `PreTriggerInclude` kerülnek átadásra, majd az eseményindító nevének átadása egy Lista objektumban.

> [!NOTE]
> Annak ellenére, hogy az eseményindító neve listaként van átadható, műveletenként továbbra is csak egy eseményindító hajtható végre.

### <a name="pre-triggers---net-sdk-v2"></a>Előindítók - .NET SDK V2

A következő kód bemutatja, hogyan regisztrálhat előindítót a .NET SDK V2 használatával:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

A következő kód bemutatja, hogyan hívható meg egy előindító a .NET SDK V2 használatával:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>Előindítók - .NET SDK V3

A következő kód bemutatja, hogyan regisztrálhat előindítót a .NET SDK V3 használatával:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

A következő kód bemutatja, hogyan hívható meg egy előindító a .NET SDK V3 használatával:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Előindítók - Java SDK

A következő kód bemutatja, hogyan regisztrálhat előeseményindítót a Java SDK használatával:

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

A következő kód bemutatja, hogyan hívhat meg előindítót a Java SDK használatával:

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

### <a name="pre-triggers---javascript-sdk"></a>Előindítók - JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat előeseményindítót a JavaScript SDK használatával:

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

A következő kód bemutatja, hogyan hívhat meg előindítót a JavaScript SDK használatával:

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

### <a name="pre-triggers---python-sdk"></a>Előindítók - Python SDK

A következő kód bemutatja, hogyan regisztrálhat egy előeseményindítót a Python SDK használatával:

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

A következő kód bemutatja, hogyan hívhat meg egy előindítót a Python SDK használatával:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>Az eseményindítók futtatása

Az alábbi példák bemutatják, hogyan regisztrálhat egy utóindító t az Azure Cosmos DB SDK-k használatával. Tekintse meg a [post-trigger példát,](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) mint a `trgPostUpdateMetadata.js`forrás a post-trigger menti a .

### <a name="post-triggers---net-sdk-v2"></a>Eseményindítók utáni műveletek - .NET SDK V2

A következő kód bemutatja, hogyan regisztrálhat egy utóindítót a .NET SDK V2 használatával:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

A következő kód bemutatja, hogyan hívható meg egy eseményindító ta-

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Eseményindítók utáni műveletek - .NET SDK V3

A következő kód bemutatja, hogyan regisztrálhat egy utóindítót a .NET SDK V3 használatával:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

A következő kód bemutatja, hogyan hívható meg egy eseményindító ta-

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Eseményindítók után - Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy eseményindítót a Java SDK használatával:

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

A következő kód bemutatja, hogyan hívhat meg egy eseményindítót a Java SDK használatával:

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

### <a name="post-triggers---javascript-sdk"></a>Eseményindítók utáni műveletek - JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy eseményindítót a JavaScript SDK használatával:

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

A következő kód bemutatja, hogyan hívhat meg egy eseményindítót a JavaScript SDK használatával:

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

### <a name="post-triggers---python-sdk"></a>Eseményindítók utáni - Python SDK

A következő kód bemutatja, hogyan regisztrálhat egy eseményindítót a Python SDK használatával:

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

A következő kód bemutatja, hogyan hívhat meg egy eseményindítót a Python SDK használatával:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>A felhasználó által definiált függvények funkcióinak funkcióinak

Az alábbi példák bemutatják, hogyan regisztrálhat egy felhasználó által definiált függvényt az Azure Cosmos DB SDK-k használatával. Tekintse meg ezt a [felhasználó által definiált függvénypéldát,](how-to-write-stored-procedures-triggers-udfs.md#udfs) `udfTax.js`mivel a post-trigger forrása a program a néven kerül mentésre.

### <a name="user-defined-functions---net-sdk-v2"></a>Felhasználó által definiált függvények - .NET SDK V2

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a .NET SDK V2 használatával:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

A következő kód bemutatja, hogyan hívható meg egy felhasználó által definiált függvény a .NET SDK V2 használatával:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Felhasználó által definiált függvények - .NET SDK V3

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a .NET SDK V3 használatával:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

A következő kód bemutatja, hogyan hívható meg egy felhasználó által definiált függvény a .NET SDK V3 használatával:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Felhasználó által definiált függvények - Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívható meg egy felhasználó által definiált függvény a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
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

### <a name="user-defined-functions---javascript-sdk"></a>Felhasználó által definiált függvények - JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

A következő kód bemutatja, hogyan hívható meg egy felhasználó által definiált függvény a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Felhasználó által definiált függvények - Python SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a Python SDK használatával:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvényt a Python SDK használatával:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>További lépések

További fogalmak és a tárolt eljárások, eseményindítók és a felhasználó által definiált függvények írásának és használatának módjáról az Azure Cosmos DB-ben:

- [Az Azure Cosmos DB tárolt eljárásainak, eseményindítóinak és felhasználó által definiált függvényeinek együttműködése az Azure Cosmos DB-ben](stored-procedures-triggers-udfs.md)
- [JavaScript-nyelvű integrált lekérdezési API-val való együttműködés az Azure Cosmos DB-ben](javascript-query-api.md)
- [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása az Azure Cosmos DB-ben](how-to-write-stored-procedures-triggers-udfs.md)
- [Tárolt eljárások és eseményindítók írása javascript-lekérdezési API használatával az Azure Cosmos DB-ben](how-to-write-javascript-query-api.md)
