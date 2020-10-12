---
title: Azure Functions Azure Table Storage-kötések
description: Ismerje meg, hogyan használhatók az Azure Table Storage-kötések Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 50706e1b525a3e3a39701ef2135d44c02c35077e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181136"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions Azure Table Storage-kötések

Ez a cikk azt ismerteti, hogyan használhatók az Azure Table Storage-kötések Azure Functionsban. Azure Functions támogatja az Azure Table Storage bemeneti és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A Table Storage-kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomagban, 2. x verzióban vannak megadva. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A Table Storage-kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomag 3. x verziójában érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input (Bemenet)

Egy Azure Storage-fiókban lévő tábla beolvasásához használja az Azure Table Storage bemeneti kötését.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Egy entitás

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy egyoszlopos sort olvas. A rendszer a várólistára küldött összes üzenet esetében elindítja a függvényt.

A (z) "{queueTrigger}" sorcsoport-érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely több olyan tábla sort olvas be, amelyből a `MyPoco` osztály származik `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` csak a [functions v1 futtatókörnyezetben](functions-versions.md)támogatott. Egy másik megoldás, ha `CloudTable` metódus-paramétert használ a tábla olvasásához az Azure Storage SDK használatával. Az alábbi példa egy olyan függvényt mutat be, amely egy Azure Functions naplózási táblázatot kérdez le:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

További információ a CloudTable használatáról: Ismerkedés [Az Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)szolgáltatással.

Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

### <a name="one-entity"></a>Egy entitás

