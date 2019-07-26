---
title: A webjobs SDK használata – Azure
description: További információ a webjobs SDK kódjának írásához. Az Azure-szolgáltatásokban és külső szolgáltatásokban tárolt adatokhoz hozzáférő eseményvezérelt háttér-feldolgozási feladatok hozhatók létre.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 3ba8a8e5922c012b93ab19a5859aab5c31d35b2b
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424177"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK használata eseményvezérelt háttérben végzett feldolgozáshoz

Ez a cikk útmutatást nyújt a Azure WebJobs SDK-val való együttműködéshez. A webjobs-feladatok azonnal elérhetővé tételéhez lásd: Ismerkedés [a Azure WEBJOBS SDK-val az eseményvezérelt háttér-feldolgozáshoz](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Webjobs SDK-verziók

Ezek a 3. verzió közötti fő különbségek. *x* és 2-es verzió. a webjobs SDK *x* -je:

* 3\. verzió. az *x* támogatja a .net Core-ot.
* A 3. verzióban. *x*, explicit módon telepítenie kell a webjobs SDK által igényelt tárolási kötési bővítményt. A 2. verzióban. *x*, a tárolási kötések szerepeltek az SDK-ban.
* Visual Studio-eszközök a .NET Core-hoz (3) *x*) a projektek eltérnek a .NET-keretrendszer (2) eszközeitől. *x*) projektek. További információ: webjobs [-feladatok fejlesztése és üzembe helyezése a Visual Studio-Azure app Service használatával](webjobs-dotnet-deploy-vs.md).

Ha lehetséges, a 3. verzióra vonatkozó példákat is meg kell adni. *x* és 2-es verzió. *x*.

> [!NOTE]
> [Azure functions](../azure-functions/functions-overview.md) a webjobs SDK-ra épül, és ez a cikk néhány témakörre mutató hivatkozásokat tartalmaz Azure functions dokumentációhoz. Figyelje meg a függvények és a webjobs SDK közötti különbségeket:
> * Azure Functions 2. verzió. az *x* a webjobs SDK 3-as verziójának felel meg. *x*és Azure functions 1. az *x* a webjobs SDK 2-nak felel meg. *x*. A forráskód-Tárházak a webjobs SDK-számozást használják.
> * Azure functions C# Class-kódtárak például a webjobs SDK-kód, például a webjobs SDK `FunctionName` -projektben nem szükséges attribútum.
> * Bizonyos kötési típusok csak olyan függvényekben támogatottak, mint például a HTTP (webhookok) és a Event Grid (amely HTTP-alapú).
>
> További információ: [a Webjobs SDK és a Azure functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)összevetése.

## <a name="webjobs-host"></a>Webjobs-gazdagép

A gazdagép a függvények futásidejű tárolója.  Ez figyeli az eseményindítókat és a hívási függvényeket. A 3. verzióban. *x*a gazdagép a (z `IHost`) implementációja. A 2. verzióban. *x*, használja az `JobHost` objektumot. Hozzon létre egy gazdagép-példányt a kódban, és írja be a kódot a viselkedésének testre szabásához.

Ez kulcsfontosságú különbség a webjobs SDK közvetlen használata és a Azure Functions használatával közvetve. Azure Functions a szolgáltatás vezérli a gazdagépet, és nem tudja testreszabni a gazdagépet a kód írásával. Azure Functions lehetővé teszi a gazdagép viselkedésének testreszabását a Host. JSON fájl beállításain keresztül. Ezek a beállítások karakterláncok, nem pedig kódok, és ez korlátozza az elvégezhető testreszabások fajtáit.

### <a name="host-connection-strings"></a>Gazdagép-kapcsolatok karakterláncai

A webjobs SDK megkeresi az Azure Storage-t, és Azure Service Bus a helyi. Settings. JSON fájlban lévő kapcsolódási karakterláncokat a helyileg futtatott vagy az Azure-ban futtatott Webjobs környezetében. Alapértelmezés szerint a nevű `AzureWebJobsStorage` tárolási kapcsolatok karakterlánc-beállításának megadása kötelező.  

