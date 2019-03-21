---
title: Azure Functions C#-parancsfájl fejlesztői referencia
description: Megismerheti, hogyan hozhat létre az Azure Functions C#-szkript használatával.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: eda6f7b8ec61f2c3472b00c76467c1379bc2ff1b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082109"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C#-szkript (.csx) fejlesztői referencia

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ez a cikk bevezetést fejlesztése az Azure Functions C#-szkript használatával (*.csx*).

Az Azure Functions C# és a C#-szkript programozási nyelveket támogatja. Ha a keresett útmutatást [egy Visual Studio hordozhatóosztálytár-projektjének a C# használatával](functions-develop-vs.md), lásd: [C# – fejlesztői referencia](functions-dotnet-class-library.md).

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői útmutató](functions-reference.md).

## <a name="how-csx-works"></a>.Csx működése

Az Azure Functions szolgáltatáshoz C# szkriptet tapasztalatok alapján a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Az adatáramlás a C#-függvény segítségével módszert argumentumok való. Argument neve meg van határozva a egy `function.json` fájlt, és ott vannak az előre meghatározott nevek eléréséhez szükséges dolgokat, mint a függvény naplózó és a megszakítási tokeneket.

A *.csx* formátum lehetővé teszi, hogy kevesebb "bolierplate" írása, és csak egy C#-függvény írására összpontosíthat. Helyett egy névterét és osztályának az alkalmazásburkoló mindent, csak adja meg egy `Run` metódust. Többek között a szokásos módon bármely összeállítási referenciát és a névterek, a fájl elején.

Egy függvényalkalmazás *.csx* fájlok összeállítása során egy példány van inicializálva. A fordítási lépés azt jelenti, például az hidegindítási hosszabb időt vehet igénybe a C# szkriptet Functions C#-osztálykódtárakat képest. Miért C#-szkript függvények szerkeszthető az Azure Portalon, míg C#-osztálykódtárakat nem a fordítási lépés is.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

A mappastruktúra a C#-szkript projekt a következőhöz hasonlóan néz ki:

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

Van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény saját kódfájl (.csx) és a kötési konfigurációs fájl (function.json) rendelkezik.

