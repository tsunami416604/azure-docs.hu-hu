---
title: "Az Azure Cosmos DB kötéseit funkciók"
description: "Az Azure Functions Azure Cosmos DB eseményindítók és kötések használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 5d90b2cd977522eab267c8c86a35e47bc61248a8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Az Azure Functions Azure Cosmos DB kötései

Ez a cikk azt ismerteti, hogyan használható [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) Azure Functions kötések. Az Azure Functions támogatja indítás, bemeneti és kimeneti Azure Cosmos DB kötései.

> [!NOTE]
> A kötés a DocumentDB eredetileg nevet kapta. Funkciók verziójában 1.x, csak az eseményindító lett átnevezett Cosmos DB; a bemeneti kötése kimeneti kötése és NuGet-csomag továbbra is a DocumentDB-nevét. A [funkciók verzió 2.x](functions-versions.md), a kötéseket és a csomag volt is átnevezett Cosmos DB. Ez a cikk 1.x használja.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Eseményindító

Az Azure Cosmos DB eseményindítót használ a [Azure Cosmos DB módosítás hírcsatorna](../cosmos-db/change-feed.md) partíciók között a módosításának figyelésére. A módosítás hírcsatorna beszúrások és frissítések, nem törlések tesz közzé. 

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egy adott adatbázis, gyűjtemény váltja ki.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja egy Cosmos DB eseményindító kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény naplóüzenetek ír, amikor a Cosmos DB rekordok módosítva lett.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

A C# parancsfájl kód itt látható:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja egy Cosmos DB eseményindító kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény naplóüzenetek ír, amikor a Cosmos DB rekordok módosítva lett.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

A JavaScript-kód itt látható:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot, amelyet a NuGet-csomag [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Az attribútum konstruktora időt vesz igénybe, az adatbázis és gyűjtemény nevét. Ezeket a beállításokat és egyéb tulajdonságok konfigurálható kapcsolatos információkért lásd: [eseményindító - konfigurációs](#trigger---configuration). Íme egy `CosmosDBTrigger` metódus-aláírás attribútum példát:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Tekintse meg a teljes például [eseményindító - C# példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `CosmosDBTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**Típusa** || meg kell `cosmosDBTrigger`. |
|**direction** || meg kell `in`. Ez a paraméter rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** || A dokumentumok a változások listájának jelölő függvény kódban használt változó neve. | 
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB fiók való kapcsolódáshoz használt kapcsolati karakterlánc tartalmazó alkalmazásbeállítás neve. |
|**databaseName**|**DatabaseName**  | A figyelt gyűjtemény Azure Cosmos DB adatbázis neve. |
|**CollectionName** |**CollectionName** | A figyelt gyűjtemény nevét. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Választható) A kapcsolati karakterláncot a szolgáltatás, amely tartalmazza a címbérlet gyűjteményt tartalmazó alkalmazásbeállítás neve. Ha nincs megadva, a `connectionStringSetting` értéket használja. Ez a paraméter értéke a kötés a portálon létrehozásakor automatikusan. A kapcsolati karakterláncot a bérletek gyűjtemény írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Választható) Az adatbázis, amely tárolja a bérletek tároló gyűjtemény nevét. Ha nincs megadva, az értékét a `databaseName` beállítást használja. Ez a paraméter értéke a kötés a portálon létrehozásakor automatikusan. |
|**leaseCollectionName** | **LeaseCollectionName** | (Választható) A bérletek tároló gyűjtemény nevét. Ha nincs megadva, az érték `leases` szolgál. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Választható) Ha beállítása `true`, a bérletek gyűjtemény automatikusan jön létre, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Választható) Rendel a bérletek gyűjtemény létrehozásakor kérjen egységek mennyisége határozza meg. Ez a beállítás csak akkor használhatók, ha a amikor nem `createLeaseCollectionIfNotExists` értéke `true`. Ez a paraméter értéke a kötés létrehozása a portál használatával automatikusan.
| |**LeaseOptions** | Tulajdonságainak beállításával egy példányát a címbérlet-beállítások konfigurálása a [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions) osztály.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az eseményindító szükséges egy második gyűjtemény, amelyet akkor használ _bérleteket_ a partíciók keresztül. A figyelt gyűjteményhez, mind a bérletek tartalmazó gyűjteményen működéséhez az eseményindító elérhetőnek kell lennie.

 >[!IMPORTANT]
 > Ha több funkciók ugyanahhoz a gyűjteményhez Cosmos DB eseményindító használatára van konfigurálva, funkciók mindegyike használjon egy dedikált bérleti gyűjtemény. Ellenkező esetben a függvény csak az egyik indul. 

Az eseményindító nem jelzi egy dokumentumot lett frissítve, vagy szúrja be, azt csak itt maga a dokumentum. Ha a frissítések és beszúrások másképp van szüksége, sikerült szeretne Timestamp típusú mezőket a Beszúrás vagy frissítés alkalmazásával.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti kötése kéri le egy vagy több Azure Cosmos DB dokumentumot, és továbbadja őket a függvény a bemeneti paraméter. A dokumentum azonosító vagy lekérdezési paramétereket az eseményindító, amely hívja meg a függvény alapján lehet meghatározni. 

