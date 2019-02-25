---
title: Az Azure WebJobs SDK - használata
description: További információ a WebJobs SDK-hoz készült kód írásával. Hozzon létre eseményvezérelt háttér-feldolgozási feladataikat az Azure-szolgáltatások és a külső szolgáltatások által adatok elérésére.
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
ms.openlocfilehash: ba9dbeb01be5a9869b69836b118651cff7f0c92d
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750548"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK használata az eseményvezérelt háttérben történő feldolgozás

Ez a cikk az Azure WebJobs SDK használata nyújt útmutatást. WebJobs-feladatokkal rögtön használatba, lásd: [eseményvezérelt háttérbeli feldolgozásra az Azure WebJobs SDK használatának első lépései](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>A WebJobs SDK-verziókra

A következő főbb különbségek verziójában a WebJobs SDK 3.x verziót képest 2.x:

* Verzió 3.x már támogatja a .NET Core.
* A verzió 3.x, explicit módon telepíteni kell a tárolási kötési bővítmény, a WebJobs SDK által igényelt. A verzió 2.x-es, a Storage kötések szerepeljenek az SDK-t.
* .NET Core (3.x) projektek hibakeresését a Visual Studio .NET-keretrendszer (2.x) projektek eltér. További információkért lásd: [fejlesztés és üzembe helyezése a Visual Studio használatával – az Azure App Service WebJobs](webjobs-dotnet-deploy-vs.md).

Ha lehetséges, példák mindkét verziót biztosít 3.x verziót és a 2.x.

> [!NOTE]
> [Az Azure Functions](../azure-functions/functions-overview.md) épül, a WebJobs SDK-val, és ez a cikk hivatkozásokat tartalmaz olyan témakörök, az Azure Functions dokumentációját. Funkciók és a WebJobs SDK közötti különbségek a következők:
> * 2.x WebJobs SDK-verziójának felel meg az Azure Functions-verzió 3.x és az Azure Functions 1.x felel meg a WebJobs SDK 2.x. Forráskódtárházak kövesse a WebJobs SDK számozása.
> * Az Azure Functions C#-osztálykódtárakat mintakód van, a WebJobs SDK-kód, nem kell azzal a különbséggel a `FunctionName` attribútum a WebJobs SDK-projektben.
> * Bizonyos kötési típusok csak az funkciók, például a HTTP, a webhook és az Event Grid (amely a HTTP-alapú) támogatottak.
>
> További információkért lásd: [összehasonlítása a WebJobs SDK-val és az Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webhobs-host"></a>WebHobs gazdagép

A gazdagép az functions runtime tárolója.  Azt figyeli, a functions eseményindítók és a hívások. A verzió 3.x, a gazdagép megvalósítását `IHost`, és verzió 2.x verzióját használja a `JobHost` objektum. Gazdagép-példány létrehozása a kódban, és testre szabhatja annak viselkedését, kód írása.

Ez a fő különbség a közvetlenül a WebJobs SDK-val és közvetve használatával az Azure Functions szolgáltatással. Az Azure Functions a szolgáltatás szabályozza a gazdagépen, és nem szabhatja testre, kód írásával. Azure Functions segítségével testre szabhatja a gazdagép viselkedésének beállításaival a *host.json* fájlt. Ezek a beállítások olyan karakterláncok, nem a kódban, ami a bármilyen típusú testreszabásokat is végezhet.

### <a name="host-connection-strings"></a>Gazdagép kapcsolati karakterláncok

A WebJobs SDK-val az Azure Storage és az Azure Service Bus kapcsolati karakterláncok keres a *local.settings.json* fájl futtatása helyileg, illetve a webjobs-feladat környezetben való futtatásakor az Azure-ban. Alapértelmezés szerint egy tárolási kapcsolati karakterlánc nevű beállítás `AzureWebJobsStorage` megadása kötelező.  

Verzió az SDK 2.x lehetővé teszi a saját nevét a kapcsolati karakterláncok használata, illetve máshol tárolhatja őket. Megadhatja azokat a kódban, itt látható módon:

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

Használja az alapértelmezett .NET Core konfigurációs API-kat, mert nincs nincs API verzió 3.x módosítható a kapcsolati karakterlánc neve.

### <a name="host-development-settings"></a>Gazdagép fejlesztői beállítások

A gazdagépen futtathatja a helyi fejlesztési hatékonyabbá teheti a fejlesztői módban. Íme néhány a fejlesztői módban való futtatáskor módosított beállítások:

| Tulajdonság | Fejlesztési beállítás |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` maximalizálhatja a napló kimenetét. |
| `Queues.MaxPollingInterval`  | Alacsony értéket annak biztosítása érdekében az üzenetsor-módszereket a azonnal aktiválódnak.  |
| `Singleton.ListenerLockPeriod` | 15 másodperc, ezzel elősegítve az gyors, iteratív fejlesztés. |

A módon, hogy engedélyezze a fejlesztői módban SDK verziójától függ. 

#### <a name="version-3x"></a>Verzió 3.x

Verzió 3.x a standard szintű ASP.NET Core API-kat használ. Hívja a [UseEnvironment](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metódust a [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példány. Egy sztring át `development`, ahogy az alábbi példában:

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

#### <a name="version-2x"></a>Verzió 2.x

A `JobHostConfiguration` osztálya rendelkezik egy `UseDevelopmentSettings` metódushoz, amely lehetővé teszi a fejlesztői módban.  Az alábbi példa bemutatja, hogyan fejlesztői beállítások használatára. Győződjön meg arról, hogy `config.IsDevelopment` vissza `true` helyi futtatáskor nevű helyi környezeti változó beállítása `AzureWebJobsEnv` értékkel `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Egyidejű kapcsolatok (2.x) kezelése

A verzió 3.x, végtelen kapcsolatok alapértelmezett a kapcsolathoz megadott korlátot. Ha valamilyen okból módosítania ezt a korlátot, akkor használhatja a [MaxConnectionsPerServer](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) tulajdonságát a [WinHttpHander](/dotnet/api/system.net.http.winhttphandler) osztály.

Verzió 2.x használatával szabályozhatja egy gazdagépre egyidejű kapcsolatok száma a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) API-t. A 2.x-es ezt az értéket az alapértelmezett 2. növelje a webjobs-feladatok gazdagép indítása előtt.

Az összes kimenő HTTP-kéréseket, amelyek egy függvény használatával teszik `HttpClient` keresztül érhetők el a `ServicePointManager`. Ha eléri a `DefaultConnectionLimit`, a `ServicePointManager` üzenetsor kérelmek elindítja őket elküldése előtt. Tegyük fel, hogy a `DefaultConnectionLimit` értéke 2. és a kód révén az 1000 HTTP-kérelmekre. Kezdetben csak két kérelmek engedélyezettek keresztül az operációs rendszerre. A többi 998 a rendszer várólistára helyezi addig, amíg nincs hely a számukra. Azt jelenti, hogy a `HttpClient` időtúllépés, lehet, mert azt *fenyegetésként észlel* , rendelkezik a kérést, de a kérést küldött az operációs rendszer soha nem a célkiszolgálón. Ezért előfordulhat, hogy nem Microsoft-jelentéssel bírnak viselkedés: a helyi `HttpClient` teljesítse a kérelmet 10 másodpercet vesz igénybe, de a szolgáltatás minden kérelemnél adja vissza a 200 ms. 

Az alapértelmezett érték az ASP.NET-alkalmazások `Int32.MaxValue`, és valószínűleg esetén működik megfelelően legalább alapszintű App Service-csomag futó webjobs-feladatok. Webjobs-feladatok általában a mindig bekapcsolva beállítást kell, és csak az alapszintű és a magasabb szintű App Service-csomagot, amely támogatott. 

Ha a webjobs-feladat fut, az ingyenes vagy közös App Service-csomag, az alkalmazás az App Service tesztkörnyezetben, amely jelenleg rendelkezik korlátozza egy [kapcsolathoz megadott korlátot, 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Egy kötetlen kapcsolathoz megadott korlátot, az a `ServicePointManager`, nagyobb a valószínűsége, hogy a tesztkörnyezet kapcsolati küszöbértéke érhető el, és a webhely leállítása. Ebben az esetben beállítás `DefaultConnectionLimit` valami alacsonyabb, mint 50 vagy 100, ezzel akadályozható és továbbra is engedélyezi a megfelelő átviteli sebességet.

A beállítás konfigurálni kell, mielőtt bármilyen HTTP-kérések. Ebből kifolyólag a WebJobs-gazdagép nem próbál szükség esetén módosítsa a beállítást automatikusan; Előfordulhat, hogy a gazdagép indításakor, és ez nem várt viselkedést eredményezhet azelőtt HTTP-kérelmekre. A legjobb módszer az, hogy az azonnal értékre állítja be a `Main` inicializálása előtt a `JobHost`, ahogyan az az alábbi példa

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Eseményindítók

Functions nyilvános metódusok kell lennie, és rendelkeznie kell egy eseményindító attribútumon vagy a [NoAutomaticTrigger](#manual-trigger) attribútum.

### <a name="automatic-trigger"></a>Automatikus eseményindító

Automatikus eseményindítók egy esemény bekövetkeztekor függvény hívása. Vegye figyelembe a következő példa egy üzenet, amely beolvas egy blobot az Azure Blog storage-ból az Azure Queue storage hozzáadott által aktivált függvény:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

A `QueueTrigger` attribútum a függvény meghívásához, amikor egy üzenetsor üzenet jelenik meg a futtatókörnyezet arra utasítja a `myqueue-items` várólista. A `Blob` attribútum arra utasítja a futtatókörnyezet, az üzenetsorban található üzenet használatával olvassa el a blobok a *minta-workitems* tároló. A függvény a átadott, az üzenetsorban található üzenet tartalmának a `myQueueItem` paraméter, a blob nevével.


### <a name="manual-trigger"></a>Manuális eseményindító

Egy függvény manuális aktiválásához használhatja az `NoAutomaticTrigger` attribútum, az alábbi példában látható módon:

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

Manuálisan aktiválja a függvényt módja függ az SDK-verzió.

#### <a name="version-3x"></a>Verzió 3.x

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

#### <a name="version-2x"></a>Verzió 2.x

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Bemeneti és kimeneti kötések

Bemeneti adatok az Azure-ban vagy külső szolgáltatások számára elérhetővé szeretné tenni a kód deklaratív módszert biztosítanak. Kimeneti kötések lehetővé teszik egy frissítheti az adatokat. A [Get lépéseket bemutató cikkben](webjobs-sdk-get-started.md) egyes egy példát mutat be.

A metódus visszatérési értékek használható egy kimeneti kötést az attribútum a metódus visszatérési érték alkalmazásával. A példa az Azure Functions szolgáltatásban [eseményindítók és kötések](../azure-functions/functions-bindings-return-value.md) cikk.

## <a name="binding-types"></a>Kötési típus

Verziója nem egyezik meg, hogy a kötési típusok telepített és felügyelt módon 3.x és a 2.x verzióját az SDK-t. Megtalálhatja a csomag telepítéséhez az adott kötési típus a **csomagok** a kötési típus szakaszában [áttekintésével foglalkozó cikkben](#binding-reference-information) az Azure Functions szolgáltatáshoz. Kivétel ez alól a fájlok trigger és kötése (a helyi fájlrendszerben;), ami nem támogatott az Azure Functions.

#### <a name="version-3x"></a>Verzió 3.x

A verzió 3.x, szerepelnek a storage-kötések a `Microsoft.Azure.WebJobs.Extensions.Storage` csomagot. Hívja a `AddAzureStorage` bővítmény metódus az `ConfigureWebJobs` metódus az alábbi példában látható módon:

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

Más eseményindítók és kötési típusok szeretné használni, telepítse a NuGet-csomagot, amely tartalmazza őket, és hívja a `Add<binding>` megvalósítva a bővítmény metódust. Például, ha szeretne egy Azure Cosmos DB-kötést használja, telepítse `Microsoft.Azure.WebJobs.Extensions.CosmosDB` hívja `AddCosmosDB`, ahogy az alábbi példában:

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

Az időzítő eseményindító vagy a fájlok használata kötelező, alapvető szolgáltatások részét képező, a hívások a `AddTimers` vagy `AddFiles` bővítő metódusokat, jelölik.

#### <a name="version-2x"></a>Verzió 2.x

A következő eseményindítót és a kötési típusok szerepelnek a verzió 2.x verzióját a `Microsoft.Azure.WebJobs` csomag:

* Blob Storage
* Queue Storage
* Table Storage

Más eseményindítók és kötési típusok szeretné használni, telepítse a NuGet-csomagot, amely tartalmazza őket, és hívja a `Use<binding>` metódust a `JobHostConfiguration` objektum. Például, ha szeretne egy időzítő indítófeltételt használjon, telepítse `Microsoft.Azure.WebJobs.Extensions` hívja `UseTimers` a a `Main` módot, ahogy ebben a példában is látható:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

a fájlok telepítése kötelező, `Microsoft.Azure.WebJobs.Extensions` hívja `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

Webjobs-feladatok lehetővé teszi a kötést létrehozni egy [ `ExecutionContext` ]. Ennek a kötésnek a hozzáférhet a [ `ExecutionContext` ] a függvényfej paraméterként. Például a következő kódot a Hívásazonosítót, amellyel korrelációját, ha egy adott függvény meghívási által előállított összes napló eléréséhez használja a context objektumot.  

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

A kötött módon a [ `ExecutionContext` ] az SDK verziójától függ.

#### <a name="version-3x"></a>Verzió 3.x

Hívja a `AddExecutionContextBinding` bővítmény metódus az `ConfigureWebJobs` metódus az alábbi példában látható módon:

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

#### <a name="version-2x"></a>Verzió 2.x

A `Microsoft.Azure.WebJobs.Extensions` a korábban említett csomag is biztosít egy speciális kötési típus, amely meghívásával regisztrálhatja a `UseCore` metódust. Ez a kötés lehetővé teszi, hogy meghatározhatja egy [ `ExecutionContext` ] parametr obecného a függvény, amely engedélyezve van a következő:

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

## <a name="binding-configuration"></a>Kötés konfigurálása

Néhány triggerét és kötéseit segítségével konfigurálhatja a felhasználók viselkedését. Konfigurálhatja őket módja függ az SDK-verzió.

* **Verzió 3.x:** Konfigurációs van beállítva, amikor a `Add<Binding>` módszert hívja meg `ConfigureWebJobs`.
* **Verzió 2.x:** Tulajdonságainak megadásával egy konfigurációs objektumot, amely adja át a `JobHost`.

Ezeket a kötés-specifikus beállításokat egyenértékűek beállításait a [host.json soubor projektu](../azure-functions/functions-host-json.md) az Azure Functions szolgáltatásban.

A következő kötéseket konfigurálhatja:

* [Azure cosmos DB-eseményindító](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs-trigger](#event-hubs-trigger-configuration-version-3x)
* [Queue storage-eseményindító](#queue-trigger-configuration)
* [A SendGrid-kötés](#sendgrid-binding-configuration-version-3x)
* [Service Bus-trigger](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Az Azure cosmos DB-eseményindító konfigurálása (verzió 3.x)

Az alábbi példa bemutatja, hogyan konfigurálhatja az Azure Cosmos DB-eseményindító:

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

További részletekért tekintse meg a [Azure cosmos DB-kötés cikk](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings).

### <a name="event-hubs-trigger-configuration-version-3x"></a>Az Event Hubs-trigger konfigurációja (verzió 3.x)

Az alábbi példa bemutatja, hogyan konfigurálhatja az Event Hubs-eseményindító:

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

További részletekért tekintse meg a [az Event Hubs kötés cikk](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings).

### <a name="queue-trigger-configuration"></a>Üzenetsor eseményindító konfigurációja

Az alábbi példák bemutatják, hogyan konfigurálhatja a tárolási üzenetsor eseményindító:

#### <a name="version-3x"></a>Verzió 3.x

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

További részletekért tekintse meg a [Queue storage kötés cikk](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings).

#### <a name="version-2x"></a>Verzió 2.x

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

További részletekért tekintse meg a [v1.x host.json-referencia](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>A SendGrid kötés configuration (verzió 3.x)

A következő példa bemutatja, hogyan konfigurálhatja a SendGrid kimeneti kötést:

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

További részletekért tekintse meg a [SendGrid kötés cikk](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings).

### <a name="service-bus-trigger-configuration-version-3x"></a>A Service Bus-trigger konfigurációja (verzió 3.x)

Az alábbi példa bemutatja, hogyan konfigurálhatja a Service Bus-trigger:

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

További részletekért tekintse meg a [kötést a cikk a Service Bus](../azure-functions/functions-bindings-service-bus.md#hostjson-settings).

### <a name="configuration-for-other-bindings"></a>Konfigurációs más kötések

Bizonyos eseményindítót és a kötési típusok saját egyéni konfiguráció típusa határozza meg. Például a fájl eseményindító megadhatja használatával figyelheti, ahogy az alábbi példák a gyökér elérési útja:

#### <a name="version-3x"></a>Verzió 3.x

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

#### <a name="version-2x"></a>Verzió 2.x

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

## <a name="binding-expressions"></a>Kötési kifejezésekben

Attribútum konstruktor paramétereket, az értékek oldja fel a különböző forrásokból származó kifejezésekkel is használhatja. Például az alábbi kódot, az elérési útját a `BlobTrigger` attribútum hoz létre egy elnevezett kifejezés `filename`. Ha a kimeneti kötés használja `filename` feloldja a riasztást kiváltó blob nevével.

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

Kötési kifejezésekben kapcsolatos további információkért lásd: [kifejezések és a minták](../azure-functions/functions-bindings-expressions-patterns.md) az Azure Functions dokumentációjában.

### <a name="custom-binding-expressions"></a>Egyéni kötési kifejezésekben

Néha szeretne hozzáadni egy üzenetsor neve, a blob nevét vagy a tároló, vagy egy Táblanév rögzítse szoftveresen helyett a kód azt. Például érdemes a várólista nevét adja meg a `QueueTrigger` attribútum egy konfigurációs fájlban vagy környezeti változóban.

Úgy teheti meg, amely megadásának egy `NameResolver` az objektum a `JobHostConfiguration` objektum. A trigger vagy kötési attribútum konstruktor paraméterek helyőrzőket tartalmaznak, és a `NameResolver` kódot biztosít a tényleges értékek ezeket a helyőrzők helyett használható. A helyőrzők azonosítja a környező őket a százalék (%) jelentkezik, az alábbi példában látható módon:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód használatát teszi lehetővé egy nevű üzenetsor `logqueuetest` a tesztkörnyezetet és egy nevű `logqueueprod` éles környezetben. Egy szokott várólista neve helyett nevét adja meg, egy bejegyzés a `appSettings` gyűjtemény.

Nincs alapértelmezett NameResolver érvénybe, ha nem ad meg egy egyéni besorolás. Az alapértelmezett értékek az alkalmazásbeállítások és a környezeti változók beolvasása.

A `NameResolver` osztály lekéri az üzenetsor neve, a `appSettings` az alábbi példában látható módon:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Verzió 3.x

A feloldási függőségi beszúrást használatával van konfigurálva. Ezek a minták a szükséges `using` utasítást:

```cs
using Microsoft.Extensions.DependencyInjection;
```

A feloldási meghívásával bekerül a [ `ConfigureServices` ] metódust a [HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder), ahogy az alábbi példában:

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

#### <a name="version-2x"></a>Verzió 2.x

Adja át a `NameResolver` az osztály a `JobHost` objektumazonosító a következő példában látható módon:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Az Azure Functions valósítja meg `INameResolver` való lekérjük az értékeket alkalmazásbeállításokat, a példában látható módon. Közvetlenül a WebJobs SDK használata esetén, amely lekérdezi a helyőrző csereértékeket bármilyen forrásból, inkább az egyéni kialakítások írhat. 

## <a name="binding-at-runtime"></a>Kötés futásidőben

Ha néhány dolgot a függvényben egy kötelező attribútum például használata előtt végrehajtandó `Queue`, `Blob`, vagy `Table`, használhatja a `IBinder` felületet.

Az alábbi példa egy bemeneti üzenetsor vesz igénybe, és egy új üzenetet hoz létre ugyanahhoz a tartalomhoz egy kimeneti várólista. A kimeneti várólista neve kódot a függvény törzsében állítja be.

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

További információkért lásd: [futásidőben kötés](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) az Azure Functions dokumentációjában.

## <a name="binding-reference-information"></a>Kötési információ

Minden egyes kötési típus referenciaadatait megtalálható az Azure Functions dokumentációját. Tárolási üzenetsor használja példaként, megtalálhatja a következő információkat az egyes kötés áttekintésével foglalkozó cikkben:

* [Csomagok](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) – mi annak érdekében, hogy a kötés biztosít a WebJobs SDK-projektben telepítse a csomagot.
* [Példák](../azure-functions/functions-bindings-storage-queue.md#trigger---example) – a C# osztály könyvtár Példa érvényes, a WebJobs SDK-val; csak hagyja el a `FunctionName` attribútum.
* [Attribútumok](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) – a kötési típus használandó attribútumok.
* [Konfigurációs](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -attribútum tulajdonságainak és konstruktor paraméterek magyarázatot.
* [Használati](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) –, milyen típusú kötést hozhasson létre, és információ a kötés működik. Példa: ciklikus lekérdezés algoritmus, ártalmas várólista feldolgozása.
  
Kötelező hivatkozás cikkek listáját lásd: **támogatott kötések** a a [eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) a cikk az Azure Functions szolgáltatáshoz. A listában a HTTP, a webhook és az Event Grid-kötések támogatottak, csak az Azure Functions, nem pedig a WebJobs SDK-val.

## <a name="disable-attribute"></a>Attribútum letiltása 

A [letiltása](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum lehetővé teszi, hogy egy függvény is elindítható. 

A következő példában ha az Alkalmazásbeállítás `Disable_TestJob` értéke "1" vagy "True" (megkülönbözteti a kis és nagybetűk megkülönböztetése nélkül), a függvény nem fog futni. Ebben az esetben a futtatókörnyezet hoz létre egy naplófájlüzenetre *"Functions.TestJob" funkció le van tiltva*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Ha megváltoztatja az alkalmazás beállítás értékeit az Azure Portalon, az azt eredményezi, a webjobs-feladat újra kell indítani, vesz fel az új beállítás.

Az attribútum a paramétert, a metódus vagy az osztály szintjén deklarálható. A beállítás neve is tartalmazhat kötési kifejezésekben.

## <a name="timeout-attribute"></a>Időtúllépés attribútumot

A [időtúllépési](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribútum okozza egy függvényt, hogy ha nem a megadott időn belül fejeződik be. A következő példában az a függvény az időkorlát nélkül egy napon futna. Az időkorlát a függvény 15 másodperc elteltével meg lett szakítva.

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

Az időtúllépési attribútumban osztály vagy metódus szintjén is alkalmazhat, és segítségével megadhatja a globális időtúllépést `JobHostConfiguration.FunctionTimeout`. Osztály vagy metódus szintű időtúllépések felülírják a globális időtúllépést.

## <a name="singleton-attribute"></a>Egyszeres attribútum

Használja a [egyszeres](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribútum annak biztosítása érdekében, hogy egy függvény csak egy példánya fut, még akkor is, ha a gazdagép webes alkalmazás több példánya. Ezt az életbe léptetésével hajtja [elosztott zárolás](#viewing-lease-blobs).

A következő példában csak egy példány lehet a `ProcessImage` lefut egy adott időpontban:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Egyes eseményindítók tartalmaz beépített támogatást az egyidejűség kezelése:

* **QueueTrigger** – beállított `JobHostConfiguration.Queues.BatchSize` 1-re.
* **ServiceBusTrigger** – beállított `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 1-re.
* **FileTrigger** – beállított `FileProcessor.MaxDegreeOfParallelism` 1-re.

Ezek a beállítások segítségével győződjön meg arról, hogy a függvény akkor fut, egypéldányosként egyetlen példányán. Annak érdekében, hogy a függvény csak egyetlen példány fut, amikor a webalkalmazás elvégzi a horizontális felskálázást több példányra, a függvény egy figyelő szintű egyszeres zárolási alkalmazni (`[Singleton(Mode = SingletonMode.Listener)]`). Figyelő zárolások beszerzett, a JobHost indításkor. Ha kiterjesztett három, minden példány egy időben indul el, csak egy példányt beszerzi a zárolást, és csak egy figyelő elindul.

### <a name="scope-values"></a>A hatókör értékei

Megadhat egy **hatókör-érték kifejezés** az egyedülálló, amely biztosítja, hogy az adott hatókörben függvény az összes végrehajtás fog szerializálni a. Részletesebb zárolás ezzel a módszerrel a végrehajtási lehetővé teszik bizonyos szintű párhuzamosság a függvény által igényeinek más indítások szerializálásakor. Ha például az alábbi példában a hatókör-kifejezés van kötve a `Region` értéket a bejövő üzenet. Ha az üzenetsor tartalmazza a három üzenetek régiók "Keleti régiója", "Kelet" és "Nyugati régiója" jelölik, majd az üzeneteket, amelyeken a régióban "Kelet" tárolókonfigurációhoz végrehajtása közben az üzenet-régióhoz, "West" végrehajtása párhuzamosan, a "Kelet."

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
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

Az alapértelmezett hatókör zárolásra `SingletonScope.Function` tehát a zárolás hatóköre (a blob lease elérési út) vannak kötve, a függvény teljesen minősített nevet. Zárolási funkciók között, adja meg a `SingletonScope.Host` és használja a hatókör azonosítója, amelyet szeretne egyidejűleg futtatandó függvény összes megegyezik. A következő példában csak egy példányban `AddItem` vagy `RemoveItem` egyszerre fut:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Bérlet blobok megtekintése

A WebJobs SDK-t használ [az Azure blob-bérletek](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) elosztott zárolás megvalósításához a háttérben. Tekintheti meg a Singleton által használt bérleti blobokat a `azure-webjobs-host` a tároló a `AzureWebJobsStorage` storage-fiók "zárolását" elérési úton. Ha például a bérlet blob elérési útja az első `ProcessImage` korábban bemutatott példák lehetnek `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Összes elérési út tartalmazza a JobHost Azonosítót, ez megkülönbözteti a kis 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Az aszinkron funkciók

A kód aszinkron funkciók kapcsolatos információkért tekintse meg az Azure Functions dokumentációját [aszinkron funkciók](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Megszakítási tokeneket

Hogyan kezelje a megszakítási tokeneket kapcsolatos információkért tekintse meg az Azure Functions dokumentációját [megszakítási tokeneket, és biztonságos leállításának](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Több példány

A webes alkalmazás több példánya fut, ha egy folyamatos webjobs-feladat figyeli a eseményindítók és a függvény hívása futtat minden egyes előfordulás esetén. A különböző eseményindító kötéseket úgy tervezték, hogy hatékonyan megoszthatja munkáját elősegítése érdekében,-példányok között, hogy a horizontális felskálázás több példányra lehetővé teszi további terhelés kezeléséhez.

Az üzenetsor és a blob-eseményindítók automatikusan megakadályozza, hogy a függvény egy üzenetsor-üzenet feldolgozása, vagy egynél többször; blob függvények nem rendelkezik, hogy idempotensek legyenek.

Az időzítő eseményindító automatikusan biztosítja az időzítő fut, hogy csak egy példánya, így egy adott ütemezett időben futó több függvény példány nem kap.

Ha azt szeretné, hogy csak egy példánya, a függvény akkor fut, akkor is, ha a gazdagép webes alkalmazás több példánya, használja a [egyszeres attribútum](#singleton-attribute).

## <a name="filters"></a>Szűrők

Függvény szűrők (előzetes verzió) teszik lehetővé a webjobs-feladatok végrehajtási folyamat saját logika a testre szabhatja. Szűrők hasonlóak [ASP.NET Core szűrők](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Ezek a függvények vagy osztályok alkalmazott deklaratív attribútumként kell végrehajtani. További információkért lásd: [függvény szűrők](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Azt javasoljuk, hogy az ASP.NET, kifejlesztett naplózási keretrendszer és a [első lépései](webjobs-sdk-get-started.md) a cikk bemutatja, hogyan kell használni. 

### <a name="log-filtering"></a>Napló szűrése

Minden naplót hoz létre egy `ILogger` példányhoz tartozik egy társított `Category` és `Level`. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) enumeráció, és az egész kód azt jelzi, hogy relatív fontosságát:

|LogLevel    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

Minden kategória egy adott egymástól függetlenül szűrésével [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel). Például előfordulhat, hogy szeretné az összes blob eseményindító feldolgozása azonban csak a naplófájlokban `Error` és minden más magasabb.

#### <a name="version-3x"></a>Verzió 3.x

Verzió 3.x az SDK támaszkodik a .NET Core épített szűrést. A `LogCategories` osztály lehetővé teszi, hogy az egyes függvények, eseményindítók vagy felhasználók kategóriái határozhatók meg. Azt is meghatározza az adott gazdagép államok, szűrők például `Startup` és `Results`. Ezzel a módszerrel finomhangolhatja a naplózás kimenetét. Ha nem talál egyezést a meghatározott kategóriákba belül, a szűrő visszavált a `Default` értékét, amikor eldönti, hogy az üzenet szűrésére.

`LogCategories` szükséges a következő using utasítást:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Az alábbi példa létrehoz egy szűrőt, amely alapértelmezés szerint minden naplókat, szűri a `Warning` szintjét. Kategóriák `Function` vagy `results` (egyenértékű `Host.Results` verzióban 2.x) szűr a rendszer a `Error` szint. A szűrő hasonlítja össze az aktuális kategória az összes regisztrált szinteket a `LogCategories` példányra, és úgy dönt, a leghosszabb egyezést. Ez azt jelenti, hogy a `Debug` szint regisztrált `Host.Triggers` egyezni fog `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi, hogy szabályozza a szélesebb körű kategóriák anélkül, hogy mindegyikhez adjon hozzá.

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

#### <a name="version-2x"></a>Verzió 2.x

A verzió az SDK 2.x a `LogCategoryFilter` osztály segítségével szabályozhatja a szűrés. A `LogCategoryFilter` rendelkezik egy `Default` tulajdonság a kezdeti értékkel `Information`, azt jelenti, hogy olyan üzeneteket szintű `Information`, `Warning`, `Error`, vagy `Critical` jelentkezett, de olyan üzeneteket szintű`Debug` vagy `Trace` azonnal vannak szűrve.

A `LogCategories` verzióban 23.x, a `CategoryLevels` tulajdonság lehetővé teszi, hogy adja meg a naplózási szintjeivel a megjelölt kategóriákat úgy finomhangolhatja a naplózás kimenetét. Ha nem talál egyezést belül a `CategoryLevels` szótárban, a szűrő visszavált a `Default` értékét, amikor eldönti, hogy az üzenet szűrésére.

Az alábbi példa létrehoz egy szűrőt, amely alapértelmezés szerint minden naplókat, szűri a `Warning` szintjét. Kategóriák `Function` vagy `Host.Results` szűrve az `Error` szint. A `LogCategoryFilter` hasonlítja össze az aktuális kategória minden regisztrált `CategoryLevels` és úgy dönt, a leghosszabb egyezést. Ez azt jelenti, hogy a `Debug` szint regisztrált `Host.Triggers` egyezni fog `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi, hogy szabályozza a szélesebb körű kategóriák anélkül, hogy mindegyikhez adjon hozzá.

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

### <a name="custom-telemetry-for-application-insights"></a>Egyéni telemetriát az Application Insights

Egyéni telemetriai megvalósító módja [Application Insights](../azure-monitor/app/app-insights-overview.md) használja az SDK verziójától függ. Az Application Insights konfigurálása kapcsolatban lásd: [Application Insights hozzáadása naplózási](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Verzió 3.x

Verzió óta 3.x a WebJobs SDK a .NET Core általános gazdagép támaszkodik, már nem létezik olyan egyéni telemetriai adat-előállítóval megadott. Azonban egyéni telemetriát adhat hozzá a folyamat használatával függőségi beszúrást. Ebben a szakaszban szereplő példák a szükséges `using` utasításokat:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A következő egyéni végrehajtása [ `ITelemetryInitializer` ] vehet fel a saját [ `ITelemetry` ](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) alapértelmezett [ `TelemetryConfiguration` ].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Hívás [ `ConfigureServices` ] a jelentéskészítőt az egyéni hozzáadása a [ `ITelemetryInitializer` ] folyamatra.

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
        // Add Logging Providers
        b.AddConsole();

        // If this key exists in any config, use it to enable App Insights
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

Ha a [ `TelemetryConfiguration` ] jön létre, az összes regisztrált típusú [ `ITelemetryInitializer` ] tartalmazza. A további tudnivalókat lásd a [Application Insights API egyéni eseményekhez és a metrikák](../azure-monitor/app/api-custom-events-metrics.md).

A verzió 3.x, már nem rendelkezik kiüríteni a [ `TelemetryClient` ] amikor a gazdagép leáll. A .NET Core függőségi injektálási rendszer automatikusan elveti, a regisztrált `ApplicationInsightsLoggerProvider`, mely kiürítéseinek száma a [ `TelemetryClient` ].

#### <a name="version-2x"></a>Verzió 2.x

Verzió 2.x-es, a [ `TelemetryClient` ] belsőleg az Application Insights-szolgáltató által létrehozott esetében a WebJobs SDK-t használ a [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Az Application Insights-végpont esetén nem érhető el, vagy a sávszélesség-szabályozási bejövő kérelmek, ez a csatorna [kérelmek menti a webalkalmazás-fájlrendszer, és később újra elküldi](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

A [ `TelemetryClient` ] jön létre, amely megvalósítja az osztályok által `ITelemetryClientFactory`. Ez az alapértelmezés szerint a [ `DefaultTelemetryClientFactory` ](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Ha szeretné módosítani az Application Insights folyamat bármely része, megadhatja a saját `ITelemetryClientFactory`, és a gazdagép létrehozására fogja használni az osztály egy [ `TelemetryClient` ]. Például ez a kód felülírja az `DefaultTelemetryClientFactory` egy tulajdonságának módosítása a `ServerTelemetryChannel`:

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

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Konfigurálja a SamplingPercentageEstimatorSettings objektum [adaptív mintavételezés](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Ez azt jelenti, hogy nagy mennyiségű bizonyos esetekben az App Insights küld telemetriai adatokat egy kijelölt részét a kiszolgálónak.

Miután létrehozta a telemetriai adatokat feldolgozó, adja át azt az Application Insights-naplózás szolgáltatóhoz:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Következő lépések

Ez az útmutató kódrészletek, amelyek bemutatják, hogyan lehet kezelni a gyakori forgatókönyvek és a WebJobs SDK együttes használata van megadva. Teljes minta, lásd: [azure-webjobs-sdk-minták](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