A kötési bővítményeket szükséges [verzió 2.x](functions-versions.md) a Functions runtime vannak meghatározva a `extensions.csproj` fájlt, a tényleges függvénytárfájlok a `bin` mappát. Ha helyileg fejlesztésével, akkor meg kell [regisztrálja a kötési bővítményeket](./functions-bindings-register.md#local-development-azure-functions-core-tools). Amikor fejlesztéséről az Azure Portalon, a regisztrációt, készen áll.

## <a name="binding-to-arguments"></a>Argumentumok kötést

A C# szkriptet függvényparaméter keresztül van kötve bemeneti vagy kimeneti adatok a `name` tulajdonságot a *function.json* konfigurációs fájlt. A következő példa bemutatja egy *function.json* fájl és *run.csx* -üzenetsor által aktivált függvény fájlt. A paraméter, amely adatokat fogad az üzenetsorban található üzenet nevű `myQueueItem` értékét, mert a `name` tulajdonság.

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

A `#r` utasítás kifejtett [a cikk későbbi részében](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Kötések támogatott típusai

Minden egyes kötés rendelkezik a saját támogatott típusok; például egy karakterlánc-paramétert, egy POCO paraméter blob eseményindító használható egy `CloudBlockBlob` paraméter, vagy számos egyéb bármelyik támogatott típusokat. A [kötés áttekintésével foglalkozó cikkben blobkötések](functions-bindings-storage-blob.md#trigger---usage) felsorolja az összes támogatott paramétertípusok a blob-eseményindítók. További információkért lásd: [eseményindítók és kötések](functions-triggers-bindings.md) és a [egyes kötési típus kötés referenciadokumentumai](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Hivatkozó egyéni osztályok

Ha szeretné használni egy egyéni egyszerű régi CLR-beli objektum (POCO) osztály, belül ugyanazt a fájlt az osztálydefiníció belefoglalhat vagy helyezni, egy különálló fájlban.

A következő példa bemutatja egy *run.csx* példa, amely tartalmaz egy POCO osztálydefinícióhoz.

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

Egy POCO osztály rendelkeznie kell egy Getter a Setter se definiált minden egyes tulajdonság.

## <a name="reusing-csx-code"></a>.Csx kód újrafelhasználása

Használható osztályok és módszerek más definiált *.csx* található fájlokat a *run.csx* fájlt. Ehhez használja `#load` irányelvek a *run.csx* fájlt. A következő példában egy naplózási rutin nevű `MyLogger` megosztott *myLogger.csx* tölti be és *run.csx* használatával a `#load` irányelv:

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

Egy megosztott használatával *.csx* fájl gyakori minta akkor, ha kifejezetten be az adatok között függvények által átadott egy POCO objektum használatával. Az alábbi egyszerű példában egy HTTP-eseményindító és az üzenetsor eseményindító megosztani egy POCO nevű objektum `Order` erősen be a rendelési adatokat:

Példa *run.csx* HTTP-eseményindító:

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

Példa *run.csx* üzenetsor eseményindító:

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

Használhat egy relatív elérési utat a `#load` irányelv:

* `#load "mylogger.csx"` betölt egy fájlt, a függvény mappában található.
* `#load "loadedfiles\mylogger.csx"` betölt egy fájlt egy mappában, a függvény mappában.
* `#load "..\shared\mylogger.csx"` betölt egy fájlt egy mappában azonos szinten, a függvény mappába, azaz közvetlenül a *wwwroot*.

A `#load` irányelv csak működik *.csx* fájlok, sem a *.cs* fájlokat.

## <a name="binding-to-method-return-value"></a>Kötelező érvényű, a metódus visszatérési értéke

A név használatával egy kimeneti kötést, a metódus visszatérési értéket is használhatja `$return` a *function.json*. Példák: [eseményindítók és kötések](./functions-bindings-return-value.md).

Akkor használja a visszaadott érték, ha mindig egy sikeres függvény végrehajtása a kimeneti kötés átadása visszatérési értéket eredményez. Ellenkező esetben használjon `ICollector` vagy `IAsyncCollector`, a következő szakaszban látható módon.

## <a name="writing-multiple-output-values"></a>Több kimeneti értékeinek írása

Több értéket írni egy kimeneti kötést, vagy ha sikeres függvényhívási nem azt eredményezheti, hogy semmit sem kell átadni a kimeneti kötés használja a [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típusokat. Ezek a típusok gyűjteményei csak írási a metódus befejezésekor a kimeneti kötés írt.

Ebben a példában több üzenetsorbeli üzenetek írja az üzenetsor használatával `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Naplózás

A folyamatos átviteli naplók, a kimeneti bejelentkezni C#, például típusú argumentumot [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Javasoljuk, hogy nevezze el `log`. Kerülje a `Console.Write` az Azure Functions szolgáltatásban.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Egy újabb naplózási keretrendszer helyett használható információ `TraceWriter`, lásd: [írási bejelentkezik a C#-függvények](functions-monitoring.md#write-logs-in-c-functions) a a **figyelése az Azure Functions** cikk.

## <a name="async"></a>Az aszinkron

Egy függvényt [aszinkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), használja a `async` kulcsszót, és lépjen vissza a `Task` objektum.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Nem használhat `out` aszinkron funkciók paramétereket. A kimeneti kötések, használja a [függvény visszatérési értéke](#binding-to-method-return-value) vagy egy [gyűjtő objektum](#writing-multiple-output-values) helyette.

## <a name="cancellation-tokens"></a>Megszakítási tokeneket

Egy függvény elfogadhatja a [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) paraméter, amely lehetővé teszi a kód értesítése, ha a funkció arra készül, hogy állítható le, az operációs rendszer. Használhatja ezt az értesítést, hogy a funkció váratlanul leáll nem úgy, hogy az adatok inkonzisztens állapotban hagyja.

Az alábbi példa bemutatja, hogyan közelgő függvény lezárást kereséséhez.

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

Ha szeretne importálni a névterek, tehát a szokásos, az teheti a `using` záradékban.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
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

## <a name="referencing-external-assemblies"></a>Hivatkozó külső szerelvények

A keretrendszer szerelvényeket, mutató hivatkozásokat tudjon felvenni használatával a `#r "AssemblyName"` irányelv.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Az alábbi szerelvények a rendszer automatikusan hozzáadja az az Azure Functions üzemeltetési környezet:

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

Az alábbi szerelvények egyszerű-név szerint lehet hivatkozni (például `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Egyéni szerelvényeknek hivatkozik

Az egyéni szerelvény hivatkozik, vagy használhatja egy *megosztott* szerelvény vagy egy *privát* sestavení:

* Megosztott szerelvényeket belül függvényalkalmazás a függvények vannak megosztva. Egy egyéni szerelvény hivatkozik, töltse fel a szerelvény nevű `bin` a a [függvény alkalmazás gyökérmappájában](functions-reference.md#folder-structure) (wwwroot).

* Privát szerelvényeket egy adott függvény helyi részét képezik, és közvetlen különböző verzióit támogatja. Privát szerelvények fel kell tölteni a egy `bin` mappa függvény a címtárban. A szerelvények a fájlnevet, például a használatával hivatkozik `#r "MyAssembly.dll"`.

A fájlok feltöltéséről a függvény mappáját információkért lásd: a szakasz a [felügyeleti csomag](#using-nuget-packages).

### <a name="watched-directories"></a>Figyelt könyvtárak

A függvény parancsfájl fájlt tartalmazó könyvtárba címzett szerelvények módosítások automatikusan figyeli. Tekintse meg a szerelvény módosításokat más címtárakban, adja hozzá őket a `watchDirectories` listájában [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>NuGet-csomagok használata
A 2.x-es NuGet-csomagok használata C# működik, és töltse fel a *function.proj* fájlt a függvényalkalmazás fájlrendszer a függvény mappájába. Íme egy példa *function.proj* fájlt, amely hozzáad egy rá *Microsoft.ProjectOxford.Face* verzió *1.1.0-s*:

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

Egy egyéni NuGet-hírcsatorna használatához adja meg a hírcsatorna- *Nuget.Config* fájlt a Függvényalkalmazás gyökerében. További információkért lásd: [konfigurálása NuGet viselkedés](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> Az 1.x C# funkciók NuGet-csomagok hivatkozott az egy *project.json* helyett a fájl egy *function.proj* fájlt.

1.x funkciók, használjon egy *project.json* inkább fájlt. Íme egy példa *project.json* fájlt: 

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

### <a name="using-a-functionproj-file"></a>Function.proj fájl használatával

1. Az Azure Portalon nyissa meg a függvényt. A naplófájlok lapján a csomag telepítése kimenetet jeleníti meg.
2. Feltölteni egy *function.proj* fájlt, az ismertetett módszerek valamelyikét használja a [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate) az Azure Functions fejlesztői referencia-témakör.
3. Miután a *function.proj* fájlt töltenek fel úgy, hogy a függvényben az alábbi példához hasonlóan kimeneti a streamelési log:

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

Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `System.Environment.GetEnvironmentVariable`, ahogyan az az alábbi példakód:

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

C# és az egyéb .NET nyelven, használhat egy [imperatív](https://en.wikipedia.org/wiki/Imperative_programming) kötés minta, nem pedig a [ *deklaratív* ](https://en.wikipedia.org/wiki/Declarative_programming) kötése *function.json*. Imperatív kötés akkor hasznos, ha a kötési paramétereket kell futásidejű kialakítása helyett időpontjában a következő időpontban számítja. Ezzel a mintával kell kötni támogatott bemeneti és kimeneti kötések a működés közbeni a függvénykódban.

Adja meg a versenyképesség kötés az alábbiak szerint:

- **Ne** egy bejegyzést a *function.json* a kívánt imperatív kötések.
- Adja meg a bemeneti paraméterek [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Az alábbi C# minta segítségével végezze el az adatkötés.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` a .NET-attribútum, amely meghatározza a kötés és `T` a kötési típus által támogatott bemeneti vagy kimeneti típus. `T` nem lehet egy `out` paraméter típusa (például `out JObject`). Például, a Mobile Apps-tábla kimeneti kötés által támogatott [hat kimeneti típusokat](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), de csak [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)a `T`.

### <a name="single-attribute-example"></a>Egyetlen attribútum példa

Az alábbi példakód létrehoz egy [Storage-blobból a kimeneti kötés](functions-bindings-storage-blob.md#output) blob a futási időben meghatározott elérési majd ír egy karakterláncot a blob.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) határozza meg a [tárolóblob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötést, és [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) egy támogatott kimeneti kötés típusa.

### <a name="multiple-attribute-example"></a>Több attribútum példa

Az előző példában a függvényalkalmazás fő Tárfiók kapcsolati sztringje alkalmazás beállításának beolvasása (amely `AzureWebJobsStorage`). Egy egyéni alkalmazást beállítást, a Storage-fiók hozzáadásával is megadhat a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) és való átadásához attribútum tömböt `BindAsync<T>()`. Használja a `Binder` paraméter nem `IBinder`.  Példa:

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

A következő táblázat felsorolja a .NET-attribútumok egyes kötési típus és a csomagok, amelyekben vannak definiálva.

> [!div class="mx-codeBreakAll"]
> | Kötés | Attribútum | Hivatkozás hozzáadása |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Tárolási üzenetsor | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tárolóblob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Storage-táblából | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Tudjon meg többet a gyakorlati tanácsok az Azure Functions szolgáltatáshoz](functions-best-practices.md)
