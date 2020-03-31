---
title: A WebJobs SDK használata
description: További információ a WebJobs SDK kódjának írásáról. Hozzon létre eseményvezérelt háttérfeldolgozási feladatokat, amelyek hozzáférnek az Azure-ban és a külső szolgáltatásokban lévő adatokhoz.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: c606f6e60b1c906a0d5c29992287d126aaa37b7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602934"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK használata eseményalapú háttérfeldolgozáshoz

Ez a cikk útmutatást nyújt az Azure WebJobs SDK használatával való munkához. A WebJobs azonnal ismerkedésével az [Azure WebJobs SDK – eseményvezérelt háttérfeldolgozás – első lépések című](webjobs-sdk-get-started.md)témakörben található. 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-verziók

Ezek a legfontosabb különbségek a 3-as verzió között. *x* és 2-es verzió. *x* a WebJobs SDK:x of the WebJobs SDK:

* 3-as verzió. *x* támogatja a .NET Core-t.
* A 3-as verzióban. *x*, explicit módon telepítenie kell a WebJobs SDK által igényelt Storage kötésbővítményt. A 2-es verzióban. *x*, a storage kötések szerepelnek az SDK.x , the Storage bindings were included in the SDK.
* Visual Studio eszköza .* x*) a projektek eltérnek a .* x*) projektek. További információ: [WebJobs fejlesztése és üzembe helyezése a Visual Studio használatával – Azure App Service.](webjobs-dotnet-deploy-vs.md)

Ha lehetséges, mindkét 3-as verzióhoz példák at szolgáltatunk. *x* és 2-es verzió. *x*.

