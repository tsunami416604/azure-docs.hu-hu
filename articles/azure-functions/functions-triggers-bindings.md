---
title: Eseményindítók és kötések az Azure Functions szolgáltatásban
description: Ismerje meg, eseményindítók és kötések használata az Azure Functions a végrehajtás online események és a felhőalapú szolgáltatásokhoz való csatlakozáshoz.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/24/2018
ms.author: glenga
ms.openlocfilehash: 694dd98caadb12571c58f9d615cf75325654c772
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801305"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Az Azure Functions eseményindítók és kötések fogalmak

Ez a cikk az eseményindítók és kötések az Azure Functions fogalmi áttekintése. Az új funkciókat, amelyek közösek az összes kötések és az összes támogatott nyelv itt ismertetjük.

## <a name="overview"></a>Áttekintés

A *eseményindító* határozza meg, hogyan függvény hívása. A függvénynek pontosan egy eseményindító. A triggerekhez társított adatok tartoznak, amelyek általában a függvényt aktiváló hasznos adatok.

Bemeneti és kimeneti *kötések* a kódon belül az adatokhoz való csatlakozáshoz deklaratív módszert biztosítanak. Kötések nem kötelező, és a egy függvényt is rendelkezik több bemeneti és kimeneti kötések. 

Eseményindítók és kötések lehetővé teszik a hardcoding elkerülheti a szolgáltatásokat, amelyek dolgozik részleteit. A függvény adatok (például üzenetsori üzenet tartalma) függvény paraméterei fogadása. A visszaadott érték a függvény akkor küldjön adatokat (például üzenetsori üzenetek létrehozásához). A C# és a C#-szkript, alternatív módszerekkel való adatküldés vannak `out` paraméterek és [gyűjtő objektumok](functions-reference-csharp.md#writing-multiple-output-values).

Amikor funkciók fejlesztése az Azure portal használatával, az eseményindítók és kötések vannak konfigurálva a *function.json* fájlt. A portálon Ez a konfiguráció egy felhasználói Felületet biztosít, de szerkesztheti a fájlt közvetlenül a módosítása a következőre a **speciális szerkesztő**.

Functions Visual Studio használatával hozhat létre egy osztálytár fejlesztésekor konfigurálásához eseményindítók és kötések módszerek és attribútumokkal rendelkező paraméterek dekorálása.

## <a name="example-trigger-and-binding"></a>Példa az eseményindító és kötése

Tegyük fel, hogy szeretne írni az Azure Table storage egy új sor, amikor egy új üzenet jelenik meg az Azure Queue storage-ban. Ez a forgatókönyv segítségével valósítható meg az Azure Queue storage-eseményindító és az Azure Table storage kimeneti kötést. 

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

Az első eleme a `bindings` a Queue storage eseményindítója. A `type` és `direction` tulajdonságok az eseményindító azonosítására. A `name` tulajdonság azonosítja a függvény paraméter, amely megkapja a várólista üzenet tartalma. Figyelni kívánt üzenetsor neve `queueName`, és a kapcsolati karakterlánc által azonosított Alkalmazásbeállítás `connection`.

A második eleme a `bindings` az Azure Table Storage kimeneti kötést. A `type` és `direction` tulajdonságok a kötés azonosítására. A `name` tulajdonság határozza meg, hogyan nyújt az a függvény a az új táblázat sorban, ebben az esetben a függvény használatával ad vissza értéket. A tábla neve `tableName`, és a kapcsolati karakterlánc által azonosított Alkalmazásbeállítás `connection`.

Megtekintheti és szerkesztheti a tartalmát *function.json* az Azure Portalon kattintson a **speciális szerkesztő** beállítást a **integráció** a függvény lapon.

> [!NOTE]
> Az érték `connection` a kapcsolati karakterláncot, nem pedig magát a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Kötések használata a kapcsolati karakterláncokat tárolja az alkalmazásbeállítások kényszeríteni a legjobb megoldás, amely *function.json* szolgáltatás titkos kulcsok nem tartalmaz.

Itt látható C# parancsfájl-kód a trigger és a kötés. Figyelje meg, hogy a paraméter, amely biztosítja az üzenetsor üzenet tartalmának neve `order`; ez nevét kötelező megadni, mert a `name` tulajdonságértéket *function.json* van `order` 

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

JavaScript-függvény használható ugyanabban a function.json fájlban:

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

Egy osztálytár, a ugyanaz az eseményindító és a kötési információ &mdash; üzenetsor és a táblázat neve, a storage-fiókok esetében függvény bemeneti és kimeneti paramétereinek &mdash; helyett egy function.json fájlt attribútumok által biztosított. Például:

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

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Információ arról, hogy mely kötések előzetes verzióként érhetők el, vagy éles környezetben való használatra jóváhagyott: [támogatott nyelvek](supported-languages.md).

## <a name="register-binding-extensions"></a>Kötési bővítményeket regisztrálása