2\. verzió. Az SDK *x* -je lehetővé teszi, hogy saját nevét használja ezeket a kapcsolatok sztringeket, vagy máshol tárolja őket. A kódban szereplő neveket a használatával is megadhatja, ahogy az [`JobHostConfiguration`]itt látható:

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
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`a naplók kimenetének maximalizálása érdekében. |
| `Queues.MaxPollingInterval`  | A várólista-metódusok azonnali indításának alacsony értéke.  |
| `Singleton.ListenerLockPeriod` | 15 másodperc a gyors iterációs fejlesztésben való segítségnyújtáshoz. |

A fejlesztési mód engedélyezésének folyamata az SDK verziójától függ. 

#### <a name="version-3x"></a>3\. verzió. *x*

3\. verzió. az *x* a standard ASP.net Core API-kat használja. Hívja meg a metódust a [példányon.`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) Adjon át egy nevű `development`karakterláncot az alábbi példában látható módon:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>2\. verzió. *x*

Az `JobHostConfiguration` osztály olyan `UseDevelopmentSettings` metódussal rendelkezik, amely lehetővé teszi a fejlesztési módot.  Az alábbi példa bemutatja, hogyan használhatók a fejlesztői beállítások. Ha helyileg futtatja a `true` `AzureWebJobsEnv` `Development`visszaállítást, állítson be egy nevű helyi környezeti változót az értékkel. `config.IsDevelopment`

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

A 3. verzióban. *x*, a kapcsolat korlátja alapértelmezés szerint a végtelen kapcsolatok. Ha valamilyen okból módosítania kell ezt a korlátot, használhatja [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) az [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) osztály tulajdonságát.

A 2. verzióban. *x*, a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API használatával szabályozható a gazdagépek egyidejű kapcsolatainak száma. 2\. *x*, a webjobs-gazdagép elindítása előtt növelje ezt az értéket az alapértelmezett 2 értékről.

Minden olyan kimenő HTTP-kérelem, amelyet egy függvényből végez `HttpClient` a `ServicePointManager`flow használatával. Miután elérte a értékét, `DefaultConnectionLimit` `ServicePointManager` a megkezdi az üzenetsor-kezelési kérelmek küldését. Tegyük `DefaultConnectionLimit` fel, hogy a 2 értékre van állítva, és a kód 1 000 http-kéréseket tesz elérhetővé. Kezdetben csak két kérelem engedélyezett az operációs rendszerre. A többi 998 várólistára kerül, amíg el nem helyezi a helyet. Ez azt jelenti `HttpClient` , hogy előfordulhat, hogy időtúllépés történt, mert úgy tűnik, hogy elvégezte a kérést, de a kérést soha nem küldte el az operációs rendszer a célkiszolgálóra. Tehát előfordulhat, hogy olyan viselkedést lát, amely nem logikus: a `HttpClient` helyi 10 másodpercet vesz igénybe a kérelem teljesítéséhez, de a szolgáltatás a 200 MS-ban minden kérést visszaad. 

A ASP.NET-alkalmazások `Int32.MaxValue`alapértelmezett értéke, és ez valószínűleg jól működik az alapszintű vagy magasabb app Service tervben futó webjobs esetében. A webjobs-nek jellemzően az Always On beállításra van szüksége, amelyet csak az alapszintű és a magasabb App Service csomagok támogatnak.

Ha a Webjobs ingyenes vagy megosztott App Service-csomagban fut, az alkalmazást a App Service sandbox korlátozza, amely jelenleg [300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)-as korláttal rendelkezik. Ha nem kötött kapcsolati korlátot `ServicePointManager`használ, annál valószínűbb, hogy a rendszer elérte a sandbox kapcsolati küszöbértékét, és a hely leáll. Ebben az esetben előfordulhat, `DefaultConnectionLimit` hogy az alacsonyabb értékre (például 50 vagy 100) való beállítás megakadályozza, hogy ez megtörténjen, és továbbra is lehetővé teszi a megfelelő átviteli sebességet.

A beállítást a HTTP-kérelmek elfogadását megelőzően kell konfigurálni. Emiatt a webjobs-gazdagépnek nem szabad automatikusan módosítania a beállítást. Lehetnek HTTP-kérelmek, amelyek a gazdagép elindítása előtt történnek, ami váratlan viselkedéshez vezethet. A legjobb módszer az `Main` `JobHost`, ha a metódusban azonnal beállítja az értéket az inicializálás előtt, ahogy az itt látható:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Triggerek

