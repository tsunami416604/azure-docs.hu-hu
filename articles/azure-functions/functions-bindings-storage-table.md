---
title: Azure Functions Azure Table Storage-kötések
description: Ismerje meg, hogyan használhatók az Azure Table Storage-kötések Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: d647a643dafe50a5768261c2b09bf8c4154be469
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086245"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions Azure Table Storage-kötések

Ez a cikk azt ismerteti, hogyan használhatók az Azure Table Storage-kötések Azure Functionsban. Azure Functions támogatja az Azure Table Storage bemeneti és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Table Storage-kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomagban, 2. x verzióban vannak megadva. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Table Storage-kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomag 3. x verziójában érhetők el. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input (Bemenet)

Egy Azure Storage-fiókban lévő tábla beolvasásához használja az Azure Table Storage bemeneti kötését.

## <a name="input---example"></a>Bemenet – példa

Tekintse meg az adott nyelvű példa:

* [C#egy entitás olvasása](#input---c-example---one-entity)
* [C#kötés a IQueryable](#input---c-example---iqueryable)
* [C#kötés a CloudTable](#input---c-example---cloudtable)
* [C#parancsfájl olvasása egy entitásból](#input---c-script-example---one-entity)
* [C#parancsfájl kötése a IQueryable](#input---c-script-example---iqueryable)
* [C#parancsfájl kötése a CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Input – C# példa – egy entitás

Az alábbi példa egy [ C# ](functions-dotnet-class-library.md) olyan függvényt mutat be, amely egy egyoszlopos sort olvas. 

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

### <a name="input---c-example---iqueryable"></a>Bemeneti – C# példa – IQueryable

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely több táblázatos sort olvas be. Vegye figyelembe, `MyPoco` hogy az osztályból `TableEntity`származik.

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

### <a name="input---c-example---cloudtable"></a>Bemeneti – C# példa – CloudTable

`IQueryable`nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik megoldás, ha `CloudTable` metódus-paramétert használ a tábla olvasásához az Azure Storage SDK használatával. Íme egy példa egy 2. x függvényre, amely lekérdez egy Azure Functions naplózási táblázatot:

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

További információ a CloudTable használatáról: Ismerkedés [Az Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md)szolgáltatással.

Ha egy hibaüzenetet próbál meg `CloudTable` kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

### <a name="input---c-script-example---one-entity"></a>Input- C# script példa – egy entitás

Az alábbi példa egy Table input-kötést mutat be egy *function. JSON* fájlban és [ C# ](functions-reference-csharp.md) a kötést használó parancsfájl-kódban. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A *function. JSON* fájl a `partitionKey` és a `rowKey`paramétert adja meg. A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

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

### <a name="input---c-script-example---iqueryable"></a>Input- C# script példa – IQueryable

Az alábbi példa egy Table input-kötést mutat be egy *function. JSON* fájlban és [ C# ](functions-reference-csharp.md) a kötést használó parancsfájl-kódban. A függvény beolvassa az entitásokat egy üzenetsor-üzenetben megadott partíciós kulcshoz.

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

A C# szkript kódja az Azure Storage SDK-ra mutató hivatkozást tartalmaz, hogy az entitás típusa a következőből származtatható `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Input- C# script példa – CloudTable

`IQueryable`nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik megoldás, ha `CloudTable` metódus-paramétert használ a tábla olvasásához az Azure Storage SDK használatával. Íme egy példa egy 2. x függvényre, amely lekérdez egy Azure Functions naplózási táblázatot:

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

További információ a CloudTable használatáról: Ismerkedés [Az Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md)szolgáltatással.

Ha egy hibaüzenetet próbál meg `CloudTable` kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

### <a name="input---f-example"></a>Bemenet – F# példa

Az alábbi példa egy Table input-kötést mutat be egy *function. JSON* fájlban és [ F# ](functions-reference-fsharp.md) a kötést használó parancsfájl-kódban. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A *function. JSON* fájl a `partitionKey` és a `rowKey`paramétert adja meg. A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

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

### <a name="input---javascript-example"></a>Bemenet – JavaScript-példa

Az alábbi példa egy Table input-kötést mutat be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötést használja. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A *function. JSON* fájl a `partitionKey` és a `rowKey`paramétert adja meg. A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

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

### <a name="input---java-example"></a>Bemenet – Java-példa

Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely egy adott partíció elemeinek teljes számát adja vissza a Table Storage-ban.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Bemenet - attribútumok
 
Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy tábla bemeneti kötésének konfigurálásához:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Az attribútum konstruktora a tábla nevét, a partíció kulcsát és a sor kulcsát veszi figyelembe. Az alábbi példában látható módon vagy a függvény visszatérési értékén is használható.

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

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

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

  A teljes példa: input- C# example.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

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

* A `Table` attribútum `Connection` tulajdonság.
* A `StorageAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `Table` attribútum.
* A `StorageAccount` attribútum a függvény a alkalmazni.
* A `StorageAccount` attribútum az osztály a alkalmazni.
* A Function alkalmazás alapértelmezett Storage-fiókja ("AzureWebJobsStorage").

## <a name="input---java-annotations"></a>Bevitel – Java-jegyzetek

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@TableInput` a jegyzeteket azon paramétereknél, amelyek értéke a Table Storage-ból származik.  Ezt a jegyzetet natív Java-típusokkal, szerializálói vagy NULL értékű értékekkel lehet használni opcionális\<T > használatával. 

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Table` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `table`. A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban.|
|**direction** | n/a | Meg kell `in`. A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban. |
|**name** | n/a | Annak a változónak a neve, amely a függvény kódjában szereplő táblát vagy entitást jelképezi. | 
|**Táblanév** | **Táblanév** | A tábla neve.| 
|**partitionKey** | **PartitionKey** |Nem kötelező. Az olvasni kívánt tábla entitás partíciós kulcsa. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**rowKey** |**RowKey** | Nem kötelező. Az olvasni kívánt tábla entitáshoz tartozó sor kulcsa. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**take** |**Eltarthat** | Nem kötelező. A JavaScriptben olvasható entitások maximális száma. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**filter** |**Szűrő** | Nem kötelező. OData-szűrési kifejezés a JavaScriptben a tábla beviteléhez. Tekintse meg a [használat](#input---usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**kapcsolat** |**kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A Table Storage bemeneti kötése a következő forgatókönyveket támogatja:

* **Egy sor C# beolvasása C# a vagy parancsfájlban**

  Beállítás `partitionKey` és `rowKey`. A tábla adataihoz a metódus paraméterének `T <paramName>`használatával férhet hozzá. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. `T`általában olyan típus, amely a- `ITableEntity` ból `TableEntity`implementál vagy származtat. A `filter` és`take` a tulajdonságok nem használatosak ebben a forgatókönyvben. 

* **Egy vagy több sor olvasása C# a C# vagy parancsfájlban**

  A tábla adataihoz a metódus paraméterének `IQueryable<T> <paramName>`használatával férhet hozzá. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. `T`olyan típusnak kell lennie, `ITableEntity` amely a-ből `TableEntity`implementál vagy származtat. A módszerek használatával `IQueryable` bármilyen szűrést végezhet. A `partitionKey` ,`rowKey`, és`take` tulajdonságokat nem használja ebben a forgatókönyvben. `filter`  

  > [!NOTE]
  > `IQueryable`nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik lehetőség egy [CloudTable paramName metódus paraméter használata](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) a tábla olvasásához az Azure Storage SDK használatával. Ha egy hibaüzenetet próbál meg `CloudTable` kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

* **Egy vagy több sor olvasása a JavaScriptben**

  Adja meg `filter` a `take` és a tulajdonságokat. Nincs beállítva `partitionKey` vagy `rowKey`. A bemeneti tábla entitás (vagy entitások) elérése `context.bindings.<name>`a használatával. A deszerializált objektumok rendelkeznek `RowKey` és `PartitionKey` tulajdonságokkal rendelkeznek.

## <a name="output"></a>Output

Egy Azure Table Storage-beli kimeneti kötés használatával entitásokat írhat egy Azure Storage-fiókba tartozó táblába.

> [!NOTE]
> Ez a kimeneti kötés nem támogatja a meglévő entitások frissítését. Egy meglévő `TableOperation.Replace` entitás frissítéséhez használja az [Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) műveletét.   

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

Az alábbi példa egy [ C# ](functions-dotnet-class-library.md) olyan függvényt mutat be, amely egy http-triggert használ egy egyoszlopos sor írásához. 

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

Az alábbi példa egy tábla kimeneti kötést mutat be egy *function. JSON* fájlban [ C# ](functions-reference-csharp.md) és a kötést használó parancsfájl kódjában. A függvény több tábla entitást ír.

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

Az alábbi példa egy tábla kimeneti kötést mutat be egy *function. JSON* fájlban [ F# ](functions-reference-fsharp.md) és a kötést használó parancsfájl kódjában. A függvény több tábla entitást ír.

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

Az alábbi példa egy Table output-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötést használja. A függvény több tábla entitást ír.

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

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Az attribútum konstruktora a tábla nevét adja meg. Az alábbi példában látható módon vagy `out` a függvény visszatérési értékén is használható.

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

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

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

`StorageAccount` Az attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: [input-attributes](#input---attributes).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Table` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `table`. A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban.|
|**direction** | n/a | Meg kell `out`. A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban. |
|**name** | n/a | A táblát vagy entitást jelölő függvény kódjában használt változó neve. `$return` Állítsa a értékre a függvény visszatérési értékének hivatkozásához.| 
|**Táblanév** |**Táblanév** | A tábla neve.| 
|**partitionKey** |**PartitionKey** | Az írni kívánt tábla entitás partíciós kulcsa. Tekintse meg a [használat című szakaszt](#output---usage) , amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**rowKey** |**RowKey** | Az írni kívánt tábla entitáshoz tartozó sor kulcsa. Tekintse meg a [használat című szakaszt](#output---usage) , amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**kapcsolat** |**kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

A tábla tárolási kimenetének kötése a következő forgatókönyveket támogatja:

* **Egy sor írása bármilyen nyelven**

  A C# ( C# z) és a (z) parancsfájlban a kimeneti tábla entitáshoz `out T paramName` a metódus paraméterének (például vagy a függvény visszatérési értéke) használatával férhet hozzá. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. `T`bármely szerializálható típus lehet, ha a Partition kulcs és a sor kulcsa a *function. JSON* fájl vagy az `Table` attribútum adja meg. Ellenkező esetben olyan típusnak kell lennie `PartitionKey` , `RowKey` amely tartalmazza a és a tulajdonságokat. `T` Ebben a forgatókönyvben `T` általában `ITableEntity` a vagy a `TableEntity`származtatja, de nem szükséges.

* **Egy vagy több sor írása C# vagy C# parancsfájl futtatása**

  A C# ( C# z) és a (z) parancsfájlban a kimeneti tábla `ICollector<T> paramName` entitást a Method paraméter vagy `IAsyncCollector<T> paramName`a használatával érheti el. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. `T`meghatározza a hozzáadni kívánt entitások sémáját. Jellemzően a `T` `TableEntity` származtatja vagy implementálja `ITableEntity`, de nem szükséges. A Partition kulcs és a sor kulcsa a *function. JSON* fájlban vagy `Table` az attribútum konstruktorában nem használatos ebben a forgatókönyvben.

  Alternatív megoldásként a `CloudTable` metódus paraméterének használatával írhat a táblázatba az Azure Storage SDK használatával. Ha egy hibaüzenetet próbál meg `CloudTable` kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással. A következőhöz kötődő `CloudTable`kód például a jelen cikk korábbi, a bemeneti kötési példák [C#](#input---c-example---cloudtable) vagy [ C# ](#input---c-script-example---cloudtable) a szkriptek című szakaszában található.

* **Egy vagy több sor írása a JavaScriptben**

  A JavaScript-függvényekben a táblázat kimenetét `context.bindings.<name>`a paranccsal érheti el.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Hivatkozás |
|---|---|
| Tábla | [Tábla Hibakódai](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Table, Queue | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue | [Hibaelhárítás](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
