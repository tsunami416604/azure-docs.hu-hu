---
title: "Az Azure tárolási táblában funkciók kötések |} Microsoft Docs"
description: "Azure Storage kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-storage-table-bindings"></a>Az Azure Functions tárolási tábla kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, konfigurálása és kód Azure Storage tábla kötések az Azure Functions. Azure Functions támogatja bemeneti és kimeneti Azure Storage-táblákat kötéseit.

A tárolási táblakötéssel a következő szituációkat ismerteti:

* **Egy C# vagy Node.js függvény egyetlen sor olvasása** - beállított `partitionKey` és `rowKey`. A `filter` és `take` tulajdonságok nem szerepel ebben a forgatókönyvben.
* **Olvassa el a C# függvényben több sort** – a Functions futtatókörnyezete biztosít egy `IQueryable<T>` objektum kötve a tábla. Típus `T` kell származnia `TableEntity` vagy megvalósítása `ITableEntity`. A `partitionKey`, `rowKey`, `filter`, és `take` tulajdonságok nem szerepel ebben a forgatókönyvben; használhatja a `IQueryable` elvégzéséhez szükséges szűrés objektum. 
* **Egy csomópont függvény több sor olvasása** – állítsa be a `filter` és `take` tulajdonságok. Nincs beállítva `partitionKey` vagy `rowKey`.
* **Egy vagy több sor írása C# függvények** -a Functions futtatókörnyezete biztosít egy `ICollector<T>` vagy `IAsyncCollector<T>` kötve a tábla, ahol `T` határozza meg a hozzáadni kívánt entitásokat sémája. Általában, írja be a `T` származik `TableEntity` vagy megvalósítja `ITableEntity`, de nem kell. A `partitionKey`, `rowKey`, `filter`, és `take` tulajdonságok nem szerepel ebben a forgatókönyvben.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Tárolási tábla bemeneti kötése
Az Azure Storage bemeneti táblakötéssel lehetővé teszi a tárolási tábla használatát a függvényben. 

A tárolási tábla bemenete egy olyan függvényt használja a következő JSON-objektumok a `bindings` function.json tömbje:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Vegye figyelembe a következőket: 

* Használjon `partitionKey` és `rowKey` egyetlen entitás elolvasására együtt. Ezek a tulajdonságok egyike sem kötelező. 
* `connection`egy alkalmazás-beállítás, amely tartalmazza a tárolási kapcsolati karakterlánc nevét kell tartalmaznia. Az Azure portálon, a szokásos szerkesztő a **integráció** lapon konfigurálja az Alkalmazásbeállítás hoz létre, a tárolási fiók vagy választja ki egy meglévőt. Emellett [konfigurálása az alkalmazás manuális beállításával](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Bemeneti kihasználtsága
A C# függvények, akkor eszközben csatlakozzon a bemeneti tábla entitás (vagy entitások) egy elnevezett paraméter a függvényaláíráshoz a például `<T> <name>`.
Ha `T` , az adatokat, írja be, hogy szeretné-e deszerializálni az adatokat, és `paramName` a megadott név a [kötés bemeneti](#input). A Node.js funkciókat érheti el a bemeneti tábla entitás (vagy entitások) használatával `context.bindings.<name>`.

A bemeneti adatok Node.js vagy C# funkciók is deszerializálható. A deszerializált objektum rendelkezik `RowKey` és `PartitionKey` tulajdonságok.

A C# funkciók is köthető a következő típusok, és a Functions futtatókörnyezete megkísérli deszerializálni a tábla adatait, hogy a típus használatával:

* Magában foglaló típussal`ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>A minta bemeneti
Kellene, hogy rendelkezik-e a következő function.json, amely várólista eseményindítót használ egy-egy sorának olvasása. Megadja a JSON `PartitionKey`  
 `RowKey`. `"rowKey": "{queueTrigger}"`azt jelzi, hogy a sorkulcs származik-e a várólista üzenet karakterlánc.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
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
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Tekintse meg a nyelvspecifikus minta a táblázat egyetlen entitás olvasó.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>A C# bemeneti minta #
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

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Az F # bemeneti minta #
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

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>A node.js bemeneti minta
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Tárolási tábla kimeneti kötése
Az Azure Storage táblázatos kimenete kötés lehetővé teszi, hogy entitások írását tárolási tábla a függvényben. 

A kimeneti a függvényt használja a következő JSON-objektumok tárolási tábla a `bindings` function.json tömbje:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Vegye figyelembe a következőket: 

* Használjon `partitionKey` és `rowKey` együtt egyetlen entitás írni. Ezek a tulajdonságok egyike sem kötelező. Azt is megadhatja, `PartitionKey` és `RowKey` létrehozásakor az entitásobjektumok gyűjteményeit a függvény kódban.
* `connection`egy alkalmazás-beállítás, amely tartalmazza a tárolási kapcsolati karakterlánc nevét kell tartalmaznia. Az Azure portálon, a szokásos szerkesztő a **integráció** lapon konfigurálja az Alkalmazásbeállítás hoz létre, a tárolási fiók vagy választja ki egy meglévőt. Emellett [konfigurálása az alkalmazás manuális beállításával](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Kimeneti használata
A C# függvények, akkor eszközben csatlakozzon a táblázatos kimenete az elnevezett `out` a függvényaláíráshoz a paraméter, például `out <T> <name>`, ahol `T` , az adatokat, írja be, hogy szeretné-e szerializálni az adatokat, és `paramName` a megadott név a [kimeneti kötése](#output). Node.js-függvény, akkor a táblának az elérésére használja `context.bindings.<name>`.

Node.js vagy C# funkciók objektumokat is szerializálni. A C# funkciók is kell kötni a következő esetében:

* Magában foglaló típussal`ITableEntity`
* `ICollector<T>`(a kimeneti több entitás. Lásd: [minta](#outcsharp).)
* `IAsyncCollector<T>`(aszinkron verzióját `ICollector<T>`)
* `CloudTable`(az Azure Storage szolgáltatás SDK használatával. Lásd: [minta](#readmulti).)

<a name="outputsample"></a>

## <a name="output-sample"></a>Minta kimenet
A következő *function.json* és *run.csx* példa bemutatja, hogyan több tábla entitás írni.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Tekintse meg a nyelvspecifikus mintát, amely több tábla entitás hoz létre.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>A C# kimeneti minta #
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Az F # kimeneti minta #
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

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Kimeneti minta node.js
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

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Minta: Olvassa el a C# több tábla entitás  #
A következő *function.json* és C# Kódpélda beolvassa az üzenetsorban lévő üzenetet megadott partíciókulcsot az entitásokat.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A C#-kódban hozzáad egy hivatkozást az Azure Storage szolgáltatás SDK, úgy, hogy az entitás típusa is származik `TableEntity`.

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

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

