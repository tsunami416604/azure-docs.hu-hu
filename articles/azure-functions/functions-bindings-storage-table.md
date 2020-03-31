---
title: Azure Table storage-kötések az Azure Functionshez
description: Ismerje meg, hogyan használhatja az Azure Table storage-kötéseket az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277179"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table storage-kötések az Azure Functionshez

Ez a cikk bemutatja, hogyan dolgozhat az Azure Table storage-kötések az Azure Functionsben. Az Azure Functions támogatja az Azure Table storage bemeneti és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

A table storage kötések a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomag 2.x-es verziójában találhatók. A csomag forráskódja az [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok - 2.x vagy újabb funkciók

A table storage kötések a [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomag 3.x-es verzióban találhatók. A csomag forráskódja az [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input (Bemenet)

Az Azure Table storage input-kötés használatával egy Azure Storage-fiókban lévő táblát olvashat.

# <a name="c"></a>[C #](#tab/csharp)

### <a name="one-entity"></a>Egy entitás

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen táblázatsort olvas be. A táblában beszúrt minden rekord esetében a függvény aktiválódik.

A(z) "{queueTrigger}" sorkulcs érték azt jelzi, hogy a sorkulcs a várólista-üzenet karakterláncából származik.

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

### <a name="iqueryable"></a>IQueryable (IQueryable)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat `MyPoco` be, `TableEntity`amely több táblázatsort olvas be, ahonnan az osztály származik.

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

### <a name="cloudtable"></a>CloudTábla

`IQueryable`a [Functions v2 futásidő](functions-versions.md)nem támogatott. Egy másik lehetőség, `CloudTable` hogy egy metódus paramétert az Azure Storage SDK használatával olvasni a táblázatot. Íme egy példa egy függvényre, amely lekérdezi az Azure Functions naplótábláját:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
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

A CloudTable használatáról az [Azure Table storage használatának első lépései](../cosmos-db/table-storage-how-to-use-dotnet.md)című témakörben talál további információt.

Ha megpróbál kötődni, `CloudTable` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

### <a name="one-entity"></a>Egy entitás

A következő példa egy tábla bemeneti kötést mutat be egy *function.json* fájlban és [a C# parancsfájlkódot,](functions-reference-csharp.md) amely a kötést használja. A függvény egy várólista-eseményindítót használ egyetlen táblasor olvasásához. 

A *function.json* fájl `partitionKey` a `rowKey`és a . A(z) `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs a várólista-üzenet karakterláncából származik.

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

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

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

### <a name="iqueryable"></a>IQueryable (IQueryable)

A következő példa egy tábla bemeneti kötést mutat be egy *function.json* fájlban és [a C# parancsfájlkódot,](functions-reference-csharp.md) amely a kötést használja. A függvény egy üzenetsorüzenetben megadott partíciókulcs entitásait olvassa be.

Itt a *function.json* fájl:

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

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájlkód hozzáad egy hivatkozást az Azure Storage SDK-hoz, így az entitástípus származhat: `TableEntity`

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
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

### <a name="cloudtable"></a>CloudTábla

`IQueryable`a [2.x-es vagy](functions-versions.md)újabb verziók functions futásideje nem támogatott). Egy másik lehetőség, `CloudTable` hogy egy metódus paramétert az Azure Storage SDK használatával olvasni a táblázatot. Íme egy példa egy függvényre, amely lekérdezi az Azure Functions naplótábláját:

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
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
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

A CloudTable használatáról az [Azure Table storage használatának első lépései](../cosmos-db/table-storage-how-to-use-dotnet.md)című témakörben talál további információt.

Ha megpróbál kötődni, `CloudTable` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.


# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy tábla bemeneti kötést mutat be egy *function.json* fájlban és [javascript kódot,](functions-reference-node.md) amely a kötést használja. A függvény egy várólista-eseményindítót használ egyetlen táblasor olvasásához. 

A *function.json* fájl `partitionKey` a `rowKey`és a . A(z) `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs a várólista-üzenet karakterláncából származik.

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

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Egyasztalos sor 

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

A következő példa egy HTTP-aktivált függvényt mutat be, amely a Table storage megadott partícióján lévő személyek objektumainak listáját adja vissza. A példában a partíciókulcs kinyeri a http útvonalat, és a tableName és a kapcsolat a függvény beállításait. 

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

A TableInput-kommentár a kötéseket is kinyerheti a kérelem json törzséből, ahogy azt a következő példa mutatja.

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

A következő példák a szűrő használatával lekérdezi az Azure-táblában egy adott nevű személyeket, és a lehetséges egyezések számát 10 eredményre korlátozza.

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

## <a name="input---attributes-and-annotations"></a>Bemenet - attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

 A [C# osztálytárakban](functions-dotnet-class-library.md)a következő attribútumokkal konfigurálhat táblabemeneti kötést:

* [TableAttribute (Táblaattribútum)](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Az attribútum konstruktora veszi a tábla nevét, partíciókulcsát és sorkulcsát. Az attribútum használható egy `out` paraméteren vagy a függvény visszatérési értékén, ahogy az a következő példában látható:

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

  Beállíthatja, `Connection` hogy a tulajdonság adja meg a tárfiókot használni, ahogy az a következő példában látható:

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

  Egy teljes példa, lásd: Input - C# példa.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módja a használandó tárfiók megadásának. A konstruktor egy tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét veszi fel. Az attribútum a paraméter, a metódus vagy az osztály szintjén alkalmazható. A következő példa az osztályszintet és a metódusszintet mutatja be:

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

A felhasználandó tárfiókot a következő sorrendben határozzuk meg:

* Az `Table` attribútum tulajdona. `Connection`
* Az `StorageAccount` `Table` attribútummal azonos paraméterre alkalmazott attribútum.
* A `StorageAccount` függvényre alkalmazott attribútum.
* Az `StorageAccount` osztályra alkalmazott attribútum.
* A függvényalkalmazás alapértelmezett tárfiókja ("AzureWebJobsStorage" alkalmazásbeállítás).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A [Java függvények futásidejű könyvtárában](/java/api/overview/azure/functions/runtime)használja a jegyzetet azon `@TableInput` paramétereken, amelyek értéke a Table storage-ból származna.  Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

---

## <a name="input---configuration"></a>Bemenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Table` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `table`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza a kötést az Azure Portalon.|
|**direction** | n/a | A beállításnak `in`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza a kötést az Azure Portalon. |
|**név** | n/a | A függvénykódban a táblát vagy entitást jelölő változó neve. | 
|**táblaneve** | **TableName** | A tábla neve.| 
|**partíciókulcs** | **PartitionKey** |Választható. A beolvasandó táblaentitás partíciókulcsa. A [tulajdonság](#input---usage) használatával kapcsolatos útmutatást a használati szakaszban talál.| 
|**rowKey (sorkulcs)** |**RowKey** | Választható. A beolvasandó táblaentitás sorkulcsa. A [tulajdonság](#input---usage) használatával kapcsolatos útmutatást a használati szakaszban talál.| 
|**venni** |**Vegyünk** | Választható. A JavaScript-ben olvasandó entitások maximális száma. A [tulajdonság](#input---usage) használatával kapcsolatos útmutatást a használati szakaszban talál.| 
|**Szűrő** |**Szűrő** | Választható. OData-szűrőkifejezés a JavaScript-alapú táblabevitelhez. A [tulajdonság](#input---usage) használatával kapcsolatos útmutatást a használati szakaszban talál.| 
|**Kapcsolat** |**Kapcsolat** | A kötéshez használandó Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például "MyStorage" beállítást ad meg, `connection` a Functions futásidejű megkeresi a "MyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

# <a name="c"></a>[C #](#tab/csharp)

* **Egy sor olvasása**

  Állítsa `partitionKey` `rowKey`be és a . A táblaadatok elérése metódusparaméter `T <paramName>`használatával. A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. `T`általában olyan típus, amely `ITableEntity` a eszközét valósítja meg vagy abból `TableEntity`származik. Ebben `filter` `take` a forgatókönyvben a és a tulajdonságok nem használatosak.

* **Egy vagy több sor olvasása**

  A táblaadatok elérése metódusparaméter `IQueryable<T> <paramName>`használatával. A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. `T`olyan típusnak kell `ITableEntity` lennie, amely `TableEntity`a kívánt eszközt hajtja végre vagy származik. A szükséges `IQueryable` szűrési módszerekkel bármilyen szűrést elvégezhet. Ebben `partitionKey` `rowKey`a `filter`forgatókönyvben a , , és `take` a tulajdonságok nem használatosak.  

  > [!NOTE]
  > `IQueryable`a [Functions v2 futásidő](functions-versions.md)nem támogatott. Egy másik lehetőség, hogy [egy CloudTable paramName metódus paraméter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) t az Azure Storage SDK használatával olvassa a táblát. Ha megpróbál kötődni, `CloudTable` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

* **Egy sor olvasása**

  Állítsa `partitionKey` `rowKey`be és a . A táblaadatok elérése metódusparaméter `T <paramName>`használatával. A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. `T`általában olyan típus, amely `ITableEntity` a eszközét valósítja meg vagy abból `TableEntity`származik. Ebben `filter` `take` a forgatókönyvben a és a tulajdonságok nem használatosak.

* **Egy vagy több sor olvasása**

  A táblaadatok elérése metódusparaméter `IQueryable<T> <paramName>`használatával. A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. `T`olyan típusnak kell `ITableEntity` lennie, amely `TableEntity`a kívánt eszközt hajtja végre vagy származik. A szükséges `IQueryable` szűrési módszerekkel bármilyen szűrést elvégezhet. Ebben `partitionKey` `rowKey`a `filter`forgatókönyvben a , , és `take` a tulajdonságok nem használatosak.  

  > [!NOTE]
  > `IQueryable`a [Functions v2 futásidő](functions-versions.md)nem támogatott. Egy másik lehetőség, hogy [egy CloudTable paramName metódus paraméter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) t az Azure Storage SDK használatával olvassa a táblát. Ha megpróbál kötődni, `CloudTable` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Állítsa `filter` be `take` a és a tulajdonságokat. Ne állítsa `partitionKey` be `rowKey`a vagy a. A beviteli tábla entitásának (vagy entitásának) elérése a használatával. `context.bindings.<BINDING_NAME>` A deszerializált objektumok és `RowKey` `PartitionKey` tulajdonságai.

# <a name="python"></a>[Python](#tab/python)

A táblaadatok JSON-karakterláncként kerül átadásra a függvénynek. Az üzenet szerializálásának lealacsonyítása a bemeneti `json.loads` [példában](#input)látható módon.

# <a name="java"></a>[Java](#tab/java)

A [TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) attribútum hozzáférést biztosít a függvényt kiváltó táblasorhoz.

---

## <a name="output"></a>Kimenet

Azure Table storage-kimeneti kötés használatával entitásokat írhat egy Azure Storage-fiók ban lévő táblába.

> [!NOTE]
> Ez a kimeneti kötés nem támogatja a meglévő entitások frissítését. Az `TableOperation.Replace` [Azure Storage SDK-ból származó](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) művelet használatával frissítheti a meglévő entitást.

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy HTTP-eseményindítót használ egyetlen táblázatsor írásához. 

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy tábla kimeneti kötést mutat be egy *function.json* fájlban és [a C# parancsfájlkódot,](functions-reference-csharp.md) amely a kötést használja. A függvény több táblaentitást ír.

Itt a *function.json* fájl:

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

A [konfigurációs](#output---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy tábla kimeneti kötést mutat be egy *function.json* fájlban és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvény több táblaentitást ír.

Itt a *function.json* fájl:

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

A [konfigurációs](#output---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

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

A következő példa bemutatja, hogyan használhatja a table storage output kötés. A `table` kötés a *function.json* függvényben van `name` `tableName`konfigurálva úgy, hogy értékeket rendel a , , `partitionKey`és `connection`:

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

A következő függvény létrehoz egy egyedi `rowKey` UUI az értéket, és megőrzi az üzenetet table storage.

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

A következő példa egy Java függvényt mutat be, amely EGY HTTP-eseményindítót használ egyetlen táblázatsor írásához.

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

A következő példa egy Java függvényt mutat be, amely HTTP-eseményindítót használ több táblázatsor írásához.

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

## <a name="output---attributes-and-annotations"></a>Kimenet - attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [TableAttribute tulajdonságot.](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

Az attribútum konstruktora felveszi a tábla nevét. Az attribútum használható egy `out` paraméteren vagy a függvény visszatérési értékén, ahogy az a következő példában látható:

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

Beállíthatja, `Connection` hogy a tulajdonság adja meg a tárfiókot használni, ahogy az a következő példában látható:

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

Egy teljes példa, lásd: [Kimenet - C# példa](#output).

Az `StorageAccount` attribútum segítségével megadhatja a tárfiókot osztály, metódus vagy paraméter szinten. További információ: [Input - attributes](#input---attributes-and-annotations).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A [Java függvények futásidejű függvénytárában](/java/api/overview/azure/functions/runtime)használja a [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) jegyzetet a paramétereken az értékek táblatárolóba írásához.

Lásd a [példát a további részletekért](#output).

---

## <a name="output---configuration"></a>Kimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Table` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `table`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza a kötést az Azure Portalon.|
|**direction** | n/a | A beállításnak `out`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza a kötést az Azure Portalon. |
|**név** | n/a | A táblát vagy entitást jelölő függvénykódban használt változónév. A `$return` függvény visszatérési értékére való hivatkozás beállítása.| 
|**táblaneve** |**TableName** | A tábla neve.| 
|**partíciókulcs** |**PartitionKey** | Az írni hozandó táblaentitás partíciókulcsa. A tulajdonság használatával kapcsolatos útmutatást a [használati szakaszban](#output---usage) talál.| 
|**rowKey (sorkulcs)** |**RowKey** | Az írandó táblaentitás sorkulcsa. A tulajdonság használatával kapcsolatos útmutatást a [használati szakaszban](#output---usage) talál.| 
|**Kapcsolat** |**Kapcsolat** | A kötéshez használandó Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például "MyStorage" beállítást ad meg, `connection` a Functions futásidejű megkeresi a "MyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet - használat

# <a name="c"></a>[C #](#tab/csharp)

A kimeneti tábla entitás elérése `ICollector<T> paramName` `IAsyncCollector<T> paramName` metódusparaméter használatával, vagy ahol `T` a és `PartitionKey` `RowKey` a tulajdonságok at tartalmazza. Ezeket a tulajdonságokat `ITableEntity` gyakran `TableEntity`a megvalósítás vagy az öröklés kíséri.

Másik lehetőségként használhatja a `CloudTable` metódus paramétert írni a táblába az Azure Storage SDK használatával. Ha megpróbál kötődni, `CloudTable` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A kimeneti tábla entitás elérése `ICollector<T> paramName` `IAsyncCollector<T> paramName` metódusparaméter használatával, vagy ahol `T` a és `PartitionKey` `RowKey` a tulajdonságok at tartalmazza. Ezeket a tulajdonságokat `ITableEntity` gyakran `TableEntity`a megvalósítás vagy az öröklés kíséri. Az `paramName` érték a `name` *function.json*tulajdonságában van megadva.

Másik lehetőségként használhatja a `CloudTable` metódus paramétert írni a táblába az Azure Storage SDK használatával. Ha megpróbál kötődni, `CloudTable` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A kimeneti esemény `context.bindings.<name>` `<name>` elérése a `name` *function.json*tulajdonságában megadott érték használatával.

# <a name="python"></a>[Python](#tab/python)

Egy táblatároló sorüzenetének megjelenítése két lehetőség közül választhat egy függvényből:

- **Visszatérési érték** `name` : Állítsa a *function.json* tulajdonságát a értékre. `$return` Ezzel a konfigurációval a függvény visszatérési értéke table storage row marad meg.

- **Elengedhetetlen:** Adja át [az](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) értéket a megadott metódusa a paraméter deklarált [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típus. Az átadott `set` érték eseményközpont-üzenetként marad meg.

# <a name="java"></a>[Java](#tab/java)

A [TableStorageOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) megjegyzés használatával két lehetőség van egy táblatárolási sor függvényből való kihirdetésére:

- **Visszatérési érték**: Ha a notációt magára a függvényre alkalmazza, a függvény visszatérési értéke table tárolósorként marad meg.

- **Elengedhetetlen**: Az üzenetérték explicit beállításához alkalmazza a megjegyzéseket a [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)típus `T` egy `PartitionKey` `RowKey` adott paraméterére, ahol a és a tulajdonságok at is tartalmazza. Ezeket a tulajdonságokat `ITableEntity` gyakran `TableEntity`a megvalósítás vagy az öröklés kíséri.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés | Referencia |
|---|---|
| Tábla | [Tábla hibakódjai](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Tábla, Várólista | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tábla, Várólista | [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)