>[!NOTE]
> Nem használja a megadott Azure Cosmos-adatbázis, vagy kimeneti kötések használata MongoDB API egy Cosmos DB fiókon. Adatsérülés lehetőség.

## <a name="input---example-1"></a>Bemenet – 1. példa

Tekintse meg a nyelvspecifikus példa, amely egyetlen dokumentum beolvasása:

* [C#](#input---c-example)
* [C# script (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Bemenet – C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) egy dokumentumot, amely lekéri egy adott adatbázis és gyűjtemény. 

Első, `Id` és `Maker` értékei egy `CarReview` példány annak a várólistára kerülnek átadásra. A kötés által használt Cosmos DB `Id` és `Maker` a várólista üzenetből a dokumentum beolvasása az adatbázisból.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Itt a `CarReview` POCO:

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>Bemenet – C# parancsfájl – példa

A következő példa bemutatja egy Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egyetlen dokumentum olvas, és frissíti a dokumentum szöveges értéket.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>Bemenet – F # – példa

A következő példa bemutatja egy Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egyetlen dokumentum olvas, és frissíti a dokumentum szöveges értéket.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Ez a példa szükséges egy `project.json` fájl, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet függőségek:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Hozzáadása egy `project.json` fájl című [F # felügyeleti csomag](functions-reference-fsharp.md#package).

### <a name="input---javascript-example"></a>Bemenet – JavaScript – példa

A következő példa bemutatja egy Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egyetlen dokumentum olvas, és frissíti a dokumentum szöveges értéket.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>Bemenet – 2. példa

Tekintse meg a nyelvspecifikus példa, amely több dokumentum beolvasása:

* [C#](#input---c-example-2)
* [C# script (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Bemenet – C# 2. példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely végrehajtja az SQL-lekérdezést. Használatához a `SqlQuery` paraméter, telepítenie kell a legújabb bétaverziója `Microsoft.Azure.WebJobs.Extensions.DocumentDB` NuGet-csomagot.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>Bemenet – C# parancsfájl 2. példa

A következő példa bemutatja egy Azure Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy SQL-lekérdezést, várólista eseményindító segítségével testre szabhatja a lekérdezési paraméter által megadott több dokumentumok kéri le.

A várólista eseményindító biztosít egy paraméter `departmentId`. A várólista üzenet `{ "departmentId" : "Finance" }` meghaladná a pénzügyi részleg összes rekordja. 

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

### <a name="input---javascript-example-2"></a>Bemenet – JavaScript 2. példa

A következő példa bemutatja egy Azure Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy SQL-lekérdezést, várólista eseményindító segítségével testre szabhatja a lekérdezési paraméter által megadott több dokumentumok kéri le.

A várólista eseményindító biztosít egy paraméter `departmentId`. A várólista üzenet `{ "departmentId" : "Finance" }` meghaladná a pénzügyi részleg összes rekordja. 

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

## <a name="input---attributes"></a>Bemenet – attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot, amelyet a NuGet-csomag [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Az attribútum konstruktora időt vesz igénybe, az adatbázis és gyűjtemény nevét. Ezeket a beállításokat és egyéb tulajdonságok konfigurálható kapcsolatos információkért lásd: [a következő konfigurációs szakasz](#input---configuration). 

## <a name="input---configuration"></a>Adjon meg - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `DocumentDB` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**Típusa**     || meg kell `documentdb`.        |
|**direction**     || meg kell `in`.         |
|**name**     || A kötési paraméter, a függvény a dokumentum jelölő neve.  |
|**databaseName** |**DatabaseName** |A a dokumentum tartalmazó adatbázis.        |
|**CollectionName** |**CollectionName** | A gyűjtemény, amely tartalmazza a dokumentum nevét. |
|**id**    | **Azonosító** | A dokumentum beolvasása azonosítója. Ez a tulajdonság támogatja [kötési kifejezésként](functions-triggers-bindings.md#binding-expressions-and-patterns). Ne állítsa be mind a **azonosító** és **sqlQuery** tulajdonságait. Ha nem állít egy, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos adatbázis SQL-lekérdezésben használt több dokumentumot. A tulajdonság támogatja a futásidejű kötések, például: `SELECT * FROM c where c.departmentId = {departmentId}`. Ne állítsa be mind a **azonosító** és **sqlQuery** tulajdonságait. Ha nem állít egy, a rendszer lekéri a teljes gyűjteményt.|
|**connection**     |**ConnectionStringSetting**|Az Alkalmazásbeállítás, amely tartalmazza az Azure Cosmos DB kapcsolati karakterlánc nevét.        |
|**partitionKey**|**PartitionKey**|Meghatározza a partíció a keresési kulcs értékét. Előfordulhat, hogy tartalmazzák a kötési paraméterek esetében.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A C# és F # függvényekben amikor sikeresen megtörtént, kilép, a függvény a bemeneti dokumentum elnevezett bemeneti paraméterek keresztül végzett módosítások automatikusan megmaradnak. 

A JavaScript-funkcióként frissítések nem automatikusan történik függvény kilépés után. Ehelyett használjon `context.bindings.<documentName>In` és `context.bindings.<documentName>Out` a frissítések. Tekintse meg a [JavaScript példa](#input---javascript-example).

## <a name="output"></a>Kimenet

A kötés segítségével Azure Cosmos DB kimeneti írni egy új dokumentumot Azure Cosmos DB-adatbázishoz. 

>[!NOTE]
> Nem használja a megadott Azure Cosmos-adatbázis, vagy kimeneti kötések használata MongoDB API egy Cosmos DB fiókon. Adatsérülés lehetőség.

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimeneti - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely ad hozzá egy adatbázist, a Queue storage az üzenetben megadott adatok.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Kimeneti - C# parancsfájl – példa

A következő példa bemutatja egy Azure Cosmos DB kimeneti kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja a várólistához, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A funkció a következő formátumban, az egyes rekordokhoz hoz létre Azure Cosmos DB dokumentumokat:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

Több dokumentumok létrehozásához köthető `ICollector<T>` vagy `IAsyncCollector<T>` ahol `T` a támogatott típusok egyike.

### <a name="output---f-example"></a>Kimeneti - F # – példa

A következő példa bemutatja egy Azure Cosmos DB kimeneti kötelező egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja a várólistához, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A funkció a következő formátumban, az egyes rekordokhoz hoz létre Azure Cosmos DB dokumentumokat:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Ez a példa szükséges egy `project.json` fájl, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet függőségek:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Hozzáadása egy `project.json` fájl című [F # felügyeleti csomag](functions-reference-fsharp.md#package).

### <a name="output---javascript-example"></a>Kimeneti - JavaScript – példa

A következő példa bemutatja egy Azure Cosmos DB kimeneti kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja a várólistához, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A funkció a következő formátumban, az egyes rekordokhoz hoz létre Azure Cosmos DB dokumentumokat:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({ 
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

## <a name="output---attributes"></a>Kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot, amelyet a NuGet-csomag [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Az attribútum konstruktora időt vesz igénybe, az adatbázis és gyűjtemény nevét. Ezeket a beállításokat és egyéb tulajdonságok konfigurálható kapcsolatos információkért lásd: [kimeneti - konfigurációs](#output---configuration). Íme egy `CosmosDB` metódus-aláírás attribútum példát:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `CosmosDB` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**Típusa**     || meg kell `documentdb`.        |
|**direction**     || meg kell `out`.         |
|**name**     || A kötési paraméter, a függvény a dokumentum jelölő neve.  |
|**databaseName** | **DatabaseName**|Az adatbázis, a gyűjteményt, ahol a dokumentum létre tartalmazó.     |
|**CollectionName** |**CollectionName**  | A gyűjtemény, ahol létrehozzák a dokumentum neve. |
|**createIfNotExists**  |**CreateIfNotExists**    | Egy logikai értéket, amely azt jelzi, hogy a gyűjtemény létrehozása, ha még nem létezik. Az alapértelmezett érték *hamis* új gyűjtemények létrejövő fenntartott átviteli, amelyek hatással vannak költsége van. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/documentdb/) olvasható.  |
|**partitionKey**|**PartitionKey** |Amikor `CreateIfNotExists` igaz értékű, meghatározza a partíció kulcs elérési útja a létrehozott gyűjteményhez.|
|**CollectionThroughput**|**CollectionThroughput**| Ha `CreateIfNotExists` igaz értékű, meghatározza a [átviteli](../cosmos-db/set-throughput.md) a létrehozott gyűjtemény.|
|**connection**    |**ConnectionStringSetting** |Az Alkalmazásbeállítás, amely tartalmazza az Azure Cosmos DB kapcsolati karakterlánc nevét.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimeneti - használat

Alapértelmezés szerint amikor a a függvénynek a kimeneti paraméter írni egy dokumentum jön létre az adatbázisban. A dokumentumban az automatikusan előállított GUID Azonosítóhoz van, a dokumentum azonosítóját. A kimeneti dokumentum dokumentumazonosító megadásával is megadhat a `id` a kimeneti paraméternek átadott a JSON-objektum tulajdonságait. 

> [!Note]  
> Amikor megad egy meglévő dokumentum Azonosítóját, a fiókbeállítása felülíródik az új kimeneti dokumentum szerint. 

## <a name="exceptions-and-return-codes"></a>Kivételeket és a visszatérési kódok

| Kötelező | Leírások |
|---|---|
| CosmosDB | [CosmosDB hibakódok](https://docs.microsoft.com/en-us/rest/api/documentdb/http-status-codes-for-documentdb) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyissa meg egy Cosmos DB eseményindító használó gyors üzembe helyezés](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [További információ a Cosmos DB számítási kiszolgáló nélküli adatbázis](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
