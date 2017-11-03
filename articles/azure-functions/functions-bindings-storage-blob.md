---
title: "Az Azure Blob Storage funkciók kötések |} Microsoft Docs"
description: "Azure Storage eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 79b9dbee89a4e33a1768343b9242d6b2b1118355
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Az Azure Functions Blob tároló kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, hogyan konfigurálását és az Azure Blob storage kötések Azure Functions használatát. Az Azure Functions támogatja eseményindító, adjon meg, és kimeneti Azure Blob Storage tárolóban kötéseit. Elérhető összes kötését lásd [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> A [blob csak a storage-fiók](../storage/common/storage-create-storage-account.md#blob-storage-accounts) nem támogatott. A BLOB storage eseményindítók és kötések általános célú tárfiók szükséges. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>A BLOB storage eseményindítók és kötések

Az Azure Blob storage eseményindítót használ, a funkciókódot nevezik egy új vagy frissített blob észlelése esetén. A blob tartalmát vannak megadva, a függvény bemenete.

Definiálhat egy blob storage eseményindító a **integráció** a Functions portálra a lap. A portál létrehoz egyet a következő definícióját a **kötések** szakasza *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

A BLOB bemeneti és kimeneti kötések meghatározása `blob` a kötés típusa:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* A `path` tulajdonság által támogatott kötelező kifejezések és a szűrő paramétereit. Lásd: [minták neve](#pattern).
* A `connection` tulajdonságának tartalmaznia kell egy olyan alkalmazás-beállítást, amely tartalmazza a tárolási kapcsolati karakterlánc nevét. Az Azure portálon, a szokásos szerkesztő a **integráció** lapon konfigurálja az Alkalmazásbeállítás meg, amikor kiválaszt egy tárfiókot.

> [!NOTE]
> Egy blob eseményindító használatakor a fogyasztás terven létezhet legfeljebb 10 perces késleltetést új blobok feldolgozása után egy függvény app inaktív állapotba került. A függvény alkalmazás futtatása után blobok feldolgozása azonnal megtörténik. A kezdeti késleltetés elkerülése érdekében fontolja meg az alábbi lehetőségek közül:
> - Használja az App Service-csomag a mindig engedélyezve van.
> - Egy másik mechanizmus használatával indul el, a blob feldolgozására, például egy üzenetsor-üzenetet, amely tartalmazza a blob neve. Egy vonatkozó példáért lásd: [várólista eseményindító blob bemeneti kötése](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Név minták
A blob neve mintát is megadhat a `path` tulajdonság, amely lehet egy szűrőt, vagy kötési kifejezés. Lásd: [kötelező kifejezések és minták](functions-triggers-bindings.md#binding-expressions-and-patterns).

Például a "eredeti" karakterlánccal kezdődő blobok szűréséhez használja a következő definícióját. Az elérési út keresése nevű blob *eredeti-Blob1.txt* a a *bemeneti* tároló és a értékének a `name` függvény a kódban a változó `Blob1`.

```json
"path": "input/original-{name}",
```

Külön-külön kötést létrehozni a blob-fájl neve és a bővítmény, két mintát használja. Ezt az elérési utat is talál nevű blob *eredeti-Blob1.txt*, és az értéke a `blobname` és `blobextension` változók a funkciókódot *eredeti-Blob1* és *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

A BLOB típusú fájl rögzített érték a kiterjesztést használatával korlátozhatja. Például csak a .png fájlok indításához használja a következő mintát:

```json
"path": "samples/{name}.png",
```

Kapcsos zárójelek speciális karakterek a mintában. Annak megadásához, blob neve kapcsos zárójelek a neve, a használatával két kapcsos zárójelek is escape. Az alábbi példa talál nevű blob *{20140101}-soundfile.mp3* a a *képek* tárolót, és a `name` változó a funkciókódot érték *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Eseményindító metaadatok

A blob eseményindító biztosít több metaadat-tulajdonságot. Ezeket a tulajdonságokat meg más kötésekben kötések kifejezések részeként vagy a kód paramétereiben használható. Ezekkel az értékekkel rendelkeznek az azonos szemantikákkal, [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Gépelje be: `string`. Az eseményindító blob elérési út
- **URI**. Gépelje be: `System.Uri`. A blob URI-azonosítóját az elsődleges helyre.
- **Tulajdonságok**. Gépelje be: `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. A blob rendszer tulajdonságai.
- **Metaadatok**. Gépelje be: `IDictionary<string,string>`. A felhasználó által definiált metaadatok a BLOB.

<a name="receipts"></a>

### <a name="blob-receipts"></a>A BLOB visszaigazolások
Az Azure Functions futtatókörnyezettel biztosítja, hogy nincs blob funkció egynél többször meghívása megtörténik a azonos új vagy frissített BLOB. Annak meghatározásához, ha egy adott blobverzió feldolgozása tart fenn *blob-visszaigazolások*.

Az Azure Functions tárolók blob-visszaigazolások nevű tároló *azure-webjobs-állomások* függvény alkalmazás az Azure storage-fiók (határozzák meg az Alkalmazásbeállítás `AzureWebJobsStorage`). Egy blob fogadását rendelkezik a következő információkat:

* A kiváltott függvény ("*&lt;függvény alkalmazás neve >*. Működik.  *&lt;függvény neve >*", például:"MyFunctionApp.Functions.CopyBlob")
* A tároló neve
* A blob típusú ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag (például egy blob verziójának azonosítója: "0x8D1DC6E70A277EF")

Egy blob újrafeldolgozása kényszerítéséhez blob fogadását, hogy a BLOB törlése a *azure-webjobs-állomások* tároló manuálisan.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Az elhalt blobok kezelése
Ha egy adott BLOB egy blob funkció nem sikerül, az Azure Functions újrapróbálkozik, amelyek összesen 5 alkalommal alapértelmezés szerint. 

Minden 5 próbálkozás sikertelen lesz, ha az Azure Functions ad hozzá egy üzenet nevű tároló várólista *webjobs-blobtrigger-poison*. Az elhalt blobok várólista üzenet egy JSON-objektum, amely tartalmazza a következő tulajdonságokkal:

* FunctionId (formátumú  *&lt;függvény alkalmazás neve >*. Működik.  *&lt;függvény neve >*)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* Blobnév
* ETag (például egy blob verziójának azonosítója: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Ciklikus lekérdezés a nagyméretű tárolók BLOB
Ha a figyelt blob tároló több mint 10 000 blobot tartalmaz, a funkciók futásidejű vizsgálatok a naplófájlok új vagy módosított blobok figyelendő. Ez a folyamat nincs valós időben. Egy függvény előfordulhat, hogy nem get indulnak el, néhány percig, vagy már a blob létrehozása után. Emellett [tárolási naplófájlokat hoz létre a lehető legjobb rendezését,"a](/rest/api/storageservices/About-Storage-Analytics-Logging) alapján. Nincs nem garantálja, hogy a rendszer rögzíti-e az összes esemény. Bizonyos körülmények között a naplók kimaradhatnak. Ha a gyorsabb és megbízhatóbb blob feldolgozási van szüksége, érdemes létrehozni egy [üzenetsor](../storage/queues/storage-dotnet-how-to-use-queues.md) a blob létrehozásakor. Ezt követően egy [várólista eseményindító](functions-bindings-storage-queue.md) helyett egy blob eseményindító a blob feldolgozni.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Egy blob eseményindítót használ, és a bemeneti kötése
.NET-es függvényeket, nyissa meg a tárfiókban tárolt adatok használatával, mint egy metódus paraméterének `Stream paramName`. Itt `paramName` az Ön által megadott érték a [eseményindító konfigurációs](#trigger). Node.js funkciók, nyissa meg a bemeneti blob adatok `context.bindings.<name>`.

A .NET köthető az típusok az alábbi listán. Ha egy bemeneti kötése, néhány, a következő típusú szükséges egy `inout` irányban kötés *function.json*. Ebben az irányban nem támogatja a szokásos szerkesztő, így a speciális szerkesztő kell használni.

* `TextReader`
* `Stream`
* `ICloudBlob`(a "inout" kötés irányát igényel)
* `CloudBlockBlob`(a "inout" kötés irányát igényel)
* `CloudPageBlob`(a "inout" kötés irányát igényel)
* `CloudAppendBlob`(a "inout" kötés irányát igényel)

Szöveges BLOB várható, ha is köthető egy .NET `string` típusa. Ez csak akkor javasolt, ha a blob mérete kisebb, mint a teljes blob tartalmát a memóriába betöltött. Általában célszerű használni egy `Stream` vagy `CloudBlockBlob` típusa.

## <a name="trigger-sample"></a>Eseményindító minta
Tegyük fel, a következő function.json, amely meghatározza egy blob storage eseményindító:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Tekintse meg a nyelvspecifikus mintát, amelyre bejelentkezik a figyelt tároló hozzáadott minden egyes blob tartalmát.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>A BLOB eseményindító példákban a C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>A node.js eseményindító – példa

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Egy blob használatával kimeneti kötése

A .NET-es függvényeket, akkor vagy használja a `out string` paraméter a függvényaláíráshoz vagy az alábbi listában szereplő egyik módszert használja. A Node.js funkciókat érheti el a kimeneti blob használatával `context.bindings.<name>`.

A .NET-es függvényeket a kimenetre küldheti a következő típusok egyikének sem:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Várólista eseményindító blob bemeneti és kimeneti minta
Tegyük fel, hogy a következő function.json rendelkezik, amely meghatározza egy [Queue Storage eseményindító](functions-bindings-storage-queue.md), a blob-tároló bemeneti és kimeneti blob-tároló. Figyelje meg a a `queueTrigger` metaadat-tulajdonságnak. a blob bemeneti és kimeneti `path` tulajdonságok:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Tekintse meg a nyelvspecifikus mintát, amely a bemeneti blob másolja a kimeneti blob.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>A BLOB kötése például a C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out Stream myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>A node.js BLOB kötés – példa

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