> [!NOTE]
> [Az Azure Functions](../azure-functions/functions-overview.md) a WebJobs SDK-ra épül, és ez a cikk az Azure Functions egyes témakörök dokumentációjára mutató hivatkozásokat tartalmaz. Vegye figyelembe a Függvények és a WebJobs SDK közötti különbségeket:
> * Az Azure Functions 2-es verziója. *x* megfelel a WebJobs SDK 3-as verziójának. *x*és az Azure Functions 1. *x* megfelel a WebJobs SDK 2-nek. *x*. A forráskód-adattárak a WebJobs SDK-számozást használják.
> * Az Azure Functions C# osztálytárak mintakódja olyan, mint a `FunctionName` WebJobs SDK-kód, kivéve, hogy nincs szükség attribútumra egy WebJobs SDK-projektben.
> * Egyes kötéstípusokat csak a Függvények, például a HTTP (Webhooks) és az Event Grid (amely HTTP-n alapul) támogat.
>
> További információ: [Összehasonlítása a WebJobs SDK és az Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs-állomás

Az állomás egy futásidejű tároló függvények.  Figyeli az eseményindítókat és a hívásokat. A 3-as verzióban. *x*, a fogadó `IHost`a megvalósítása . A 2-es verzióban. *x*, használja `JobHost` az objektumot. Hozzon létre egy gazdapéldányt a kódban, és írja a kódot, hogy testre szabhassa annak viselkedését.

Ez egy kulcsfontosságú különbség a WebJobs SDK közvetlen és az Azure Functions en keresztül közvetetten történő használata között. Az Azure Functions, a szolgáltatás szabályozza a gazdagép, és nem szabhatja testre a gazdagép kód írásával. Az Azure Functions lehetővé teszi a gazdagép viselkedésének testreszabását a host.json fájl beállításaival. Ezek a beállítások karakterláncok, nem kódok, és ez korlátozza a megtehetsz testreszabások típusait.

### <a name="host-connection-strings"></a>Állomáskapcsolati karakterláncok

A WebJobs SDK megkeresi az Azure Storage és az Azure Service Bus kapcsolati karakterláncok a local.settings.json fájlt, ha helyileg fut, vagy a környezetben a WebJob, amikor fut az Azure-ban. Alapértelmezés szerint meg kell nevezni `AzureWebJobsStorage` egy elnevezett tárolási kapcsolati karakterlánc-beállítást.  

2. verziójú. *x* az SDK lehetővé teszi, hogy használja a saját nevét ezeket a kapcsolati karakterláncok vagy tárolja őket máshol. A kódot a használatával [`JobHostConfiguration`]állíthatja be a alkalmazásban, ahogy az itt látható:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Mert a 3-as verzió. *x* az alapértelmezett .NET Core konfigurációs API-kat használja, nincs API a kapcsolati karakterlánc nevének módosítására.

### <a name="host-development-settings"></a>Állomásfejlesztési beállítások

A helyi fejlesztés hatékonyabbá tétele érdekében a gazdagép fejlesztési módban futtatható. A fejlesztési módban történő futtatáskor az alábbiakban olvashat:

| Tulajdonság | Fejlesztési beállítás |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`a naplókimenet maximalizálása érdekében. |
| `Queues.MaxPollingInterval`  | Alacsony érték, amely biztosítja, hogy a várólista-metódusok azonnal aktiválódjanak.  |
| `Singleton.ListenerLockPeriod` | 15 másodperc, hogy segítse a gyors iteratív fejlődés. |

A fejlesztési mód engedélyezésének folyamata az SDK-verziótól függ. 

#### <a name="version-3x"></a>3-as verzió. *x*

3-as verzió. *x* a szabványos ASP.NET Core API-kat használja. Hívja [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) meg a [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) metódust a példányon. Adja át `development`a , nevű karakterláncot, ahogy ebben a példában:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2. verziójú. *x*

Az `JobHostConfiguration` osztály `UseDevelopmentSettings` rendelkezik egy módszerrel, amely lehetővé teszi a fejlesztési módot.  A következő példa bemutatja, hogyan kell használni a fejlesztési beállításokat. Ha `config.IsDevelopment` a `true` helyi futtatáskor szeretne visszatérni, `AzureWebJobsEnv` állítson `Development`be egy helyi környezeti változót, amelynek neve a .

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Egyidejű kapcsolatok kezelése (2.-es verzió.* x*)

A 3-as verzióban. *x*, a kapcsolat korlátozza az alapértelmezett végtelen kapcsolatokat. Ha valamilyen okból módosítania kell ezt a [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) korlátot, használhatja az [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) osztály tulajdonságát.

A 2-es verzióban. *x*, a [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API segítségével szabályozhatja az állomáshoz való egyidejű kapcsolatok számát. A 2-ben. *x*, a WebJobs-állomás indítása előtt növelje ezt az értéket az alapértelmezett 2-ről.

Az összes kimenő HTTP-kérés, amelyet `HttpClient` egy `ServicePointManager`függvényből a folyamaton keresztül hajt végre. Miután elérte a `DefaultConnectionLimit`beállított `ServicePointManager` értéket, a küldés előtt elindítja a kérelmek várólistára értekezletét. Tegyük `DefaultConnectionLimit` fel, hogy a beállítása 2, és a kód 1000 HTTP-kérelmet tesz. Kezdetben csak két kérelem engedélyezett az operációs rendszerbe. A többi 998-as addig áll, amíg nincs hely nekik. Ez azt `HttpClient` jelenti, hogy időtúllépésre lehet, mert úgy tűnik, hogy a kérést, de a kérelmet soha nem küldte el az operációs rendszer a célkiszolgálóra. Így előfordulhat, hogy olyan viselkedést lát, amelynek `HttpClient` látszólag nincs értelme: a helyi 10 másodpercet vesz igénybe a kérés teljesítéséhez, de a szolgáltatás minden kérést 200 ms-ban visszaküld. 

A ASP.NET alkalmazások alapértelmezett `Int32.MaxValue`értéke a , és ez valószínűleg jól működik az alapszintű vagy magasabb szintű App Service-csomagban futó WebJobs-ok esetében. A WebJobs-nak általában szüksége van az Mindig bekapcsolva beállításra, és ezt csak az alapszintű és a magasabb szintű App Service-csomagok támogatják.

Ha a WebJob ingyenes vagy megosztott alkalmazásszolgáltatási csomagban fut, az alkalmazást korlátozza az App Service homokozója, amely jelenleg [300-as kapcsolati korláttal](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)rendelkezik. Kötetlen kapcsolatkorlát esetén `ServicePointManager`valószínűbb, hogy a rendszer eléri a sandbox-kapcsolat küszöbértékét, és a hely leáll. Ebben az esetben `DefaultConnectionLimit` az 50 vagy 100-as szintre állítás megakadályozhatja ezt, és továbbra is lehetővé teszi a megfelelő átviteli.

A beállítást a HTTP-kérelmek megtervezése előtt konfigurálni kell. Emiatt a WebJobs-állomás nem módosíthatja automatikusan a beállítást. Előfordulhat, hogy a gazdagép indítása előtt HTTP-kérelmek érkeznek, ami váratlan viselkedéshez vezethet. A legjobb megközelítés az, hogy az `Main` értéket közvetlenül a módszerinicializálás `JobHost`előtt állítsa be, ahogy az itt látható:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Eseményindítók

A függvényeknek nyilvános metódusnak kell lenniük, és rendelkezniük kell egy eseményindító attribútummal vagy az [`NoAutomaticTrigger`](#manual-triggers) attribútummal.

### <a name="automatic-triggers"></a>Automatikus eseményindítók

Az automatikus eseményindítók függvényt hívnak meg egy eseményre válaszul. Tekintsük ezt a példát egy olyan függvényre, amelyet az Azure Queue storage-ba hozzáadott üzenet vált ki. Úgy válaszol, hogy elolvas egy blobot az Azure Blob storage-ból:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Az `QueueTrigger` attribútum arra utasítja a futásidejűt, hogy `myqueue-items` hívja meg a függvényt, ha várólistaüzenet jelenik meg a várólistában. Az `Blob` attribútum azt mondja a futásidejűnek, hogy a várólista-üzenet használatával olvassa el a blobot a *mintamunkaelemek* tárolójában. A várólista-üzenet tartalma, amely a `myQueueItem` paraméter ben lévő függvénynek adták át, a blob neve.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Kézi eseményindítók

A függvény manuális indításához `NoAutomaticTrigger` használja az attribútumot, ahogy az itt látható:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

A funkció manuális aktiválásának folyamata az SDK-verziótól függ.

#### <a name="version-3x"></a>3-as verzió. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>2. verziójú. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Bemeneti és kimeneti kötések

A bemeneti kötések deklaratív módot biztosítanak arra, hogy az Azure-ból vagy külső szolgáltatásokból származó adatokat elérhetővé tegyék a kód számára. A kimeneti kötések lekínálják az adatok frissítését. Az [Első lépések](webjobs-sdk-get-started.md) cikk egy-egy példát mutat be.

A kimeneti kötéshez a metódus visszatérési értékét úgy használhatja, hogy az attribútumot alkalmazza a metódus visszatérési értékére. Tekintse meg a példát [az Azure-függvény visszatérési értékének használata](../azure-functions/functions-bindings-return-value.md)című témakörben.

## <a name="binding-types"></a>Kötéstípusok

A kötéstípusok telepítésének és kezelésének folyamata attól függ, hogy a 3-as verziót használja-e. *x* vagy 2-es verzió. *x* az SDK-t. Az adott kötéstípushoz telepíthető csomagot az adott kötéstípus Azure Functions [referenciacikkének](#binding-reference-information)"Csomagok" szakaszában találja. Kivételt képez a Fájlok eseményindító és kötés (a helyi fájlrendszer), amely nem támogatja az Azure Functions.

#### <a name="version-3x"></a>3-as verzió. *x*

A 3-as verzióban. *x*, a tárolókötések `Microsoft.Azure.WebJobs.Extensions.Storage` a csomagban találhatók. Hívja `AddAzureStorage` meg a `ConfigureWebJobs` kiterjesztés módszer a módszer, az itt látható:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Más eseményindító- és kötéstípusok használatához telepítse az azokat `Add<binding>` tartalmazó NuGet csomagot, és hívja meg a bővítményben megvalósított bővítménymetódust. Ha például egy Azure Cosmos DB-kötést `Microsoft.Azure.WebJobs.Extensions.CosmosDB` szeretne `AddCosmosDB`használni, telepítse és hívja meg a következőket:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Az időzítő eseményindító vagy a fájlok kötésének használatához, `AddTimers` amelyek `AddFiles` az alapvető szolgáltatások részét képezik, hívja meg a vagy a bővítmény metódusait.

#### <a name="version-2x"></a>2. verziójú. *x*

Ezek az eseményindító- és kötéstípusok a 2-es verzióban találhatók. *x* a `Microsoft.Azure.WebJobs` csomag:

* Blob Storage
* Queue Storage
* Table Storage

Más eseményindító- és kötéstípusok használatához telepítse az azokat `Use<binding>` tartalmazó `JobHostConfiguration` NuGet csomagot, és hívja meg az objektum metódusát. Ha például időzítő-eseményindítót szeretne `Microsoft.Azure.WebJobs.Extensions` használni, `UseTimers` telepítse `Main` és hívja meg a metódust, ahogy az itt látható:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

A Fájlok kötésének `Microsoft.Azure.WebJobs.Extensions` használatához `UseFiles`telepítse és hívja meg a programot.

### <a name="executioncontext"></a>ExecutionContext

A WebJobs lehetővé [`ExecutionContext`]teszi a kötést. Ezzel a kötéssel elérheti [`ExecutionContext`] a paramétert a függvényaláírásában. A következő kód például a környezeti objektumot használja a meghívási azonosító eléréséhez, amely segítségével korrelálhat egy adott függvény meghívása által létrehozott összes naplóval.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

A kötés folyamata [`ExecutionContext`] az SDK-verziótól függ.

#### <a name="version-3x"></a>3-as verzió. *x*

Hívja `AddExecutionContextBinding` meg a `ConfigureWebJobs` kiterjesztés módszer a módszer, az itt látható:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2. verziójú. *x*

A `Microsoft.Azure.WebJobs.Extensions` korábban említett csomag egy speciális kötéstípust is `UseCore` biztosít, amelyet a metódus hívásával regisztrálhat. Ez a kötés [`ExecutionContext`] lehetővé teszi egy paraméter definiálását a függvényaláírásban, amely az így van engedélyezve:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Kötési konfiguráció

Egyes eseményindítók és kötések viselkedését konfigurálhatja. A konfigurálásuk folyamata az SDK-verziótól függ.

* **3-as verzió. *x*:** Állítsa `Add<Binding>` be a `ConfigureWebJobs`konfigurációt a metódus befújásakor.
* **2. verziójú. *x*:** A konfiguráció beállításához állítsa be a `JobHost`tulajdonságokat egy konfigurációs objektumban, amelybe a beadja a programot.

Ezek a kötés-specifikus beállítások egyenértékűek az Azure Functions [host.json projektfájljának](../azure-functions/functions-host-json.md) beállításaival.

A következő kötéseket állíthatja be:

* [Az Azure CosmosDB eseményindítója](#azure-cosmosdb-trigger-configuration-version-3x)
* [Eseményközpontok eseményindítója](#event-hubs-trigger-configuration-version-3x)
* [Queue Storage-eseményindító](#queue-storage-trigger-configuration)
* [SendGrid-kötés](#sendgrid-binding-configuration-version-3x)
* [Service Bus eseményindító](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Az Azure CosmosDB eseményindító konfigurációja (3-as verzió.* x*)

Ez a példa bemutatja, hogyan konfigurálhatja az Azure Cosmos DB eseményindítóját:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

További részletekért tekintse meg az [Azure CosmosDB kötési](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) cikket.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Az Event Hubs eseményindító-konfigurációja (3-as verzió.* x*)

Ez a példa bemutatja, hogyan konfigurálható az Eseményközpontok eseményindítója:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

További részletekért tekintse meg az [Event Hubs kötési](../azure-functions/functions-bindings-event-hubs-output.md#hostjson-settings) cikket.

### <a name="queue-storage-trigger-configuration"></a>Várólista-tároló eseményindítójának konfigurációja

Ezek a példák bemutatják, hogyan konfigurálható a várólista-tárolási eseményindító:

#### <a name="version-3x"></a>3-as verzió. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

További részletekért tekintse meg a [várólista tárolási kötési](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) cikket.

#### <a name="version-2x"></a>2. verziójú. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

További részletek a [host.json 1.x című témakörben talál.](../azure-functions/functions-host-json-v1.md#queues)

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid kötéskonfiguráció (3-as verzió.* x*)

Ez a példa bemutatja, hogyan konfigurálható a SendGrid kimeneti kötés:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

További információt a [SendGrid kötési](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) cikkében talál.

### <a name="service-bus-trigger-configuration-version-3x"></a>A Service Bus eseményindítójának konfigurációja (3-as verzió.* x*)

Ez a példa bemutatja, hogyan konfigurálható a Service Bus eseményindító:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

További részletekért tekintse meg a [Service Bus kötési](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) cikket.

### <a name="configuration-for-other-bindings"></a>Más kötések konfigurálása

Egyes eseményindító- és kötéstípusok saját egyéni konfigurációtípusokat határoznak meg. A Fájl eseményindító például lehetővé teszi a figyelni kívánt gyökérelérési út megadását, mint például a következő példákban:

#### <a name="version-3x"></a>3-as verzió. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2. verziójú. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Kötéskifejezések

Az attribútumkonstruktor paraméterekben olyan kifejezéseket használhat, amelyek különböző forrásokból származó értékekre oldódnak fel. A következő kódban például az `BlobTrigger` attribútum elérési útja `filename`létrehoz egy kifejezést. A kimeneti kötés, `filename` feloldja az eseményindító blob nevét.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

A kötési kifejezésekről további információt az Azure Functions dokumentációjában [található Kötési kifejezések és minták](../azure-functions/functions-bindings-expressions-patterns.md) című témakörben talál.

### <a name="custom-binding-expressions"></a>Egyéni kötési kifejezések

Néha meg szeretne adni egy várólista nevét, egy blob nevét vagy tárolóját, vagy egy táblanevet a kódban, nem pedig a kódba. Előfordulhat például, hogy meg szeretné adni `QueueTrigger` az attribútum várólistájának nevét egy konfigurációs fájlban vagy környezeti változóban.

Ezt úgy teheti meg, hogy `NameResolver` `JobHostConfiguration` egy objektumot az objektumnak ad át. Helyőrzőket is felvehet az eseményindító vagy a `NameResolver` kötési attribútumkonstruktor paramétereibe, és a kód biztosítja a helyőrzők helyett használandó tényleges értékeket. A helyőrzőket úgy azonosíthatja, hogy körülveszi őket százalékkal (%) jelek, amint az itt látható:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód lehetővé `logqueuetest` teszi, hogy a `logqueueprod` tesztkörnyezetben megnevezett és az éles környezetben megnevezett várólistát használjon. A kódolt várólista neve helyett a `appSettings` gyűjteményben lévő bejegyzés nevét kell megadni.

Van egy alapértelmezett, `NameResolver` amely akkor lép érvénybe, ha nem ad meg egyénit. Az alapértelmezett értékeket az alkalmazásbeállításokból vagy a környezeti változókból kapja meg.

Az `NameResolver` osztály a várólista nevét a ből `appSettings`kapja, ahogy az itt látható:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>3-as verzió. *x*

A feloldót függőségi injektálással konfigurálhatja. Ezeka minták `using` a következő nyilatkozatot igénylik:

```cs
using Microsoft.Extensions.DependencyInjection;
```

A feloldót úgy [`ConfigureServices`] adja hozzá, hogy meghívja a bővítménymetódust, [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)ahogy ebben a példában is:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2. verziójú. *x*

Adja `NameResolver` át az `JobHost` osztályt az objektumnak, ahogy az itt látható:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Az Azure `INameResolver` Functions az alkalmazásbeállításokból származó értékek bekéseléséhez implementálható, ahogy az a példában látható. Ha közvetlenül használja a WebJobs SDK-t, írhat egy egyéni implementációt, amely helyőrző csereértékeket kap bármilyen forrásból.

## <a name="binding-at-runtime"></a>Kötés futásidőben

Ha a kötési attribútum használata előtt el kell végeznie `Blob`a `Table`munkát a `IBinder` függvényben, például `Queue`a , vagy a , használhatja a felületet.

A következő példa egy bemeneti várólista-üzenetet vesz fel, és létrehoz egy új üzenetet ugyanazzal a tartalommal egy kimeneti várólistában. A kimeneti várólista nevét a függvény törzsében lévő kód állítja be.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

További információ: [Kötés futásidőben](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) az Azure Functions dokumentációjában.

## <a name="binding-reference-information"></a>Kötelező hivatkozási információ

Az Azure Functions dokumentációja az egyes kötéstípusokra vonatkozó referenciainformációkat tartalmaz. Az alábbi információkat minden kötési referenciacikkben megtalálja. (Ez a példa a tárolási várólistán alapul.)

* [Csomagok](../azure-functions/functions-bindings-storage-queue.md). A webjobs SDK-projekt kötésének támogatásához telepíteni kívánt csomag.
* [Példák](../azure-functions/functions-bindings-storage-queue-trigger.md). Kódminták. A C# osztálykönyvtár-példa a WebJobs SDK-ra vonatkozik. Csak hagyja `FunctionName` ki az attribútumot.
* [Attribútumok](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). A kötéstípushoz használandó attribútumok.
* [Konfiguráció](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Az attribútumtulajdonságok és a konstruktorparaméterek magyarázata.
* [Használat](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). A kötéshez köthető típusok és a kötés működésével kapcsolatos információk. Például: lekérdezési algoritmus, méreg várólista feldolgozása.
  
A kötési referenciacikkek listáját az Azure Functions [eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) című cikk "Támogatott kötések" című témakörében olvashat. Ebben a listában a HTTP, Webhooks és Event Grid kötések csak az Azure Functions által támogatott, a WebJobs SDK nem.

## <a name="disable-attribute"></a>Attribútum letiltása 

Az [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum lehetővé teszi annak szabályozását, hogy egy függvény aktiválható-e. 

A következő példában, ha `Disable_TestJob` az alkalmazás `1` `True` beállítása értéke vagy (kis- és nagybetűk nem megkülönböztető), a függvény nem fog futni. Ebben az esetben a futásidejű létrehoz egy *naplóüzenetet, amely a "Functions.TestJob" függvény t le van tiltva.*

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Amikor módosítja az alkalmazásbeállítási értékeket az Azure Portalon, a WebJob újraindul az új beállítás felvételéhez.

Az attribútum deklarálható paraméter, metódus vagy osztály szinten. A beállítás neve kötési kifejezéseket is tartalmazhat.

## <a name="timeout-attribute"></a>Időeltetési attribútum

Az [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribútum hatására egy függvény megszakad, ha nem fejeződik be egy megadott időn belül. A következő példában a függvény egy napig futna a Timeout attribútum nélkül. Az időeltetés hatására a függvény 15 másodperc elteltével megszakad.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Az Időelés attribútumot osztály- vagy metódusszinten is alkalmazhatja, és `JobHostConfiguration.FunctionTimeout`globális időmegadást is megadhat a használatával. Az osztályszintű vagy metódusszintű időtúljáromok felülbírálják a globális időtúlelőnyöket.

## <a name="singleton-attribute"></a>Singleton attribútum

Az [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribútum biztosítja, hogy egy függvénynek csak egy példánya fut, még akkor is, ha a gazdawebalkalmazás több példánya is van. Ezt [elosztott zárolással](#viewing-lease-blobs)teszi.

Ebben a példában a `ProcessImage` függvénynek csak egy példánya fut egy adott időpontban:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Egyes eseményindítók beépített támogatást nyújtanak az egyidejűség kezeléséhez:

* **QueueTrigger**. A `JobHostConfiguration.Queues.BatchSize` `1`beállítás a ikonra van állítva.
* **ServiceBusTrigger**. A `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` `1`beállítás a ikonra van állítva.
* **FileTrigger**. A `FileProcessor.MaxDegreeOfParallelism` `1`beállítás a ikonra van állítva.

Ezekkel a beállításokkal biztosíthatja, hogy a függvény egyetlen példányon egyetlen példányban egyetlen példányként futjon. Annak érdekében, hogy a függvénynek csak egy példánya fusson, amikor a webalkalmazás több példányra skálázódik, alkalmazzon figyelőszintű singleton zárolást a függvényen (`[Singleton(Mode = SingletonMode.Listener)]`). Figyelő zárolások beszerzett, amikor a JobHost elindul. Ha három horizontális felskálázott példány egyszerre kezdődik, csak az egyik példány szerzi be a zárolást, és csak egy figyelő indul el.

### <a name="scope-values"></a>Hatókör-értékek

Egyetlen tononon megadhatja a *hatókör kifejezését/értékét.* A kifejezés/érték biztosítja, hogy a függvény egy adott hatókörben történő összes végrehajtása szerializálva lesz. A részletesebb zárolás ily módon történő megvalósítása lehetővé teheti a funkció bizonyos szintű párhuzamosságát, miközben szerializálja az ön igényeinek megfelelő egyéb meghívásokat. A következő kódban például a hatókörkifejezés `Region` a bejövő üzenet értékéhez kötődik. Ha a várólista három üzenetet tartalmaz a kelet, a keleti és a nyugati régióban, a Keleti régióval rendelkező üzenetek sorozatosan futnak, míg a Nyugati régióval rendelkező üzenet a keleti régióval párhuzamosan fut.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

A zárolás alapértelmezett hatóköre , `SingletonScope.Function`azaz a zárolási hatókör (a blob bérleti útvonala) a teljesen minősített függvény nevéhez van kötve. A függvények közötti `SingletonScope.Host` zároláshoz adjon meg és használjon olyan hatókörazonosító nevet, amely megegyezik az összes olyan függvényen, amelyet nem szeretne egyszerre futtatni. A következő példában egyszerre `AddItem` csak `RemoveItem` egy példány jelenik meg vagy fut:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Címbérlet-blobok megtekintése

A WebJobs SDK [azure blob bérletek](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) a fedelek alatt az elosztott zárolás megvalósítása. A Singleton által használt bérletblobok `azure-webjobs-host` a tárolóban találhatók a `AzureWebJobsStorage` "zárolások" elérési út alatt. Például a bérleti blob elérési `ProcessImage` útja az első `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`példa korábban látható lehet. Minden elérési út tartalmazza a JobHost-azonosítót, ebben az esetben a 061851c758f04938a4426a9ab3869c0.

## <a name="async-functions"></a>Aszinkron függvények

Az aszinkron függvények kódolásáról az [Azure Functions dokumentációjában](../azure-functions/functions-dotnet-class-library.md#async)olvashat.

## <a name="cancellation-tokens"></a>Törlési tokenek

A törlési jogkivonatok kezeléséről az Azure Functions dokumentációjában olvashat a [törlési jogkivonatokról és a szabályos leállításról.](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)

## <a name="multiple-instances"></a>Több példány

Ha a webalkalmazás több példányon fut, a folyamatos WebJob fut minden példányon, figyeli az eseményindítók és a hívó függvények. A különböző eseményindító-kötések úgy vannak kialakítva, hogy hatékonyan osszák meg a munkát a példányok között, így a további példányokra való skálázás lehetővé teszi a nagyobb terhelés kezelését.

Míg egyes eseményindítók eredményezheti a kettős feldolgozás, várólista és blob tárolási eseményindítók automatikusan megakadályozza, hogy egy függvény feldolgozása egy várólista-üzenet vagy blob többször. További információ: [Tervezése azonos bevitelhez](../azure-functions/functions-idempotent.md) az Azure Functions dokumentációjában.

Az időzítő eseményindító automatikusan biztosítja, hogy csak egy példánya fut, így nem kap egynél több függvénypéldány fut egy adott ütemezett időpontban.

Ha azt szeretné, hogy egy függvénynek csak egy példánya futjon, még akkor is, ha a gazdawebalkalmazás több példánya van, használhatja az [`Singleton`](#singleton-attribute) attribútumot.

## <a name="filters"></a>Szűrők

A függvényszűrők (előzetes verzió) lehetővé teszik a WebJobs végrehajtási folyamat testreszabását a saját logikájával. A szűrők hasonlóak [ASP.NET Core szűrőkhöz.](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters) Deklaratív attribútumként valósíthatja meg őket, amelyek a függvényekre vagy osztályokra vannak alkalmazva. További információt a Funkciószűrők című [témakörben talál.](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Javasoljuk, hogy a naplózási keretrendszert, amely et ASP.NET fejlesztettki. Az [Első lépések](webjobs-sdk-get-started.md) című cikk bemutatja, hogyan kell használni. 

### <a name="log-filtering"></a>Naplószűrés

Egy `ILogger` példány által létrehozott minden `Category` `Level`naplóhoz tartozik egy társított és . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)egy számbavétel, és az egész kód relatív fontosságot jelez:

|Naplózási szint    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

Az egyes kategóriákat egymástól [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)függetlenül szűrheti egy adott kategóriára. Például érdemes lehet látni az összes naplót blob `Error` eseményindító feldolgozása, de csak és magasabb minden más.

#### <a name="version-3x"></a>3-as verzió. *x*

3-as verzió. *x* az SDK-k a .NET Core-ba beépített szűrésre támaszkodik. Az `LogCategories` osztály lehetővé teszi kategóriák definiálását adott függvényekhez, eseményindítókhoz vagy felhasználókhoz. Emellett szűrőket is meghatároz az `Startup` adott `Results`gazdaállapothoz, például a és a hoz. Ez lehetővé teszi a naplózási kimenet finomhangolását. Ha a megadott kategóriákban nem található egyezés, `Default` a szűrő visszaesik az értékre, amikor eldönti, hogy szűrje-e az üzenetet.

`LogCategories`a következő utasítást igényli:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

A következő példa létrehoz egy szűrőt, amely alapértelmezés `Warning` szerint az összes naplót a szinten szűri. A `Function` `results` és a kategóriák `Host.Results` (egyenértékű a 2.* x*) a `Error` szinten szűrjük. A szűrő összehasonlítja az aktuális kategóriát `LogCategories` a példány összes regisztrált szintjével, és kiválasztja a leghosszabb egyezést. Ez azt `Debug` jelenti, `Host.Triggers` hogy `Host.Triggers.Queue` `Host.Triggers.Blob`a kiegyenlített vagy. Ez lehetővé teszi, hogy a szélesebb kategóriákat anélkül szabályozhatja, hogy mindegyiket hozzá kellene adnia.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2. verziójú. *x*

A 2-es verzióban. *x* az SDK, akkor `LogCategoryFilter` használja az osztályt, hogy ellenőrizzék szűrés. A `LogCategoryFilter` `Default` tulajdonság kezdeti értéke `Information`, ami azt jelenti, `Information` `Warning`hogy `Error`a `Critical` , , vagy szinteken `Debug` `Trace` lévő üzeneteket a rendszer naplózza, de a vagy a szinteken lévő üzeneteket a rendszer kiszűri.

Mint `LogCategories` a 3-as verzióban. *x*, `CategoryLevels` a tulajdonság lehetővé teszi, hogy adja meg a napló szintje az egyes kategóriák, így finomhangolható a naplózási kimenet. Ha nem található egyezés a `CategoryLevels` szótárban, `Default` a szűrő visszaesik az értékre, amikor eldönti, hogy szűri-e az üzenetet.

A következő példa egy szűrőt hoz létre, `Warning` amely alapértelmezés szerint szűri az összes naplót a szinten. A `Function` `Host.Results` és a kategóriák szűrése a `Error` szinten. Az `LogCategoryFilter` összehasonlítja az aktuális `CategoryLevels` kategóriát az összes regisztrált, és kiválasztja a leghosszabb egyezést. Tehát `Debug` a regisztrált `Host.Triggers` szint `Host.Triggers.Queue` `Host.Triggers.Blob`egyezik vagy . Ez lehetővé teszi, hogy a szélesebb kategóriákat anélkül szabályozhatja, hogy mindegyiket hozzá kellene adnia.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Egyéni telemetria az Application Insightsszámára

Az [Application Insights](../azure-monitor/app/app-insights-overview.md) egyéni telemetriai adatainak megvalósításának folyamata az SDK-verziótól függ. Az Application Insights konfigurálásáról az [Application Insights naplózásának hozzáadása](webjobs-sdk-get-started.md#add-application-insights-logging)című témakörben olvashat.

#### <a name="version-3x"></a>3-as verzió. *x*

Mert a 3-as verzió. *x* a WebJobs SDK támaszkodik a .NET Core általános állomás, egy egyéni telemetriai gyár már nem biztosított. De egyéni telemetriai adatokat adhat hozzá a folyamathoz függőségi injektálás használatával. Az ebben a szakaszban `using` szereplő példáka következő állításokat igényel:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A következő egyéni [`ITelemetryInitializer`] megvalósításlehetővé [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) teszi, [`TelemetryConfiguration`]hogy sajátot adjon hozzá az alapértelmezett hez.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Hívja [`ConfigureServices`] meg a szerkesztőt, és adja hozzá az egyéni [`ITelemetryInitializer`] folyamathoz.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

[`TelemetryConfiguration`] A létrehozáskor az összes [`ITelemetryInitializer`] regisztrált típus szerepel a csomagban. További információ: [Application Insights API for custom events and metrics.](../azure-monitor/app/api-custom-events-metrics.md)

A 3-as verzióban. *x*, akkor már nem [`TelemetryClient`] kell öblíteni a, amikor a gazdagép leáll. A . `ApplicationInsightsLoggerProvider` [`TelemetryClient`]

#### <a name="version-2x"></a>2. verziójú. *x*

A 2-es verzióban. *x*, [`TelemetryClient`] amelyet az Application Insights-szolgáltató hozott létre [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)a WebJobs SDK számára. Ha az Application Insights-végpont nem érhető el, vagy a bejövő kérelmek szabályozása, ez a csatorna [menti a kérelmeket a webalkalmazás fájlrendszerében, és később újra elküldi őket.](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)

Az-t [`TelemetryClient`] egy osztály hozlétre, amely megvalósítja a . `ITelemetryClientFactory` Alapértelmezés szerint ez [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)a .

Ha módosítani szeretné az Application Insights-folyamat bármely részét, `ITelemetryClientFactory`megadhat sajátnak is, és [`TelemetryClient`]a gazdagép az osztályt fogja használni a létrehozásához. Ez a kód például felülbírálja `DefaultTelemetryClientFactory` `ServerTelemetryChannel`a következők tulajdonságának módosítását:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Az `SamplingPercentageEstimatorSettings` objektum [adaptív mintavételezést](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)konfigurál. Ez azt jelenti, hogy bizonyos nagy mennyiségű forgatókönyvek, Applications Insights küld egy kiválasztott részhalmaza telemetriai adatok a kiszolgálóra.

Miután létrehozta a telemetriai-feldolgozó, adja át az Application Insights naplózási szolgáltató:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Következő lépések

Ez a cikk olyan kódrészleteket adott meg, amelyek bemutatják, hogyan kezelhetők a WebJobs SDK-val való együttműködés gyakori forgatókönyvei. A teljes mintákat lásd: [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

["ExecutionContext"]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
["Telemetriai ügyfél"]: /dotnet/api/microsoft.applicationinsights.telemetryclient
['ConfigureServices']: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
["ITelemetryInitializer"]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemettryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
