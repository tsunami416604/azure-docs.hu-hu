---
title: A webjobs SDK használata
description: További információ a webjobs SDK kódjának írásához. Hozzon létre eseményvezérelt háttér-feldolgozási feladatokat, amelyek az Azure-ban és a külső szolgáltatásokban tárolt adatokhoz férnek hozzá.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 715415929afaad36e4854e75a2b7b5360d22a6bf
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486342"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK használata eseményvezérelt háttérben végzett feldolgozáshoz

Ez a cikk útmutatást nyújt a Azure WebJobs SDK-val való együttműködéshez. A webjobs-feladatok azonnal elérhetővé tételéhez lásd: Ismerkedés [a Azure WEBJOBS SDK-val az eseményvezérelt háttér-feldolgozáshoz](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Webjobs SDK-verziók

Ezek a 3. verzió közötti fő különbségek. *x* és 2-es verzió. a webjobs SDK *x* -je:

* 3\. verzió. az *x* támogatja a .net Core-ot.
* A 3. verzióban. *x*, explicit módon telepítenie kell a webjobs SDK által igényelt tárolási kötési bővítményt. A 2. verzióban. *x*, a tárolási kötések szerepeltek az SDK-ban.
* Visual Studio-eszközök a .NET Core-hoz (3) *x*) a projektek eltérnek a .NET-keretrendszer (2) eszközeitől. *x*) projektek. További információ: [webjobs-feladatok fejlesztése és üzembe helyezése a Visual Studio-Azure app Service használatával](webjobs-dotnet-deploy-vs.md).

Ha lehetséges, a 3. verzióra vonatkozó példákat is meg kell adni. *x* és 2-es verzió. *x*.

