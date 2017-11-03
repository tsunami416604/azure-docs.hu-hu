---
title: "Eseményindítók és kötések az Azure Functions együttműködve |} Microsoft Docs"
description: "Megtudhatja, hogyan csatlakozhat eseményindítók és kötések az Azure Functions a kód végrehajtása online események és a felhő alapú szolgáltatások."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: 74933d9c3535ab71f47c792e20bfbc35e589ec08
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Az Azure Functions eseményindítók és kötések fogalmak
Az Azure Functions lehetővé teszi a kód válaszul Azure-ban és egyéb szolgáltatások események írása keresztül *eseményindítók* és *kötések*. Ez a cikk eseményindítók elméleti áttekintését és kötések az összes támogatott programozási nyelveket. Funkciókat, amelyek megegyeznek az összes kötését dokumentum ismerteti.

## <a name="overview"></a>Áttekintés

Eseményindítók és kötések egy deklaratív lehetőséget adja meg, hogyan függvényt hívják, és milyen adatokat is működik. A *eseményindító* határozza meg, hogyan függvényt hívják. A függvénynek pontosan egy eseményindító kell rendelkeznie. Eseményindítók olyan adatok, amely általában a tartalom, a függvény kiváltó társítva. 

Bemeneti és kimeneti *kötések* a kód az adatokhoz történő kapcsolódáshoz deklaratív lehetőséget biztosít. Eseményindítók hasonló, megadhatja a kapcsolati karakterláncokat és egyéb tulajdonságok függvény konfigurációs. Kötések nem kötelező, és egy függvény több bemeneti és a kimeneti kötéseket. 

Az eseményindítók és kötések, írhat kódot a további általános és nem megoldás biztosítja a szolgáltatások részleteit, amely együttműködik. Egyszerűen vált szolgáltatások bemeneti értékeket a funkciókódot érkező adatokat. A kimeneti adatok (például létrehozhat egy új sor Azure Table Storage-ban) egy másik szolgáltatásba, használja a metódus visszatérési értéke. Vagy ha több érték kimeneti van szüksége, használja a segítő objektuma. Eseményindítók és kötések rendelkezik egy **neve** tulajdonságot, amelynek azonosítója a kódban a kötés elérésére használhat.

Eseményindítók és kötések is konfigurálhatja a **integráció** az Azure Functions portálon lapján. A színfalak a felhasználói felületen módosítja a customdataexample.xml fájlt *function.json* fájl a függvény. Ez a fájl szerkesztésével módosítása a **speciális szerkesztő**.

A következő táblázat az eseményindítók és kötések az Azure Functions által támogatott. 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>Példa: a várólista eseményindító és tábla kimeneti kötése

Tegyük fel, hogy egy új sort írhat Azure Table Storage, amikor az Azure Queue Storage egy új üzenet jelenik meg. Ebben a forgatókönyvben az Azure Queue valósítható eseményindító és az Azure Table Storage kimeneti kötése. 

Az Azure Queue Storage eseményindító igényel a következő információkat a **integráció** lapon:

* Az Alkalmazásbeállítás, amely tartalmazza az Azure Queue Storage az Azure Storage-fiók kapcsolati karakterlánc neve
* A várólista neve
* A kód beolvassa az üzenetsorban lévő üzenetet tartalmát, mint az azonosító `order`.

Az Azure Table Storage használja egy kimeneti kötése a következő adatokkal:

* Az Alkalmazásbeállítás, amely tartalmazza az Azure Table Storage az Azure Storage-fiók kapcsolati karakterlánc neve
* A tábla neve
* Az azonosító létrehozása kimeneti elemek vagy az eredményül kapott értéket a függvény a kódban.

Kötések használja a kapcsolati karakterláncokat az alkalmazás beállításaiban kényszeríteni a legjobb tárolt értékek gyakorlat az, hogy *function.json* szolgáltatás titkos kulcsok tartalmazza, majd helyette egyszerűen tartalmazza a nevét, az alkalmazás beállításaiban.

Ezután használja az Azure Storage integrálása a kódban megadott azonosítók.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

Itt a *function.json* , amely megfelel a fenti kódot. Vegye figyelembe, hogy ugyanazt a konfigurációt használhatja, függetlenül attól, a függvény végrehajtása nyelvét.

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
Megtekintheti és szerkesztheti a tartalmát *function.json* az Azure portálon kattintson a **speciális szerkesztő** beállítást a **integráció** lapon, a függvény.

További példákat és részleteinek integrálása az Azure Storage: [Azure Functions eseményindítók és kötések az Azure Storage](functions-bindings-storage.md).

### <a name="binding-direction"></a>Kötési iránya

Az összes eseményindítók és kötések vannak egy `direction` tulajdonság:

- Az eseményindítók az irány mindig van kapcsolva`in`
- Bemeneti és kimeneti kötések használhatják `in` és`out`
- Néhány kötések támogatja a speciális paraméterirányt `inout`. Ha `inout`, csak a **speciális szerkesztő** érhető el a **integráció** fülre.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>A függvény visszatérési típusa használatával egyetlen kimeneti vissza

