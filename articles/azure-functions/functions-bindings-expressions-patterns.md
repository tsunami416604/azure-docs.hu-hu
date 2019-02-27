---
title: Az Azure Functions kötések kifejezések és minták
description: Ismerje meg, hozhat létre a különböző Azure Functions kötés kifejezések közös mintáknak megfelelően.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887182"
---
# <a name="azure-functions-binding-expression-patterns"></a>Az Azure Functions kötés kifejezés minták

Az egyik leghatékonyabb szolgáltatása [eseményindítók és kötések](./functions-triggers-bindings.md) van *kötési kifejezésekben*. Az a *function.json* fájlt, és a függvény paraméterei és a kódot, használhatja, hogy a különböző forrásokból származó értékek feloldható kifejezések.

A legtöbb kifejezések azonosítja alkalmazásburkoló ezeket a kapcsos zárójelek közé. Például az üzenetsor eseményindító függvény `{queueTrigger}` oldja fel az üzenetsor üzenet szövege. Ha a `path` tulajdonság a BLOB kimeneti kötés `container/{queueTrigger}` és a egy üzenetsor által aktivált függvény `HelloWorld`, nevű blobba `HelloWorld` jön létre.

Kötés kifejezéstípusok

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Eseményindító-fájl neve](#trigger-file-name)
* [Eseményindító-metaadatok](#trigger-metadata)
* [JSON-adattartalmat](#json-payloads)
* [Új GUID-ja](#create-guids)
* [Aktuális dátum és idő](#current-time)

## <a name="binding-expressions---app-settings"></a>Kötési kifejezésekben - Alkalmazásbeállítások

Ajánlott eljárásként titkos kulcsok és a kapcsolati karakterláncok kezelt Alkalmazásbeállítások ahelyett, hogy konfigurációs fájlok használatával. Ez korlátozza a hozzáférést a titkos adatokat, és lehetővé teszi a biztonságos, mint például a fájlok tárolására *function.json* nyilvános forráskódú vezérlő tárházakban.

Alkalmazásbeállítások minden alkalommal, amikor módosítja a konfiguráció a környezet alapján is hasznosak. Például egy tesztkörnyezetben, előfordulhat, hogy figyelni kívánt egy másik várólista- vagy blob storage-tárolóba.

Alkalmazás beállítás kötési kifejezésekben azonosított eltérően a más kötési kifejezésekben: kapcsos zárójelek helyett százalékjelek burkolja azokat. Például ha a blob kimeneti kötés elérési út `%Environment%/newblob.txt` és a `Environment` alkalmazás beállítás értéke `Development`, létrejön egy blobot a `Development` tároló.

Ha helyileg futtatja a függvényt, alkalmazás-beállítás értékeit származnak az *local.settings.json* fájlt.

Vegye figyelembe, hogy a `connection` eseményindítók és kötések tulajdonsága egy különleges esetben, és automatikusan oldja fel az értékeket alkalmazásbeállításokként százalékjelek nélkül. 

Az alábbi példában egy Azure Queue Storage-eseményindító, amely egy alkalmazásbeállításhoz `%input-queue-name%` meghatározásához a várólistát az indításhoz.

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

Ugyanezzel a módszerrel a osztálykódtárakat használhatja:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Eseményindító-fájl neve

A `path` Blob eseményindító lehet egy mintát, amely lehetővé teszi a blob nevét, a riasztást kiváltó más kötéseiben tekintse meg, és működik a kódot. A minta adja meg, melyik blobok kiválthatja egy függvény meghívási szűrési feltételeket is tartalmazhatnak.

Például az a következő Blob eseményindító kötelező érvényű a `path` minta `sample-images/{filename}`, ami létrehoz egy kötés kifejezés nevű `filename`:

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

A kifejezés `filename` majd használható a kimeneti kötés létrehozása a blob nevének megadása:

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

Függvény kód hozzáfér a Ez ugyanaz az érték használatával `filename` , a paraméter neve:

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

Az azonos tudják használni a kötési kifejezésekben és minták osztálykódtárakat az attribútumokra vonatkozik. A következő példában az attribútum a konstruktor paraméterek azonosak `path` értékek, mint az előző *function.json* példák: 

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

A fájl nevét, például a bővítmény részei a kifejezéseket is létrehozhat. A Blob elérési útja karakterláncban kifejezéseket és minták használatával további információkért lásd: a [tárolási kötés blobhivatkozást](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Eseményindító-metaadatok

Az adattartalom-trigger (például aktivált függvényt az üzenetsorban található üzenet tartalmának) által biztosított mellett számos eseményindító további metaadatok értékeket ad meg. Ezeket az értékeket a bemeneti paraméterek használhatók C# és F# vagy a tulajdonságok a `context.bindings` JavaScript-objektumában. 

Például az Azure Queue storage-eseményindító támogatja az alábbi tulajdonságokat:

* QueueTrigger - üzenet tartalma aktiválása, ha érvénytelen karakterláncot tartalmaz
* DequeueCount
* ExpirationTime
* Azonosító
* InsertionTime
* NextVisibleTime
* PopReceipt

Ezek metaadatértékeket érhetők el az *function.json* fájl tulajdonságai. Tegyük fel például, egy üzenetsor eseményindító használ, és az üzenetsorban található üzenet a beolvasni kívánt blob nevét tartalmazza. Az a *function.json* fájlt használhatja `queueTrigger` a BLOB metaadat-tulajdonságot `path` tulajdonság, az alábbi példában látható módon:

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

A megfelelő áttekintésével foglalkozó cikkben ismertetett egyes eseményindítóhoz tartozó metaadat-tulajdonságainak részleteit. Egy vonatkozó példáért lásd: [üzenetsor eseményindító metaadatai](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentáció is áll rendelkezésre az a **integráció** lapra a portál, a a **dokumentáció** szakasz a kötés konfigurációs terület alatt.  

## <a name="json-payloads"></a>JSON-adattartalmat

A trigger JSON hasznos, ha hivatkozhat más kötések ugyanannak a függvénynek és a függvény kódját a konfigurációban a tulajdonságait.

A következő példa bemutatja a *function.json* egy webhook-függvény, amely megkapja a blob nevében JSON-fájlt: `{"BlobName":"HelloWorld.txt"}`. Egy Blob bemeneti kötést beolvassa a blobot, és a HTTP kimeneti kötés értéket ad vissza a blob tartalmát a HTTP-válaszban. Figyelje meg, hogy a Blob bemeneti kötést lépésként tekintse át a közvetlenül a blob neveként lekérdezi a `BlobName` tulajdonság (`"path": "strings/{BlobName}"`)

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

Funkció működéséhez C# és F#, egy osztály, amely meghatározza a mezőket az alábbi példában látható módon lehet deszerializálni van szüksége:

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

A JavaScript JSON-deszerializálás esetében automatikusan történik.

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

### <a name="dot-notation"></a>Felépítését

A tulajdonságokat a JSON-adattartalom néhány tulajdonságokkal rendelkező objektumok, ha azokat közvetlenül a pontjelöléssel hivatkozhat. Tegyük fel, hogy a JSON néz ki:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Közvetlenül hivatkozhat `FileName` , `BlobName.FileName`. A JSON formátumú, mi a `path` tulajdonság az előző példában láthatóhoz hasonló:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

Két osztályba kell a C#-ban:

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

## <a name="create-guids"></a>Hozzon létre GUID-azonosítói

A `{rand-guid}` kifejezés kötés létrehoz egy GUID Azonosítót. A következő blob elérési utat egy `function.json` fájl egy blobot hoz létre vagy hasonló néven *50710cb5-84b9 - 4d 87-9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Aktuális idő

A kötés kifejezés `DateTime` mutat `DateTime.UtcNow`. A következő blob elérési utat egy `function.json` fájl egy blobot hoz létre vagy hasonló néven *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Kötés futásidőben

C# és az egyéb .NET nyelven, használhatja az imperatív kötés minta, ellentétben a deklaratív kötése *function.json* és attribútumok. Imperatív kötés akkor hasznos, ha a kötési paramétereket kell futásidejű kialakítása helyett időpontjában a következő időpontban számítja. További tudnivalókért tekintse meg a [C# – fejlesztői referencia](functions-dotnet-class-library.md#binding-at-runtime) vagy a [C# szkript fejlesztői segédanyagok](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure-függvény visszaadott értékének használata](./functions-bindings-return-value.md)
