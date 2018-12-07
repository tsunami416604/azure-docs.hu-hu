---
title: Az Azure storage táblakötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Table storage-kötések az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 3fc31306af1c85a67a1afca8a34be82a711f2527
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999534"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Az Azure storage táblakötések az Azure Functions szolgáltatáshoz

Ez a cikk bemutatja, hogyan használható az Azure Table storage-kötések az Azure Functions szolgáltatásban. Az Azure Functions támogatja a bemeneti és kimeneti kötések az Azure Table storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Table storage kötéseket szerepelnek a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Table storage kötéseket szerepelnek a [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input (Bemenet)

Az Azure Table storage bemeneti kötés használatával olvassa el az Azure Storage-fiók egyik táblájában.

## <a name="input---example"></a>Adjon meg – példa

Tekintse meg az adott nyelvű példa:

* [C# egy entitás olvasása](#input---c-example---one-entity)
* [Az IQueryable kötési C#](#input---c-example---iqueryable)
* [C# CloudTable kötéssel](#input---c-example---cloudtable)
* [C#-szkript egy entitás olvasása](#input---c-script-example---one-entity)
* [C# szkriptet kötéskérelmek IQueryable](#input---c-script-example---iqueryable)
* [C# szkriptet kötéskérelmek CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Adjon meg egy entitás – C#-példa –

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egy tábla egyetlen sor beolvasása. 

A sorok "{queueTrigger}" kulcs értéke azt jelzi, hogy a sorkulcs származik-e a várólista üzenet karakterlánc.

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

### <a name="input---c-example---iqueryable"></a>Bemenet - C#-példa – IQueryable

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely több tábla sorait olvassa. Vegye figyelembe, hogy a `MyPoco` származik- `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Bemenet - C#-példa – CloudTable

`IQueryable` nem támogatott a [funkciók v2 modul](functions-versions.md). A másik lehetőség használatához egy `CloudTable` metódus paraméteren a tábla olvasása az Azure Storage SDK használatával. Íme egy példa, amely egy Azure Functions naplótábláját 2.x függvény:

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

CloudTable használatával kapcsolatos további információkért lásd: [Azure Table storage használatának első lépései](../cosmos-db/table-storage-how-to-use-dotnet.md).

Ha megpróbál kötést létrehozni `CloudTable` és a egy hibaüzenet, győződjön meg arról, hogy rendelkezik-e egy hivatkozást, [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Bemenet - C#-példaszkript – egyetlen entitást

Az alábbi példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [C#-szkript](functions-reference-csharp.md) a kötést használó kód. A funkció egy üzenetsor eseményindító egyetlen tábla sorokat olvashat el. 

A *function.json* fájl adja meg azt a `partitionKey` és a egy `rowKey`. A `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs származik-e a várólista üzenet karakterlánc.

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

### <a name="input---c-script-example---iqueryable"></a>Bemenet - C#-példaszkript – IQueryable

Az alábbi példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [C#-szkript](functions-reference-csharp.md) a kötést használó kód. A függvény egy partíciókulcsot, egy üzenetsor-üzenetet a megadott tartozó entitásokat olvas be.

Íme a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A C#-szkriptkódot, hogy az entitás típusa származtathatók ad hozzá egy hivatkozást az Azure Storage SDK `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Bemenet - C#-példaszkript – CloudTable

`IQueryable` nem támogatott a [funkciók v2 modul](functions-versions.md). A másik lehetőség használatához egy `CloudTable` metódus paraméteren a tábla olvasása az Azure Storage SDK használatával. Íme egy példa, amely egy Azure Functions naplótábláját 2.x függvény:

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

CloudTable használatával kapcsolatos további információkért lásd: [Azure Table storage használatának első lépései](../cosmos-db/table-storage-how-to-use-dotnet.md).

Ha megpróbál kötést létrehozni `CloudTable` és a egy hibaüzenet, győződjön meg arról, hogy rendelkezik-e egy hivatkozást, [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Bemenet - F# példa

Az alábbi példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [ F# parancsfájl](functions-reference-fsharp.md) a kötést használó kód. A funkció egy üzenetsor eseményindító egyetlen tábla sorokat olvashat el. 

A *function.json* fájl adja meg azt a `partitionKey` és a egy `rowKey`. A `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs származik-e a várólista üzenet karakterlánc.

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a F# kódot:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Bemenet - JavaScript-példa

Az alábbi példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [JavaScript-kódot](functions-reference-node.md) , amely a kötés használja. A funkció egy üzenetsor eseményindító egyetlen tábla sorokat olvashat el. 

A *function.json* fájl adja meg azt a `partitionKey` és a egy `rowKey`. A `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs származik-e a várólista üzenet karakterlánc.

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Bemenet - Java-példában

Az alábbi példa bemutatja egy HTTP által aktivált függvényt, amely elemek teljes számát adja meg a Table storage-ban megadott partíción.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {"get"},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Bemenet - attribútumok
 
A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a következő attribútumokat a bemeneti tábla kötésének konfigurálása:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Az attribútum konstruktorának paramétereként meg a táblázat neve, a partíciókulcs és a sorkulcs. Használat egy out paraméterben, illetve a függvény visszatérési értéke a következő példában látható módon:

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

  Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

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

  Egy teljes példa: [bevitel – C#-példa](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  A használandó tárfiókot adjon meg egy másik megoldást kínál. A konstruktorban vesz igénybe egy tárolási kapcsolati karakterlánccal tartalmazó alkalmazásbeállítás neve. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

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

A használandó tárfiókot határozza meg a következő sorrendben:

* A `Table` attribútum `Connection` tulajdonság.
* A `StorageAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `Table` attribútum.
* A `StorageAccount` attribútum a függvény a alkalmazni.
* A `StorageAccount` attribútum az osztály a alkalmazni.
* Az alapértelmezett tárfiókot a függvényalkalmazás ("AzureWebJobsStorage" alkalmazás beállítás).

## <a name="input---java-annotations"></a>Bemenet - Java-jegyzetek

Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@TableInput` jegyzet paraméterekkel, amelynek az értéke lenne származnak, Table storage-ból.  A jegyzet használható natív Java-típusokat, POJOs vagy nullázható értékek használata nem kötelező<T>. 

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Table` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `table`. Ez a tulajdonság beállítása automatikusan történik, a kötés létrehozásakor az Azure Portalon.|
|**direction** | n/a | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, a kötés létrehozásakor az Azure Portalon. |
|**name** | n/a | A tábla vagy a függvénykódot az entitást képviselő változó neve. | 
|**Táblanév** | **Táblanév** | A tábla neve.| 
|**partitionKey** | **PartitionKey** |Választható. A partíciókulcs a tábla entitás olvasni. Tekintse meg a [használati](#input---usage) hogyan használja ezt a tulajdonságot a következő szakaszban.| 
|**Rowkey tulajdonságok esetén** |**Rowkey tulajdonságok esetén** | Választható. A sorkulcs beolvasni a tábla entitás. Tekintse meg a [használati](#input---usage) hogyan használja ezt a tulajdonságot a következő szakaszban.| 
|**hajtsa végre a megfelelő** |**hajtsa végre a megfelelő** | Választható. Olvassa el a JavaScript entitások maximális számát. Tekintse meg a [használati](#input---usage) hogyan használja ezt a tulajdonságot a következő szakaszban.| 
|**filter** |**Szűrő** | Választható. JavaScript a bemeneti tábla OData szűrési kifejezés. Tekintse meg a [használati](#input---usage) hogyan használja ezt a tulajdonságot a következő szakaszban.| 
|**kapcsolat** |**kapcsolat** | A tárolási kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A Table storage bemeneti kötésnek a következő eseteket támogatja:

* **C# vagy a C#-szkript egyetlen sor olvasása**

  Állítsa be `partitionKey` és `rowKey`. Táblák adatainak eléréséhez egy metódus-paramétert használatával `T <paramName>`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. `T` általában olyan típusú, amely megvalósítja az `ITableEntity` vagy származó `TableEntity`. A `filter` és `take` tulajdonságai nem szerepel ebben a forgatókönyvben. 

* **C# vagy a C#-szkript egy vagy több sor olvasása**

  Táblák adatainak eléréséhez egy metódus-paramétert használatával `IQueryable<T> <paramName>`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. `T` megvalósító típusúnak kell lennie `ITableEntity` vagy származó `TableEntity`. Használhat `IQueryable` módszereket a megadott szűréseket szükséges. A `partitionKey`, `rowKey`, `filter`, és `take` tulajdonságai nem szerepel ebben a forgatókönyvben.  

  > [!NOTE]
  > `IQueryable` nem támogatott a [funkciók v2 modul](functions-versions.md). Alternatív [CloudTable paramName metódus-paramétert használja](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) a tábla olvasása az Azure Storage SDK használatával. Ha megpróbál kötést létrehozni `CloudTable` és a egy hibaüzenet, győződjön meg arról, hogy rendelkezik-e egy hivatkozást, [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

* **A JavaScript egy vagy több sor olvasása**

  Állítsa be a `filter` és `take` tulajdonságait. Nem állít be `partitionKey` vagy `rowKey`. A bemeneti tábla entitás (vagy entitások) használatával eléréséhez `context.bindings.<name>`. A deszerializált objektum rendelkezik `RowKey` és `PartitionKey` tulajdonságait.

## <a name="output"></a>Kimenet

Használja az Azure Table-tároló kimeneti kötését entitások írni az Azure Storage-fiók egyik táblájában.

> [!NOTE]
> A kimeneti kötés nem támogatja a meglévő entitások frissítése. Használja a `TableOperation.Replace` művelet [Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) frissíteni egy létező entitásba.   

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) egy HTTP-triggert használó egyetlen tábla sor írása. 

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

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa bemutatja egy rendszertábla kimenetében a kötés egy *function.json* fájl és [C#-szkript](functions-reference-csharp.md) a kötést használó kód. A függvény több táblaentitások ír.

Íme a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

### <a name="output---f-example"></a>Kimenete – F# példa

Az alábbi példa bemutatja egy rendszertábla kimenetében a kötés egy *function.json* fájl és [ F# parancsfájl](functions-reference-fsharp.md) a kötést használó kód. A függvény több táblaentitások ír.

Íme a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a F# kódot:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa bemutatja egy rendszertábla kimenetében a kötés egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény több táblaentitások ír.

Íme a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

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

## <a name="output---attributes"></a>Kimenet – attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Az attribútum konstruktorának paramétereként meg a tábla neve. A használat egy `out` paraméter vagy a visszaadott érték a függvény az alábbi példában látható módon:

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

Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

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

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

Használhatja a `StorageAccount` attribútumot az osztályra, módszer vagy paraméter szintű tárfiókot adjon meg. További információkért lásd: [bemenet - attribútumok](#input---attributes).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Table` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `table`. Ez a tulajdonság beállítása automatikusan történik, a kötés létrehozásakor az Azure Portalon.|
|**direction** | n/a | Meg kell `out`. Ez a tulajdonság beállítása automatikusan történik, a kötés létrehozásakor az Azure Portalon. |
|**name** | n/a | A függvény kódját a táblával vagy entitással a használt változó neve. Állítsa be `$return` való hivatkozáshoz függvény visszatérési értéke.| 
|**Táblanév** |**Táblanév** | A tábla neve.| 
|**partitionKey** |**PartitionKey** | A partíciókulcs a tábla entitás írni. Tekintse meg a [szakaszban](#output---usage) hogyan használni ezt a tulajdonságot.| 
|**Rowkey tulajdonságok esetén** |**Rowkey tulajdonságok esetén** | A tábla entitás írni a sorkulcs. Tekintse meg a [szakaszban](#output---usage) hogyan használni ezt a tulajdonságot.| 
|**kapcsolat** |**kapcsolat** | A tárolási kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

A Table storage kimeneti kötés támogatja a következő esetekben:

* **Bármilyen nyelven egy sor írása**

  A C# és a C#-szkript, eléréséhez a kimeneti tábla entitás parametr metody használatával `out T paramName` vagy a függvény visszatérési értéke. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. `T` lehet bármilyen szerializálható típust, ha a partíciókulcs és a sorkulcs által biztosított a *function.json* fájl vagy a `Table` attribútum. Ellenkező esetben `T` tartalmazó típusnak kell lennie `PartitionKey` és `RowKey` tulajdonságait. Ebben a forgatókönyvben `T` általában valósít meg `ITableEntity` vagy származó `TableEntity`, de nem kell.

* **Egy vagy több sor írása C# vagy a C#**

  A C# és a C#-szkript, elérni a kimeneti tábla entitás parametr metody `ICollector<T> paramName` vagy `IAsyncCollector<T> paramName`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. `T` Adja meg a hozzáadni kívánt entitásokat sémája. Általában `T` származó `TableEntity` vagy valósít meg `ITableEntity`, de nem kell. A partíciókulcs és a sor a kulcsértékek *function.json* vagy a `Table` attribútum konstruktorának nem szerepel ebben a forgatókönyvben.

  A másik lehetőség használatához egy `CloudTable` metódus paraméteren a tábla írni az Azure Storage SDK használatával. Ha megpróbál kötést létrehozni `CloudTable` és a egy hibaüzenet, győződjön meg arról, hogy rendelkezik-e egy hivatkozást, [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x). Például egy kódot, amely összekapcsolja `CloudTable`, tekintse meg a bemeneti kötési példák [C#](#input---c-example---cloudtable) vagy [C#-szkript](#input---c-script-example---cloudtable) jelen cikk korábbi.

* **Egy vagy több sor írását a JavaScript**

  JavaScript-függvények, férnek hozzá a táblázatos kimenetet használatával `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Leírások |
|---|---|
| Tábla | [Tábla hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tábla, üzenetsor | [Storage-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor | [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
