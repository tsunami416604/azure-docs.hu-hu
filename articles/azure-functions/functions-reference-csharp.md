---
title: Az Azure Functions C# parancsfájlfejlesztői hivatkozás
description: Ismerje meg, hogyan fejlesztheti az Azure Functions c# parancsfájl használatával.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 76af1f51c83e9554a51e6c17266fac739e6bd6b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276815"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# script (.csx) fejlesztői hivatkozás

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ez a cikk az Azure Functions C# parancsfájl (*.csx)* használatával történő fejlesztésének bemutatkozása.

Az Azure Functions támogatja a C# és a C# parancsfájl programozási nyelvek. Ha útmutatást szeretne a [C# visual studio-i osztálykönyvtár-projektben való használatával](functions-develop-vs.md)kapcsolatos útmutatásért, olvassa el a [C# fejlesztői útmutatót.](functions-dotnet-class-library.md)

Ez a cikk feltételezi, hogy már elolvasta az [Azure Functions fejlesztői útmutatóját.](functions-reference.md)

## <a name="how-csx-works"></a>A .csx működése

Az Azure Functions C# parancsfájl-élménye az [Azure WebJobs SDK-n](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction)alapul. Az adatok a C# függvénybe kerülnek metódusargumentumokon keresztül. Az argumentumnevek egy `function.json` fájlban vannak megadva, és előre definiált nevek vannak a függvénynaplózó és a törlési jogkivonatok eléréséhez.

A *.csx* formátum lehetővé teszi, hogy írjon kevesebb "sablonos", és összpontosítani írásban csak egy C # funkciót. Ahelyett, hogy mindent egy névtérbe és `Run` osztályba csomagolna, csak definiáljon egy módszert. A szokásos módon a fájl elejére sorasztja a kódösszeállítás-hivatkozásokat és névtereket.

A függvényalkalmazás *.csx* fájljai fordítása egy példány inicializálásakor történik. Ez a fordítási lépés azt jelenti, hogy a hidegindítás hosszabb időt vehet igénybe a C# parancsfájlfüggvények számára a C# osztálykönyvtárakhoz képest. Ez a fordítási lépés is, ezért C# parancsfájl függvények szerkeszthetők az Azure Portalon, míg a C# osztálytárak könyvtárak nem.

## <a name="folder-structure"></a>Mappastruktúra

A C# parancsfájl-projekt mappastruktúrája a következőképpen néz ki:

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

Van egy megosztott [host.json](functions-host-json.md) fájl, amely a függvényalkalmazás konfigurálásához használható. Minden függvény saját kódfájllal (.csx) és kötési konfigurációs fájllal (function.json) rendelkezik.