A függvényeknek nyilvános metódusoknak kell lenniük, és rendelkezniük kell egy [`NoAutomaticTrigger`](#manual-triggers) trigger attribútummal vagy az attribútummal.

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

Az `QueueTrigger` attribútum közli a futtatókörnyezettel, hogy meghívja a függvényt, valahányszor üzenetsor `myqueue-items` -üzenet jelenik meg a várólistán. Az `Blob` attribútum közli a futtatókörnyezettel, hogy a várólista-üzenettel beolvassa a blobot a *Sample-workitems* tárolóban. A várólista-üzenetnek a `myQueueItem` paraméterben szereplő függvénynek átadott tartalma a blob neve.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Manuális eseményindítók

A függvények manuális elindításához használja az `NoAutomaticTrigger` attribútumot az itt látható módon:

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

A 3. verzióban. *x*, a tárolási kötések beletartoznak a `Microsoft.Azure.WebJobs.Extensions.Storage` csomagba. `AddAzureStorage` Hívja`ConfigureWebJobs` meg a metódus kiterjesztési metódusát az itt látható módon:

```cs
static void Main()
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
        host.Run();
    }
}
```

Más triggerek és kötési típusok használatához telepítse az azokat tartalmazó NuGet-csomagot, és hívja `Add<binding>` meg a bővítményben megvalósított kiterjesztési módszert. Ha például Azure Cosmos db kötést szeretne használni, telepítse `Microsoft.Azure.WebJobs.Extensions.CosmosDB` és hívja `AddCosmosDB`meg a következőt:

```cs
static void Main()
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
        host.Run();
    }
}
```

Az alapszolgáltatások részét képező időzítő vagy fájlok kötésének használatához hívja meg a `AddTimers` vagy `AddFiles` a kiterjesztés metódusait.

#### <a name="version-2x"></a>2\. verzió. *x*

Ezek az triggerek és kötési típusok a 2. verzióban szerepelnek.  a`Microsoft.Azure.WebJobs` csomag x:

* Blob Storage
* Queue Storage
* Táblatároló

Más triggerek és kötési típusok használatához telepítse az azokat tartalmazó NuGet-csomagot, és hívja `Use<binding>` meg a metódust az `JobHostConfiguration` objektumon. Ha például időzítő-triggert szeretne használni, akkor az itt látható `Microsoft.Azure.WebJobs.Extensions` módon telepítse `UseTimers` és hívja `Main` meg a metódust:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

A fájlok kötésének használatához telepítse `Microsoft.Azure.WebJobs.Extensions` és hívja `UseFiles`meg a következőt:.

### <a name="executioncontext"></a>ExecutionContext

A webjobs segítségével kötést hozhat [`ExecutionContext`]létre. Ezzel a kötéssel elérheti a [`ExecutionContext`] paramétert a függvény aláírásában. Az alábbi kód például a környezeti objektum használatával fér hozzá a Meghívási AZONOSÍTÓhoz, amellyel az adott függvény által létrehozott összes naplót összekapcsolhatja.  

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

A kötésének [`ExecutionContext`] folyamata az SDK verziójától függ.

#### <a name="version-3x"></a>3\. verzió. *x*

`AddExecutionContextBinding` Hívja`ConfigureWebJobs` meg a metódus kiterjesztési metódusát az itt látható módon:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>2\. verzió. *x*

A `Microsoft.Azure.WebJobs.Extensions` korábban említett csomag egy speciális kötési típust is biztosít, amelyet a `UseCore` metódus meghívásával regisztrálhat. Ez a kötés lehetővé teszi, [`ExecutionContext`] hogy Definiáljon egy paramétert a függvény aláírásában, amely az alábbihoz hasonlóan van engedélyezve:

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

* **3. verzió. *x*:** Állítsa be a konfigurációt, `Add<Binding>` `ConfigureWebJobs`ha a metódus hívása megtörténik.
* **2. verzió. *x*:** Állítsa be a konfigurációt egy olyan konfigurációs objektum tulajdonságainak beállításával, amelybe be van adva `JobHost`.

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
static void Main()
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

        host.Run();
    }
}
```

További részletekért tekintse meg az [Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) -kötést ismertető cikket.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs trigger konfigurációja (3. verzió). *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a Event Hubs triggert:

```cs
static void Main()
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

        host.Run();
    }
}
```

További részletekért tekintse meg a [Event Hubs kötést](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) ismertető cikket.

### <a name="queue-storage-trigger-configuration"></a>Várólista-tároló triggerének konfigurációja

A következő példák bemutatják, hogyan konfigurálhatja a várólista-tároló triggert:

#### <a name="version-3x"></a>3\. verzió. *x*

```cs
static void Main()
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

        host.Run();
    }
}
```

További részletekért tekintse meg a [várólista-tároló kötését](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) ismertető cikket.

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
static void Main()
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

        host.Run();
    }
}
```

