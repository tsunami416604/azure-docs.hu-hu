---
title: "Az Azure Cosmos DB kötéseket a következő funkciók |} Microsoft Docs"
description: "Az Azure Functions Azure Cosmos DB eseményindítók és kötések használatának megismerése."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: d05c0342e771e229a7175570ad227c4359980990
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Az Azure Cosmos DB kötéseit funkciók
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, konfigurálása és az Azure Functions Azure Cosmos DB kötések kódot. Funkciók által támogatott indítás, bemeneti és kimeneti Azure Cosmos DB kötései.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

A kiszolgáló nélküli számítástechnikai Azure Cosmos DB további információkért lásd: [Azure Cosmos DB: kiszolgáló nélküli adatbázis számítási Azure Functions használatával](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Az Azure Cosmos DB eseményindító

Az Azure Cosmos DB eseményindítót használ a [Azure Cosmos DB módosítás hírcsatorna](../cosmos-db/change-feed.md) partíciók között a módosításának figyelésére. Az eseményindító szükséges egy második gyűjtemény, amelyet akkor használ _bérleteket_ a partíciók keresztül.

A figyelt gyűjteményhez, mind a bérletek tartalmazó gyűjteményen működéséhez az eseményindító elérhetőnek kell lennie.

Az Azure Cosmos DB eseményindító támogatja a következő tulajdonságokkal:

|Tulajdonság  |Leírás  |
|---------|---------|
|**típusa** | meg kell `cosmosDBTrigger`. |
|**név** | A dokumentumok a változások listájának jelölő függvény kódban használt változó neve. | 
|**iránya** | meg kell `in`. Ez a paraméter rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**connectionStringSetting** | A figyelt Azure Cosmos DB fiók való kapcsolódáshoz használt kapcsolati karakterlánc tartalmazó alkalmazásbeállítás neve. |
|**databaseName** | A figyelt gyűjtemény Azure Cosmos DB adatbázis neve. |
|**collectionName** | A figyelt gyűjtemény nevét. |
| **leaseConnectionStringSetting** | (Választható) A kapcsolati karakterláncot a szolgáltatás, amely tartalmazza a címbérlet gyűjteményt tartalmazó alkalmazásbeállítás neve. Ha nincs megadva, a `connectionStringSetting` értéket használja. Ez a paraméter értéke a kötés a portálon létrehozásakor automatikusan. |
| **leaseDatabaseName** | (Választható) Az adatbázis, amely tárolja a bérletek tároló gyűjtemény nevét. Ha nincs megadva, az értékét a `databaseName` beállítást használja. Ez a paraméter értéke a kötés a portálon létrehozásakor automatikusan. |
| **leaseCollectionName** | (Választható) A bérletek tároló gyűjtemény nevét. Ha nincs megadva, az érték `leases` szolgál. |
| **createLeaseCollectionIfNotExists** | (Választható) Ha beállítása `true`, a bérletek gyűjtemény automatikusan jön létre, ha még nem létezik. Az alapértelmezett érték `false`. |
| **leaseCollectionThroughput** | (Választható) Rendel a bérletek gyűjtemény létrehozásakor kérjen egységek mennyisége határozza meg. Ez a beállítás csak akkor használhatók, ha a amikor nem `createLeaseCollectionIfNotExists` értéke `true`. Ez a paraméter értéke a kötés létrehozása a portál használatával automatikusan.

>[!NOTE] 
>A bérletek gyűjteményhez való kapcsolódáshoz használt kapcsolati karakterlánc írási engedéllyel kell rendelkeznie.

Ezeket a tulajdonságokat a függvény az Azure portálon vagy szerkesztésével integráció lapján állítható be a `function.json` projektfájlt.

## <a name="using-an-azure-cosmos-db-trigger"></a>Egy Azure Cosmos DB eseményindító használatával

Ebben a szakaszban található példák bemutatják, hogyan használható az Azure Cosmos DB eseményindító. A példák azt feltételezik, hogy egy eseményindító metaadatokat, amelyek a következőképpen néznek:

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
 
Egy Azure Cosmos DB eseményindító létrehozása a portálon, a függvény alkalmazásból példáért lásd: [hozzon létre egy Azure Cosmos DB által indított függvényt](functions-create-cosmos-db-triggered-function.md). 

### <a name="trigger-sample-in-c"></a>A C# eseményindító minta #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>A JavaScript eseményindító minta
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>A DocumentDB API bemeneti kötése
A DocumentDB API bemeneti kötése kéri le az Azure Cosmos DB dokumentum, és átadja a függvény az elnevezett bemeneti paraméter. A dokumentum azonosító is meg lehet határozni alapján az eseményindító, amely meghívja a függvényt. 

A DocumentDB API bemeneti kötése a következő tulajdonságokkal rendelkeznek *function.json*:

|Tulajdonság  |Leírás  |
|---------|---------|
|**név**     | A kötési paraméter, a függvény a dokumentum jelölő neve.  |
|**típusa**     | meg kell `documentdb`.        |
|**databaseName** | A a dokumentum tartalmazó adatbázis.        |
|**collectionName**  | A gyűjtemény, amely tartalmazza a dokumentum nevét. |
|**azonosítója**     | A dokumentum beolvasása azonosítója. Ez a tulajdonság támogatja a kötések paraméterek. További tudnivalókért lásd: [egy kötési kifejezése egyéni bemeneti tulajdonságok kötése](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | Egy Azure Cosmos adatbázis SQL-lekérdezésben használt több dokumentumot. A lekérdezés támogatja futásidejű kötések, például a példában: `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**kapcsolat**     |Az Alkalmazásbeállítás, amely tartalmazza az Azure Cosmos DB kapcsolati karakterlánc nevét.        |
|**iránya**     | meg kell `in`.         |

Nem állítható be mind a **azonosító** és **sqlQuery** tulajdonságait. Ha nem, a rendszer lekéri a teljes gyűjteményt.

## <a name="using-a-documentdb-api-input-binding"></a>A DocumentDB API bemeneti kötés használata

* A C# és F # függvényekben amikor sikeresen megtörtént, kilép, a függvény a bemeneti dokumentum elnevezett bemeneti paraméterek keresztül végzett módosítások automatikusan megmaradnak. 
* A JavaScript-funkcióként frissítések nem automatikusan történik függvény kilépés után. Ehelyett használjon `context.bindings.<documentName>In` és `context.bindings.<documentName>Out` a frissítések. Tekintse meg a [JavaScript minta](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Egyetlen dokumentum bemeneti minta
Tegyük fel, hogy a következő DocumentDB API bemeneti kötések a `bindings` function.json tömbje:

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

Tekintse meg a nyelvspecifikus mintát, amely a bemeneti kötés alapján frissíti a dokumentum szöveges érték.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>A C# bemeneti minta #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Az F # bemeneti minta #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Ez a minta szükséges egy `project.json` fájl, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet függőségek:

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>A JavaScript bemeneti minta

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Több dokumentumokkal bemeneti minta

Tegyük fel, hogy kívánja egy SQL-lekérdezést, amelyet több dokumentumok beolvasása várólista eseményindító segítségével testre szabhatja a lekérdezési paramétereket. 

Ebben a példában a várólista eseményindító biztosít egy paraméter `departmentId`. A várólista üzenet `{ "departmentId" : "Finance" }` meghaladná a pénzügyi részleg összes rekordja. Használja a következő *function.json*:

```
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

### <a name="input-sample-with-multiple-documents-in-c"></a>A C# több dokumentumokkal bemeneti minta

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

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Több JavaScript-dokumentumok bemeneti minta

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

## <a id="docdboutput"></a>A DocumentDB API kimeneti kötése
A DocumentDB API kimeneti kötés lehetővé írni egy új dokumentumot egy Azure Cosmos DB adatbázisban. A következő tulajdonságokkal rendelkezik *function.json*:

|Tulajdonság  |Leírás  |
|---------|---------|
|**név**     | A kötési paraméter, a függvény a dokumentum jelölő neve.  |
|**típusa**     | meg kell `documentdb`.        |
|**databaseName** | Az adatbázis, a gyűjteményt, ahol a dokumentum létre tartalmazó.     |
|**collectionName**  | A gyűjtemény, ahol létrehozzák a dokumentum neve. |
|**createIfNotExists**     | Egy logikai értéket, amely azt jelzi, hogy a gyűjtemény létrehozása, ha még nem létezik. Az alapértelmezett érték *hamis*. Ennek oka az, új gyűjtemények fenntartott átviteli sebességet, amelyek hatással vannak költsége van jönnek létre. További részletekért tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**kapcsolat**     |Az Alkalmazásbeállítás, amely tartalmazza az Azure Cosmos DB kapcsolati karakterlánc nevét.        |
|**iránya**     | meg kell `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>A DocumentDB API-jával kimeneti kötése
Ez a szakasz bemutatja, hogyan használhatja a DocumentDB API kimeneti kötelező a funkciókódot.

Alapértelmezés szerint amikor a a függvénynek a kimeneti paraméter írni egy dokumentum jön létre az adatbázisban. A dokumentumban az automatikusan előállított GUID Azonosítóhoz van, a dokumentum azonosítóját. Megadhatja a kimeneti dokumentum-Dokumentumazonosítója megadásával a `id` a kimeneti paraméternek átadott a JSON-objektum tulajdonságait. 

>[!Note]  
>Amikor megad egy meglévő dokumentum Azonosítóját, a fiókbeállítása felülíródik az új kimeneti dokumentum szerint. 

Több dokumentumot a mentendő is köthető `ICollector<T>` vagy `IAsyncCollector<T>` ahol `T` a támogatott típusok egyike.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>A DocumentDB API kimeneti kötése minta
Tegyük fel, hogy a következő DocumentDB API kimeneti kötések a `bindings` function.json tömbje:

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

És egy várólista JSON megkapja a következő formátumban várólista bemeneti kötése van:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

És szeretne létrehozni Azure Cosmos DB dokumentumok minden egyes rekord a következő formátumban:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Tekintse meg a nyelvspecifikus minta, amelyeket a kimeneti kötés használ a dokumentumok hozzáadása az adatbázishoz.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>A C# kimeneti minta #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
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

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Az F # kimeneti minta #

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

Ez a minta szükséges egy `project.json` fájl, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet függőségek:

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>A JavaScript kimeneti minta

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