> [!NOTE]
> [Azure functions](../azure-functions/functions-overview.md) a webjobs SDK-ra épül, és ez a cikk néhány témakörre mutató hivatkozásokat tartalmaz Azure functions dokumentációhoz. Figyelje meg a függvények és a webjobs SDK közötti különbségeket:
> * Azure Functions 2. verzió. az *x* a webjobs SDK 3-as verziójának felel meg. *x*és Azure functions 1. az *x* a webjobs SDK 2-nak felel meg. *x*. A forráskód-Tárházak a webjobs SDK-számozást használják.
> * Azure Functions C# Class-kódtárak például a webjobs SDK-kód, például a webjobs SDK-projektben nem szükséges `FunctionName` attribútum.
> * Bizonyos kötési típusok csak olyan függvényekben támogatottak, mint például a HTTP (webhookok) és a Event Grid (amely HTTP-alapú).
>
> További információ: [a Webjobs SDK és a Azure functions összevetése](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Webjobs-gazdagép

A gazdagép a függvények futásidejű tárolója.  Ez figyeli az eseményindítókat és a hívási függvényeket. A 3. verzióban. *x*, a gazdagép a `IHost`implementációja. A 2. verzióban. *x*, használja a `JobHost` objektumot. Hozzon létre egy gazdagép-példányt a kódban, és írja be a kódot a viselkedésének testre szabásához.

Ez kulcsfontosságú különbség a webjobs SDK közvetlen használata és a Azure Functions használatával közvetve. Azure Functions a szolgáltatás vezérli a gazdagépet, és nem tudja testreszabni a gazdagépet a kód írásával. Azure Functions lehetővé teszi a gazdagép viselkedésének testreszabását a Host. JSON fájl beállításain keresztül. Ezek a beállítások karakterláncok, nem pedig kódok, és ez korlátozza az elvégezhető testreszabások fajtáit.

### <a name="host-connection-strings"></a>Gazdagép-kapcsolatok karakterláncai

A webjobs SDK megkeresi az Azure Storage-t, és Azure Service Bus a helyi. Settings. JSON fájlban lévő kapcsolódási karakterláncokat a helyileg futtatott vagy az Azure-ban futtatott Webjobs környezetében. Alapértelmezés szerint egy `AzureWebJobsStorage` nevű Storage-karakterlánc-beállítást kell megadni.  

2\. verzió. Az SDK *x* -je lehetővé teszi, hogy saját nevét használja ezeket a kapcsolatok sztringeket, vagy máshol tárolja őket. A kódban szereplő neveket a [`JobHostConfiguration`]használatával is megadhatja, ahogy az itt látható:

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

Mivel a 3. verzió. az *x* az alapértelmezett .net Core konfigurációs API-kat használja, nincs API a kapcsolódási karakterlánc nevének módosításához.

### <a name="host-development-settings"></a>Gazdagép-fejlesztési beállítások

A helyi fejlesztés hatékonyabbá tételéhez a gazdagépet fejlesztői módban is futtathatja. Íme néhány olyan beállítás, amely a fejlesztői módban történő futtatáskor módosul:

| Tulajdonság | Fejlesztési beállítás |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` a naplók kimenetének maximalizálása érdekében. |
| `Queues.MaxPollingInterval`  | A várólista-metódusok azonnali indításának alacsony értéke.  |
| `Singleton.ListenerLockPeriod` | 15 másodperc a gyors iterációs fejlesztésben való segítségnyújtáshoz. |

A fejlesztési mód engedélyezésének folyamata az SDK verziójától függ. 

#### <a name="version-3x"></a>3\. verzió. *x*

3\. verzió. az *x* a standard ASP.net Core API-kat használja. Hívja meg a [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metódust a [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példányon. Adjon át egy `development`nevű karakterláncot, ahogy az alábbi példában látható:

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

#### <a name="version-2x"></a>2\. verzió. *x*

A `JobHostConfiguration` osztálynak van egy `UseDevelopmentSettings` metódusa, amely lehetővé teszi a fejlesztési módot.  Az alábbi példa bemutatja, hogyan használhatók a fejlesztői beállítások. Ha helyileg futtatja a `config.IsDevelopment` visszatérési `true`, akkor a `AzureWebJobsEnv` nevű helyi környezeti változót a `Development`értékre kell állítani.

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

### <a name="jobhost-servicepointmanager-settings"></a>Egyidejű kapcsolatok kezelése (2. verzió). *x*)

A 3. verzióban. *x*, a kapcsolat korlátja alapértelmezés szerint a végtelen kapcsolatok. Ha valamilyen okból módosítania kell ezt a korlátot, használhatja a [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) osztály [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) tulajdonságát.

A 2. verzióban. *x*, a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API használatával szabályozható a gazdagépek egyidejű kapcsolatainak száma. 2\. *x*, a webjobs-gazdagép elindítása előtt növelje ezt az értéket az alapértelmezett 2 értékről.

Minden olyan kimenő HTTP-kérelem, amelyet egy függvényből a `HttpClient` flow használatával `ServicePointManager`keresztül végez. Miután elérte a `DefaultConnectionLimit`ban beállított értéket, `ServicePointManager` elindítja a várólista-kérelmek küldését. Tegyük fel, hogy a `DefaultConnectionLimit` 2 értékre van állítva, és a kód 1 000 HTTP-kérelmeket tesz lehetővé. Kezdetben csak két kérelem engedélyezett az operációs rendszerre. A többi 998 várólistára kerül, amíg el nem helyezi a helyet. Ez azt jelenti, hogy a `HttpClient` időtúllépés miatt megszakadt, mert úgy tűnik, hogy elvégezte a kérést, de az operációs rendszer nem küldte el a kérést a célkiszolgálóra. Tehát előfordulhat, hogy olyan viselkedést lát, amely nem tűnik ésszerűnek: a helyi `HttpClient` 10 másodpercet vesz igénybe a kérelem teljesítéséhez, de a szolgáltatás az 200 MS-ban minden kérést visszaad. 

A ASP.NET-alkalmazások alapértelmezett értéke `Int32.MaxValue`, és ez valószínűleg jól működik az alapszintű vagy magasabb App Service-tervben futó webjobs esetében is. A webjobs-nek jellemzően az Always On beállításra van szüksége, amelyet csak az alapszintű és a magasabb App Service csomagok támogatnak.

Ha a Webjobs ingyenes vagy megosztott App Service-csomagban fut, az alkalmazást a App Service sandbox korlátozza, amely jelenleg 300-as [korláttal](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)rendelkezik. Ha `ServicePointManager`nem kötött kapcsolati korlátot használ, annál valószínűbb, hogy a rendszer elérte a sandbox kapcsolati küszöbértékét, és a hely leáll. Ebben az esetben előfordulhat, hogy a `DefaultConnectionLimit`t, például az 50-es vagy a 100-es értéket állítja be, ami megakadályozza, hogy ez megtörténjen, és továbbra is elegendő átviteli sebességet

A beállítást a HTTP-kérelmek elfogadását megelőzően kell konfigurálni. Emiatt a webjobs-gazdagépnek nem szabad automatikusan módosítania a beállítást. Lehetnek HTTP-kérelmek, amelyek a gazdagép elindítása előtt történnek, ami váratlan viselkedéshez vezethet. A legjobb módszer az, ha a `Main` metódusban azonnal beállítja az értéket a `JobHost`inicializálása előtt, ahogy az itt látható:

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

A függvényeknek nyilvános metódusoknak kell lenniük, és rendelkezniük kell egy trigger attribútummal vagy a [`NoAutomaticTrigger`](#manual-triggers) attribútummal.

### <a name="automatic-triggers"></a>Automatikus eseményindítók

Az automatikus eseményindítók meghívnak egy függvényt egy eseményre válaszul. Gondolja át ezt a példát egy olyan függvényre, amelyet az Azure üzenetsor-tárolóba felvett üzenet indít el. Az Azure Blob Storage-ból származó Blobok beolvasásával válaszol:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

A `QueueTrigger` attribútum arra utasítja a futtatókörnyezetet, hogy hívja meg a függvényt, amikor egy üzenetsor-üzenet jelenik meg a `myqueue-items`-várólistában. A `Blob` attribútum azt jelzi, hogy a futásidejű üzenettel a workitems tárolóban található blob beolvasása a üzenetsor *-* üzenetet használja. A várólista-üzenetnek a `myQueueItem` paraméterben szereplő függvénynek átadott tartalma a blob neve.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Manuális eseményindítók

A függvények manuális elindításához használja a `NoAutomaticTrigger` attribútumot, ahogy az itt látható:

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

A függvény manuális aktiválásának folyamata az SDK verziójától függ.

#### <a name="version-3x"></a>3\. verzió. *x*

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

#### <a name="version-2x"></a>2\. verzió. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Bemeneti és kimeneti kötések

A bemeneti kötések deklaratív módszert biztosítanak az Azure-ból vagy külső szolgáltatásokból származó adatoknak a kód számára elérhetővé tételéhez. A kimeneti kötések lehetővé teszik az adatfrissítést. Az [első lépések](webjobs-sdk-get-started.md) című cikk egy példát mutat be.

A metódus visszatérési értékét egy kimeneti kötéshez használhatja, ha az attribútumot a metódus visszatérési értékére alkalmazza. Tekintse meg a példát az [Azure Function Return értékének használatával](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Kötési típusok

A kötési típusok telepítésének és kezelésének folyamata attól függ, hogy a 3. verziót használja-e. *x* vagy 2-es verzió. Az SDK *x* -je. Az adott kötési típushoz telepítendő csomagot a kötési típus Azure Functions [hivatkozási cikkének](#binding-reference-information)"csomagok" szakaszában találja. Kivételt képeznek a fájlok triggerei és kötései (a helyi fájlrendszer esetében), amelyet a Azure Functions nem támogat.

#### <a name="version-3x"></a>3\. verzió. *x*

A 3. verzióban. *x*, a tárolási kötések beletartoznak a `Microsoft.Azure.WebJobs.Extensions.Storage` csomagba. Hívja meg a `AddAzureStorage` Extension metódust a `ConfigureWebJobs` metódusban az itt látható módon:

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

Más triggerek és kötési típusok használatához telepítse az azokat tartalmazó NuGet-csomagot, és hívja meg a bővítményben megvalósított `Add<binding>`-bővítményi módszert. Ha például egy Azure Cosmos DB kötést szeretne használni, telepítse a `Microsoft.Azure.WebJobs.Extensions.CosmosDB`t, és hívja meg a `AddCosmosDB`, például a következőt:

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

Az alapszolgáltatások részét képező időzítő vagy fájlok kötésének használatához hívja meg a `AddTimers` vagy `AddFiles` bővítmény metódusait.

#### <a name="version-2x"></a>2\. verzió. *x*

Ezek az triggerek és kötési típusok a 2. verzióban szerepelnek. a `Microsoft.Azure.WebJobs` csomag *x* :

* Blob Storage
* Queue Storage
* Table Storage

Más triggerek és kötési típusok használatához telepítse az azokat tartalmazó NuGet-csomagot, és hívja meg a `Use<binding>` metódust a `JobHostConfiguration` objektumon. Ha például időzítő-triggert szeretne használni, telepítse a `Microsoft.Azure.WebJobs.Extensions`t, és hívja meg `UseTimers` a `Main` metódusban, ahogy az itt látható:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

A fájlok kötésének használatához telepítse a `Microsoft.Azure.WebJobs.Extensions`t, és hívja meg `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

A webjobs lehetővé teszi egy [`ExecutionContext`]kötését. Ezzel a kötéssel a [`ExecutionContext`] a függvény aláírása paraméterként érheti el. Az alábbi kód például a környezeti objektum használatával fér hozzá a Meghívási AZONOSÍTÓhoz, amellyel az adott függvény által létrehozott összes naplót összekapcsolhatja.  

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

A [`ExecutionContext`] kötésének folyamata az SDK verziójától függ.

#### <a name="version-3x"></a>3\. verzió. *x*

Hívja meg a `AddExecutionContextBinding` Extension metódust a `ConfigureWebJobs` metódusban az itt látható módon:

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

#### <a name="version-2x"></a>2\. verzió. *x*

A korábban említett `Microsoft.Azure.WebJobs.Extensions` csomag egy speciális kötési típust is biztosít, amelyet a `UseCore` metódus meghívásával regisztrálhat. Ez a kötés lehetővé teszi, hogy egy [`ExecutionContext`] paramétert definiáljon a függvény aláírásában, amely a következőképpen van engedélyezve:

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

Beállíthatja egyes eseményindítók és kötések viselkedését. A konfigurálásának folyamata az SDK verziójától függ.

* **3. verzió. *x*:** állítsa be a konfigurációt, ha a `Add<Binding>` metódust `ConfigureWebJobs`ban hívja meg.
* **2. verzió. *x*:** állítsa be a konfigurációt úgy, hogy a tulajdonságokat egy olyan konfigurációs objektumban állítja be, amelyet a `JobHost`ba.

Ezek a kötési beállítások egyenértékűek a [Host. JSON projektfájl](../azure-functions/functions-host-json.md) Azure Functionsban található beállításokkal.

A következő kötéseket állíthatja be:

* [Azure CosmosDB trigger](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs trigger](#event-hubs-trigger-configuration-version-3x)
* [Üzenetsor-tárolási trigger](#queue-storage-trigger-configuration)
* [SendGrid kötés](#sendgrid-binding-configuration-version-3x)
* [Service Bus trigger](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Az Azure CosmosDB trigger konfigurációja (3. verzió). *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a Azure Cosmos DB triggert:

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

További részletekért tekintse meg az [Azure CosmosDB-kötést](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) ismertető cikket.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs trigger konfigurációja (3. verzió). *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a Event Hubs triggert:

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

További részletekért tekintse meg a [Event Hubs kötést](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) ismertető cikket.

### <a name="queue-storage-trigger-configuration"></a>Várólista-tároló triggerének konfigurációja

A következő példák bemutatják, hogyan konfigurálhatja a várólista-tároló triggert:

#### <a name="version-3x"></a>3\. verzió. *x*

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

További részletekért tekintse meg a [várólista-tároló kötését](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) ismertető cikket.

#### <a name="version-2x"></a>2\. verzió. *x*

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

További részletekért tekintse meg a [Host. JSON v1. x hivatkozást](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid-kötési konfiguráció (3. verzió). *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a SendGrid kimeneti kötését:

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

További részletekért tekintse meg a [SendGrid-kötést](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) ismertető cikket.

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus trigger konfigurációja (3. verzió). *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a Service Bus triggert:

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

További részletekért tekintse meg a [Service Bus kötést](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) ismertető cikket.

### <a name="configuration-for-other-bindings"></a>Egyéb kötések konfigurálása

Egyes triggerek és kötési típusok saját egyéni konfigurációs típusokat határoznak meg. A fájl-triggerrel például megadhatja a figyelni kívánt gyökér elérési utat, ahogy az alábbi példákban is látható:

#### <a name="version-3x"></a>3\. verzió. *x*

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

#### <a name="version-2x"></a>2\. verzió. *x*

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

## <a name="binding-expressions"></a>Kötési kifejezések

Az attribútumok konstruktorának paraméterei között olyan kifejezéseket használhat, amelyek különböző forrásokból származó értékekre vannak feloldva. Például a következő kódban a `BlobTrigger` attribútum elérési útja létrehoz egy `filename`nevű kifejezést. Ha a kimeneti kötést használja, `filename` az kiváltó blob nevére lesz feloldva.

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

A kötési kifejezésekkel kapcsolatos további információkért tekintse meg a Azure Functions dokumentációjában található [kötési kifejezések és minták](../azure-functions/functions-bindings-expressions-patterns.md) című témakört.

### <a name="custom-binding-expressions"></a>Egyéni kötési kifejezések

Időnként meg kell adnia egy üzenetsor nevét, egy blob nevét vagy tárolóját, vagy egy kódot a kódban, nem pedig a rögzített kódolást. Előfordulhat például, hogy egy konfigurációs fájlban vagy környezeti változóban szeretné megadni a `QueueTrigger` attribútumhoz tartozó üzenetsor nevét.

Ezt úgy teheti meg, hogy egy `NameResolver` objektumot továbbít a `JobHostConfiguration` objektumba. Helyőrzőket is felvesz az trigger vagy a kötési attribútumok konstruktorában lévő paraméterekbe, és a `NameResolver` kódja biztosítja a helyőrzők helyett használandó tényleges értékeket. A helyőrzőket a százalék (%) alapján azonosíthatja. jelek, ahogy az itt látható:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód lehetővé teszi, hogy egy `logqueuetest` nevű várólistát használjon a tesztkörnyezetben, és egy `logqueueprod` az éles környezetben. A rögzített üzenetsor neve helyett egy bejegyzés nevét kell megadnia a `appSettings` gyűjteményben.

Van egy alapértelmezett `NameResolver`, amely akkor lép érvénybe, ha nem ad meg egy egyénit. Az alapértelmezett érték az Alkalmazásbeállítások vagy a környezeti változók értékeinek beolvasása.

A `NameResolver` osztály a várólista nevét a `appSettings`ból kapja meg, az itt látható módon:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>3\. verzió. *x*

A feloldót függőségi befecskendezés használatával konfigurálhatja. Ezek a minták a következő `using` utasítást igénylik:

```cs
using Microsoft.Extensions.DependencyInjection;
```

A feloldót úgy adhatja hozzá, hogy a [`ConfigureServices`] Extension metódust hívja meg [`HostBuilder`n ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), az alábbi példában látható módon:

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

#### <a name="version-2x"></a>2\. verzió. *x*

Adja át a `NameResolver` osztályt a `JobHost` objektumba az itt látható módon:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementálja `INameResolver` az Alkalmazásbeállítások értékének beolvasásához, ahogy az a példában látható. Ha közvetlenül használja a webjobs SDK-t, megírhat egy egyéni implementációt, amely helyőrző helyettesítő értékeket kap bármilyen forrástól.

## <a name="binding-at-runtime"></a>Kötés futásidőben

Ha a függvényben valamilyen műveletet kell végrehajtania, mielőtt olyan kötési attribútumot használ, mint például `Queue`, `Blob`vagy `Table`, használhatja a `IBinder` felületet.

A következő példa egy bemeneti üzenetsor-üzenetet hoz létre, és létrehoz egy új üzenetet ugyanazzal a tartalommal egy kimeneti várólistában. A kimeneti várólista nevét a függvény törzsében található kód állítja be.

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

További információkért lásd: a Azure Functions dokumentációjában a [futásidejű kötés](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) .

## <a name="binding-reference-information"></a>Kötési hivatkozási információ

Az Azure Functions dokumentációja az egyes kötési típusokra vonatkozó hivatkozási információkat tartalmazza. Az alábbi információkat az egyes kötési dokumentációban találja. (Ez a példa a tárolási üzenetsor alapján történik.)

* [Csomagok](../azure-functions/functions-bindings-storage-queue.md). A telepíteni kívánt csomag tartalmazza a kötés támogatását egy webjobs SDK-projektben.
* [Példák](../azure-functions/functions-bindings-storage-queue-trigger.md). Kód minták. Az C# osztály könyvtára példa a webjobs SDK-ra. Csak hagyja ki a `FunctionName` attribútumot.
* [Attribútumok](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). A kötési típushoz használandó attribútumok
* [Konfiguráció](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Az attribútum tulajdonságainak és a konstruktor paramétereinek magyarázata.
* [Használat](../azure-functions/functions-bindings-storage-queue-trigger.md#usage) – A kötéshez köthető típusok, valamint a kötés működésével kapcsolatos információk. Például: lekérdezési algoritmus, méreg-várólista feldolgozása.
  
A kötési hivatkozási cikkek listáját a Azure Functions [Eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) című cikkben, a "támogatott kötések" című részben találja. Ebben a listában a HTTP-t, webhookokat és Event Grid kötéseket csak Azure Functions, a webjobs SDK nem támogatja.

## <a name="disable-attribute"></a>Attribútum letiltása 

A [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum lehetővé teszi annak szabályozását, hogy egy függvény aktiválható-e. 

Ha a következő példában a `Disable_TestJob` alkalmazás értéke `1` vagy `True` (kis-és nagybetűk megkülönböztetése), a függvény nem fog futni. Ebben az esetben a futtatókörnyezet létrehoz egy *"functions. TestJob" nevű log Message függvényt, amely le van tiltva*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Ha megváltoztatja az Alkalmazásbeállítások értékét a Azure Portalban, a Webjobs újraindul, hogy felvegye az új beállítást.

Az attribútum deklarálható a paraméter, a metódus vagy az osztály szintjén. A beállítás neve is tartalmazhat kötési kifejezéseket.

## <a name="timeout-attribute"></a>Időtúllépési attribútum

A [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribútum azt eredményezi, hogy a függvényt megszakítja a rendszer, ha az adott időtartamon belül nem fejeződik be. A következő példában a függvény egy napig fog futni az időkorlát attribútum nélkül. Az időtúllépés miatt a függvény 15 másodperc után meg lesz szakítva.

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

Az időtúllépés attribútumot az osztály vagy a metódus szintjén alkalmazhatja, és a `JobHostConfiguration.FunctionTimeout`használatával globális időtúllépést is megadhat. Az osztály szintű vagy a metódus szintű időtúllépések felülbírálják a globális időtúllépéseket.

## <a name="singleton-attribute"></a>Egyszeres attribútum

A [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribútum biztosítja, hogy a függvény csak egy példánya fusson, még akkor is, ha a gazda webalkalmazásnak több példánya is van. Ezt az [elosztott zárolás](#viewing-lease-blobs)használatával végezheti el.

Ebben a példában a `ProcessImage` függvény egyetlen példánya fut a megadott időpontban:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Egyes eseményindítók beépített támogatást biztosítanak a párhuzamosságok kezeléséhez:

* **QueueTrigger**. `JobHostConfiguration.Queues.BatchSize` beállítása `1`re.
* **ServiceBusTrigger**. `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` beállítása `1`re.
* **FileTrigger**. `FileProcessor.MaxDegreeOfParallelism` beállítása `1`re.

Ezekkel a beállításokkal biztosíthatja, hogy a függvény önállóként fusson egyetlen példányon. Annak biztosítása érdekében, hogy csak a függvény egyetlen példánya fusson, amikor a webalkalmazás több példányra is méretezhető, alkalmazza a figyelő szintű önálló zárolást a függvényre (`[Singleton(Mode = SingletonMode.Listener)]`). A JobHost indításakor a figyelő zárolásai is beszerezhetők. Ha három Felskálázási példány indul el egyszerre, csak az egyik példány szerzi be a zárolást, és csak egy figyelő indul el.

### <a name="scope-values"></a>Hatókör értékei

Egy egypéldányos *hatókör-kifejezés/érték* is megadható. A kifejezés/érték biztosítja, hogy egy adott hatókörben lévő függvény összes végrehajtása szerializálva lesz. A részletesebb zárolás ily módon történő megvalósítása lehetővé teszi a függvény bizonyos szintű párhuzamosságát, miközben más, a követelmények által diktált meghívások szerializálására is képes. A következő kódban például a hatókör kifejezés a bejövő üzenet `Region` értékéhez kötődik. Ha a várólista három üzenetet tartalmaz a keleti, a keleti és a nyugati régiókban, akkor a keleti régióval rendelkező üzenetek sorosan futnak, miközben a West régióval párhuzamosan futtatott üzenet a Kelettel együtt fut.

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

A zárolás alapértelmezett hatóköre `SingletonScope.Function`, ami azt jelenti, hogy a zárolási hatókör (a blob címbérleti útvonala) a teljes függvény nevéhez van kötve. A függvények közötti zároláshoz adja meg a `SingletonScope.Host`, és használja a hatókör-azonosító nevét, amely ugyanaz, mint az összes olyan függvény, amelyet nem szeretne egyszerre futtatni. A következő példában `AddItem` vagy `RemoveItem` egyszerre csak egy példánya fut:

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

### <a name="viewing-lease-blobs"></a>Címbérleti Blobok megtekintése

A webjobs SDK az [Azure Blob bérleteit](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) használja a borítók alatt az elosztott zárolás megvalósításához. Az Egypéldányos bérletben használt Blobok a `AzureWebJobsStorage` Storage-fiók `azure-webjobs-host` tárolójában találhatók a "zárolások" elérési úton. Például előfordulhat, hogy a korábban bemutatott első `ProcessImage` példa címbérleti blobjának elérési útja `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Az összes elérési út tartalmazza a JobHost AZONOSÍTÓját, ebben az esetben a 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Aszinkron függvények

További információ az aszinkron függvények kódolásáról: [Azure functions dokumentáció](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Visszavonási tokenek

A lemondási tokenek kezelésével kapcsolatos információkért tekintse meg a [lemondási tokenek](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)Azure functions dokumentációját, valamint a zökkenőmentes leállítást.

## <a name="multiple-instances"></a>Több példány

Ha a webalkalmazás több példányon fut, folyamatos Webjobs fut az egyes példányokon, és figyeli az eseményindítókat és hívja a függvényeket. A különféle trigger-kötések úgy vannak kialakítva, hogy hatékonyan megosszák egymással a különböző példányokat, így a több példányra történő horizontális felskálázás lehetővé teszi a nagyobb terhelés kezelését.

Míg egyes eseményindítók kettős feldolgozást eredményezhetnek, a várólista-és blob-tároló-eseményindítók automatikusan megakadályozzák, hogy egy függvény egynél többször dolgozza fel a várólista-üzenetet vagy a blobot. További információ: az Azure Functions dokumentációban megjelenő [azonos adatbevitel megtervezése](../azure-functions/functions-idempotent.md) .

Az időzítő-trigger automatikusan biztosítja, hogy az időzítő csak egy példányban fusson, így egy adott ütemezett időpontban nem fog egynél több függvényt futtatni.

Ha biztosítani szeretné, hogy a függvény csak egy példánya fusson, akkor is, ha a gazda webalkalmazás több példánya is létezik, használhatja a [`Singleton`](#singleton-attribute) attribútumot.

## <a name="filters"></a>Szűrők

A függvények szűrői (előzetes verzió) lehetőséget biztosítanak a webjobs-végrehajtási folyamat saját logikával való testreszabására. A szűrők a [ASP.net Core szűrőkhöz](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)hasonlítanak. Ezeket a függvények vagy osztályok által alkalmazott deklaratív attribútumokként implementálhatja. További információ: [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Javasoljuk a ASP.NET fejlesztett naplózási keretrendszert. Az [első lépéseket](webjobs-sdk-get-started.md) ismertető cikk bemutatja, hogyan használhatja azt. 

### <a name="log-filtering"></a>Naplózási szűrés

Minden `ILogger`-példány által létrehozott naplóhoz társított `Category` és `Level`tartozik. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) egy enumerálás, és az egész szám kód relatív fontosságot jelez:

|LogLevel    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|Nincs        | 6 |

Az egyes kategóriák egymástól függetlenül szűrhetők egy adott [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Előfordulhat például, hogy meg szeretné jeleníteni az összes naplót a blob-trigger feldolgozásához, de csak `Error` és magasabb minden más számára.

#### <a name="version-3x"></a>3\. verzió. *x*

3\. verzió. Az SDK *x* -je a .net Core-ba épített szűrésre támaszkodik. A `LogCategories` osztály lehetővé teszi adott függvények, eseményindítók vagy felhasználók kategóriáinak definiálását. Emellett meghatározott gazdagépi állapotokhoz is definiál szűrőket, például `Startup` és `Results`. Ez lehetővé teszi a naplózási kimenet finomhangolását. Ha a megadott kategóriákban nem találhatók egyezések, a szűrő visszakerül a `Default` értékre, amikor eldönti, hogy szűrni kívánja-e az üzenetet.

`LogCategories` a következő using utasítást kell használnia:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Az alábbi példa olyan szűrőt állít össze, amely alapértelmezés szerint a `Warning` szinten szűri az összes naplót. A `Function` és `results` kategóriák (a 2. verzióban `Host.Results`nak felel meg. *x*) a `Error` szinten vannak szűrve. A szűrő összehasonlítja az aktuális kategóriát az `LogCategories`-példány összes regisztrált szintjével, és kiválasztja a leghosszabb egyezést. Ez azt jelenti, hogy a `Host.Triggers` regisztrált `Debug` szint megfelel `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi a szélesebb kategóriák szabályozását anélkül, hogy hozzá kellene adni egyet.

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

#### <a name="version-2x"></a>2\. verzió. *x*

A 2. verzióban. Az SDK *x* -je a `LogCategoryFilter` osztály használatával szabályozza a szűrést. A `LogCategoryFilter` `Default` tulajdonsága `Information`kezdeti értékkel rendelkezik, ami azt jelenti, hogy a `Information`, `Warning`, `Error`vagy `Critical` szinten lévő összes üzenet naplózva van, de a `Debug` vagy `Trace` szinten lévő üzenetek szűrve lesznek.

A 3. verzióban `LogCategories`. *x*, a `CategoryLevels` tulajdonság lehetővé teszi, hogy meghatározott kategóriáknál adja meg a naplózási szinteket, így a naplózási kimenet finomhangolása is megadható. Ha a `CategoryLevels` szótárban nem található egyezés, a szűrő visszakerül a `Default` értékre, amikor a rendszer eldönti, hogy szűrni kívánja-e az üzenetet.

A következő példa egy szűrőt állít össze, amely alapértelmezés szerint a `Warning` szinten lévő összes naplót szűri. A `Function` és `Host.Results` kategóriák a `Error` szinten vannak szűrve. A `LogCategoryFilter` összehasonlítja az aktuális kategóriát az összes regisztrált `CategoryLevels`, és kiválasztja a leghosszabb egyezést. Így a `Host.Triggers`hoz regisztrált `Debug` szint megfelel `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi a szélesebb kategóriák szabályozását anélkül, hogy hozzá kellene adni egyet.

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

### <a name="custom-telemetry-for-application-insights"></a>Application Insights egyéni telemetria

A [Application Insights](../azure-monitor/app/app-insights-overview.md) egyéni telemetria megvalósításának folyamata az SDK verziójától függ. A Application Insights konfigurálásának megismeréséhez tekintse meg [Application Insights naplózás hozzáadása](webjobs-sdk-get-started.md#add-application-insights-logging)című témakört.

#### <a name="version-3x"></a>3\. verzió. *x*

Mivel a 3. verzió. a webjobs SDK *x* -je a .net Core általános gazdagépre támaszkodik, és az egyéni telemetria-gyár már nincs megadva. A függőségi befecskendezés használatával azonban egyéni telemetria is hozzáadhat a folyamathoz. Az ebben a szakaszban szereplő példákhoz a következő `using` utasítások szükségesek:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A [`ITelemetryInitializer`] következő egyéni implementálása lehetővé teszi saját [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) hozzáadását az alapértelmezett [`TelemetryConfiguration`hoz ].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

A Build [`ConfigureServices`] meghívásával adja hozzá az egyéni [`ITelemetryInitializer`] a folyamathoz.

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

A [`TelemetryConfiguration`] létrehozásakor a rendszer a [`ITelemetryInitializer`] összes regisztrált típusát tartalmazza. További információ: [Application INSIGHTS API egyéni eseményekhez és mérőszámokhoz](../azure-monitor/app/api-custom-events-metrics.md).

A 3. verzióban. *x*, már nem kell kiürítenie a [`TelemetryClient`] , amikor a gazdagép leáll. A .NET Core függőségi befecskendező rendszer automatikusan elveti a regisztrált `ApplicationInsightsLoggerProvider`, amely kiüríti a [`TelemetryClient`].

#### <a name="version-2x"></a>2\. verzió. *x*

A 2. verzióban. *x*, a Webjobs SDK Application Insights-szolgáltatója belsőleg létrehozott [`TelemetryClient`] a [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)-t használja. Ha a Application Insights végpont nem érhető el, vagy a bejövő kérelmek szabályozása folyamatban van, a csatorna [a webalkalmazás fájlrendszerében menti a kérelmeket, és később újra elküldi őket](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

A [`TelemetryClient`] `ITelemetryClientFactory`t megvalósító osztály hozza létre. Alapértelmezés szerint ez a [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Ha módosítani szeretné a Application Insights folyamat bármely részét, megadhatja a saját `ITelemetryClientFactory`, és a gazdagép az osztályt fogja használni a [`TelemetryClient`]létrehozásához. Ez a kód például felülbírálja `DefaultTelemetryClientFactory` `ServerTelemetryChannel`tulajdonságának módosítását:

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

Az `SamplingPercentageEstimatorSettings` objektum [adaptív mintavételezést](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)konfigurál. Ez azt jelenti, hogy bizonyos nagy mennyiségű forgatókönyvekben az alkalmazás-elemzések a telemetria-adatok kiválasztott részhalmazát küldi el a kiszolgálónak.

A telemetria-előállító létrehozása után átadja azt a Application Insights naplózási szolgáltatónak:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Következő lépések

Ez a cikk kódrészleteket adott meg, amelyek bemutatják, hogyan kezelheti a webjobs SDK-val való használat gyakori forgatókönyveit. A teljes mintákat lásd: [Azure-webjobs-SDK-Samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