Az alábbi példa egy táblázatos beviteli kötést mutat be egy *function.jsa* fájl-és [C#-parancsfájlhoz](functions-reference-csharp.md) , amely a kötést használja. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A fájl *function.js* az a és a paramétert adja meg `partitionKey` `rowKey` . A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

Az alábbi példa egy táblázatos beviteli kötést mutat be egy *function.jsa* fájl-és [C#-parancsfájlhoz](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az entitásokat egy üzenetsor-üzenetben megadott partíciós kulcshoz.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C#-szkript kód az Azure Storage SDK-ra mutató hivatkozást tartalmaz, hogy az entitás típusa a következőből származtatható `TableEntity` :

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` nem támogatott a [2. x vagy újabb verziók](functions-versions.md)functions futtatókörnyezetében. Egy másik megoldás, ha `CloudTable` metódus-paramétert használ a tábla olvasásához az Azure Storage SDK használatával. Az alábbi példa egy olyan függvényt mutat be, amely egy Azure Functions naplózási táblázatot kérdez le:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

További információ a CloudTable használatáról: Ismerkedés [Az Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)szolgáltatással.

Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy tábla beviteli kötést mutat be egy *function.jsa* fájlban és a [JavaScript-kódban](functions-reference-node.md) , amely a kötést használja. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A fájl *function.js* az a és a paramétert adja meg `partitionKey` `rowKey` . A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Egytáblás sor 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely egy, a Table Storage-ban megadott partícióban található, személyre szabott objektumok listáját adja vissza. A példában a partíciós kulcsot a rendszer kinyeri a http-útvonalból, és a táblanév és a kapcsolatok a függvény beállításaiból származnak. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

A TableInput jegyzet a kérelem JSON-törzsében is kinyerheti a kötéseket, például az alábbi példában látható módon.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Az alábbi példák a szűrőt használják egy adott névvel rendelkező személyek lekérdezésére egy Azure-táblában, és korlátozzák a lehetséges egyezések számát 10 találatra.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Bemenet – attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

 A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy tábla bemeneti kötésének konfigurálásához:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Az attribútum konstruktora a tábla nevét, a partíció kulcsát és a sor kulcsát veszi figyelembe. Az attribútum az `out` alábbi példában látható módon vagy a függvény visszatérési értékén is használható:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Teljes példa: input-C# példa.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A használandó Storage-fiók a következő sorrendben van meghatározva:

* Az `Table` attribútum `Connection` tulajdonsága.
* A `StorageAccount` attribútummal megegyező paraméterre alkalmazott attribútum `Table` .
* A `StorageAccount` függvényre alkalmazott attribútum.
* Az `StorageAccount` osztályra alkalmazott attribútum.
* A Function alkalmazás alapértelmezett Storage-fiókja ("AzureWebJobsStorage").

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@TableInput` jegyzeteket azon paramétereknél, amelyek értéke a Table Storage-ból származik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

---

## <a name="input---configuration"></a>Bemenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Table` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | Értékre kell állítani `table` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban.|
|**irányba** | n/a | Értékre kell állítani `in` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában szereplő táblát vagy entitást jelképezi. | 
|**tableName** | **Táblanév** | A tábla neve.| 
|**partitionKey** | **PartitionKey** |Választható. Az olvasni kívánt tábla entitás partíciós kulcsa. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**rowKey** |**RowKey** | Választható. Az olvasni kívánt tábla entitáshoz tartozó sor kulcsa. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**take** |**Eltarthat** | Választható. A JavaScriptben olvasható entitások maximális száma. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**szűrő** |**Szűrő** | Választható. OData-szűrési kifejezés a JavaScriptben a tábla beviteléhez. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**kapcsolat** |**Kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. A beállítás lehet egy "AzureWebJobs" előre rögzített alkalmazás vagy a kapcsolatok karakterláncának neve. Ha például a beállítás neve "AzureWebJobsMyStorage", itt adhatja meg a "MyStorage" értéket. A functions futtatókörnyezet automatikusan megkeresi a "AzureWebJobsMyStorage" nevű alkalmazás-beállítást. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet – használat

# <a name="c"></a>[C#](#tab/csharp)

* **Egy sor olvasása a következőben:**

  Beállítás `partitionKey` és `rowKey` . A tábla adataihoz a metódus paraméterének használatával férhet hozzá `T <paramName>` . A C#-szkriptben a `paramName` `name` *function.js*tulajdonságában megadott érték van megadva. `T` általában olyan típus, amely `ITableEntity` a-ból implementál vagy származtat `TableEntity` . A `filter` és a `take` Tulajdonságok nem használatosak ebben a forgatókönyvben.

* **Egy vagy több sor olvasása**

  A tábla adataihoz a metódus paraméterének használatával férhet hozzá `IQueryable<T> <paramName>` . A C#-szkriptben a `paramName` `name` *function.js*tulajdonságában megadott érték van megadva. `T` olyan típusnak kell lennie, amely `ITableEntity` a-ből implementál vagy származtat `TableEntity` . `IQueryable`A módszerek használatával bármilyen szűrést végezhet. A `partitionKey` , `rowKey` , `filter` és `take` tulajdonságokat nem használja ebben a forgatókönyvben.  

  > [!NOTE]
  > `IQueryable` nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik lehetőség egy [CloudTable paramName metódus paraméter használata](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) a tábla olvasásához az Azure Storage SDK használatával. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

* **Egy sor olvasása a következőben:**

  Beállítás `partitionKey` és `rowKey` . A tábla adataihoz a metódus paraméterének használatával férhet hozzá `T <paramName>` . A C#-szkriptben a `paramName` `name` *function.js*tulajdonságában megadott érték van megadva. `T` általában olyan típus, amely `ITableEntity` a-ból implementál vagy származtat `TableEntity` . A `filter` és a `take` Tulajdonságok nem használatosak ebben a forgatókönyvben.

* **Egy vagy több sor olvasása**

  A tábla adataihoz a metódus paraméterének használatával férhet hozzá `IQueryable<T> <paramName>` . A C#-szkriptben a `paramName` `name` *function.js*tulajdonságában megadott érték van megadva. `T` olyan típusnak kell lennie, amely `ITableEntity` a-ből implementál vagy származtat `TableEntity` . `IQueryable`A módszerek használatával bármilyen szűrést végezhet. A `partitionKey` , `rowKey` , `filter` és `take` tulajdonságokat nem használja ebben a forgatókönyvben.  

  > [!NOTE]
  > `IQueryable` nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik lehetőség egy [CloudTable paramName metódus paraméter használata](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) a tábla olvasásához az Azure Storage SDK használatával. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Adja meg a `filter` és a `take` tulajdonságokat. Nincs beállítva `partitionKey` vagy `rowKey` . A bemeneti tábla entitás (vagy entitások) elérése a használatával `context.bindings.<BINDING_NAME>` . A deszerializált objektumok rendelkeznek `RowKey` és `PartitionKey` tulajdonságokkal rendelkeznek.

# <a name="python"></a>[Python](#tab/python)

A tábla adatát a rendszer JSON-karakterláncként adja át a függvénynek. Az üzenet deszerializálása a `json.loads` bemeneti [példában](#input)látható módon.

# <a name="java"></a>[Java](#tab/java)

A [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) attribútum hozzáférést biztosít a függvényt kiváltó tábla sorához.

---

## <a name="output"></a>Kimenet

Egy Azure Table Storage-beli kimeneti kötés használatával entitásokat írhat egy Azure Storage-fiókba tartozó táblába.

> [!NOTE]
> Ez a kimeneti kötés nem támogatja a meglévő entitások frissítését. `TableOperation.Replace`Egy meglévő entitás frissítéséhez használja az [Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) műveletét.

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy http-triggert használ egy egyoszlopos sor írásához. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy tábla kimeneti kötést mutat be egy *function.jsa* fájl-és [C#-parancsfájlhoz](functions-reference-csharp.md) , amely a kötést használja. A függvény több tábla entitást ír.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy tábla kimeneti kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény több tábla entitást ír.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan használható a Table Storage kimeneti kötése. A `table` kötés úgy van konfigurálva a *function.jsban* , hogy értékeket rendel hozzá,, `name` `tableName` `partitionKey` és `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

A következő függvény egyedi UUI hoz létre az `rowKey` értékhez, és megőrzi az üzenetet a Table Storage szolgáltatásban.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy olyan Java-függvényt mutat be, amely egy HTTP-triggert használ egy egyoszlopos sor írásához.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Az alábbi példa egy olyan Java-függvényt mutat be, amely egy HTTP-triggert használ több táblázat sorok írásához.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Kimenet – attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Az attribútum konstruktora a tábla nevét adja meg. Az attribútum az `out` alábbi példában látható módon vagy a függvény visszatérési értékén is használható:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Teljes példa: [output-C# példa](#output).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: [input-attributes](#input---attributes-and-annotations).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a paraméterek [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) jegyzetét, hogy értékeket írjon a Table Storage-ba.

[További részletekért](#output)tekintse meg a példát.

---

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Table` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | Értékre kell állítani `table` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban.|
|**irányba** | n/a | Értékre kell állítani `out` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n/a | A táblát vagy entitást jelölő függvény kódjában használt változó neve. Állítsa a értékre `$return` a függvény visszatérési értékének hivatkozásához.| 
|**tableName** |**Táblanév** | A tábla neve.| 
|**partitionKey** |**PartitionKey** | Az írni kívánt tábla entitás partíciós kulcsa. Tekintse meg a [használat című szakaszt](#output---usage) , amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**rowKey** |**RowKey** | Az írni kívánt tábla entitáshoz tartozó sor kulcsa. Tekintse meg a [használat című szakaszt](#output---usage) , amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**kapcsolat** |**Kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" értékre van állítva `connection` , a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

# <a name="c"></a>[C#](#tab/csharp)

A kimeneti tábla entitásának elérése metódus-paraméterrel `ICollector<T> paramName` , vagy a (z `IAsyncCollector<T> paramName` `T` ) és a (z `PartitionKey` `RowKey` ) tulajdonságot tartalmazza. Ezeket a tulajdonságokat gyakran a megvalósítás vagy az `ITableEntity` öröklés kíséri `TableEntity` .

Azt is megteheti, `CloudTable` hogy az Azure Storage SDK használatával metódus paraméterrel ír a táblába. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A kimeneti tábla entitásának elérése metódus-paraméterrel `ICollector<T> paramName` , vagy a (z `IAsyncCollector<T> paramName` `T` ) és a (z `PartitionKey` `RowKey` ) tulajdonságot tartalmazza. Ezeket a tulajdonságokat gyakran a megvalósítás vagy az `ITableEntity` öröklés kíséri `TableEntity` . Az `paramName` érték a `name` *function.js*tulajdonságában van megadva.

Azt is megteheti, `CloudTable` hogy az Azure Storage SDK használatával metódus paraméterrel ír a táblába. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti esemény eléréséhez használja `context.bindings.<name>` a `<name>` `name` *function.js*tulajdonságában megadott értéket.

# <a name="python"></a>[Python](#tab/python)

Két lehetőség áll rendelkezésre a táblázatos tárolási sor üzenetének egy függvényből való kiosztására:

- Visszaadott **érték**: állítsa be `name` *function.js* tulajdonságát a értékre `$return` . Ezzel a konfigurációval a függvény visszatérési értéke táblázatos tárolási sorként marad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metódusnak, amely [kimenő](/python/api/azure-functions/azure.functions.out?view=azure-python) típusként van deklarálva. Az átadott érték az `set` Event hub-üzenetként is megmarad.

# <a name="java"></a>[Java](#tab/java)

Két lehetőség áll rendelkezésre a Table Storage-sorok függvényből történő kiosztására a [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) -jegyzet használatával:

- Visszaadott **érték**: a jegyzetnek a függvényhez való alkalmazásával a függvény visszatérési értéke Table Storage-sorként marad.

- **Fontos**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet egy adott típusú paraméterre [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , ahol `T` a tartalmazza a `PartitionKey` és a `RowKey` tulajdonságokat. Ezeket a tulajdonságokat gyakran a megvalósítás vagy az `ITableEntity` öröklés kíséri `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| Táblázat | [Tábla Hibakódai](/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tábla, üzenetsor | [Tárolási hibakódok](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor | [Hibaelhárítás](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