További részletekért tekintse meg a [SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) -kötést ismertető cikket.

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus trigger konfigurációja (3. verzió). *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a Service Bus triggert:

```cs
static void Main()
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

        host.Run();
    }
}
```

További részletekért tekintse meg a [Service Bus kötést](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) ismertető cikket.

### <a name="configuration-for-other-bindings"></a>Egyéb kötések konfigurálása

Egyes triggerek és kötési típusok saját egyéni konfigurációs típusokat határoznak meg. A fájl-triggerrel például megadhatja a figyelni kívánt gyökér elérési utat, ahogy az alábbi példákban is látható:

#### <a name="version-3x"></a>3\. verzió. *x*

```cs
static void Main()
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

        host.Run();
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

Az attribútumok konstruktorának paraméterei között olyan kifejezéseket használhat, amelyek különböző forrásokból származó értékekre vannak feloldva. A következő kódban például az `BlobTrigger` attribútum elérési útja létrehoz egy nevű `filename`kifejezést. A kimeneti kötés `filename` használatakor a rendszer az indító blob nevét oldja fel.

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

Időnként meg kell adnia egy üzenetsor nevét, egy blob nevét vagy tárolóját, vagy egy kódot a kódban, nem pedig a rögzített kódolást. Előfordulhat például, hogy egy konfigurációs fájlban vagy környezeti változóban szeretné megadni `QueueTrigger` az attribútum üzenetsor-nevét.

Ezt úgy teheti meg, hogy `NameResolver` egy objektumot továbbít az `JobHostConfiguration` objektumba. A helyőrzőket belefoglalja az trigger vagy a kötési attribútumok konstruktorához tartozó `NameResolver` paraméterekbe, és a kód megadja a helyőrzők helyett használandó tényleges értékeket. A helyőrzőket a százalék (%) alapján azonosíthatja. jelek, ahogy az itt látható:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód lehetővé teszi egy nevű `logqueuetest` várólista használatát a tesztkörnyezetben, az `logqueueprod` egyiket pedig éles környezetben. A rögzített üzenetsor neve helyett meg kell adnia egy bejegyzés nevét a `appSettings` gyűjteményben.

Az alapértelmezett érték `NameResolver` akkor lép érvénybe, ha nem ad meg egy egyénit. Az alapértelmezett érték az Alkalmazásbeállítások vagy a környezeti változók értékeinek beolvasása.

Az osztály beolvassa az üzenetsor `appSettings`nevét a alkalmazásból, ahogy az itt látható: `NameResolver`

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

A feloldót a [`ConfigureServices`] bővítmény [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)metódusának meghívásával adja hozzá a következő példához hasonlóan:

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

Adja át az `JobHost` osztálytazobjektumnakaz`NameResolver` itt látható módon:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure functions implementálja `INameResolver` az Alkalmazásbeállítások értékének beolvasását, ahogy az a példában látható. Ha közvetlenül használja a webjobs SDK-t, megírhat egy egyéni implementációt, amely helyőrző helyettesítő értékeket kap bármilyen forrástól.

## <a name="binding-at-runtime"></a>Kötés futásidőben

Ha a függvényben valamilyen műveletet kell végrehajtania, `Queue` `Blob`mielőtt olyan kötési attribútumot használ, mint a, `Table`vagy a, használhatja a `IBinder` felületet.

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

* [Csomagok](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). A telepíteni kívánt csomag tartalmazza a kötés támogatását egy webjobs SDK-projektben.
* [Példák](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Kód minták. Az C# osztály könyvtára példa a webjobs SDK-ra. Csak hagyja ki `FunctionName` az attribútumot.
* [Attribútumok](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). A kötési típushoz használandó attribútumok
* [Konfiguráció](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Az attribútum tulajdonságainak és a konstruktor paramétereinek magyarázata.
* [Használat](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) – A kötéshez köthető típusok, valamint a kötés működésével kapcsolatos információk. Például: lekérdezési algoritmus, méreg-várólista feldolgozása.
  
A kötési hivatkozási cikkek listáját a Azure Functions [Eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) című cikkben, a "támogatott kötések" című részben találja. Ebben a listában a HTTP-t, webhookokat és Event Grid kötéseket csak Azure Functions, a webjobs SDK nem támogatja.

## <a name="disable-attribute"></a>Attribútum letiltása 

Az [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum lehetővé teszi annak szabályozását, hogy egy függvény aktiválható-e. 

Ha a következő példában az `Disable_TestJob` Alkalmazásbeállítások `1` értéke vagy (kis-és `True` nagybetűk megkülönböztetése), a függvény nem fog futni. Ebben az esetben a futtatókörnyezet létrehoz egy *"functions. TestJob"* nevű log Message függvényt, amely le van tiltva.

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

Az [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribútum azt eredményezi, hogy a függvényt a rendszer megszakítja, ha az adott időtartamon belül nem fejeződik be. A következő példában a függvény egy napig fog futni az időkorlát attribútum nélkül. Az időtúllépés miatt a függvény 15 másodperc után meg lesz szakítva.

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

Az időtúllépés attribútumot az osztály vagy a metódus szintjén is alkalmazhatja, és a használatával `JobHostConfiguration.FunctionTimeout`globális időtúllépést is megadhat. Az osztály szintű vagy a metódus szintű időtúllépések felülbírálják a globális időtúllépéseket.

## <a name="singleton-attribute"></a>Egyszeres attribútum

Az [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribútum biztosítja, hogy a függvény csak egy példánya fusson, még akkor is, ha a gazda webalkalmazásnak több példánya is van. Ezt az [elosztott zárolás](#viewing-lease-blobs)használatával végezheti el.

Ebben a példában a `ProcessImage` függvény csak egyetlen példánya fut le egy adott időpontban:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Egyes eseményindítók beépített támogatást biztosítanak a párhuzamosságok kezeléséhez:

* **QueueTrigger**. Állítsa `JobHostConfiguration.Queues.BatchSize` a`1`következőre:.
* **ServiceBusTrigger**. Állítsa `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` a`1`következőre:.
* **FileTrigger**. Állítsa `FileProcessor.MaxDegreeOfParallelism` a`1`következőre:.

Ezekkel a beállításokkal biztosíthatja, hogy a függvény önállóként fusson egyetlen példányon. Annak biztosítása érdekében, hogy csak a függvény egyetlen példánya fusson, amikor a webalkalmazás több példányra is kiterjed, a Function (`[Singleton(Mode = SingletonMode.Listener)]`) figyelő-szintű önálló zárolását alkalmazza. A JobHost indításakor a figyelő zárolásai is beszerezhetők. Ha három Felskálázási példány indul el egyszerre, csak az egyik példány szerzi be a zárolást, és csak egy figyelő indul el.

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

A zárolás `SingletonScope.Function`alapértelmezett hatóköre, vagyis a zárolási hatókör (a blob címbérleti útvonala) a teljes függvény nevéhez van kötve. A függvények közötti zároláshoz adja `SingletonScope.Host` meg és használja a hatókör-azonosító nevét, amely az összes olyan függvénynél azonos, amelyet nem szeretne egyszerre futtatni. A következő példában csak egy példánya `AddItem` vagy `RemoveItem` fut egyszerre:

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

A webjobs SDK az [Azure Blob bérleteit](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) használja a borítók alatt az elosztott zárolás megvalósításához. Az Egypéldányos `azure-webjobs-host` bérletet használó Blobok a `AzureWebJobsStorage` Storage-fiókban található tárolóban találhatók a "zárolások" elérési úton. Előfordulhat például, hogy a korábban bemutatott első `ProcessImage` példa címbérleti blobjának elérési útja a következő lehet `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`:. Az összes elérési út tartalmazza a JobHost AZONOSÍTÓját, ebben az esetben a 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Aszinkron függvények

További információ az aszinkron függvények kódolásáról: [Azure functions dokumentáció](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Visszavonási tokenek

A lemondási tokenek kezelésével kapcsolatos információkért tekintse meg a lemondási tokenek Azure Functions dokumentációját, [valamint](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)a zökkenőmentes leállítást.

## <a name="multiple-instances"></a>Több példány

Ha a webalkalmazás több példányon fut, folyamatos Webjobs fut az egyes példányokon, és figyeli az eseményindítókat és hívja a függvényeket. A különféle trigger-kötések úgy vannak kialakítva, hogy hatékonyan megosszák egymással a különböző példányokat, így a több példányra történő horizontális felskálázás lehetővé teszi a nagyobb terhelés kezelését.

A várólista és a blob-eseményindítók automatikusan megakadályozzák, hogy egy függvény egynél többször dolgozza fel a várólista-üzeneteket vagy a blobokat. a függvényeknek nem kell idempotens.

Az időzítő-trigger automatikusan biztosítja, hogy az időzítő csak egy példányban fusson, így egy adott ütemezett időpontban nem fog egynél több függvényt futtatni.

Ha azt szeretné, hogy a függvény csak egy példánya fusson, akkor is, ha a gazda webalkalmazás több példánya van, használhatja az [`Singleton`](#singleton-attribute) attribútumot.

## <a name="filters"></a>Szűrők

A függvények szűrői (előzetes verzió) lehetőséget biztosítanak a webjobs-végrehajtási folyamat saját logikával való testreszabására. A szűrők a [ASP.net Core szűrőkhöz](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)hasonlítanak. Ezeket a függvények vagy osztályok által alkalmazott deklaratív attribútumokként implementálhatja. További információ: [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Javasoljuk a ASP.NET fejlesztett naplózási keretrendszert. Az [első lépéseket](webjobs-sdk-get-started.md) ismertető cikk bemutatja, hogyan használhatja azt. 

### <a name="log-filtering"></a>Naplózási szűrés

A `ILogger` példányok által létrehozott összes naplóhoz társítva `Level` `Category` és. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)egy enumerálás, és az egész szám kód relatív fontosságot jelez:

|LogLevel    |Kód|
|------------|---|
|Híváslánc       | 0 |
|Hibakeresés       | 1 |
|Information | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|Nincsenek        | 6 |

Az egyes kategóriák külön is szűrhetők [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Előfordulhat például, hogy meg szeretné jeleníteni az összes naplót a blob-triggerek `Error` feldolgozásához, de minden más esetében csak a magasabbra.

#### <a name="version-3x"></a>3\. verzió. *x*

3\. verzió. Az SDK *x* -je a .net Core-ba épített szűrésre támaszkodik. A `LogCategories` osztály lehetővé teszi adott függvények, eseményindítók vagy felhasználók kategóriáinak definiálását. Az adott gazdagép-állapotok, `Startup` például a és `Results`a szűrőit is meghatározza. Ez lehetővé teszi a naplózási kimenet finomhangolását. Ha a megadott kategóriákban nem találhatók egyezések, a szűrő visszakerül az `Default` értékre, amikor a rendszer eldönti, hogy szűrni kívánja-e az üzenetet.

`LogCategories`a következő using utasítást igényli:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Az alábbi példa egy olyan szűrőt állít össze, amely alapértelmezés szerint az összes naplót szűri a `Warning` szinten. A `Function` és `results` a kategóriák (a `Host.Results` 2. verzióban egyenértékűek. *x*) a `Error` szinten vannak szűrve. A szűrő összehasonlítja az aktuális kategóriát a `LogCategories` példány összes regisztrált szintjével, és kiválasztja a leghosszabb egyezést. Ez azt jelenti, `Debug` hogy az `Host.Triggers` egyezésekhez `Host.Triggers.Queue` vagy `Host.Triggers.Blob`a-hoz regisztrált szint. Ez lehetővé teszi a szélesebb kategóriák szabályozását anélkül, hogy hozzá kellene adni egyet.

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

A 2. verzióban. *x* az SDK-ban a szűrés vezérléséhez használja az `LogCategoryFilter` osztályt. A `LogCategoryFilter` `Default` tulajdonság a kezdeti értékkel `Information`rendelkezik `Critical` `Debug` , ami azt jelenti, hogy a `Information` (z),,vagyszintenlévőüzeneteknaplózásatörténik,dea`Error` `Warning` vagy `Trace` a szintek szűrve vannak.

`LogCategories` Csakúgy, mint a 3. verzióban. *x*, a `CategoryLevels` tulajdonság lehetővé teszi, hogy meghatározott kategóriáknál adja meg a naplózási szinteket, így a naplózási kimenet finomhangolása is megadható. Ha a `CategoryLevels` szótárban nem található egyezés, a szűrő visszakerül az `Default` értékre, amikor a rendszer eldönti, hogy szűrni kívánja-e az üzenetet.

A következő példa egy szűrőt állít össze, amely alapértelmezés szerint szűri az összes `Warning` naplót a szinten. A `Function` `Error` és `Host.Results` a kategóriák a szinten vannak szűrve. Az `LogCategoryFilter` összehasonlítja az aktuális kategóriát az `CategoryLevels` összes regisztrált értékkel, és kiválasztja a leghosszabb egyezést. Így a `Debug` regisztrált szint megfelel `Host.Triggers.Queue` akövetkezőnek`Host.Triggers` :. `Host.Triggers.Blob` Ez lehetővé teszi a szélesebb kategóriák szabályozását anélkül, hogy hozzá kellene adni egyet.

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

Mivel a 3. verzió. a webjobs SDK *x* -je a .net Core általános gazdagépre támaszkodik, és az egyéni telemetria-gyár már nincs megadva. A függőségi befecskendezés használatával azonban egyéni telemetria is hozzáadhat a folyamathoz. Az ebben a szakaszban szereplő példákhoz a `using` következő utasítások szükségesek:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A következő egyéni implementációja [`ITelemetryInitializer`] lehetővé teszi, hogy saját [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) maga adja hozzá [`TelemetryConfiguration`]az alapértelmezett értéket.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Hívja [`ConfigureServices`] meg a buildet, és adja [`ITelemetryInitializer`] hozzá az egyénit a folyamathoz.

```cs
static void Main()
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

        host.Run();
    }
}
```

A kiépítésekor a [`TelemetryConfiguration`] rendszer minden regisztrált [`ITelemetryInitializer`] típust tartalmaz. További információ: [Application INSIGHTS API egyéni eseményekhez és mérőszámokhoz](../azure-monitor/app/api-custom-events-metrics.md).

A 3. verzióban. *x*esetén már nem kell kiürítenie a [`TelemetryClient`] gazdagép leállását. A .net Core függőségi befecskendező rendszer automatikusan elvégzi a regisztrációt `ApplicationInsightsLoggerProvider`, ami kiüríti a [`TelemetryClient`]-t.

#### <a name="version-2x"></a>2\. verzió. *x*

A 2. verzióban. *x*, a [`TelemetryClient`] webjobs SDK által használt [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)Application Insights szolgáltató belsőleg létrehozva. Ha a Application Insights végpont nem érhető el, vagy a bejövő kérelmek szabályozása folyamatban van, a csatorna [a webalkalmazás fájlrendszerében menti a kérelmeket, és később újra elküldi őket](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

A [`TelemetryClient`] -t egy olyan osztály hozza létre, `ITelemetryClientFactory`amely megvalósítja. Alapértelmezés szerint ez a [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)következő:.

Ha módosítani szeretné a Application Insightsi folyamat bármely részét, megadhatja saját `ITelemetryClientFactory`adatait, és a gazdagép az osztályt [`TelemetryClient`]fogja használni a létrehozásához. Ez a kód például felülbírálja `DefaultTelemetryClientFactory` a következő `ServerTelemetryChannel`tulajdonságának módosítását:

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

Ez a cikk kódrészleteket adott meg, amelyek bemutatják, hogyan kezelheti a webjobs SDK-val való használat gyakori forgatókönyveit. A teljes mintákat lásd: [Azure-webjobs-SDK-Samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
