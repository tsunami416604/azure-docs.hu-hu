---
title: "Az Azure parancsfájl fejlesztői leírás funkciók C#"
description: "Megtudhatja, hogyan fejleszthet Azure Functions C#-parancsfájl használatával."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: b6b18f79b0ef50c30335218ef45ba6ed932cb586
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Az Azure parancsfájl (.csx) fejlesztői leírás funkciók C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ebben a cikkben megismerkedhet az Azure Functions fejlesztése C#-parancsfájl használatával (*.csx*).

Az Azure Functions támogatja a C# és C# programozási nyelvek parancsfájl. Ha a keresett útmutatást [C# használatát a Visual Studio hordozhatóosztálytár-projektjének](functions-develop-vs.md), lásd: [C# fejlesztői leírás](functions-dotnet-class-library.md).

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői útmutatója](functions-reference.md).

## <a name="how-csx-works"></a>.Csx működése

Az Azure Functions C# parancsfájl tapasztalatok alapján a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Az adatáramlás a C# függvénynek keresztül metódus argumentumait. Argumentum neve meg van határozva a egy `function.json` fájlt, és nincs előre meghatározott dolgot eléréséhez nevét, például a függvény naplózó és a megszakítási jogkivonatokat.

A *.csx* formátum lehetővé teszi a kisebb "bolierplate" írása, és csak egy C# függvény írása összpontosítanak. Ahelyett, hogy a névtér és osztály alkalmazásburkoló mindent, csak adja meg a `Run` metódust. Például bármely szerelvényhivatkozások és a névterek, a fájl elején a szokásos módon.

Egy függvény app *.csx* fájlok fordításának példányának inicializálásakor. A fordítás lépés azt jelenti, hogy a mint hidegindítás hosszabb időt vehet igénybe a C# parancsfájl funkciók C# osztálykönyvtárakhoz képest. A fordítás lépés is, miért C# parancsfájl funkciók szerkeszthető az Azure portálon, míg a C# osztálykönyvtárakhoz nem.

## <a name="binding-to-arguments"></a>Argumentum kötése

Bemeneti vagy kimeneti adatokat egy C# parancsfájl függvényparaméter keresztül van kötve a `name` tulajdonságot a *function.json* konfigurációs fájlt. Az alábbi példa mutatja egy *function.json* fájl és *run.csx* a várólista-eseményindítóval aktivált függvény fájlt. A paraméter, amely adatokat fogad az üzenetsorban lévő üzenetet nevű `myQueueItem` értéke, mert a `name` tulajdonság.

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

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

