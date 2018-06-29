---
title: Eseményindítók és kötések az Azure Functions
description: Megtudhatja, hogyan csatlakozhat eseményindítók és kötések az Azure Functions a kód végrehajtása online események és a felhő alapú szolgáltatások.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/24/2018
ms.author: tdykstra
ms.openlocfilehash: 5e7e6608003b365d5516ca2e94a51c0710ad1125
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061353"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Az Azure Functions eseményindítók és kötések fogalmak

Ez a cikk az eseményindítók és kötések az Azure Functions elméleti áttekintését. Itt ismerteti a funkciókat, amelyek megegyeznek az összes kötését és minden támogatott nyelven.

## <a name="overview"></a>Áttekintés

A *eseményindító* határozza meg, hogyan függvényt hívják. A függvénynek pontosan egy eseményindító kell rendelkeznie. Eseményindítók olyan adatok, amely általában a tartalom, a függvény kiváltó társítva.

Bemeneti és kimeneti *kötések* a kód az adatokhoz történő kapcsolódáshoz deklaratív lehetőséget biztosít. Kötések nem kötelező, és egy függvény több bemeneti és a kimeneti kötéseket. 

Eseményindítók és kötések lehetővé teszik, hogy kerülje hardcoding dolgozunk szolgáltatások részleteit. A függvény függvény paramétereiben kap adatokat (például egy üzenetsor-üzenetet tartalmát). Akkor küldjön adatokat (például egy üzenetsor létrehozásához) a függvény visszatérési értéke egy `out` paraméter, vagy egy [gyűjtő objektum](functions-reference-csharp.md#writing-multiple-output-values).

Amikor funkciók fejlesztése az Azure portál használatával, az eseményindítók és kötések vannak konfigurálva a *function.json* fájlt. A portál egy felhasználói Felületet biztosít az ebben a konfigurációban, de tudja szerkeszteni a fájlt közvetlenül módosítása a **speciális szerkesztő**.

Visual Studio használatával hozzon létre egy osztálytár funkciók fejlesztésekor konfigurálásához eseményindítók és kötések dekoráció módszerek és attribútumokkal rendelkező paraméterek.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Információ arról, hogy mely kötések még csak előzetes verziójúak vagy üzemi használatra jóváhagyott: [támogatott nyelv](supported-languages.md).

## <a name="register-binding-extensions"></a>Regisztrálja a kötési bővítmény

Bizonyos fejlesztési környezetekben, hogy explicit módon *regisztrálása* használni kívánt egyik kötése. Kötési bővítmények NuGet-csomagok szerepelnek, és regisztrálhat egy kiterjesztést a csomag telepítése. A következő táblázat azt jelzi, hogy mikor és hogyan regisztrálja kötés bővítmények.

|Fejlesztési környezet |Regisztráció<br/> a funkciók 1.x  |Regisztráció<br/> a funkciók 2.x  |
|---------|---------|---------|
|Azure Portal|Automatikus|[Automatikus kérdéshez](#azure-portal-development)|
|Helyi az Azure Functions alapvető eszközökkel|Automatikus|[Alapvető eszközök parancssori felület parancsai használni](#local-development-azure-functions-core-tools)|
|A Visual Studio 2017 használatával C# osztálytár|[NuGet-eszközök](#c-class-library-with-visual-studio-2017)|[NuGet-eszközök](#c-class-library-with-visual-studio-2017)|
|Visual Studio Code használatával C# osztálytár|–|[A .NET Core CLI használata](#c-class-library-with-visual-studio-code)|

A következő kötéstípusok kivételeket, amelyek nem igényelnek explicit regisztrálása, mert a rendszer automatikusan regisztrálja az összes verziója és környezetekben: HTTP időzítő és Azure Storage (BLOB, üzenetsorok és táblák). 

### <a name="azure-portal-development"></a>Azure portál fejlesztési

Hozzon létre egy függvényt vagy felvenni egy kötést, megkérdezi, ha az eseményindító vagy a kötési bővítmény regisztrációs igényel. Válaszolnia kell a figyelmeztetésre kattintva **telepítése** regisztrálni a bővítményt. Telepítés egy fogyasztás terv akár 10 percet vehet igénybe.

Csak telepítenie kell minden egyes bővítmény egy adott funkció alkalmazást egyszer. 

### <a name="local-development-azure-functions-core-tools"></a>Helyi fejlesztési Azure Functions Core eszközök

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>C# osztály függvénytár, amely a Visual Studio 2017

A **Visual Studio 2017**, csomagokat a Csomagkezelő konzol használatával telepítheti a [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) parancs, a következő példában látható módon:

```powershell
Install-Package Microsoft.Azure.WebJobs.ServiceBus --Version <target_version>
```

Egy adott kötéshez használandó a csomag nevét, hogy a kötéshez a áttekintésével foglalkozó cikkben találhatók. Egy vonatkozó példáért lásd: a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakasza](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<target_version>` a példában a csomaghoz, az adott verzióval rendelkező például `3.0.0-beta5`. A következő egyes csomagot lapokon felsorolt érvényes verziók [NuGet.org](https://nuget.org). A Functions futtatókörnyezete megfelelő Főverziók a áttekintésével foglalkozó cikkben a kötéshez megadott 1.x vagy 2.x.

### <a name="c-class-library-with-visual-studio-code"></a>C# osztálytár a Visual Studio Code

A **Visual Studio Code**, a parancssor a csomagok telepítése is a [dotnet csomag hozzáadása](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) a .NET Core CLI-t, a parancsot a következő példában látható módon:

```terminal
dotnet add package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

A .NET Core CLI csak az Azure Functions 2.x fejlesztési használható.

Egy adott kötéshez használandó a csomag nevét, hogy a kötéshez a áttekintésével foglalkozó cikkben találhatók. Egy vonatkozó példáért lásd: a [csomagok a Service Bus kötés áttekintésével foglalkozó cikkben szakasza](functions-bindings-service-bus.md#packages---functions-1x).

Cserélje le `<target_version>` a példában a csomaghoz, az adott verzióval rendelkező például `3.0.0-beta5`. A következő egyes csomagot lapokon felsorolt érvényes verziók [NuGet.org](https://nuget.org). A Functions futtatókörnyezete megfelelő Főverziók a áttekintésével foglalkozó cikkben a kötéshez megadott 1.x vagy 2.x.

## <a name="example-trigger-and-binding"></a>Példa eseményindító és kötés

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

Az első eleme a `bindings` tömbjének értéke a várólista tárolási eseményindító. A `type` és `direction` tulajdonságok azonosítsa az eseményindító. A `name` tulajdonság, amely megkapja a várólista üzenettartalom függvényparaméter azonosítja. A figyelheti a várólista nevét kell `queueName`, és a kapcsolati karakterlánc: az alkalmazás-beállítás által azonosított `connection`.

A második eleme a `bindings` az Azure Table Storage kimeneti kötése. A `type` és `direction` tulajdonságok azonosíthatja a kötés. A `name` tulajdonság határozza meg, hogy a függvény biztosítja az új táblázat sor kerül, ebben az esetben a függvény használatával érték. A tábla neve `tableName`, és a kapcsolati karakterlánc: az alkalmazás-beállítás által azonosított `connection`.

Megtekintheti és szerkesztheti a tartalmát *function.json* az Azure portálon kattintson a **speciális szerkesztő** beállítást a **integráció** lapon, a függvény.

> [!NOTE]
> Értékének `connection` , amely tartalmazza a kapcsolati karakterlánc, nem pedig magát a kapcsolati karakterlánc alkalmazásbeállítás neve. Kötések kapcsolat használatát a legjobb kényszerítéséhez Alkalmazásbeállítások tárolt karakterláncok gyakorlat az, hogy *function.json* szolgáltatás titkos kulcsokat tartalmaz.

Ez a C# parancsfájlkód, amely kompatibilis a eseményindító és kötés. Figyelje meg, hogy a paraméter, amely a várólista üzenettartalom neve `order`; a név szükség, mert a `name` tulajdonságérték *function.json* van `order` 

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

Egy osztálytár, a következő eseményindítót és a kötési információ &mdash; várólista és a táblázat neve, a storage-fiókok függvény bemeneti és kimeneti paramétereinek &mdash; function.json fájl helyett attribútumok biztosítja. Például:

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

- Az eseményindítók az irány mindig van kapcsolva `in`
- Bemeneti és kimeneti kötések használhatják `in` és `out`
- Néhány kötések támogatja a speciális paraméterirányt `inout`. Ha `inout`, csak a **speciális szerkesztő** érhető el a **integráció** fülre.

Használata esetén [egy osztálytár attribútumokat](functions-dotnet-class-library.md) eseményindítók és kötések konfigurálásához irányát az attribútumok konstruktorában megadott vagy következtetni a paraméter típusának.

## <a name="using-the-function-return-value"></a>Függvény visszatérési értéke

Visszatérési értékkel rendelkező nyelven köthető egy kimeneti kötése az eredményül kapott értéket:

* A C# osztálytár a metódus visszatérési érték a kimeneti kötése attribútum vonatkozik.
* Más nyelveken, állítsa be a `name` tulajdonság *function.json* való `$return`.

Ha írási egynél több elem van szüksége, használja a [gyűjtő objektum](functions-reference-csharp.md#writing-multiple-output-values) a visszatérési érték helyett. Ha több kimeneti kötése, használ az eredményül kapott értéket csak az egyiket.

Tekintse meg a nyelvspecifikus példát:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# – példa

Az alábbiakban C#-kódban, amely az eredményül kapott értéket használ egy kimeneti kötése, aszinkron például követi:

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

### <a name="c-script-example"></a>C# parancsfájl – példa

Itt van a kimeneti kötése a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ez a C# parancsfájlkód, aszinkron például követi:

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

### <a name="f-example"></a>F # – példa

Itt van a kimeneti kötése a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A F # kód itt látható:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript – példa

Itt van a kimeneti kötése a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A JavaScript, az eredményül kapott értéket a második paraméterben kerül `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
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

## <a name="binding-expressions-and-patterns"></a>Kötelező kifejezések és minták

Az egyik leghatékonyabb részeit, eseményindítók és kötések *kötési kifejezésként*. Az a *function.json* fájlt, és függvényparamétereket és kód, használhatja a különböző forrásokból származó értékek feloldható kifejezések.

A legtöbb kifejezések használatával őket kapcsos zárójelek azonosítja. A várólista funkció, például a `{queueTrigger}` oldja fel a sor szövege. Ha a `path` tulajdonság a BLOB kimeneti kötése `container/{queueTrigger}` és egy üzenetsor váltja ki a függvény `HelloWorld`, nevű blob `HelloWorld` jön létre.

Kötési kifejezés típusai

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Eseményindító fájl neve](#binding-expressions---trigger-file-name)
* [Eseményindító metaadatok](#binding-expressions---trigger-metadata)
* [JSON hasznos adat található](#binding-expressions---json-payloads)
* [Új GUID-ja](#binding-expressions---create-guids)
* [Aktuális dátum és idő](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Kötési kifejezésként - Alkalmazásbeállítások

Ajánlott eljárásként titkos kulcsok és a kapcsolati karakterláncok használatával kell irányítani Alkalmazásbeállítások, nem pedig konfigurációs fájlok. Ez korlátozza, hogy ezeknek a kulcsoknak access, és lehetővé teszi a biztonságos, mint például a fájlok tárolására szolgáló *function.json* a nyilvános forráskódú vezérlő tárházak találhatók.

Alkalmazásbeállítások is hasznosak, ha meg szeretné változtatni a konfiguráció a környezet alapján. Például egy tesztkörnyezetben, érdemes lehet egy másik várólista vagy a blob-tároló figyelésére.

App beállítás kötési kifejezésekben másképp azonosítja a kötés kifejezésekre: kapcsos zárójelek helyett százalékjelek csomagolni vannak. Például ha a kimeneti blob kötési útvonal `%Environment%/newblob.txt` és a `Environment` app beállítás értéke `Development`, blob létrejön a `Development` tároló.

Egy függvény helyben fut, amikor app beállításértékek származik a *local.settings.json* fájlt.

Vegye figyelembe, hogy a `connection` eseményindítók és kötések tulajdonsága egy különleges esetben, és automatikusan feloldja az értékeket, Alkalmazásbeállítások százalékjelek nélkül. 

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

### <a name="binding-expressions---trigger-file-name"></a>Kötési kifejezésként - eseményindító fájl neve

A `path` Blob eseményindító egy mintát, amely lehetővé teszi, hogy tekintse meg az eseményindító blob meg más kötésekben nevét, és működnek kódot is járhat. A mintát adja meg, melyik blobok elindítható egy függvény meghívása szűrési feltételeket is tartalmazhatnak.

A következő Blob eseményindító kötés, például a a `path` minta `sample-images/{filename}`, ami létrehoz egy kötési kifejezése nevű `filename`:

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

A kifejezés `filename` majd használható egy kimeneti kötése a létrehozás alatt áll a BLOB nevének megadásához:

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

Funkciókódot hozzáfér az ugyanazon érték használatával `filename` paraméter neve:

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

Az azonos nem tudják használni a kötés kifejezések és a minták azokra az attribútumokra vonatkozik az osztálykönyvtárakhoz. A következő példában a attribútum konstruktorparaméterek megegyeznek `path` értékeket az előző *function.json* példák: 

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

A fájlnevet, például a bővítmény részei a kifejezéseket is létrehozhat. A Blob elérési út karakterláncát kifejezések és minták használatával további információkért lásd: a [tárolási blob kötési hivatkozási](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Kötési kifejezés - eseményindító metaadatok

Sok eseményindítók mellett a hasznos adatforgalmat egy eseményindító (például az üzenetsorban található üzenetet függvény kiváltó tartalmának) által biztosított, adja meg a további metaadatokat értékét. Ezeket az értékeket a C# és F # vagy tulajdonságok bemeneti paraméter használható a `context.bindings` JavaScript objektumban. 

Például az Azure Queue storage eseményindító támogatja a következő tulajdonságokkal:

* QueueTrigger - indítására üzenet tartalmát, ha egy érvényes karakterláncot
* DequeueCount
* expirationTime
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

### <a name="binding-expressions---json-payloads"></a>Kötési kifejezésként - JSON hasznos adat található

Ha egy eseményindító hasznos JSON, hivatkozhat más kötésekben ugyanabban a függvényben, és a funkciókódot konfigurációja tulajdonságát.

Az alábbi példa azt mutatja meg a *function.json* fájl, amely egy blob neve megkapja a JSON-ban webhook függvény: `{"BlobName":"HelloWorld.txt"}`. Egy Blob bemeneti kötése beolvassa a blob, és a HTTP kimeneti kötése értéket ad vissza a blob tartalmát a HTTP-válasz. Figyelje meg, hogy a Blob bemeneti kötése azáltal közvetlenül a blob nevének beolvasása a `BlobName` tulajdonság (`"path": "strings/{BlobName}"`)

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

Ennek a C# és F # működéséhez szükséges egy osztály, amely meghatározza a mezők nem deszerializálhatók, az alábbi példában látható módon:

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

A JavaScript JSON-deszerializálás automatikusan történik.

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

A tulajdonságokat a JSON-adattartalmat némelyike tulajdonságokkal rendelkező objektumok, ha azokat közvetlenül által pontjelöléssel hivatkozhat. Tegyük fel például, hogy a JSON néz ki:

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

Olvassa el a közvetlenül `FileName` , `BlobName.FileName`. A JSON formátumú Íme, mi a `path` tulajdonságot az előző példában az alábbihoz hasonlóan fog kinézni:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

A C# a két osztály kellene:

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

### <a name="binding-expressions---create-guids"></a>Kötési kifejezés - GUID-EK létrehozása

A `{rand-guid}` kötési kifejezés létrehoz egy GUID Azonosítót. A következő blob elérési utat egy `function.json` fájlt hoz létre egy blobot egy nevet, például a *50710cb5-84b9 - 4d 87 – 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>A kötési kifejezésként - jelenlegi időpontnál

A kötési kifejezés `DateTime` feloldása egy olyan `DateTime.UtcNow`. A következő blob elérési utat egy `function.json` fájlt hoz létre egy blobot egy nevet, például a *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Futásidejű kötés

C# és egyéb .NET-nyelveket, használhat egy imperatív kötés mintát, szemben a deklaratív kötések *function.json* és attribútumok. Imperatív kötés akkor hasznos, ha a kötési paraméterekhez kell számítani a Tervező helyett futásidejű időpontban. További tudnivalókért tekintse meg a [C# fejlesztői leírás](functions-dotnet-class-library.md#binding-at-runtime) vagy a [C# parancsfájl fejlesztői leírás](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Function.JSON fájl séma

A *function.json* fájl séma érhető el: [ http://json.schemastore.org/function ](http://json.schemastore.org/function).

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
