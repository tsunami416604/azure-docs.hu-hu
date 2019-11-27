---
title: Azure Functions C# szkriptek fejlesztői referenciája
description: Megtudhatja, hogyan fejlesztheti Azure Functions parancsfájl használatával C# .
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 3b05b0a4a56332cce1068f53a23a7d118a2e6bfc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230422"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# parancsfájl (. CSX) fejlesztői referenciája

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ez a cikk bevezetést mutat be a Azure Functions C# a szkript ( *. CSX*) használatával történő fejlesztéséhez.

Azure Functions támogatja C# és C# parancsfájl-programozási nyelveket támogat. Ha a [Visual Studio Class Library-projektben való használattal C# ](functions-develop-vs.md)kapcsolatos útmutatást keres, tekintse [ C# meg a fejlesztői referenciát](functions-dotnet-class-library.md).

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői útmutatót](functions-reference.md).

## <a name="how-csx-works"></a>A. CSX működése

A C# Azure functions parancsfájl-felülete a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction)-ra épül. Az adatfolyamatok C# metódus argumentumai használatával áramlanak a függvénybe. Az argumentumok neve egy `function.json` fájlban van megadva, és előre definiált nevek vannak az olyan dolgokhoz való hozzáféréshez, mint a Function Logger és a lemondási tokenek.

A *. CSX* formátum lehetővé teszi, hogy kevesebb "szöveggel" írjon be egy C# függvényt, és csak a függvények írására összpontosítsanak. A névtér és az osztály összes elemének becsomagolása helyett csak egy `Run` metódust adjon meg. A szokásos módon adja meg a fájl elején található szerelvény-hivatkozásokat és névtereket.

A függvény alkalmazás *. CSX* fájljait a rendszer lefordítja egy példány inicializálásakor. Ez a fordítási lépés azt jelenti, hogy az olyan dolgok C# , mint a hűtőházi C# indítás, hosszabb időt vehetnek igénybe a parancsfájl-függvények számára a Ez a fordítási lépés azt C# is indokolja, hogy a parancsfájl-függvények C# szerkeszthető legyenek a Azure Portalban, míg az osztály könyvtárai nem.

## <a name="folder-structure"></a>Mappa szerkezete

A C# parancsfájl-projekthez tartozó mappastruktúrát a következőhöz hasonlóan néz ki:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Létezik egy megosztott [Host. JSON](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Mindegyik függvényhez saját kódlap (. CSX) és kötési konfigurációs fájl (function. JSON) tartozik.

