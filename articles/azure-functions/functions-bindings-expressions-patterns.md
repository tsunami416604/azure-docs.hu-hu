---
title: Az Azure Functions kifejezéseket és mintákat köt össze
description: Ismerje meg, hogy hozzon létre különböző Azure Functions kötési kifejezések közös minták alapján.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277647"
---
# <a name="azure-functions-binding-expression-patterns"></a>Az Azure Functions kötési kifejezésmintái

Az [eseményindítók és kötések](./functions-triggers-bindings.md) egyik leghatékonyabb jellemzője a *kötési kifejezések.* A *function.json* fájlban, valamint a függvényparaméterekben és a kódban olyan kifejezéseket használhat, amelyek különböző forrásokból származó értékeket oldanak fel.

A legtöbb ilyen kifejezés könnyen azonosítható, mivel kapcsos zárójelek között vannak. Például egy várólista-eseményindító függvényben `{queueTrigger}` a várólistaüzenet szövegére oldódik fel. Ha `path` a blob kimeneti `container/{queueTrigger}` kötés tulajdonsága, és a `HelloWorld`függvényt egy `HelloWorld` várólista-üzenet váltja ki, létrejön egy nevű blob.

Kötéskifejezések típusai

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Eseményindító fájl neve](#trigger-file-name)
* [Eseményindító metaadatok](#trigger-metadata)
* [JSON-adattartalmak](#json-payloads)
* [Új GUID](#create-guids)
* [Aktuális dátum és idő](#current-time)

## <a name="binding-expressions---app-settings"></a>Kötési kifejezések - alkalmazásbeállítások

Ajánlott eljárásként a titkos kulcsokat és a kapcsolati karakterláncokat az alkalmazásbeállítások, nem pedig a konfigurációs fájlok használatával kell kezelni. Ez korlátozza a hozzáférést ezekhez a titkos kulcsokhoz, és biztonságossá teszi a fájlok, például *a function.json* nyilvános forrásvezérlő tárolókban való tárolását.

Az alkalmazásbeállítások akkor is hasznosak, ha a környezet alapján szeretné módosítani a konfigurációt. Például egy tesztkörnyezetben érdemes lehet figyelni egy másik várólista vagy blob tároló tároló.

Az alkalmazásbeállítási kötési kifejezések azonosítása a többi kötési kifejezéstől eltérően van azonosítva: a kapcsos zárójelek helyett százalékjelekbe vannak csomagolva. Ha például a blob `%Environment%/newblob.txt` kimeneti `Environment` kötéselérési `Development`útvonal, és az alkalmazás `Development` beállítási értéke, akkor egy blob jön létre a tárolóban.

Ha egy függvény helyileg fut, az alkalmazásbeállítási értékek a *local.settings.json* fájlból származnak.

Vegye figyelembe, hogy az `connection` eseményindítók és kötések tulajdonsága egy speciális eset, és automatikusan feloldja az értékeket alkalmazásbeállításokként, százalékjelek nélkül. 

A következő példa egy Azure Queue Storage `%input-queue-name%` eseményindító, amely egy alkalmazásbeállítást használ a várólista aktiválásához.

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

Ugyanezt a módszert használhatja az osztálytárakban is:

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

A `path` Blob-eseményindító lehet egy minta, amely lehetővé teszi, hogy hivatkozzon az aktiváló blob nevét más kötések és a függvénykód. A minta szűrési feltételeket is tartalmazhat, amelyek meghatározzák, hogy mely blobok válthatnak ki függvénymeghívást.

Például a következő Blob-eseményindító-kötésben a `path` minta a `sample-images/{filename}`, amely létrehoz egy kötési kifejezést: `filename`

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

A `filename` kifejezés ezután egy kimeneti kötésben használható a létrehozandó blob nevének megadásához:

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

A függvénykód ugyanahhoz az `filename` értékhez hozzáféréssel rendelkezik, ha paraméternévként használja:

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

A kötési kifejezések és minták használatának lehetősége az osztálytárak attribútumaira is vonatkozik. A következő példában az attribútumkonstruktor paraméterei megegyeznek `path` az előző *function.json* példákkal: 

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

A fájlnév egyes részeihez kifejezéseket is létrehozhat. A következő példában a függvény csak a mintának megfelelő fájlneveken aktiválódik:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

A Kifejezések és minták blob elérési út karakterláncában való használatáról a [Storage blob kötési hivatkozása című témakörben talál](functions-bindings-storage-blob.md)további információt.

## <a name="trigger-metadata"></a>Eseményindító metaadatok

Az eseményindító által biztosított adattartalomon (például a függvényt kiváltó várólista-üzenet tartalmán) kívül számos eseményindító további metaadat-értékeket is biztosít. Ezek az értékek a C# és F# bemeneti `context.bindings` paraméterekként, illetve az objektum JavaScript-beli tulajdonságaiként használhatók. 

Például egy Azure Queue storage-eseményindító a következő tulajdonságokat támogatja:

* QueueTrigger - üzenettartalom aktiválása érvényes karakterlánc esetén
* Várólista-számláló törlése
* ExpirationTime
* Azonosító
* InsertionTime
* NextVisibleTime (Következőlátható idő)
* PopReceipt

Ezek a metaadat-értékek a *function.json* fájl tulajdonságaiban érhetők el. Tegyük fel például, hogy egy várólista-eseményindítót használ, és a várólista-üzenet tartalmazza az olvasni kívánt blob nevét. A *function.json* fájlban metaadat-tulajdonságot `queueTrigger` használhat `path` a blob tulajdonságban, ahogy az a következő példában látható:

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

Az egyes eseményindítók metaadat-tulajdonságainak részleteit a megfelelő referenciacikk ismerteti. Például lásd a [várólista-eseményindítómetaadatait.](functions-bindings-storage-queue-trigger.md#message-metadata) A dokumentáció a portál **Integrálás** lapján, a **kötési** konfigurációs terület alatti Dokumentáció szakaszban is elérhető.  

## <a name="json-payloads"></a>JSON-adattartalmak

Ha egy eseményindító hasznos tartalom JSON, hivatkozhat a tulajdonságaira konfigurációban más kötések ugyanabban a függvényben és a függvénykódban.

A következő példa a *function.json* fájlt mutatja be egy webhook függvényhez, amely blobnevet kap a JSON-ban: `{"BlobName":"HelloWorld.txt"}`. A Blob bemeneti kötés beolvassa a blobot, és a HTTP-kimeneti kötés visszaadja a blob tartalmát a HTTP-válaszban. Figyelje meg, hogy a Blob bemeneti kötés `BlobName` leadja a blob nevét közvetlenül a tulajdonságra ( )`"path": "strings/{BlobName}"`hivatkozva

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

Ahhoz, hogy ez C# és F# nyelven működjön, olyan osztályra van szükség, amely meghatározza a deszerializálandó mezőket, ahogy az a következő példában látható:

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

JavaScript ben a JSON deszerializálása automatikusan végrehajtásra kerül.

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

### <a name="dot-notation"></a>Dot jelölés

Ha a JSON-tartalom egyes tulajdonságai tulajdonságokkal rendelkező objektumok, akkor ezekre közvetlenül pontjelöléssel hivatkozhat. Tegyük fel például, hogy a JSON így néz ki:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Közvetlenül is `FileName` hivatkozhat a. `BlobName.FileName` Ezzel a JSON formátumban, `path` itt van, amit a tulajdonság az előző példában nézne:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

A C#-ban két osztályra van szükség:

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

## <a name="create-guids"></a>GUID azonosítók létrehozása

A `{rand-guid}` kötési kifejezés GUID azonosítót hoz létre. A fájlban a `function.json` következő blobelérési út hoz létre egy blobot, amelynek neve *50710cb5-84b9-4d87-9d83-a03d6976a682.txt.*

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuális idő

A kötési `DateTime` kifejezés `DateTime.UtcNow`feloldása . A `function.json` fájlban lévő következő blobelérési út létrehoz egy blobot, amelynek neve például *2018-02-16T17-59-55Z.txt.*

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Kötés futásidőben

C# és más .NET nyelveken a *function.json* és az attribútumok deklaratív kötéseivel szemben egy imperatív kötést is használhat. A minós kötés akkor hasznos, ha a kötési paramétereket futásidőben kell kiszámítani, nem pedig tervezési időben. További információért tekintse meg a [C# fejlesztői hivatkozást](functions-dotnet-class-library.md#binding-at-runtime) vagy a [C# parancsfájl fejlesztői hivatkozását.](functions-reference-csharp.md#binding-at-runtime)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Függvény visszatérési értékének használata](./functions-bindings-return-value.md)
