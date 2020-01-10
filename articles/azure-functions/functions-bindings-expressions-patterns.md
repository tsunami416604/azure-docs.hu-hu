---
title: Azure Functions kötések és minták
description: Ismerje meg, hogyan hozhat létre különböző Azure Functions kötési kifejezéseket közös minták alapján.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: a9c45321d12b659febfeb4913d66ea3732813918
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769523"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions kötési kifejezés mintái

Az [Eseményindítók és kötések](./functions-triggers-bindings.md) egyik leghatékonyabb funkciója a *kötés kifejezése*. A *function. JSON* fájlban és a függvény paraméterei és kódja területen olyan kifejezéseket használhat, amelyek különböző forrásokból származó értékekre vannak feloldva.

A legtöbb ilyen kifejezés könnyen azonosítható, mivel kapcsos zárójelek között vannak. Egy üzenetsor-kiváltó függvényben például `{queueTrigger}` a rendszer feloldja az üzenetsor-üzenet szövegét. Ha egy blob kimeneti kötés `path` tulajdonsága `container/{queueTrigger}`, és a függvényt egy üzenetsor-üzenet `HelloWorld`indítja el, a rendszer létrehoz egy `HelloWorld` nevű blobot.

Kötéskifejezések típusai

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Trigger fájljának neve](#trigger-file-name)
* [Trigger metaadatainak](#trigger-metadata)
* [JSON-adattartalom](#json-payloads)
* [Új GUID](#create-guids)
* [Aktuális dátum és idő](#current-time)

## <a name="binding-expressions---app-settings"></a>Kötési kifejezések – Alkalmazásbeállítások

Ajánlott eljárásként a titkokat és a kapcsolatok karakterláncait az Alkalmazásbeállítások használatával kell felügyelni, nem pedig a konfigurációs fájlokat. Ez korlátozza a hozzáférést ezekhez a titkokhoz, és biztonságos módon tárolja az olyan fájlokat, mint a *function. JSON* a nyilvános forrású vezérlő adattárakban.

Az Alkalmazásbeállítások akkor is hasznosak, ha módosítani szeretné a környezet alapján a konfigurációt. Tesztelési környezetben például érdemes lehet figyelni egy másik üzenetsor-vagy blob Storage-tárolót.

Az Alkalmazásbeállítások kötési kifejezései más kötési kifejezésektől eltérően vannak azonosítva: a rendszer a kapcsos zárójelek helyett százalékos jelekbe csomagolja őket. Ha például a blob kimeneti kötési útvonala `%Environment%/newblob.txt`, és az `Environment` Alkalmazásbeállítások értéke `Development`, a rendszer létrehoz egy blobot a `Development` tárolóban.

Ha egy függvény helyileg fut, az Alkalmazásbeállítások értékei a *Local. Settings. JSON* fájlból származnak.

Vegye figyelembe, hogy az eseményindítók és kötések `connection` tulajdonsága egy speciális eset, és automatikusan feloldja az értékeket az alkalmazás beállításainál, a százalékos jelek nélkül. 

Az alábbi példa egy olyan Azure Queue Storage-trigger, amely egy alkalmazás-beállítási `%input-queue-name%` használatával határozza meg, hogy a várólista Mikor aktiválódik.

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

A blob-triggerek `path` egy olyan minta lehet, amely lehetővé teszi az indító blob nevét más kötésekben és a függvény kódjában. A minta tartalmazhat olyan szűrési feltételeket is, amelyek meghatározzák, hogy mely Blobok indíthatnak el egy függvényt.

Például a blob-trigger következő kötésében a `path` minta `sample-images/{filename}`, amely létrehoz egy `filename`nevű kötési kifejezést:

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

A `filename` kifejezés ezután használható kimeneti kötésben a létrehozandó blob nevének megadásához:

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

A függvény kódjához a `filename` paraméter neveként való használata esetén ugyanaz az érték tartozik:

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

A kötési kifejezések és minták használata is ugyanaz, mint az osztályok könyvtáraiban található attribútumok esetében. A következő példában az attribútumok konstruktorának paraméterei ugyanazok `path` értékek, mint az előző *függvény. JSON* -példák: 

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

A fájlnevek részeihez (például a kiterjesztéshez) is létrehozhat kifejezéseket. A kifejezéseknek és mintáknak a blob Path karakterláncban történő használatáról további információt a [Storage blob kötési referenciájában](functions-bindings-storage-blob.md)talál.

## <a name="trigger-metadata"></a>Eseményindító metaadatok

Az eseményindító által biztosított adattartalom (például a függvényt kiváltó üzenetsor-üzenet tartalma) mellett számos eseményindító további metaadat-értékeket is biztosít. Ezek az értékek a (z) C# és a ( F# z) és a (z) `context.bindings` objektumban található bemeneti paraméterekként használhatók a JavaScriptben. 

Az Azure üzenetsor-tárolói trigger például a következő tulajdonságokat támogatja:

* QueueTrigger – az üzenet tartalmának elindítása, ha érvényes karakterlánc
* DequeueCount
* ExpirationTime
* Azonosító
* InsertionTime
* NextVisibleTime
* PopReceipt

Ezek a metaadat-értékek a *function. JSON* fájl tulajdonságaiban érhetők el. Tegyük fel például, hogy üzenetsor-triggert használ, és az üzenetsor-üzenet tartalmazza az olvasni kívánt blob nevét. A *function. JSON* fájlban `queueTrigger` metadata tulajdonságot használhat a blob `path` tulajdonságban az alábbi példában látható módon:

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

Az egyes triggerek metaadat-tulajdonságainak részletes ismertetését a megfelelő hivatkozási cikk ismerteti. Példa: [üzenetsor-trigger metaadatainak](functions-bindings-storage-queue.md#trigger---message-metadata). A dokumentáció a portál Integration ( **integrálás** ) lapján, a kötési konfiguráció terület alatti **dokumentáció** szakaszban is elérhető.  

## <a name="json-payloads"></a>JSON-adattartalmak

Ha egy trigger adattartalma JSON, akkor a más kötésekhez tartozó tulajdonságokat a konfigurációban tekintheti meg ugyanabban a függvényben és a függvény kódjában.

A következő példa egy olyan webhook-függvény *function. JSON* fájlját mutatja be, amely egy blob nevét kapja a JSON-ban: `{"BlobName":"HelloWorld.txt"}`. A blob bemeneti kötése beolvassa a blobot, és a HTTP-kimeneti kötés visszaadja a blob tartalmát a HTTP-válaszban. Figyelje meg, hogy a blob bemeneti kötése lekéri a blob nevét úgy, hogy közvetlenül a `BlobName` tulajdonságra hivatkozik (`"path": "strings/{BlobName}"`)

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

Ahhoz, hogy ez működjön C# a F#és a alkalmazásban, olyan osztályra van szüksége, amely meghatározza a deszerializálni kívánt mezőket, ahogy az alábbi példában is látható:

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

A `FileName` `BlobName.FileName`ként közvetlenül is hivatkozhat. Ezzel a JSON-formátummal a fenti példában szereplő `path` tulajdonság az alábbihoz hasonló lesz:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

A C#-ben két osztályra van szükség:

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

A `{rand-guid}` kötési kifejezés létrehoz egy GUID azonosítót. Egy `function.json` fájlban a blob elérési útja egy olyan nevű blobot hoz létre, amelynek neve például *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuális idő

A kötési kifejezés `DateTime` `DateTime.UtcNow`. Egy `function.json` fájlban a blob elérési útja egy olyan nevű blobot hoz létre, amelynek neve például *2018-02-16T17-59 -55z. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Kötés futásidőben

A C# -ben és más .net-nyelveken egy kötelező kötési mintát is használhat a *function. JSON* és attribútumok deklaratív kötéseinek használatával. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. További információért lásd a [ C# fejlesztői referenciát](functions-dotnet-class-library.md#binding-at-runtime) vagy a [ C# szkript fejlesztői referenciáját](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
