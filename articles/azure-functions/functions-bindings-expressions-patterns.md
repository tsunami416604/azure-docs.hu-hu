---
title: Azure Functions kötések és minták
description: Ismerje meg, hogyan hozhat létre különböző Azure Functions kötési kifejezéseket közös minták alapján.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277647"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions kötési kifejezés mintái

Az [Eseményindítók és kötések](./functions-triggers-bindings.md) egyik leghatékonyabb funkciója a *kötés kifejezése*. A *function. JSON* fájlban és a függvény paraméterei és kódja területen olyan kifejezéseket használhat, amelyek különböző forrásokból származó értékekre vannak feloldva.

A legtöbb ilyen kifejezés könnyen azonosítható, mivel kapcsos zárójelek között vannak. Egy üzenetsor-kiváltó függvényben `{queueTrigger}` például a rendszer feloldja az üzenetsor-üzenet szövegét. Ha egy `path` blob kimeneti kötés tulajdonsága `container/{queueTrigger}` és a függvény üzenetsor-üzenettel `HelloWorld`aktiválódik, a rendszer létrehoz egy nevű `HelloWorld` blobot.

Kötéskifejezések típusai

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Eseményindító fájl neve](#trigger-file-name)
* [Eseményindító metaadatok](#trigger-metadata)
* [JSON-adattartalmak](#json-payloads)
* [Új GUID](#create-guids)
* [Aktuális dátum és idő](#current-time)

## <a name="binding-expressions---app-settings"></a>Kötési kifejezések – Alkalmazásbeállítások

Ajánlott eljárásként a titkokat és a kapcsolatok karakterláncait az Alkalmazásbeállítások használatával kell felügyelni, nem pedig a konfigurációs fájlokat. Ez korlátozza a hozzáférést ezekhez a titkokhoz, és biztonságos módon tárolja az olyan fájlokat, mint a *function. JSON* a nyilvános forrású vezérlő adattárakban.

Az Alkalmazásbeállítások akkor is hasznosak, ha módosítani szeretné a környezet alapján a konfigurációt. Tesztelési környezetben például érdemes lehet figyelni egy másik üzenetsor-vagy blob Storage-tárolót.

Az Alkalmazásbeállítások kötési kifejezései más kötési kifejezésektől eltérően vannak azonosítva: a rendszer a kapcsos zárójelek helyett százalékos jelekbe csomagolja őket. Ha például a blob kimeneti kötési útvonala, `%Environment%/newblob.txt` és az `Environment` Alkalmazásbeállítások értéke `Development`, a rendszer létrehoz egy blobot a `Development` tárolóban.

Ha egy függvény helyileg fut, az Alkalmazásbeállítások értékei a *Local. Settings. JSON* fájlból származnak.

Vegye figyelembe, `connection` hogy az eseményindítók és kötések tulajdonsága egy különleges eset, és automatikusan feloldja az értékeket az Alkalmazásbeállítások nélkül, százalékban megadva. 

Az alábbi példa egy Azure Queue Storage-trigger, amely egy `%input-queue-name%` Alkalmazásbeállítások használatával határozza meg, hogy a várólista aktiválva legyen.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Ugyanezt a megközelítést használhatja az osztályok könyvtáraiban:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Eseményindító fájl neve

A `path` blob-triggerek esetében olyan minta lehet, amely lehetővé teszi, hogy az aktiváló blob nevét más kötésekben és a függvény kódjában tekintse át. A minta tartalmazhat olyan szűrési feltételeket is, amelyek meghatározzák, hogy mely Blobok indíthatnak el egy függvényt.

A blob-trigger következő kötésében például a minta a `path` (z `sample-images/{filename}`), amely létrehoz egy nevű `filename`kötési kifejezést:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

A kifejezés `filename` ezután használható kimeneti kötésben a létrehozandó blob nevének megadásához:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

A függvény kódja paraméter neveként a következővel érheti el `filename` ugyanezt az értéket:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

A kötési kifejezések és minták használata is ugyanaz, mint az osztályok könyvtáraiban található attribútumok esetében. A következő példában az attribútumok konstruktorának paramétereinek értékei ugyanazok `path` , mint az előző *function. JSON* -példák: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

A fájlnevek részeihez is létrehozhat kifejezéseket. A következő példában a függvény csak olyan fájlneveken aktiválódik, amelyek megfelelnek a mintának:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

A kifejezéseknek és mintáknak a blob Path karakterláncban történő használatáról további információt a [Storage blob kötési referenciájában](functions-bindings-storage-blob.md)talál.

## <a name="trigger-metadata"></a>Eseményindító metaadatok

Az eseményindító által biztosított adattartalom (például a függvényt kiváltó üzenetsor-üzenet tartalma) mellett számos eseményindító további metaadat-értékeket is biztosít. Ezek az értékek a C# és F # bemeneti paraméterként, illetve a `context.bindings` JavaScriptben lévő objektum tulajdonságaiban is használhatók. 

Az Azure üzenetsor-tárolói trigger például a következő tulajdonságokat támogatja:

* QueueTrigger – az üzenet tartalmának elindítása, ha érvényes karakterlánc
* DequeueCount
* ExpirationTime
* Azonosító
* InsertionTime
* NextVisibleTime
* PopReceipt

Ezek a metaadat-értékek a *function. JSON* fájl tulajdonságaiban érhetők el. Tegyük fel például, hogy üzenetsor-triggert használ, és az üzenetsor-üzenet tartalmazza az olvasni kívánt blob nevét. A *function. JSON* fájlban a blob `queueTrigger` `path` tulajdonságban használhatja a metadata tulajdonságot az alábbi példában látható módon:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Az egyes triggerek metaadat-tulajdonságainak részletes ismertetését a megfelelő hivatkozási cikk ismerteti. Példa: [üzenetsor-trigger metaadatainak](functions-bindings-storage-queue-trigger.md#message-metadata). A dokumentáció a portál Integration ( **integrálás** ) lapján, a kötési konfiguráció terület alatti **dokumentáció** szakaszban is elérhető.  

## <a name="json-payloads"></a>JSON-adattartalmak

Ha egy trigger adattartalma JSON, akkor a más kötésekhez tartozó tulajdonságokat a konfigurációban tekintheti meg ugyanabban a függvényben és a függvény kódjában.

A következő példa egy olyan webhook-függvény *function. JSON* fájlját mutatja be, amely egy blob nevét fogadja a JSON-ban: `{"BlobName":"HelloWorld.txt"}`. A blob bemeneti kötése beolvassa a blobot, és a HTTP-kimeneti kötés visszaadja a blob tartalmát a HTTP-válaszban. Figyelje meg, hogy a blob bemeneti kötése lekéri a blob nevét úgy, hogy `BlobName` közvetlenül a`"path": "strings/{BlobName}"`tulajdonságra hivatkozik ()

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Ahhoz, hogy ez a C# és az F # használatával működjön, szüksége van egy osztályra, amely meghatározza a deszerializálni kívánt mezőket, ahogy az alábbi példában is látható:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

A JavaScriptben a JSON-deszerializálás automatikusan történik.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Pont jelölése

Ha a JSON-adattartalom egyes tulajdonságai a tulajdonságokkal rendelkező objektumok, akkor ezeket közvetlenül a dot jelölés használatával tekintheti meg. Tegyük fel például, hogy a JSON így néz ki:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Közvetlenül is hivatkozhat a `FileName` következőre `BlobName.FileName`:. Ezzel a JSON-formátummal az előző példában `path` szereplő tulajdonság a következőképpen fog kinézni:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

A C# nyelvben két osztályra van szükség:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID-azonosítók létrehozása

A `{rand-guid}` kötési kifejezés létrehoz egy GUID azonosítót. Egy `function.json` fájl következő blob-elérési útja létrehoz egy blobot egy olyan névvel, mint a *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuális idő

A kötési kifejezés `DateTime` a következőhöz lesz `DateTime.UtcNow`feloldva:. Egy `function.json` fájl következő blob-elérési útja létrehoz egy blobot egy olyan névvel, mint *2018-02-16T17-59 -55z. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Kötés futásidőben

A C#-ban és más .NET-nyelveken egy kötelező kötési mintát használhat a *function. JSON* és az attribútumok deklaratív kötéseivel szemben. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. További információért lásd a [C# fejlesztői referenciáját](functions-dotnet-class-library.md#binding-at-runtime) vagy a [c# parancsfájl fejlesztői referenciáját](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