A Functions futásidejű [2.x-es és újabb verzióiban](functions-versions.md) szükséges `extensions.csproj` kötési kiterjesztések a fájlban vannak definiálva, a mappában lévő `bin` tényleges könyvtárfájlokkal együtt. Helyi fejlesztés esetén kötelező kiterjesztéseket kell [regisztrálnia.](./functions-bindings-register.md#extension-bundles) Amikor funkciókat fejleszt az Azure Portalon, ez a regisztráció az Ön számára történik.

## <a name="binding-to-arguments"></a>Az argumentumokra kötelező

A bemeneti vagy kimeneti adatok a `name` *function.json konfigurációs* fájlban lévő tulajdonságon keresztül c# parancsfájl-függvény paraméterhez vannak kötve. A következő példa egy *function.json* fájlt és *run.csx* fájlt mutat be egy várólista által aktivált függvényhez. A várólistaüzenetből adatokat fogadó `myQueueItem` paraméter neve, mert `name` ez a tulajdonság értéke.

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

A `#r` kijelentést a [cikk későbbi részében ismertetjük.](#referencing-external-assemblies)

## <a name="supported-types-for-bindings"></a>Támogatott típusú kötések

Minden kötésnek saját támogatott típusa van; például egy blob eseményindító használható egy karakterlánc-paraméter, egy `CloudBlockBlob` POCO paraméter, egy paraméter, vagy bármely más támogatott típusok. A [blob-kötések kötési referenciacikke](functions-bindings-storage-blob-trigger.md#usage) felsorolja a blob-eseményindítók összes támogatott paramétertípusát. További információ: [Eseményindítók és kötések,](functions-triggers-bindings.md) valamint az [egyes kötéstípusokkötési hivatkozási dokumentumai.](functions-triggers-bindings.md#next-steps)

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Hivatkozás egyéni osztályokra

Ha egyéni Egyszerű, régi CLR-objektum (POCO) osztályt kell használnia, az osztálydefiníciót ugyanabban a fájlban is felveheti, vagy külön fájlba helyezheti.

A következő példa egy *run.csx* példát mutat be, amely POCO osztálydefiníciót tartalmaz.

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

A POCO-osztálynak minden tulajdonsághoz meg kell határoznia egy gettert és beállítót.

## <a name="reusing-csx-code"></a>.csx kód újrafelhasználása

A *run.csx* fájlban más *.csx* fájlokban definiált osztályokat és metódusokat is használhat. Ehhez használjon `#load` utasításokat a *run.csx* fájlban. A következő példában egy `MyLogger` megnevezett naplózási rutin van megosztva a *myLogger.csx* fájlban, és a *run.csx* fájlba töltődik be az `#load` irányelv használatával:

Példa *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Példa *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

A megosztott *.csx* fájl használata gyakori minta, ha a függvények között POCO-objektum mal átadott adatokat szeretné erősen beírni. A következő egyszerűsített példában egy HTTP-eseményindító és várólista-eseményindító megoszt egy POCO-objektumot, amelynek neve `Order` a rendelési adatok erős beírásához:

Példa a HTTP-eseményindítóhoz: *run.csx* for HTTP trigger:

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

Példa a várólista-eseményindítóra: *run.csx*

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

Példa *order.csx*:

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

Az `#load` irányelvhez relatív elérési utat is használhat:

* `#load "mylogger.csx"`betölt egy fájlt, amely a függvénymappában található.
* `#load "loadedfiles\mylogger.csx"`betölt egy fájlt, amely a függvénymappa egyik mappájában található.
* `#load "..\shared\mylogger.csx"`betölt egy fájlt, amely egy mappában található, ugyanazon a szinten, mint a függvénymappa, azaz közvetlenül a *wwwroot*alatt.

Az `#load` irányelv csak *.csx* fájlokkal működik, *a .cs* fájlokkal nem.

## <a name="binding-to-method-return-value"></a>Kötés a metódus visszatérési értékéhez

A `$return` *függvény.json*név használatával metódusvisszatérési értéket használhat egy kimeneti kötéshez. Példák: [Eseményindítók és kötések](./functions-bindings-return-value.md).

Csak akkor használja a visszatérési értéket, ha a sikeres függvényvégrehajtás mindig a kimeneti kötésnek átadandó visszatérési értéket eredményez. Ellenkező esetben `ICollector` `IAsyncCollector`használja a vagyat, ahogy az a következő szakaszban látható.

## <a name="writing-multiple-output-values"></a>Több kimeneti érték írása

Ha több értéket szeretne írni egy kimeneti kötésbe, vagy ha egy sikeres függvénymeghívás [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nem [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) eredményez semmit a kimeneti kötésnek, használja a vagy típusokat. Ezek a típusok csak írható gyűjtemények, amelyek a metódus befejezésekor a kimeneti kötésbe íródnak.

Ez a példa több várólista-üzenetet ír ugyanabba a várólistába a következő használatával: `ICollector`

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Naplózás

A c#-ban lévő streamelési naplók kimenetének naplózásához adjon meg egy [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot. Javasoljuk, hogy nevezze `log`el. Ne `Console.Write` használja az Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Az Azure Functions `TraceWriter` **figyelése** cikkben a Naplók [írása C# függvényekben](functions-monitoring.md#write-logs-in-c-functions) című témakörben olvashat.

## <a name="async"></a>Aszinkron

Ahhoz, hogy egy függvény [aszinkron,](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)használja `async` `Task` a kulcsszót, és vissza egy objektumot.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Az aszinkron `out` függvényekben nem használhatók paraméterek. Kimeneti kötések esetén használja a [függvény visszatérési értékét](#binding-to-method-return-value) vagy egy [gyűjtőobjektumot.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Törlési tokenek

Egy függvény elfogadhatja [az CancellationToken](/dotnet/api/system.threading.cancellationtoken) paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a kódot, amikor a függvény meg fog szűnni. Ezzel az értesítéssel biztosíthatja, hogy a függvény ne fejeződjön be váratlanul úgy, hogy az adatok inkonzisztens állapotban maradnak.

A következő példa bemutatja, hogyan ellenőrizheti a közelgő függvények leállítását.

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

Ha névtereket kell importálnia, ezt a `using` szokásos módon teheti meg a záradékkal.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

A következő névterek automatikusan importálásra kerülnek, ezért nem kötelezőek:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Hivatkozás külső összeállításokra

A keretszerelvények esetében az `#r "AssemblyName"` irányelv használatával adjon hozzá hivatkozásokat.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Az Azure Functions üzemeltetési környezete automatikusan hozzáadja a következő összeállításokat:

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

A következő szerelvények egyszerű névvel hivatkozhatnak (például): `#r "AssemblyName"`

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Egyéni összeállításokra való hivatkozás

Egyéni kódösszeállításra való hivatkozáshoz *használhat megosztott* vagy *magánszerelvényt:*

* A megosztott szerelvények meg vannak osztva a függvényalkalmazás on belüli összes függvény között. Egyéni szerelvényre való hivatkozáshoz töltse fel `bin` a szerelvényt a [függvényalkalmazás gyökérmappájában](functions-reference.md#folder-structure) (wwwroot) elnevezett mappába.

* A privát szerelvények egy adott függvény környezetének részét képezik, és támogatják a különböző verziók oldalsó betöltését. A privát szerelvényeket a függvénykönyvtár `bin` egy mappájába kell feltölteni. Hivatkozzon a szerelvényekre a fájlnév `#r "MyAssembly.dll"`használatával, például .

A fájlok függvénymappába való feltöltésével kapcsolatos további tudnivalókért tekintse meg a [csomagkezelésről](#using-nuget-packages)szóló szakaszt.

### <a name="watched-directories"></a>Figyelt könyvtárak

A függvényparancsfájl-fájlt tartalmazó könyvtárat a rendszer automatikusan figyeli az összeállítások módosításai miatt. Ha figyelni szeretné a többi könyvtár összeállítási `watchDirectories` változásait, adja hozzá őket a [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>NuGet csomagok használata
Ha 2.x és újabb C# függvényben szeretné használni a NuGet csomagokat, töltsön fel egy *function.proj* fájlt a függvényalkalmazás fájlrendszerében lévő függvény mappájába. Íme egy példa *function.proj* fájl, amely hozzáad egy hivatkozást a *Microsoft.ProjectOxford.Face* *1.1.0*verzió :

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

Egyéni NuGet-hírcsatorna használatához adja meg a hírcsatornát egy *Nuget.Config* fájlban a Függvényalkalmazás gyökérében. További információt a [NuGet viselkedésének konfigurálása](/nuget/consume-packages/configuring-nuget-behavior)című témakörben talál.

> [!NOTE]
> Az 1.x C# függvényekben a NuGet csomagokra egy *project.json* fájl hivatkozik *a function.proj* fájl helyett.

1.x függvények esetén használjon *project.json* fájlt. Íme egy példa *project.json* fájl:

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

### <a name="using-a-functionproj-file"></a>Function.proj fájl használata

1. Nyissa meg a függvényt az Azure Portalon. A naplók lapon megjelenik a csomag telepítési kimenete.
2. *Egy function.proj* fájl feltöltéséhez használja az Azure Functions fejlesztői referenciatémakörben a [Függvényalkalmazás-fájlok frissítése](functions-reference.md#fileupdate) című témakörben ismertetett módszerek egyikét.
3. A *function.proj* fájl feltöltése után a következő példához hasonló kimenet jelenik meg a függvény streamelési naplójában:

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

Környezeti változó vagy alkalmazásbeállítási érték `System.Environment.GetEnvironmentVariable`lekérése, használja a használatát, ahogy az a következő kódpéldában látható:

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

C# és más .NET nyelveken a *function.json*ban található [*deklaratív*](https://en.wikipedia.org/wiki/Declarative_programming) kötésekkel szemben [egy imperatív](https://en.wikipedia.org/wiki/Imperative_programming) kötést is használhat. A minós kötés akkor hasznos, ha a kötési paramétereket futásidőben kell kiszámítani, nem pedig tervezési időben. Ezzel a mintával a függvénykódban menet közben is köthető a támogatott bemeneti és kimeneti kötésekhez.

A következőképpen határozhatja meg a kötelező kötést:

- **Ne** adjon meg egy bejegyzést *function.json* a kívánt imperatív kötések.
- Adja meg a [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)bemeneti paramétert vagy a lehetőséget.
- Az adatkötés végrehajtásához használja a következő C# mintát.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`A .NET attribútum, amely meghatározza `T` a kötést, és egy bemeneti vagy kimeneti típus, amelyet az adott kötéstípus támogat. `T`nem lehet `out` paramétertípus (például `out JObject`). A Mobile Apps tábla kimeneti kötése például [hat kimeneti típust](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)támogat, de csak [az ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) a. `T`

### <a name="single-attribute-example"></a>Példa egyetlen attribútumra

A következő példakód létrehoz egy [Storage blob kimeneti kötést](functions-bindings-storage-blob-output.md) a blob elérési útját, amely futásidőben van definiálva, majd egy karakterláncot ír a blobba.

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

[A BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiálja a [Storage blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötést, és a [TextWriter](/dotnet/api/system.io.textwriter) egy támogatott kimeneti kötéstípus.

### <a name="multiple-attribute-example"></a>Példa több attribútumra

Az előző példa lekéri az alkalmazás beállítását a függvényalkalmazás `AzureWebJobsStorage`fő storage-fiók kapcsolati karakterláncához (amely). A [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) attribútum hozzáadásával és az attribútumtömb nek való átadásával egyéni `BindAsync<T>()`alkalmazásbeállítást adhat meg a StorageAccountAttribute attribútumhozzáadásával. Ne `Binder` a `IBinder`paramétert használja.  Példa:

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

Az alábbi táblázat az egyes kötéstípusok .NET attribútumait és azokat a csomagokat sorolja fel, amelyekben definiálva vannak.

> [!div class="mx-codeBreakAll"]
> | Kötés | Attribútum | Hivatkozás hozzáadása |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Tárolási üzenetsor | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tárolótábla | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítókról és kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ az Azure Functions bevált módszereiről](functions-best-practices.md)