A `#r` utasítás kifejtett [Ez a cikk későbbi](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusok

Minden kötésnek rendelkezik saját támogatott típusok; például egy karakterlánc-paramétert, a POCO paramétert, a blob eseményindító használható egy `CloudBlockBlob` paraméter, sem annak több más támogatott típusok. A [kötés áttekintésével foglalkozó cikkben blob kötések](functions-bindings-storage-blob.md#trigger---usage) blob eseményindítók paramétereinek felsorolja az összes támogatott. További információkért lásd: [eseményindítók és kötések](functions-triggers-bindings.md) és a [kötési hivatkozási docs az egyes kötés](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Egyéni osztályok hivatkozik

Ha szüksége egy egyszerű régi CLR objektum (POCO) egyéni osztály, közé tartoznak az osztálydefinícióban belül ugyanazt a fájlt, vagy külön fájlban elhelyezi.

Az alábbi példa mutatja egy *run.csx* példa, amely egy POCO osztály definícióját tartalmazza.

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

Egy POCO osztály hozzá elérő és beállító mindegyik tulajdonsághoz megadott kell rendelkeznie.

## <a name="reusing-csx-code"></a>Újbóli felhasználása .csx kódot

Osztályok és az egyéb megadott módszerek *.csx* -fájlok a *run.csx* fájlt. Ehhez használja `#load` irányelvek a *run.csx* fájlt. A következő példában egy naplózási rutin nevű `MyLogger` a megosztott *myLogger.csx* és töltve *run.csx* használatával a `#load` irányelv:

Példa *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Példa *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

Egy megosztott használatával *.csx* fájl egy közös mintát akkor, ha a használni kívánt erősen thet adatok között funkciók által átadott egy POCO objektum használatával. A következő egyszerűsített példában egy HTTP-eseményindítóval és várólista eseményindító megosztani egy POCO nevű objektum `Order` erősen be a sorrendben adatokat:

Példa *run.csx* HTTP-eseményindító:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

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

Példa *run.csx* várólista eseményindító:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

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

Relatív elérési utat is használhatja a `#load` irányelv:

* `#load "mylogger.csx"`a függvény mappában található fájl betöltése.
* `#load "loadedfiles\mylogger.csx"`a függvény mappában mappában található fájl betöltése.
* `#load "..\shared\mylogger.csx"`Ez azt jelenti, hogy egy függvény mappát ugyanazon a szinten mappában található fájl betöltése közvetlenül alatt *wwwroot*.

A `#load` direktíva csak működik *.csx* fájlok, nem pedig az *.cs* fájlokat.

## <a name="binding-to-method-return-value"></a>Kötése metódus visszatérési értéke

Használhatja a metódus visszatérési érték egy kimeneti kötés neve `$return` a *function.json*:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "$return",
    "queueName": "outqueue",
    "connection": "MyStorageConnectionString",
}
```

```csharp
public static string Run(string input, TraceWriter log)
{
    return input;
}
```

## <a name="writing-multiple-output-values"></a>Több kimeneti értékeinek írása

Több érték írni egy kimeneti kötése, használja a [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típusok. Ezek a típusok csak írható gyűjtemények metódus befejezésekor a kimeneti kötés írt.

Ebben a példában több üzenetek ír be várólista használatával `ICollector`:

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>Naplózás

A folyamatos átviteli naplók, a C# kimeneti jelentkeznek, tartalmazza a típusú argumentumot `TraceWriter`. Ajánlott nevezze el `log`. Kerülje a `Console.Write` az Azure Functions. 

`TraceWriter`a van definiálva a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). A naplózási szint a `TraceWriter` konfigurálható [host.json](functions-host-json.md).

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Egy újabb naplózási keretrendszert, melyekkel ahelyett, hogy információt `TraceWriter`, lásd: [írási naplói a C# funkciók](functions-monitoring.md#write-logs-in-c-functions) a a **figyelése az Azure Functions** cikk.

## <a name="async"></a>Async

Ahhoz, hogy egy függvény aszinkron, használja a `async` kulcsszót, és térjen vissza a `Task` objektum.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

## <a name="cancellation-tokens"></a>Megszakítási jogkivonatok

Egyes műveletek szabályos leállítást igényel. Mindig is írhat kódot, amelyet kezelni tud összeomló ajánlott, azokban az esetekben, ahol szeretné kezelni leállítási kérelmeket, adja meg a [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) megadott argumentum.  A `CancellationToken` valósul meg, hogy jelezze, hogy a gazdagép leállítása elindul.

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    string blobName,
    Stream blobInput,
    Stream blobOutput,
    CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Névterek importálása

Ha a névterek importálni kell, akkor teheti ezt a szokásos, a a `using` záradékban.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

A következő névterek a rendszer automatikusan importálja, és ezért nem kötelező:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Külső szerelvények hivatkozik

A keretrendszer szerelvényei közé, hivatkozások hozzáadása használatával a `#r "AssemblyName"` direktívát.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

A következő szerelvények a rendszer automatikusan hozzáadja a üzemeltetési környezet az Azure Functions:

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

A következő szerelvények készletekre lehet hivatkozni, egyszerű-név szerint (például `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Egyéni szerelvényeket hivatkozik

Egy egyéni szerelvény hivatkozni, választhat egy *megosztott* szerelvény vagy egy *titkos* szerelvény:
- Közös szerelvényekre összes funkciók, a függvény alkalmazások között vannak megosztva. Egy egyéni szerelvény hivatkozni, töltse fel a szerelvény, a függvény alkalmazáshoz, többek között a egy `bin` mappa függvény alkalmazás gyökérkönyvtárában. 
- Titkos szerelvények egy adott funkció környezet részét képezik, és a tesztcélú különböző verzióit támogatja. Személyes szerelvények fel kell tölteni a egy `bin` mappa, a függvény könyvtárban. A szerelvényeket a fájl nevét, például a használatával hivatkozik `#r "MyAssembly.dll"`. 

A fájlok feltöltéséről a függvény mappába információkért lásd: a szakasz a [felügyeleti csomag](#using-nuget-packages).

### <a name="watched-directories"></a>Figyelt könyvtárak

A függvény parancsfájlt tartalmazó könyvtár automatikusan figyelt szerelvények változásait. Tekintse meg a szerelvény változásokat más címtárakban, vegye fel őket a `watchDirectories` listájában [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>NuGet-csomagok használata

A C# függvényben a NuGet-csomagok használatához feltöltése egy *project.json* fájlt a függvény app fájlrendszer a függvény mappájába. Példa *project.json* fájlt, amely hozzáad egy hivatkozást Microsoft.ProjectOxford.Face 1.1.0-ás verzió:

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

Az Azure-ban működik 1.x, csak a .NET keretrendszer 4.6-os támogatott, ezért győződjön meg arról, hogy a *project.json* fájl határozza meg `net46` itt látható módon.

Feltöltése egy *project.json* fájl, a futtatókörnyezet lekérdezi a csomagokat, és automatikusan hozzáadja a csomag szerelvények hivatkozik. Nem kell hozzáadnia `#r "AssemblyName"` irányelvek. A NuGet-csomagok; definiált típusok használata a szükséges hozzá `using` utasítást, hogy a *run.csx* fájlt. 

A Functions futtatókörnyezete NuGet visszaállítási összehasonlításával működik `project.json` és `project.lock.json`. Ha a fájlok dátum és idő bélyegzők **nem** egyezik, a NuGet-visszaállítás fut, és a NuGet-letöltések csomagok frissítése. Azonban ha a fájlok dátum és idő bélyegzők **tegye** egyezik, NuGet, a visszaállítás nem hajtható végre. Ezért `project.lock.json` nem kell telepíteni, mivel azt eredményezi, hogy a NuGet csomag visszaállítási kihagyását. A zárolási fájl telepítése elkerüléséhez adja hozzá a `project.lock.json` való a `.gitignore` fájlt.

A hírcsatorna egyéni NuGet használatához adja meg az adatcsatorna egy *Nuget.Config* függvény App fájl. További információkért lásd: [konfigurálása NuGet viselkedés](/nuget/consume-packages/configuring-nuget-behavior).

### <a name="using-a-projectjson-file"></a>Project.json fájl használatával

1. Nyissa meg a függvény az Azure portálon. A naplófájlok lap megjeleníti a csomag telepítési kimenetet.
2. Project.json-fájl feltöltése, használja az ismertetett módszerek valamelyikét a [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate) Azure Functions fejlesztői témakör.
3. Miután a *project.json* fájl feltöltése úgy, hogy a függvény az alábbi példához hasonló kimenetet a streaming napló:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Környezeti változók

Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `System.Environment.GetEnvironmentVariable`, ahogy az az alábbi példakód:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Futásidejű kötés

C# és egyéb .NET nyelven, használhat egy [imperatív](https://en.wikipedia.org/wiki/Imperative_programming) kötés mintát, nem pedig a [ *deklaratív* ](https://en.wikipedia.org/wiki/Declarative_programming) kötések *function.json*. Imperatív kötés akkor hasznos, ha a kötési paraméterekhez kell számítani a Tervező helyett futásidejű időpontban. Az ebben a mintában köthető támogatott bemeneti és kimeneti kötések az azonnali a függvény kódban.

Adja meg a következő kötés dolgozik:

- **Ne** egy bejegyzést a *function.json* számára a kívánt imperatív kötések.
- A bemeneti paraméter fázis [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- A következő C# minta segítségével hajtható végre a adatkötés.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`a .NET-attribútum, amely meghatározza a kötés és `T` bemeneti vagy kimeneti típus a kötési típus által támogatott. `T`nem lehet egy `out` típusú paraméter (például `out JObject`). Például a Mobile Apps tábla kimeneti kötése támogatja [hat kimeneti típusok](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), de csak használható [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)a `T`.

### <a name="single-attribute-example"></a>Egyetlen attribútum – példa

Az alábbi példakód létrehoz egy [tárolási blob kimeneti kötése](functions-bindings-storage-blob.md#output) blobbal együtt futási időben megadott elérési út ezután ír egy karakterláncot a blobra mutató.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) határozza meg a [tárolási blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötelező, és [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) támogatott kimeneti kötési típus.

### <a name="multiple-attribute-example"></a>Több attribútum – példa

Az előző példában lekérdezi az alkalmazás beállítása a függvény alkalmazás fő tárolási fiók kapcsolati karakterlánc (amely `AzureWebJobsStorage`). Megadhat egy egyéni alkalmazás beállítást, a tárfiók hozzáadásával a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) , és átadja a attribútum tömb be `BindAsync<T>()`. Használja a `Binder` paraméter nem `IBinder`.  Példa:

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

Az alábbi táblázat a .NET-attribútumok minden kötési típus és a csomagok, amelyekben vannak definiálva.

> [!div class="mx-codeBreakAll"]
| Kötelező | Attribútum | Hivatkozás hozzáadása |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Tárolási üzenetsor | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage-blobba | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tárolási tábla | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információk a gyakorlati tanácsok az Azure Functions](functions-best-practices.md)