Az előző példa bemutatja, hogyan kimeneti egy kötést, amely használatával a különleges name paramétert adja meg a függvény visszatérési értéke használandó `$return`. (Ez csak akkor támogatott a nyelveket, amelyeken a visszatérési érték, például a C#, JavaScript és F #.) Ha egy függvény több kimeneti kötése, `$return` csak az egyik a kimeneti kötéseket. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Az alábbi megjelenítése Példák hogyan visszatérési típusok használhatók kimeneti kötések C#, JavaScript és F #.

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

A .NET használja a bemeneti adatok adatok típusának azonosítására. Használja például a `string` várólista eseményindító, egy bájttömböt bináris és egy POCO objektum deszerializálása egyedi típus beolvasni szöveg kötődni.

Például a JavaScriptek dinamikusan beírt nyelven, használja a `dataType` tulajdonság kötése definíciójában. Olvassa el a tartalom HTTP-kérések bináris formátumú, például típust használjon `binary`:

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

## <a name="trigger-metadata-properties"></a>Eseményindító metaadat-tulajdonságainak

Sok eseményindítók mellett a hasznos adatforgalmat egy eseményindító (például az üzenetsorban található üzenetet függvény kiváltó) által biztosított, adja meg a további metaadatokat értékét. Ezeket az értékeket a C# és F # vagy tulajdonságok bemeneti paraméter használható a `context.bindings` JavaScript objektumban. 

Például egy Azure Storage Üzenetsorába eseményindító támogatja a következő tulajdonságokkal:

* QueueTrigger - indítására üzenet tartalmát, ha egy érvényes karakterláncot
* DequeueCount
* ExpirationTime
* Azonosító
* InsertionTime
* NextVisibleTime
* PopReceipt

A megfelelő referencia-témakör ismerteti a metaadat-tulajdonságainak minden eseményindító részleteit. Dokumentáció is rendelkezésre áll, az a **integráció** a portál lapján, a a **dokumentáció** című szakaszt a kötési konfigurációja területen.  

Például blob eseményindítók rendelkezik néhány késések, mivel segítségével várólista eseményindító futtassa a funkciót (lásd: [Blob Storage eseményindító](functions-bindings-storage-blob.md#storage-blob-trigger)). Az üzenetsorban lévő üzenetet tartalmaz a blob fájlnevét elindítani a. Használja a `queueTrigger` metaadat-tulajdonságnak adhat meg ezt a viselkedést összes konfigurációjáról, nem pedig a kódot.

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

Egy metaadat-tulajdonságot is használható egy *kötési kifejezés* egy másik kötés, a következő szakaszban leírt módon.

## <a name="binding-expressions-and-patterns"></a>Kötelező kifejezések és minták

Az egyik leghatékonyabb részeit, eseményindítók és kötések *kötési kifejezésként*. A kötés belül mintát kifejezések, amelyek ezután felhasználhatók adhat meg más kötésekben vagy a kód. Eseményindító metaadatait is használható a kötési kifejezésként, mint az előző szakaszban leírt minta megjelenítése.

Tegyük fel például, adott blob storage tárolóban, hasonló képek átméretezése szeretné a **kép méret** -sablon a **új függvény** lap. Nyissa meg a **új függvény** -> nyelvi **C#** forgatókönyv -> **minták** -> **ImageResizer-c Sharp**. 

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

Figyelje meg, hogy a `filename` paraméter van megadva a mind a blob eseményindító definícióját, valamint a blob kimeneti kötése. Ez a paraméter funkciókódot is használható.

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


### <a name="random-guids"></a>Véletlenszerű GUID azonosítók
Az Azure Functions kényelmi szintaxist tartalmaz a GUID előállítása érdekében a kötéseiben keresztül a `{rand-guid}` kötési kifejezés. Az alábbi példa használja ahhoz, hogy a blob egyedi nevet létrehozni: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Aktuális idő

A kötési kifejezés használható `DateTime`, amely feloldása egy olyan `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>A kötési kifejezése egyéni bemeneti tulajdonságok kötése

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

C# és egyéb .NET-nyelveket, használhat egy imperatív kötés mintát, szemben a deklaratív kötések *function.json*. Imperatív kötés akkor hasznos, ha a kötési paraméterekhez kell számítani a Tervező helyett futásidejű időpontban. További tudnivalókért lásd: [imperatív kötéseken keresztül futásidőben kötés](functions-reference-csharp.md#imperative-bindings) a C# fejlesztői útmutatóban.

## <a name="next-steps"></a>Következő lépések
Egy adott kötés további információkért tekintse meg a következő cikkeket:

- [HTTP és webhookok](functions-bindings-http-webhook.md)
- [Időzítő](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [Event Hub](functions-bindings-event-hubs.md)
- [Szolgáltatásbusz](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Értesítési központ](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Külső fájl](functions-bindings-external-file.md)