A functions futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési kiterjesztések a `extensions.csproj` fájlban vannak definiálva, a `bin` mappában lévő fájlok tényleges fájljaival. Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](./functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

## <a name="binding-to-arguments"></a>Argumentumok kötése

A bemeneti vagy kimeneti adatok egy C# script Function paraméterhez vannak kötve a *function. json* konfigurációs fájl `name` tulajdonságán keresztül. A következő példa egy *function. JSON* fájlt mutat be, és a. CSX fájlt egy üzenetsor által aktivált függvényhez *futtatja* . Az üzenetsor-üzenetet fogadó paraméter neve `myQueueItem`, mert ez a `name` tulajdonság értéke.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

A `#r` utasítást a [cikk későbbi részében](#referencing-external-assemblies)ismertetjük.

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusai

Minden kötés saját támogatott típusokkal rendelkezik; például egy blob trigger használható karakterlánc-paraméterrel, egy POCO paraméterrel, egy `CloudBlockBlob` paraméterrel vagy számos más támogatott típussal. A [blob-kötésekhez tartozó kötési útmutató](functions-bindings-storage-blob.md#trigger---usage) a blob-eseményindítók összes támogatott paraméter-típusát listázza. További információ: [triggerek és kötések](functions-triggers-bindings.md) , valamint az [egyes kötési típusok kötési dokumentációja](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Egyéni osztályok referenciája

Ha egyéni egyszerű CLR-objektum (POCO) osztályt kell használnia, az osztály definícióját is megadhatja ugyanabban a fájlban, vagy egy külön fájlba helyezheti.

Az alábbi példa egy *Run. CSX* példát mutat be, amely egy poco osztály definícióját tartalmazza.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

A POCO osztálynak minden tulajdonsághoz meg kell adni egy leolvasót és egy beállító-állítót.

## <a name="reusing-csx-code"></a>A. CSX kód újrafelhasználása

A *Run. CSX* fájl más *. CSX* fájljaiban definiált osztályokat és metódusokat is használhat. Ehhez a *Run. CSX* fájlban `#load` irányelveket kell használnia. A következő példában egy `MyLogger` nevű naplózási rutin van megosztva a *myLogger. CSX* -ben, és be kell tölteni a *Run. CSX* -be a `#load` direktíva használatával:

Példa a *Run. CSX*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Példa *mylogger. CSX*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

A Shared *. CSX* fájl használata gyakori minta, ha a függvények között a Poco objektum használatával erősen szeretné beírni a függvényeket. A következő egyszerűsített példában a HTTP-trigger és a várólista-trigger egy `Order` nevű POCO-objektumot oszt meg, hogy erősen beírja a megrendelési adatkészletet:

Példa a *Run. CSX* http-triggerre:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Példa a *Run. CSX* for üzenetsor-triggerre:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Példa *Order. CSX*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

Relatív elérési utat is használhat a `#load` direktívával:

* `#load "mylogger.csx"` betölti a Function mappában található fájlt.
* `#load "loadedfiles\mylogger.csx"` betölti a függvény mappájában található fájlt.
* `#load "..\shared\mylogger.csx"` betölti a mappában található fájlt a Function mappával megegyező szinten, közvetlenül a *wwwroot*alatt.

Az `#load` direktíva kizárólag. *cs* fájlokkal rendelkező *. CSX* -fájlokkal működik.

## <a name="binding-to-method-return-value"></a>Kötés a metódus visszatérési értékéhez

A metódus visszatérési értékét egy kimeneti kötéshez használhatja a *function. JSON*fájlban `$return` néven. Példákat az [Eseményindítók és kötések](./functions-bindings-return-value.md)című témakörben talál.

Csak akkor használja a visszaadott értéket, ha egy sikeres függvény végrehajtása mindig visszatérési értéket ad eredményül a kimeneti kötésnek. Ellenkező esetben használja a `ICollector` vagy `IAsyncCollector`, ahogy az a következő szakaszban látható.

## <a name="writing-multiple-output-values"></a>Több kimeneti érték írása

Ha több értéket szeretne írni egy kimeneti kötésbe, vagy ha egy sikeres függvény meghívása nem eredményez semmit a kimeneti kötéshez, használja a [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típust. Ezek a típusok olyan írásvédett gyűjtemények, amelyek a metódus befejeződése után a kimeneti kötésbe íródnak.

Ez a példa több üzenetsor-üzenetet ír ugyanabba a várólistába `ICollector`használatával:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Naplózás

Ha a kimenetét be szeretné jelentkezni a C#folyamatos átviteli naplókba, vegyen fel egy [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot. Javasoljuk, hogy nevezze el `log`. Kerülje a Azure Functions `Console.Write` használatának elkerülését.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> További információ a `TraceWriter`helyett használható újabb naplózási keretrendszerről: [naplók írása a C# functions](functions-monitoring.md#write-logs-in-c-functions) szolgáltatásban a **figyelő Azure functions** cikkben.

## <a name="async"></a>Aszinkron

A függvények [aszinkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)végrehajtásához használja a `async` kulcsszót, és egy `Task` objektumot ad vissza.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Aszinkron függvények nem használhatnak `out` paramétereket. Kimeneti kötések esetén használja helyette a [függvény visszatérési értékét](#binding-to-method-return-value) vagy egy [gyűjtő objektumot](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Visszavonási tokenek

A függvények elfogadják a [CancellationToken](/dotnet/api/system.threading.cancellationtoken) paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a kódot, ha a függvény hamarosan leáll. Ezzel az értesítéssel meggyőződhet arról, hogy a függvény váratlanul leáll olyan módon, amely inkonzisztens állapotban hagyja az adatvesztést.

Az alábbi példa bemutatja, hogyan ellenőrizhető a közelgő függvények leállítása.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Névterek importálása

Ha névtereket kell importálnia, a szokásos módon megteheti a `using` záradékkal.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

A rendszer automatikusan importálja a következő névtereket, ezért nem kötelező:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Külső szerelvények hivatkozása

A keretrendszer-szerelvények esetében adja hozzá a hivatkozásokat a `#r "AssemblyName"` direktíva segítségével.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

A Azure Functions üzemeltetési környezet automatikusan hozzáadja a következő szerelvényeket:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

A következő szerelvényeket egyszerű névvel lehet hivatkozni (például `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Egyéni szerelvények referenciája

Ha egyéni szerelvényre szeretne hivatkozni, használhatja a *megosztott* szerelvényt vagy egy *privát* szerelvényt:

* A megosztott szerelvények a Function alkalmazás összes funkciója között megoszthatók. Egy egyéni szerelvényre való hivatkozáshoz töltse fel a szerelvényt egy `bin` nevű mappába a [Function App Root mappában](functions-reference.md#folder-structure) (wwwroot).

* A privát szerelvények egy adott függvény környezetének részét képezik, és támogatják a különböző verziók egymáshoz való betöltését. A magánhálózati szerelvényeket fel kell tölteni egy `bin` mappában a függvény könyvtárában. Hivatkozzon a szerelvényekre a fájlnév használatával, például `#r "MyAssembly.dll"`.

A fájlok a függvény mappájába való feltöltésével kapcsolatos további információkért lásd a [csomagkezelő](#using-nuget-packages)című szakaszt.

### <a name="watched-directories"></a>Figyelt könyvtárak

A függvény parancsfájlját tartalmazó könyvtár automatikusan figyeli a szerelvények módosításait. Ha más címtárakban szeretné megtekinteni a szerelvények változásait, adja hozzá őket a [Host. json](functions-host-json.md)`watchDirectories` listájához.

## <a name="using-nuget-packages"></a>NuGet-csomagok használata
Ha a NuGet-csomagokat egy 2. x C# függvényben szeretné használni, töltsön fel egy *function. Proj* fájlt a függvény mappájába a Function alkalmazás fájlrendszerében. Íme egy példa *function. Proj* fájl, amely a *Microsoft. ProjectOxford. Face* Version *1.1.0*-re mutató hivatkozást tartalmaz:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Ha egyéni NuGet-hírcsatornát szeretne használni, a függvényalkalmazás gyökérkönyvtárában található *NuGet. config* fájlban lévő hírcsatornát kell megadnia. További információ: a [NuGet viselkedésének konfigurálása](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> Az 1. x C# függvények esetében a NuGet-csomagokat a *function. Proj* fájl helyett egy *Project. JSON* fájllal hivatkozunk.

1\. x függvények esetén Ehelyett használjon *Project. JSON* fájlt. Példa a *Project. JSON* fájlra:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Function. Proj fájl használata

1. Nyissa meg a függvényt a Azure Portalban. A naplók lap megjeleníti a csomag telepítési kimenetét.
2. A *function. Proj* fájl feltöltéséhez használja a Azure functions fejlesztői útmutatójának a [Function app Files frissítése](functions-reference.md#fileupdate) című témakörében ismertetett módszerek egyikét.
3. A *function. Proj* fájl feltöltése után a következő példához hasonló kimenet jelenik meg a függvény streaming naplójában:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Környezeti változók

Környezeti változó vagy Alkalmazásbeállítások értékének beszerzéséhez használja a `System.Environment.GetEnvironmentVariable`, ahogy az a következő példában látható:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Kötés futásidőben

A C# -ben és más .net-nyelveken egy [kötelező](https://en.wikipedia.org/wiki/Imperative_programming) kötési mintát is használhat a *function. JSON*fájlban található [*deklaratív*](https://en.wikipedia.org/wiki/Declarative_programming) kötések helyett. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. Ezzel a mintával a függvény kódjában a támogatott bemeneti és kimeneti kötésekhez köthető.

A következő módon adjon meg egy kötelező kötést:

- **Ne tartalmazzon bejegyzést** a *function. JSON* fájlban a kívánt kényszerített kötésekhez.
- Adjon át egy bemeneti paramétert [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Az adatkötés C# végrehajtásához használja a következő mintát.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` a kötést definiáló .NET-attribútum, a `T` pedig az adott kötési típus által támogatott bemeneti vagy kimeneti típus. `T` nem lehet `out` paraméter típusa (például `out JObject`). Például a Mobile Apps tábla kimeneti kötése [hat kimeneti típust](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)támogat, de csak a [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) használhatja `T`hoz.

### <a name="single-attribute-example"></a>Példa egyetlen attribútumra

A következő mintakód létrehoz egy [tárolási blob kimeneti kötést](functions-bindings-storage-blob.md#output) a futási időben definiált blob elérési úttal, majd karakterláncot ír a blobba.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

A [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) meghatározza a [Storage blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötését, és a [TextWriter](/dotnet/api/system.io.textwriter) egy támogatott kimeneti kötési típus.

### <a name="multiple-attribute-example"></a>Több attribútum – példa

Az előző példában beolvassa a Function alkalmazás fő Storage-fiókjának (`AzureWebJobsStorage`) az alkalmazás beállítását. A Storage-fiókhoz a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) hozzáadásával és az attribútum tömb `BindAsync<T>()`ba való átadásával adhat meg egyéni alkalmazás-beállítást. `Binder` paraméter használata nem `IBinder`.  Például:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

A következő táblázat felsorolja az egyes kötési típusok .NET-attribútumait, valamint azokat a csomagokat, amelyekben definiálva vannak.

> [!div class="mx-codeBreakAll"]
> | Kötés | Attribútum | Hivatkozás hozzáadása |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Tárolási üzenetsor | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Storage-tábla | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítók és kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ a Azure Functions ajánlott eljárásairól](functions-best-practices.md)
