---
title: "Eseményindítók és kötések az Azure Functions"
description: "Megtudhatja, hogyan csatlakozhat eseményindítók és kötések az Azure Functions a kód végrehajtása online események és a felhő alapú szolgáltatások."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 90a192f58f0e4b285f7aece8a3555c08df051f38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Az Azure Functions eseményindítók és kötések fogalmak

Ez a cikk az eseményindítók és kötések az Azure Functions elméleti áttekintését. Itt ismerteti a funkciókat, amelyek megegyeznek az összes kötését és minden támogatott nyelven.

## <a name="overview"></a>Áttekintés

A *eseményindító* határozza meg, hogyan függvényt hívják. A függvénynek pontosan egy eseményindító kell rendelkeznie. Eseményindítók olyan adatok, amely általában a tartalom, a függvény kiváltó társítva.

Bemeneti és kimeneti *kötések* a kód az adatokhoz történő kapcsolódáshoz deklaratív lehetőséget biztosít. Kötések nem kötelező, és egy függvény több bemeneti és a kimeneti kötéseket. 

Eseményindítók és kötések lehetővé teszik, hogy kerülje hardcoding dolgozunk szolgáltatások részleteit. Ön függvény függvény paramétereiben kap adatokat (például egy üzenetsor-üzenetet tartalmát). Akkor küldjön adatokat (például egy üzenetsor létrehozásához) a függvény visszatérési értéke egy `out` paraméter, vagy egy [gyűjtő objektum](functions-reference-csharp.md#writing-multiple-output-values).

Amikor funkciók fejlesztése az Azure portál használatával, az eseményindítók és kötések vannak konfigurálva a *function.json* fájlt. A portál egy felhasználói Felületet biztosít az ebben a konfigurációban, de tudja szerkeszteni a fájlt közvetlenül módosítása a **speciális szerkesztő**.

Visual Studio használatával hozzon létre egy osztálytár funkciók fejlesztésekor konfigurálásához eseményindítók és kötések dekoráció módszerek és attribútumokkal rendelkező paraméterek.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Információ arról, hogy mely kötések még csak előzetes verziójúak vagy üzemi használatra jóváhagyott: [támogatott nyelv](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Példa: a várólista eseményindító és tábla kimeneti kötése

Tegyük fel, hogy egy új sort írhat Azure Table storage, amikor egy új üzenet jelenik meg az Azure Queue storage. Ebben a forgatókönyvben az Azure Queue valósítható tárolási eseményindító és az Azure Table storage kimeneti kötése. 

Íme egy *function.json* fájl ebben a forgatókönyvben. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Az első eleme a `bindings` tömbjének értéke a várólista tárolási eseményindító. A `type` és `direction` tulajdonságok azonosítsa az eseményindító. A `name` tulajdonság a várólista üzenet tartalma fog kapni. függvényparaméter azonosítja. A figyelheti a várólista nevét kell `queueName`, és a kapcsolati karakterlánc: az alkalmazás-beállítás által azonosított `connection`.

A második eleme a `bindings` az Azure Table Storage kimeneti kötése. A `type` és `direction` tulajdonságok azonosíthatja a kötés. A `name` tulajdonság határozza meg, hogyan a függvény fog adja meg az új táblazatsorok ebben az esetben a függvény visszatérési értéke. A tábla neve `tableName`, és a kapcsolati karakterlánc: az alkalmazás-beállítás által azonosított `connection`.

Megtekintheti és szerkesztheti a tartalmát *function.json* az Azure portálon kattintson a **speciális szerkesztő** beállítást a **integráció** lapon, a függvény.

> [!NOTE]
> Értékének `connection` , amely tartalmazza a kapcsolati karakterlánc, nem pedig magát a kapcsolati karakterlánc alkalmazásbeállítás neve. Kötések kapcsolat használatát a legjobb kényszerítéséhez Alkalmazásbeállítások tárolt karakterláncok gyakorlat az, hogy *function.json* szolgáltatás titkos kulcsokat tartalmaz.

Ez a C# parancsfájlkód, amely kompatibilis a eseményindító és kötés. Figyelje meg, hogy a paraméter, amely a várólista üzenettartalom neve `order`; a név szükség, mert a `name` tulajdonságérték *function.json* van`order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Az azonos function.json fájlt a JavaScript funkcióval rendelkező használhatja:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

Egy osztálytár, a következő eseményindítót és a kötési információ &mdash; várólista és a táblázat neve, a storage-fiókok függvény bemeneti és kimeneti paramétereinek &mdash; attribútumok biztosítja:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Kötési iránya

Az összes eseményindítók és kötések vannak egy `direction` tulajdonságot a *function.json* fájlt:

- Az eseményindítók az irány mindig van kapcsolva`in`
- Bemeneti és kimeneti kötések használhatják `in` és`out`
- Néhány kötések támogatja a speciális paraméterirányt `inout`. Ha `inout`, csak a **speciális szerkesztő** érhető el a **integráció** fülre.

Használata esetén [egy osztálytár attribútumokat](functions-dotnet-class-library.md) eseményindítók és kötések konfigurálásához irányát az attribútumok konstruktorában megadott vagy következtetni a paraméter típusának.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>A függvény visszatérési típusa használatával egyetlen kimeneti vissza

A fenti példa bemutatja, hogyan kimeneti adhat egy kötést, amely a megadott függvény visszatérési értéke használandó *function.json* különleges értéke `$return` a a `name` tulajdonság. (Ez csak akkor támogatott a nyelveket, amelyeken a visszatérési érték, például a parancsfájl C#, JavaScript és F #.) Ha egy függvény több kimeneti kötése, `$return` csak az egyik a kimeneti kötéseket. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Az alábbi megjelenítése Példák hogyan visszatérési típusok használhatók kimeneti kötések C# parancsfájl, JavaScript és F #.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Kötés dataType tulajdonsága

A .NET a következő típusú paraméter segítségével adja meg a bemeneti adatok az adatok típusát. Használja például a `string` várólista eseményindító, egy bájttömböt bináris és egy POCO objektum deszerializálása egyedi típus beolvasni szöveg kötődni.

Például a JavaScriptek dinamikusan beírt nyelven, használja a `dataType` tulajdonságot a *function.json* fájlt. Olvassa el a tartalom HTTP-kérések bináris formátumú, például állítsa be a `dataType` való `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Más beállításokat a `dataType` vannak `stream` és `string`.

## <a name="resolving-app-settings"></a>Alkalmazásbeállítások feloldása

Ajánlott eljárásként titkos kulcsok és a kapcsolati karakterláncok használatával kell irányítani Alkalmazásbeállítások, nem pedig konfigurációs fájlok. Ez korlátozza, hogy ezeknek a kulcsoknak access, és lehetővé teszi a biztonságos tárolására *function.json* a egy nyilvános verziókövetési tárházzal.

Alkalmazásbeállítások is hasznosak, ha meg szeretné változtatni a konfiguráció a környezet alapján. Például egy tesztkörnyezetben, érdemes lehet egy másik várólista vagy a blob-tároló figyelésére.

Alkalmazásbeállítások fakadó problémák megoldásával, amikor egy érték szimpla százalékjelek, például a `%MyAppSetting%`. Vegye figyelembe, hogy a `connection` eseményindítók és kötések tulajdonsága egy különleges esetben, és automatikusan feloldja az értékeket, ha az alkalmazás beállításait. 

A következő példa egy egy Alkalmazásbeállítás használó Azure Queue Storage eseményindító `%input-queue-name%` elindítani a várólista meghatározásához.

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

Használhatja ugyanezt a megközelítést osztálykönyvtárakhoz:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Eseményindító metaadat-tulajdonságainak

Sok eseményindítók mellett a hasznos adatforgalmat egy eseményindító (például az üzenetsorban található üzenetet függvény kiváltó) által biztosított, adja meg a további metaadatokat értékét. Ezeket az értékeket a C# és F # vagy tulajdonságok bemeneti paraméter használható a `context.bindings` JavaScript objektumban. 

Például az Azure Queue storage eseményindító támogatja a következő tulajdonságokkal:

* QueueTrigger - indítására üzenet tartalmát, ha egy érvényes karakterláncot
* DequeueCount
* ExpirationTime
* Azonosító
* InsertionTime
* NextVisibleTime
* PopReceipt

A metaadatok értékek érhetők el az *function.json* fájl tulajdonságai. Tegyük fel például, egy várólista eseményindítót használ, és az üzenetsorban lévő üzenetet szeretné olvasni blob nevét tartalmazza. Az a *function.json* fájlt, használhatja `queueTrigger` metaadat-tulajdonságnak a BLOB `path` tulajdonság, az alábbi példában látható módon:

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

A megfelelő áttekintésével foglalkozó cikkben minden eseményindító metaadat-tulajdonságainak részleteit ismerteti. Egy vonatkozó példáért lásd: [várólista eseményindító metaadatok](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentáció is rendelkezésre áll, az a **integráció** a portál lapján, a a **dokumentáció** című szakaszt a kötési konfigurációja területen.  

## <a name="binding-expressions-and-patterns"></a>Kötelező kifejezések és minták

Az egyik leghatékonyabb részeit, eseményindítók és kötések *kötési kifejezésként*. A konfigurációban az egyik kötéshez ezután felhasználhatók más kötésekben vagy a kód a minta kifejezésekre adhat meg. Eseményindító metaadatok is használható a kötési kifejezésekben, az előző szakaszban ismertetett módon.

Tegyük fel például, egy adott blob storage tárolót, hasonlóan a képek átméretezése szeretné a **kép méret** -sablon a **új függvény** az Azure-portál oldalának (lásd a **minták**  forgatókönyv). 

Itt a *function.json* definíciója:

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

Figyelje meg, hogy a `filename` paraméter van megadva a blob eseményindító definícióját, mind a blob a kimeneti kötése. Ez a paraméter funkciókódot is használható.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Az azonos nem tudják használni a kötés kifejezések és a minták azokra az attribútumokra vonatkozik az osztálykönyvtárakhoz. Például ez egy kép átméretezése függvény osztály könyvtárban:

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="create-guids"></a>GUID azonosítók létrehozása

A `{rand-guid}` kötési kifejezés létrehoz egy GUID Azonosítót. A következő példában egy GUID hozzon létre egy egyedi blob neve: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Aktuális idő

A kötési kifejezés `DateTime` feloldása egy olyan `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Egyéni bemeneti tulajdonságok kötése

Kötési kifejezésként tulajdonságok határozzák meg az eseményindító forgalma maga is hivatkozhat. Érdemes lehet például egy olyan webhook megadott fájlnév a blob storage fájlba dinamikusan kötni.

Például a következő *function.json* tulajdonságot használja `BlobName` az eseményindító forgalma a:

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

Ehhez a C# és F #, meg kell határoznia egy POCO, amely meghatározza a mezőket, amelyeknek deszerializálása az eseményindító tartalmaz.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

A JavaScript JSON-deszerializálás automatikusan történik, és közvetlenül a tulajdonságok használhatók.

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

## <a name="configuring-binding-data-at-runtime"></a>Futásidőben kötés adatok konfigurálása

C# és egyéb .NET-nyelveket, használhat egy imperatív kötés mintát, szemben a deklaratív kötések *function.json* és attribútumok. Imperatív kötés akkor hasznos, ha a kötési paraméterekhez kell számítani a Tervező helyett futásidejű időpontban. További tudnivalókért lásd: [imperatív kötéseken keresztül futásidőben kötés](functions-reference-csharp.md#imperative-bindings) a C# fejlesztői útmutatóban.

## <a name="functionjson-file-schema"></a>Function.JSON fájl séma

A *function.json* fájl séma érhető el: [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Kötési hibák kezelése

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

A funkciók által támogatott különböző szolgáltatások összes vonatkozó hiba témakörökre mutató hivatkozásokat, tekintse meg a [hibakódok kötés](functions-bindings-error-pages.md#binding-error-codes) szakasza a [Azure Functions hibakezelés](functions-bindings-error-pages.md) áttekintését.  

## <a name="next-steps"></a>További lépések

Egy adott kötés további információkért tekintse meg a következő cikkeket:

- [HTTP és webhookok](functions-bindings-http-webhook.md)
- [Időzítő](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [Event Hub](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Értesítési központ](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Külső fájl](functions-bindings-external-file.md)