Az egyes fejlesztési környezetek kell explicit módon *regisztrálása* egy kötést, amelyet használni szeretne. Kötési bővítményeket NuGet-csomagok találhatók, és a bővítmények regisztrálásához a csomag telepítése. Az alábbi táblázat azt jelzi, hogy mikor és hogyan regisztrálja kötési bővítményeket.

|Fejlesztési környezet |Regisztráció<br/> a függvények 1.x  |Regisztráció<br/> a függvények 2.x  |
|---------|---------|---------|
|Azure Portal|Automatikus|[A kérés automatikus](#azure-portal-development)|
|Helyi Azure Functions Core Tools használatával|Automatikus|[Core Tools CLI-parancsok használata](#local-development-azure-functions-core-tools)|
|Visual Studio 2017 használatával C# osztálytár|[NuGet-eszközök](#c-class-library-with-visual-studio-2017)|[NuGet-eszközök](#c-class-library-with-visual-studio-2017)|
|A Visual Studio Code C# osztálytár|–|[A .NET Core parancssori felület használata](#c-class-library-with-visual-studio-code)|

A következő kötéstípusok kivételek, amelyek nem igényelnek explicit regisztrálása, mert automatikusan regisztrálva vannak az összes verziója és környezetek: HTTP és időzítőhöz.

### <a name="azure-portal-development"></a>Azure portal-fejlesztés

Ez a szakasz csak az funkciók vonatkozik 2.x. Kötési bővítményeket nem kell explicit módon regisztrálni az funkciók 1.x.

Hozzon létre egy függvényt, vagy a felvenni egy kötést, ha kéri a bővítmény számára az eseményindítóval vagy kötéssel van szükség a regisztráció során. A gombra kattintva megismert **telepítése** regisztrálni a bővítményt. Telepítés akár 10 percet is igénybe a használatalapú csomag is.

Csak telepítenie kell minden egyes bővítmény megadott függvényalkalmazás egy alkalommal. Támogatott kötések, amelyek nem állnak rendelkezésre a portálon, vagy frissíteni a telepített kiterjesztést is [manuálisan telepíteni vagy frissíteni a kötési bővítményeket a portálról az Azure Functions](install-update-binding-extensions-manual.md).  

### <a name="local-development-azure-functions-core-tools"></a>Helyi fejlesztés az Azure Functions Core Tools

Ez a szakasz csak az funkciók vonatkozik 2.x. Kötési bővítményeket nem kell explicit módon regisztrálni az funkciók 1.x.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>C# osztálytár a Visual Studio 2017

A **Visual Studio 2017**, csomagokat a Package Manager konzol használatával lehet telepíteni a [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) parancsot, az alábbi példában látható módon:

```powershell
Install-Package Microsoft.Azure.WebJobs.ServiceBus --Version <target_version>
```

A csomag használatára egy adott kötéshez neve a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<target_version>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

### <a name="c-class-library-with-visual-studio-code"></a>C# osztálytár Visual Studio Code használatával

A **Visual Studio Code**, a parancssor használatával csomagokat telepíthet a [dotnet-csomag hozzáadása](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) a .NET Core-CLI-parancs az alábbi példában látható módon:

```terminal
dotnet add package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

A .NET Core CLI csak akkor használható, az Azure Functions 2.x fejlesztéséhez.

A csomag használatára egy adott kötéshez neve a áttekintésével foglalkozó cikkben megtalálható a kötéshez. Egy vonatkozó példáért tekintse meg a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakaszában](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<target_version>` a példában a csomaghoz, egy adott verzióját a például `3.0.0-beta5`. Érvényes verzió szerepel az egyes csomagot oldalakon [NuGet.org](https://nuget.org). A főbb verziók, hogy a Functions futtatókörnyezete megfelelnek a referenciacikk a kötési 1.x vagy 2.x vannak megadva.

## <a name="binding-direction"></a>Kötés iránya

Az összes eseményindítók és kötések rendelkezik egy `direction` tulajdonság a *function.json* fájlt:

- Az eseményindítók az irány mindig van kapcsolva `in`
- Bemeneti és kimeneti kötések használata `in` és `out`
- Néhány speciális iránya kötéseket `inout`. Ha `inout`, csak a **speciális szerkesztő** érhető el a **integráció** fülre.

Ha használ [egy osztálytár attribútumok](functions-dotnet-class-library.md) eseményindítók és kötések konfigurálásához irányát az attribútumok konstruktorában megadott vagy a paraméter típusa fióktól vette a beállításait.

## <a name="using-the-function-return-value"></a>A függvény visszaadott értékének használata

Visszatérési értékkel nyelveken hozhasson létre egy kimeneti kötést, a visszaadott érték:

* C# osztálytár, a alkalmazni a kimeneti kötés attribútum a metódus visszatérési értéke.
* Más nyelven, állítsa be a `name` tulajdonság *function.json* való `$return`.

Ha több kimeneti kötést, a visszaadott értékének használata csak az egyik.

C# és a C#-szkript, adatokat küldeni a kimeneti kötés egyéb módjai a következők `out` paraméterek és [gyűjtő objektumok](functions-reference-csharp.md#writing-multiple-output-values).

Tekintse meg a nyelvspecifikus példa a visszaadott érték használatát:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#-példa

Az alábbiakban C#-kódot, amely a visszaadott érték egy kimeneti kötést, aszinkron például követ használ:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>C#-szkript példa

Itt van a kimeneti kötés a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Íme a C# szkriptkódot, aszinkron például követi:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F #-példa

Itt van a kimeneti kötés a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Az F #-kód itt látható:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript-példa

Itt van a kimeneti kötés a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A JavaScript, a visszaadott érték kerül a második paraméter `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="binding-datatype-property"></a>Kötés dataType tulajdonsága

A .NET a paraméter típusa használatával adja meg a bemeneti adatok adattípusát. Használja például a `string` kötést létrehozni egy üzenetsor eseményindító, egy bájttömböt a bináris és a egy egyéni típus egy POCO objektum deszerializálása olvasás szövegét.

Dinamikusan típusú például a JavaScript nyelven, használja a `dataType` tulajdonságot a *function.json* fájlt. Olvassa el a tartalom HTTP-kérés bináris formátumú, például állítsa be a `dataType` való `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Egyéb lehetőségek `dataType` vannak `stream` és `string`.

## <a name="binding-expressions-and-patterns"></a>Kötelező kifejezések és minták

Az egyik leghatékonyabb szolgáltatása, eseményindítók és kötések *kötési kifejezésekben*. Az a *function.json* fájlt, és a függvény paraméterei és a kódot, használhatja, hogy a különböző forrásokból származó értékek feloldható kifejezések.

A legtöbb kifejezések azonosítja alkalmazásburkoló ezeket a kapcsos zárójelek közé. Például az üzenetsor eseményindító függvény `{queueTrigger}` oldja fel az üzenetsor üzenet szövege. Ha a `path` tulajdonság a BLOB kimeneti kötés `container/{queueTrigger}` és a egy üzenetsor által aktivált függvény `HelloWorld`, nevű blobba `HelloWorld` jön létre.

Kötés kifejezéstípusok

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Eseményindító-fájl neve](#binding-expressions---trigger-file-name)
* [Eseményindító-metaadatok](#binding-expressions---trigger-metadata)
* [JSON-adattartalmat](#binding-expressions---json-payloads)
* [Új GUID-ja](#binding-expressions---create-guids)
* [Aktuális dátum és idő](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Kötési kifejezésekben - Alkalmazásbeállítások

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
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>Kötési kifejezésekben - trigger a fájl neve

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
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
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
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

A fájl nevét, például a bővítmény részei a kifejezéseket is létrehozhat. A Blob elérési útja karakterláncban kifejezéseket és minták használatával további információkért lásd: a [tárolási kötés blobhivatkozást](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Kötési kifejezésekben - eseményindító metaadatok

Az adattartalom-trigger (például aktivált függvényt az üzenetsorban található üzenet tartalmának) által biztosított mellett számos eseményindító további metaadatok értékeket ad meg. Ezeket az értékeket a használható a C# és az F # vagy tulajdonságok bemeneti paraméterként a `context.bindings` JavaScript-objektumában. 

Például az Azure Queue storage-eseményindító támogatja az alábbi tulajdonságokat:

* QueueTrigger - üzenet tartalma aktiválása, ha érvénytelen karakterláncot tartalmaz
* DequeueCount
* expirationTime
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

### <a name="binding-expressions---json-payloads"></a>Kötési kifejezésekben - JSON is észleltünk adattartalmakat.

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

Ehhez a C# és az F # szüksége lesz egy osztály, amely meghatározza a mezőket az alábbi példában látható módon lehet deszerializálni:

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, TraceWriter log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.Info($"Processing: {info.BlobName}");

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

#### <a name="dot-notation"></a>Felépítését

A tulajdonságokat a JSON-adattartalom néhány tulajdonságokkal rendelkező objektumok, ha azokat közvetlenül a pontjelöléssel hivatkozhat. Tegyük fel, hogy a JSON néz ki:

```json
{"BlobName": {
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

### <a name="binding-expressions---create-guids"></a>Kötési kifejezésekben - GUID azonosítók létrehozása

A `{rand-guid}` kifejezés kötés létrehoz egy GUID Azonosítót. A következő blob elérési utat egy `function.json` fájl egy blobot hoz létre vagy hasonló néven *50710cb5-84b9 - 4d 87-9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Kötési kifejezésekben - aktuális idő

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

## <a name="functionjson-file-schema"></a>Function.JSON fájlt séma

A *function.json* sémáját mindig elérhető legyen [ http://json.schemastore.org/function ](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Kötési hibák kezelése

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

A különböző szolgáltatások, funkciók által támogatott összes kapcsolódó hiba témakörökre mutató hivatkozások: a [hibakódok kötés](functions-bindings-error-pages.md#binding-error-codes) szakaszában a [hibakezelés az Azure Functions](functions-bindings-error-pages.md) áttekintése című témakörben.  

## <a name="next-steps"></a>További lépések

Egy adott kötés további információkért lásd a következő cikkeket:

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
