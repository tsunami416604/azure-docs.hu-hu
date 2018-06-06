---
title: Azure Table storage kötései Azure Functions
description: Az Azure Functions az Azure Table storage kötések használatának megismerése.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 51b9f7bfd25da7dfd4ae9038f8dab70e9232b944
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724581"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table storage kötései Azure Functions

Ez a cikk ismerteti az Azure Functions kötések Azure Table storage használata. Az Azure Functions támogatja bemeneti és kimeneti Azure Table storage kötései.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x működik

A Table storage kötések szerepelnek a [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomag verziója 2.x. A csomag forráskódja van a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-tárházban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Csomagok - 2.x működik

A Table storage kötések szerepelnek a [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomag verziója 3.x. A csomag forráskódja van a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-tárházban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="input"></a>Input (Bemenet)

Az Azure Table storage bemeneti kötése segítségével olvasni egy táblát az Azure Storage-fiók.

## <a name="input---example"></a>Bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C# egy entitás olvasása](#input---c-example-1)
* [C# több entitás olvasása](#input---c-example-2)
* [C# parancsfájl - olvasni egy entitás](#input---c-script-example-1)
* [C# parancsfájl - több entitás olvasása](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Bemenet – C# 1. példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amelyek egy-egy sorának olvassa be. 

A sor kulcs értéke "{queueTrigger}" azt jelzi, hogy a sorkulcs származik-e a várólista üzenet-karakterlánc.

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
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example-2"></a>Bemenet – C# 2. példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely több tábla sorainak olvassa be. Vegye figyelembe, hogy a `MyPoco` származik- `TableEntity`.

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
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

  > [!NOTE]
  > `IQueryable` nem állítható be a [funkciók v2 futásidejű](functions-versions.md). Alternatív [CloudTable paramName metódusparaméter használja](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) olvasni a táblát az Azure Storage szolgáltatás SDK használatával. Ha mégis megpróbálja köthető `CloudTable` és egy hibaüzenet jelenik meg, győződjön meg arról, hogy rendelkezik-e mutató hivatkozás [a megfelelő tárolási SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example-1"></a>Bemenet – C# parancsfájl 1. példa

A következő példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [C# parancsfájl](functions-reference-csharp.md) kódot, amely a kötés használja. A funkció egy várólista eseményindító egy-egy sorának olvasása. 

A *function.json* fájl határozza meg a `partitionKey` és egy `rowKey`. A `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs származik-e a várólista üzenet-karakterlánc.

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

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example-2"></a>Bemenet – C# parancsfájl 2. példa

A következő példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [C# parancsfájl](functions-reference-csharp.md) kódot, amely a kötés használja. A függvény egy üzenetsor megadott partíciós kulcsok entitások olvassa be.

Itt a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájlkód hozzáad egy hivatkozást az Azure Storage szolgáltatás SDK, úgy, hogy az entitás típusa is származik `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---f-example"></a>Bemenet – F # – példa

A következő példa bemutatja a bemeneti tábla kötés egy *function.json* fájl és [F # parancsfájl](functions-reference-fsharp.md) kódot, amely a kötés használja. A funkció egy várólista eseményindító egy-egy sorának olvasása. 

A *function.json* fájl határozza meg a `partitionKey` és egy `rowKey`. A `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs származik-e a várólista üzenet-karakterlánc.

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

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Bemenet – JavaScript – példa

A következő példa bemutatja a bemeneti tábla kötés egy *function.json* fájl- és [JavaScript-kód] (funkciók-referencia-node.md), amely a kötés használja. A funkció egy várólista eseményindító egy-egy sorának olvasása. 

A *function.json* fájl határozza meg a `partitionKey` és egy `rowKey`. A `rowKey` "{queueTrigger}" érték azt jelzi, hogy a sorkulcs származik-e a várólista üzenet-karakterlánc.

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

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>Bemenet – attribútumok
 
A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), bemeneti táblakötéssel konfigurálása a következő attribútumokat használhatja:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Az attribútum konstruktora a tábla nevét, a partíciós kulcs és a sorkulcs vesz igénybe. Használat egy kimeneti paramétert vagy a függvény visszatérési értéke a következő példában látható módon:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Beállíthatja a `Connection` tulajdonság adja meg a tárfiókot, a következő példában látható módon:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Tekintse meg a teljes például [bemenet – C# példa](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Adja meg a tárfiókot egy másik lehetőséget nyújt. A konstruktornak, amely tartalmazza a tárolási kapcsolati karakterlánc alkalmazásbeállítás neve vesz igénybe. Az attribútum a paraméter, módszer vagy osztály szintjén is alkalmazható. A következő példa bemutatja az osztály és módszer:

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

A használt tárfiók határozza meg a következő sorrendben:

* A `Table` attribútum `Connection` tulajdonság.
* A `StorageAccount` attribútuma ugyanezt a paramétert, mint a `Table` attribútum.
* A `StorageAccount` függvény attribútuma.
* A `StorageAccount` osztály attribútuma.
* Az alapértelmezett tárfiók a függvény ("AzureWebJobsStorage" alkalmazásbeállítás) alkalmazás.

## <a name="input---configuration"></a>Adjon meg - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Table` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | meg kell `table`. Ez a tulajdonság értéke automatikusan kötésének létrehozásakor az Azure portálon.|
|**direction** | n/a | meg kell `in`. Ez a tulajdonság értéke automatikusan kötésének létrehozásakor az Azure portálon. |
|**name** | n/a | A tábla vagy a funkciókódot entitás jelölő neve. | 
|**Táblanév** | **Táblanév** | A tábla neve.| 
|**partitionKey** | **PartitionKey** |Választható. A partíciókulcs a tábla entitás olvasni. Tekintse meg a [használati](#input---usage) a szakaszban a tulajdonság használatával.| 
|**RowKey** |**RowKey** | Választható. Olvassa el a tábla entitás sorkulcsa. Tekintse meg a [használati](#input---usage) a szakaszban a tulajdonság használatával.| 
|**hajtsa végre a megfelelő** |**hajtsa végre a megfelelő** | Választható. A JavaScript olvasni entitások maximális száma. Tekintse meg a [használati](#input---usage) a szakaszban a tulajdonság használatával.| 
|**filter** |**Szűrő** | Választható. Egy OData szűrőkifejezés JavaScript a bemeneti tábla. Tekintse meg a [használati](#input---usage) a szakaszban a tulajdonság használatával.| 
|**Kapcsolat** |**Kapcsolat** | A tárolási kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a nevét itt csak a maradékot. Ha például `connection` "MyStorage", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyStorage." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc az nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A Table storage bemeneti kötése a következő szituációkat ismerteti:

* **C# vagy C# a parancsfájlt egy sor olvasása**

  Állítsa be `partitionKey` és `rowKey`. A tábla adatai hozzáférhet a metódusparaméter `T <paramName>`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. `T` általában olyan típusú, amely megvalósítja az `ITableEntity` vagy abból származó `TableEntity`. A `filter` és `take` tulajdonságok nem szerepel ebben a forgatókönyvben. 

* **C# vagy C# parancsfájl egy vagy több sor olvasása**

  A tábla adatai hozzáférhet a metódusparaméter `IQueryable<T> <paramName>`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. `T` lehet, amely típus `ITableEntity` vagy abból származó `TableEntity`. Használhat `IQueryable` módszereket a szűrés szükséges. A `partitionKey`, `rowKey`, `filter`, és `take` tulajdonságok nem szerepel ebben a forgatókönyvben.  

  > [!NOTE]
  > `IQueryable` nem állítható be a [funkciók v2 futásidejű](functions-versions.md). Alternatív [CloudTable paramName metódusparaméter használja](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) olvasni a táblát az Azure Storage szolgáltatás SDK használatával. Ha mégis megpróbálja köthető `CloudTable` és egy hibaüzenet jelenik meg, győződjön meg arról, hogy rendelkezik-e mutató hivatkozás [a megfelelő tárolási SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

* **A JavaScript egy vagy több sor olvasása**

  Állítsa be a `filter` és `take` tulajdonságok. Nincs beállítva `partitionKey` vagy `rowKey`. A bemeneti tábla entitás (vagy entitások) használatával `context.bindings.<name>`. A deszerializált objektum rendelkezik `RowKey` és `PartitionKey` tulajdonságok.

## <a name="output"></a>Kimenet

Egy Azure Table storage kimeneti entitások írni egy Azure Storage-fiókban lévő táblázat kötés használja.

> [!NOTE]
> A kimeneti kötés nem támogatja a meglévő entitások. Használja a `TableOperation.Replace` művelet [az Azure Storage SDK-ból](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) meglévő entitás frissítése.   

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimeneti - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) írni egy-egy sorának egy HTTP-eseményindítóval, amely használja. 

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
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Kimeneti - C# parancsfájl – példa

A következő példa bemutatja egy kötelező táblázatos kimenete egy *function.json* fájl és [C# parancsfájl](functions-reference-csharp.md) kódot, amely a kötés használja. A függvény több tábla entitás ír.

Itt a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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

### <a name="output---f-example"></a>Kimeneti - F # – példa

A következő példa bemutatja egy kötelező táblázatos kimenete egy *function.json* fájl és [F # parancsfájl](functions-reference-fsharp.md) kódot, amely a kötés használja. A függvény több tábla entitás ír.

Itt a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Kimeneti - JavaScript – példa

A következő példa bemutatja egy kötelező táblázatos kimenete egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény több tábla entitás ír.

Itt a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

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

## <a name="output---attributes"></a>Kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs).

Az attribútum konstruktora a táblanév vesz igénybe. A használat egy `out` paraméter vagy a visszatérési érték a, a következő példában látható módon:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Beállíthatja a `Connection` tulajdonság adja meg a tárfiókot, a következő példában látható módon:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

Használhatja a `StorageAccount` attribútum segítségével adhatja meg a tárfiók osztály, módszer vagy paraméter szinten. További információkért lásd: [bemenet - attribútumok](#input---attributes).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Table` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | meg kell `table`. Ez a tulajdonság értéke automatikusan kötésének létrehozásakor az Azure portálon.|
|**direction** | n/a | meg kell `out`. Ez a tulajdonság értéke automatikusan kötésének létrehozásakor az Azure portálon. |
|**name** | n/a | A változó nevét, amely a tábla vagy entitás függvény kódban használt. Beállítása `$return` hivatkozni, a függvény visszatérési értéke.| 
|**Táblanév** |**Táblanév** | A tábla neve.| 
|**partitionKey** |**PartitionKey** | A partíciókulcs a tábla entitás írni. Tekintse meg a [használati adatai](#output---usage) kapcsolatos útmutatás a tulajdonságot használni.| 
|**RowKey** |**RowKey** | A tábla entitás írni a sorkulcs. Tekintse meg a [használati adatai](#output---usage) kapcsolatos útmutatás a tulajdonságot használni.| 
|**Kapcsolat** |**Kapcsolat** | A tárolási kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a nevét itt csak a maradékot. Ha például `connection` "MyStorage", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyStorage." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc az nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimeneti - használat

A Table storage kimeneti kötése támogatja a következő esetekben:

* **Egy sor írása bármilyen nyelven**

  C# és C# a parancsfájlt, nyissa meg a kimeneti táblaentitássá metódusparaméter használatával `out T paramName` vagy a függvény visszatérési értéke. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. `T` bármilyen szerializálható típus lehet, ha a partíciós kulcs és a sorkulcs által biztosított a *function.json* fájl vagy a `Table` attribútum. Ellenkező esetben `T` tartalmazó típusnak kell lennie `PartitionKey` és `RowKey` tulajdonságok. Ebben a forgatókönyvben `T` általában megvalósítja `ITableEntity` vagy abból származó `TableEntity`, de nem kell.

* **Egy vagy több sor írása C# vagy C#**

  A C# és C# a parancsfájlt, hozzáférhet a kimeneti táblaentitássá a metódusparaméter `ICollector<T> paramName` vagy `IAsyncCollector<T> paramName`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. `T` Adja meg a hozzáadni kívánt entitásokat sémája. Általában `T` származik `TableEntity` vagy megvalósítja `ITableEntity`, de nem kell. A partíciós kulcs és a sor kulcsértékek a *function.json* vagy a `Table` attribútum konstruktora nem szerepel ebben a forgatókönyvben.

  Helyett használja a `CloudTable paramName` metódus paraméterének írni, hogy a tábla az Azure Storage szolgáltatás SDK használatával. Ha mégis megpróbálja köthető `CloudTable` és egy hibaüzenet jelenik meg, győződjön meg arról, hogy rendelkezik-e mutató hivatkozás [a megfelelő tárolási SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

* **Egy vagy több sor írását a JavaScript**

  JavaScript-funkcióként, nyissa meg a tábla használatával kimeneti `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Kivételeket és a visszatérési kódok

| Kötelező | Leírások |
|---|---|
| Tábla | [Tábla hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| A BLOB, Table, várólista | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| A BLOB, Table, várólista | [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
