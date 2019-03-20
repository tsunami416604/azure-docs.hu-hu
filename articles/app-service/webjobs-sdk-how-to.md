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
ms.openlocfilehash: 0da4e1a0b20874c4452dd77bf77df0860dec455f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848073"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK használata az eseményvezérelt háttérben történő feldolgozás

Ez a cikk az Azure WebJobs SDK használata nyújt útmutatást. WebJobs-feladatokkal rögtön használatba, lásd: [eseményvezérelt háttérbeli feldolgozásra az Azure WebJobs SDK használatának első lépései](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>A WebJobs SDK-verziókra

Ezek azok a 3-as verziójú közötti fő különbségeket. *x* és 2. *x* , a WebJobs SDK-val:

* 3. verziója. *x* támogatja a .NET Core.
* A 3-as verziójú. *x*, a WebJobs SDK által igényelt, a Storage kötési bővítmény explicit módon telepíteni kell. A 2. verzióban. *x*, a Storage-kötések szerepeljenek az SDK-t.
* Visual Studio-eszközöket a .NET Core (3. *x*) projektek eltér a .NET-keretrendszer-felügyeleti (2. *x*) projektek. További tudnivalókért lásd: [fejlesztés és üzembe helyezése a Visual Studio használatával – az Azure App Service WebJobs](webjobs-dotnet-deploy-vs.md).

Ha lehetséges, példák mindkét 3-as verziójú-okat. *x* és 2. *x*.

> [!NOTE]
> [Az Azure Functions](../azure-functions/functions-overview.md) épül, a WebJobs SDK-t, és ez a cikk az Azure Functions dokumentációját az egyes témakörök mutató hivatkozásokat biztosít. Megjegyzés: a Functions és a WebJobs SDK közötti különbségeket:
> * Az Azure Functions 2. verzió. *x* felel meg a WebJobs SDK 3-as verziójú. *x*, és az Azure Functions 1. *x* felel meg a WebJobs SDK-t 2. *x*. Forráskódtárházak használata a WebJobs SDK számozása.
> * Mintakód az Azure Functions C# osztálykódtárakat van, a WebJobs SDK a kódhoz hasonlóan, azzal a különbséggel, nem kell egy `FunctionName` attribútum a WebJobs SDK-projektben.
> * Néhány kötési típusok támogatottak, csak az funkciók, például a HTTP (Webhookok) és az Event Grid (amely a HTTP alapul).
>
> További információkért lásd: [összehasonlítása a WebJobs SDK-val és az Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Webjobs-feladatok gazdagép

A gazdagép az functions runtime tárolója.  Azt figyeli, a functions eseményindítók és a hívások. A 3-as verziójú. *x*, a gazdagép megvalósítását `IHost`. A 2. verzióban. *x*, használja a `JobHost` objektum. Gazdagép-példány létrehozása a kódban, és testre szabhatja annak viselkedését, kód írása.

Ez a fő különbség a közvetlenül a WebJobs SDK-val és az Azure Functions keresztül közvetve használatával. Az Azure Functions a szolgáltatás vezérli a gazdagépen, és nem szabhatja testre a gazdagép kód írásával. Azure Functions segítségével testre szabható gazdagép viselkedése a host.json fájl beállításain keresztül. Ezek a beállítások olyan karakterláncok, nem a kódban, és ezzel a megoldással a típusú testreszabásokat is végezhet.

### <a name="host-connection-strings"></a>Gazdagép kapcsolati karakterláncok

A WebJobs SDK a local.settings.json fájlban, ha helyileg futtatja, vagy a környezet a webjobs-feladatot az Azure Storage és az Azure Service Bus-kapcsolati karakterláncok keres, amikor az Azure-ban futtatja. Alapértelmezés szerint egy tárolási kapcsolati karakterlánc nevű beállítás `AzureWebJobsStorage` megadása kötelező.  

2. verziója. *x* az SDK lehetővé teszi a saját nevét a kapcsolati karakterláncok használata vagy máshol tárolhatja őket. A kód használatával beállíthatja a neveket a [ `JobHostConfiguration` ], ahogy az itt látható:

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

Mivel a 3-as verziójú. *x* konfigurációt használja az alapértelmezett .NET Core API-k, nincs API módosítható kapcsolati karakterlánc neve van.

### <a name="host-development-settings"></a>Gazdagép fejlesztői beállítások

A gazdagépen futtathatja a helyi fejlesztési hatékonyabbá teheti a fejlesztői módban. Íme néhány, a beállításokat, amelyek módosítják a fejlesztői módban futtatásakor:

| Tulajdonság | Fejlesztési beállítás |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` maximalizálhatja a napló kimenetét. |
| `Queues.MaxPollingInterval`  | Alacsony értéket annak biztosítása érdekében az üzenetsor-módszereket a azonnal aktiválódnak.  |
| `Singleton.ListenerLockPeriod` | 15 másodperc, ezzel elősegítve az gyors, iteratív fejlesztés. |

Fejlesztői mód engedélyezéséhez a folyamat függ, az SDK-verzió. 

#### <a name="version-3x"></a>3. verziója. *x*

3. verziója. *x* a standard szintű ASP.NET Core API-k. Hívja a [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metódust a [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példány. Egy sztring át `development`, amint az ebben a példában:

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

#### <a name="version-2x"></a>2. verziója. *x*

A `JobHostConfiguration` osztálya rendelkezik egy `UseDevelopmentSettings` metódushoz, amely lehetővé teszi a fejlesztői módban.  Az alábbi példa bemutatja, hogyan fejlesztői beállítások használatára. Győződjön meg arról, hogy `config.IsDevelopment` vissza `true` helyben fut, amikor egy nevű helyi környezeti változó beállítása `AzureWebJobsEnv` értékkel `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Egyidejű kapcsolatok kezelése (2. verzióban. *x*)

A 3-as verziójú. *x*, végtelen kapcsolatok az alapértelmezett a kapcsolathoz megadott korlátot. Ha valamilyen okból módosítania ezt a korlátot, akkor használhatja a [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) tulajdonságát a [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) osztály.

A 2. verzióban. *x*, segítségével szabályozhatja az egyidejű kapcsolatok száma egy gazdagépre a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) API-t. A 2. *x*, ezt az értéket az alapértelmezett 2. növelje a webjobs-feladatok gazdagép indítása előtt.

Az összes kimenő HTTP-kéréseket, amelyek egy függvény használatával teszik `HttpClient` áramlása `ServicePointManager`. Ha eléri beállított érték `DefaultConnectionLimit`, `ServicePointManager` üzenetsor kérelmek elindítja őket elküldése előtt. Tegyük fel, hogy a `DefaultConnectionLimit` értéke 2. és a kód révén az 1000 HTTP-kérelmekre. Kezdetben csak két kérelmek engedélyezettek keresztül az operációs rendszerre. A többi 998 a rendszer várólistára helyezi addig, amíg nincs hely a számukra. Azt jelenti, hogy a `HttpClient` előfordulhat, hogy időkorlátja, mert úgy tűnik, hogy a kérést leadó rendelkezik, de a kérést küldött az operációs rendszer soha nem a célkiszolgálón. Ezért előfordulhat, hogy nem Microsoft-jelentéssel bírnak viselkedés: a helyi `HttpClient` teljesítse a kérelmet 10 másodpercet vesz igénybe, de a szolgáltatás minden kérelemnél adja vissza a 200 ms. 

Az alapértelmezett érték az ASP.NET-alkalmazások `Int32.MaxValue`, és valószínűleg esetén működik megfelelően a webjobs-feladatok futtatása egy legalább alapszintű App Service-csomag. Webjobs-feladatok általában a mindig bekapcsolva beállítást kell, és csak az alapszintű és a magasabb szintű App Service-csomagban, amely támogatott.

Ha a webjobs-feladat fut, az ingyenes vagy közös App Service-csomag, az alkalmazás az App Service tesztkörnyezetben, amely jelenleg rendelkezik korlátozza egy [kapcsolathoz megadott korlátot, 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Egy kötetlen kapcsolathoz megadott korlátot, az a `ServicePointManager`, nagyobb a valószínűsége, hogy a tesztkörnyezet kapcsolati küszöbértéke érhető el, és a hely le fog állni. Ebben az esetben beállítás `DefaultConnectionLimit` valami alacsonyabb, mint 50 vagy 100, ezzel akadályozható és továbbra is engedélyezi a megfelelő átviteli sebességet.

A beállítás konfigurálni kell, mielőtt bármilyen HTTP-kérések. Ebből kifolyólag a WebJobs-gazdagép nem szükség esetén módosítsa a beállítást automatikusan. Mielőtt a gazdagép elindul, nem várt viselkedést okozhat HTTP-kérelmekre is lehet. A legjobb módszer az, hogy az azonnal értékre állítja be a `Main` inicializálása előtt `JobHost`, ahogy az itt látható:

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

Functions nyilvános metódusok kell lennie, és rendelkeznie kell egy eseményindító attribútumon vagy a [ `NoAutomaticTrigger` ](#manual-trigger) attribútum.

### <a name="automatic-triggers"></a>Automatikus eseményindítók

Automatikus eseményindítók egy esemény bekövetkeztekor függvény hívása. Fontolja meg ebben a példában egy üzenet hozzáadva az Azure Queue storage által aktivált függvény. Ezt úgy válaszol, hogy egy blob olvasása az Azure Blob storage-ból:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

A `QueueTrigger` attribútum a függvény meghívásához, amikor egy üzenetsor üzenet jelenik meg a futtatókörnyezet arra utasítja a `myqueue-items` várólista. A `Blob` attribútum arra utasítja a futtatókörnyezet, az üzenetsorban található üzenet használatával olvassa el a blobok a *minta-workitems* tároló. A függvény az átadott, az üzenetsorban található üzenet tartalmának a `myQueueItem` paraméter, a blob nevével.


### <a name="manual-triggers"></a>Manuális eseményindítók

Egy függvény manuális aktiválásához használhatja az `NoAutomaticTrigger` attribútum, az itt látható módon:

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

A folyamat manuális indítására, a függvény az SDK verziójától függ.

#### <a name="version-3x"></a>3. verziója. *x*

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

#### <a name="version-2x"></a>2. verziója. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Bemeneti és kimeneti kötések

Bemeneti adatok az Azure-ban vagy külső szolgáltatások számára elérhetővé szeretné tenni a kód deklaratív módszert biztosítanak. Kimeneti kötések lehetővé teszik egy frissítheti az adatokat. A [Ismerkedés](webjobs-sdk-get-started.md) cikk egyes egy példát mutat be.

A metódus visszatérési értékek használható egy kimeneti kötést az attribútum a metódus visszatérési érték alkalmazásával. A példa a [használata az Azure-függvény visszatérési érték](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Kötési típus

A telepítése és kezelése a kötési típusok folyamata attól függ, hogy 3-as verziójú használ. *x* vagy 2-es verzió. *x* SDK. A csomag telepítéséhez, egy adott kötési típus a "Packages" szakaszban az Azure Functions a kötési típus a annak [áttekintésével foglalkozó cikkben](#binding-reference-information). Kivétel ez alól a fájlok trigger és kötése (a helyi fájlrendszerben;), amely nem támogatott az Azure Functions.

#### <a name="version-3x"></a>3. verziója. *x*

A 3-as verziójú. *x*, a storage-kötések szerepelnek a `Microsoft.Azure.WebJobs.Extensions.Storage` csomagot. Hívja a `AddAzureStorage` a metódust a `ConfigureWebJobs` módszerhez, ahogy az itt látható:

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

Más eseményindítók és kötési típusok szeretné használni, telepítse a NuGet-csomagot, amely tartalmazza őket, és hívja a `Add<binding>` megvalósítva a bővítmény metódust. Például, ha szeretne egy Azure Cosmos DB-kötést használja, telepítse `Microsoft.Azure.WebJobs.Extensions.CosmosDB` hívja `AddCosmosDB`, ehhez hasonló:

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

#### <a name="version-2x"></a>2. verziója. *x*

Ezek a trigger és a kötési típusok 2-es verzió szerepel. *x* , a `Microsoft.Azure.WebJobs` csomag:

* Blob Storage
* Queue Storage
* Table Storage

Más eseményindítók és kötési típusok szeretné használni, telepítse a NuGet-csomagot, amely tartalmazza őket, és hívja a `Use<binding>` metódust a `JobHostConfiguration` objektum. Például ha egy időzítő indítófeltételt használni kívánt, telepítse `Microsoft.Azure.WebJobs.Extensions` hívja `UseTimers` a a `Main` módszerhez, ahogy az itt látható:

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

A folyamat a kötést a [ `ExecutionContext` ] az SDK verziójától függ.

#### <a name="version-3x"></a>3. verziója. *x*

Hívja a `AddExecutionContextBinding` a metódust a `ConfigureWebJobs` módszerhez, ahogy az itt látható:

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

#### <a name="version-2x"></a>2. verziója. *x*

A `Microsoft.Azure.WebJobs.Extensions` a korábban említett csomag is biztosít egy speciális kötési típus, amely meghívásával regisztrálhatja a `UseCore` metódust. Ez a kötés lehetővé teszi, hogy meghatározhatja egy [ `ExecutionContext` ] parametr obecného a függvény, amely engedélyezve van az ehhez hasonló:

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

Konfigurálhatja az egyes eseményindítók és kötések viselkedését. A konfigurálás folyamata attól függ, hogy az SDK-verzió.

* **3. verziója. *x*:** Konfiguráció beállítása során a `Add<Binding>` módszert hívja meg `ConfigureWebJobs`.
* **2. verziója. *x*:** Egy konfigurációs objektumot, amelyeket átad a tulajdonságainak beállításával konfigurálásának `JobHost`.

Ezeket a kötés-specifikus beállításokat egyenértékűek beállításait a [host.json soubor projektu](../azure-functions/functions-host-json.md) az Azure Functions szolgáltatásban.

A következő kötéseket konfigurálhatja:

* [Azure cosmos DB-eseményindító](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs-trigger](#event-hubs-trigger-configuration-version-3x)
* Queue storage-eseményindító
* [A SendGrid-kötés](#sendgrid-binding-configuration-version-3x)
* [Service Bus-trigger](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Az Azure cosmos DB-eseményindító konfigurálása (3-as verziójú. *x*)

Ez a példa bemutatja, hogyan konfigurálhatja az Azure Cosmos DB-eseményindító:

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

További részletekért tekintse meg a [Azure cosmos DB-kötés](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) cikk.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Az Event Hubs-trigger konfiguráció (3-as verziójú. *x*)

Ez a példa bemutatja, hogyan konfigurálhatja az Event Hubs-eseményindító:

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

További részletekért tekintse meg a [az Event Hubs-kötés](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) cikk.

### <a name="queue-storage-trigger-configuration"></a>Queue storage trigger konfigurációja

Ezek a példák bemutatják, hogyan konfigurálhatja a Queue storage-eseményindító:

#### <a name="version-3x"></a>3. verziója. *x*

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

További részletekért tekintse meg a [tárkötéshez várólistára](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) cikk.

#### <a name="version-2x"></a>2. verziója. *x*

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

### <a name="sendgrid-binding-configuration-version-3x"></a>A SendGrid kötés konfiguráció (3-as verziójú. *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a SendGrid kimeneti kötést:

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

További részletekért tekintse meg a [SendGrid kötés](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) cikk.

### <a name="service-bus-trigger-configuration-version-3x"></a>A Service Bus-trigger konfigurációja (3-as verziójú. *x*)

Ez a példa bemutatja, hogyan konfigurálhatja a Service Bus-trigger:

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

További részletekért tekintse meg a [a Service Bus-kötés](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) cikk.

### <a name="configuration-for-other-bindings"></a>Konfigurációs más kötések

Bizonyos eseményindítót és a kötési típusok saját egyéni konfigurációs típust határoznak meg. Például a fájl eseményindító megadhatja a gyökér elérési útja a következő példákhoz hasonlóan monitorozásához:

#### <a name="version-3x"></a>3. verziója. *x*

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

#### <a name="version-2x"></a>2. verziója. *x*

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

Néha szeretne megadni egy üzenetsor neve, a blob neve vagy a tároló vagy egy Táblanév fix kódolása, helyett kódot. Például érdemes a várólista nevét adja meg a `QueueTrigger` attribútum egy konfigurációs fájlban vagy környezeti változóban.

Úgy teheti meg, amely átadja egy `NameResolver` az objektumot a `JobHostConfiguration` objektum. A trigger vagy kötési attribútum konstruktor paraméterek helyőrzőket tartalmaznak, és a `NameResolver` kódot biztosít a tényleges értékek ezeket a helyőrzők helyett használható. Azonosíthatja a helyőrzők téve őket a százalék (%) jelentkezik, itt látható módon:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód használatát teszi lehetővé egy nevű üzenetsor `logqueuetest` a tesztkörnyezetet és egy nevű `logqueueprod` éles környezetben. Egy szokott várólista neve helyett nevét adja meg, egy bejegyzés a `appSettings` gyűjtemény.

Van egy alapértelmezett `NameResolver` , amely lép érvénybe, ha nem ad meg egy egyéni besorolás. Az alapértelmezett értékek az alkalmazásbeállítások és a környezeti változók beolvasása.

A `NameResolver` osztály lekéri az üzenetsor neve, a `appSettings`, ahogy az itt látható:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>3. verziója. *x*

A feloldási függőségi beszúrást segítségével konfigurálhatja. Ezek a minták a szükséges `using` utasítást:

```cs
using Microsoft.Extensions.DependencyInjection;
```

A feloldó meghívásával adja hozzá a [ `ConfigureServices` ] metódust a [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), amint az ebben a példában:

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

#### <a name="version-2x"></a>2. verziója. *x*

Adja át a `NameResolver` az osztály a `JobHost` objektumot, ahogy az itt látható:

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

Ha ehhez néhány dolgot a függvényben, például egy kötelező attribútum használata előtt kell `Queue`, `Blob`, vagy `Table`, használhatja a `IBinder` felületet.

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

Az Azure Functions dokumentációját tájékoztatással egyes kötési típus. Az alábbi adatokat az egyes kötés áttekintésével foglalkozó cikkben találhat. (Ebben a példában tárolási üzenetsor alapul.)

* [Csomagok](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). A WebJobs SDK-projektben a kötési támogatását telepítenie kell a csomag.
* [Példák](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Kódminták. A C# class library Példa érvényes a WebJobs SDK-val. Egyszerűen hagyja el a `FunctionName` attribútum.
* [Attribútumok](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). A kötési típus használandó attribútumok.
* [Konfigurációs](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Az attribútum tulajdonságainak és konstruktor paraméterek találja.
* [Használat](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) – A kell kötni típusát és a kötés működésével kapcsolatos információkat. Példa: ciklikus lekérdezés algoritmus, ártalmas várólista feldolgozása.
  
Kötelező hivatkozás cikkek listáját lásd a "Támogatott kötések" a [eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) a cikk az Azure Functions szolgáltatáshoz. A listában az Event Grid, HTTP és Webhookok kötések támogatottak, csak az Azure Functions, nem pedig a WebJobs SDK-val.

## <a name="disable-attribute"></a>Attribútum letiltása 

A [ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum lehetővé teszi, hogy egy függvény is elindítható. 

A következő példában ha az Alkalmazásbeállítás `Disable_TestJob` értéke `1` vagy `True` (kis-és nagybetűket), a függvény nem futtatja. Ebben az esetben a futtatókörnyezet hoz létre egy naplófájlüzenetre *"Functions.TestJob" funkció le van tiltva*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Ha megváltoztatja az alkalmazás beállítás értékeit az Azure Portalon, a webjobs-feladatot az új beállítás csomópontmetrikák újraindul.

Az attribútum a paramétert, a metódus vagy az osztály szintjén deklarálható. A beállítás neve is tartalmazhat kötési kifejezésekben.

## <a name="timeout-attribute"></a>Időtúllépés attribútumot

A [ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribútum okozza egy függvényt, amely szakítható meg, ha nem a megadott időn belül fejeződik be. A következő példában az a függvény az időtúllépési attribútumban nélkül egy napon futna. Időtúllépés a függvény 15 másodperc elteltével megszakítandó okoz.

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

Az időtúllépési attribútumot az osztály vagy metódus szintjén is alkalmazhat, és megadhat egy globális időtúllépést használatával `JobHostConfiguration.FunctionTimeout`. Osztály- vagy metódus-szintjén időtúllépések globális időtúllépések bírálhatja felül.

## <a name="singleton-attribute"></a>Egyszeres attribútum

A [ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribútum biztosítja, hogy egy függvény csak egy példányban fut le, még akkor is, ha a gazdagép webes alkalmazás több példánya. Ennek segítségével hajtja [elosztott zárolás](#viewing-lease-blobs).

Ebben a példában csak egy példány lehet a `ProcessImage` lefut egy adott időpontban:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Egyes eseményindítók tartalmaz beépített támogatást az egyidejűség kezelése:

* **QueueTrigger**. Állítsa be `JobHostConfiguration.Queues.BatchSize` való `1`.
* **ServiceBusTrigger**. Állítsa be `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` való `1`.
* **FileTrigger**. Állítsa be `FileProcessor.MaxDegreeOfParallelism` való `1`.

Ezek a beállítások segítségével győződjön meg arról, hogy a függvény akkor fut, egypéldányosként egyetlen példányán. Győződjön meg arról, hogy a függvény csak egyetlen példánya fut, amikor a webalkalmazás elvégzi a horizontális felskálázást több példányra, egy figyelő szintű egyszeres zárolási alkalmazni a függvény (`[Singleton(Mode = SingletonMode.Listener)]`). Figyelő zárolások megszerzése a JobHost indításakor. Ha kiterjesztett három, minden példány egy időben indul el, csak egy példányt beszerzi a zárolást, és csak egy figyelő elindul.

### <a name="scope-values"></a>A hatókör értékei

Megadhat egy *hatókör-érték kifejezés* az Egypéldányos. Kifejezés értéke biztosítja, hogy egy adott hatókörnél a függvény az összes végrehajtás szerializálható lesz. Részletesebb zárolás ezzel a módszerrel a végrehajtási lehetővé teszik bizonyos szintű párhuzamosság a függvény által igényeinek más indítások szerializálásakor. Ha például az alábbi kódot, a hatókör-kifejezés van kötve a `Region` értéket a bejövő üzenet. A várólista régióban három üzeneteket tartalmaz, a keleti, a keleti és nyugati, illetve, ha az üzeneteket, amelyek tárolókonfigurációhoz futnak, keleti régió során nyugati fut párhuzamosan, a keleti régió rendelkező üzenet.

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

Az alapértelmezett hatókör zárolásra `SingletonScope.Function`, ami azt jelenti, a zárolás hatóköre (a blob lease elérési út) van kötve a teljesen minősített függvény nevét. Zárolási funkciók között, adja meg a `SingletonScope.Host` és hatókör azonosító nevet, amelyet el ugyanaz az összes függvényt, amelyet szeretne egyszerre futtatni. A következő példában csak egy példányban `AddItem` vagy `RemoveItem` egyszerre fut:

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

### <a name="viewing-lease-blobs"></a>Bérlet blobok megtekintése

A WebJobs SDK-t használ [az Azure blob-bérletek](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) elosztott zárolás megvalósításához a háttérben. A bérlet blobok egyszeres által használt található a `azure-webjobs-host` tárolót az a `AzureWebJobsStorage` storage-fiók "zárolását" útvonalon. Ha például a bérlet blob elérési útja az első `ProcessImage` korábban bemutatott példák lehetnek `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Összes elérési út tartalmazza a JobHost Azonosítót, ez megkülönbözteti a kis 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Az aszinkron funkciók

Az aszinkron kódfunkciók kapcsolatos információkért lásd: a [Azure Functions dokumentációját](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Megszakítási tokeneket

Hogyan kezelje a megszakítási tokeneket kapcsolatos információkért tekintse meg az Azure Functions dokumentációját [megszakítási tokeneket, és biztonságos leállításának](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Több példány

A webes alkalmazás több példánya fut, ha egy folyamatos webjobs-feladat figyeli a eseményindítók és a függvény hívása futtat minden egyes előfordulás esetén. A különböző eseményindító kötéseket úgy tervezték, hogy hatékonyan megoszthatja munkáját elősegítése érdekében,-példányok között, hogy a horizontális felskálázás több példányra lehetővé teszi további terhelés kezeléséhez.

Az üzenetsor és a blob-eseményindítók automatikusan megakadályozza, hogy a függvény egy üzenetsor-üzenet feldolgozása, vagy egynél többször; blob függvények nem rendelkezik, hogy idempotensek legyenek.

Az időzítő eseményindító automatikusan biztosítja az időzítő fut, hogy csak egy példánya, így egy adott ütemezett időben futó több függvény példány nem kap.

Ha azt szeretné, hogy csak egy példánya, a függvény akkor fut, akkor is, ha a gazdagép webes alkalmazás több példánya, használja a [ `Singleton` ](#singleton-attribute) attribútum.

## <a name="filters"></a>Szűrők

Függvény szűrők (előzetes verzió) teszik lehetővé a webjobs-feladatok végrehajtási folyamat saját logika a testre szabhatja. Szűrők hasonlóak [ASP.NET Core-szűrők](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Azok a funkciók vagy osztályok alkalmazott deklaratív attribútumként valósítható meg. További információkért lásd: [függvény szűrők](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Azt javasoljuk, hogy a naplózási keretrendszer fejlesztett ASP.NET-hez. A [Ismerkedés](webjobs-sdk-get-started.md) a cikk bemutatja, hogyan kell használni. 

### <a name="log-filtering"></a>Napló szűrése

Minden naplót hoz létre egy `ILogger` példányhoz tartozik egy társított `Category` és `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) az enumerálás, és az egész kód azt jelzi, hogy relatív fontosságát:

|LogLevel    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

Egymástól függetlenül szűrheti egy adott legördülőmenü [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Például előfordulhat, hogy szeretné az összes blob eseményindító feldolgozása azonban csak a naplófájlokban `Error` és minden más magasabb.

#### <a name="version-3x"></a>3. verziója. *x*

3. verziója. *x* az SDK támaszkodik a .NET Core épített szűrést. A `LogCategories` osztály lehetővé teszi, hogy az egyes függvények, eseményindítók vagy felhasználók kategóriái határozhatók meg. Azt is meghatározza az adott gazdagép államok, szűrők például `Startup` és `Results`. Ez lehetővé teszi, hogy a naplózás kimeneti finomhangolásához. Ha nem talál egyezést a meghatározott kategóriákba belül, a szűrő visszavált a `Default` értékét, amikor eldönti, hogy az üzenet szűrésére.

`LogCategories` szükséges a következő using utasítást:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Az alábbi példa létrehoz egy működő szűrő, alapértelmezés szerint az összes napló, a `Warning` szintjét. A `Function` és `results` kategóriák (egyenértékű `Host.Results` a 2. verzióban. *x*) szűr a rendszer a `Error` szintjét. A szűrő hasonlítja össze az aktuális kategória az összes regisztrált szinteket a `LogCategories` példányra, és úgy dönt, a leghosszabb egyezést. Ez azt jelenti, hogy a `Debug` szint regisztrált `Host.Triggers` megegyezik `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi, hogy szabályozza a szélesebb körű kategóriák anélkül, hogy mindegyikhez adjon hozzá.

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

#### <a name="version-2x"></a>2. verziója. *x*

A 2. verzióban. *x* SDK, használja a `LogCategoryFilter` szabályozhatja a szűrés osztály. A `LogCategoryFilter` rendelkezik egy `Default` tulajdonság a kezdeti értékkel `Information`, azt jelenti, hogy olyan üzeneteket, a `Information`, `Warning`, `Error`, vagy `Critical` szintek jelentkezett, de a következő üzeneteket a `Debug` vagy `Trace` szintek azonnal vannak szűrve.

A `LogCategories` a 3-as verziójú. *x*, a `CategoryLevels` tulajdonság lehetővé teszi, hogy adja meg a naplózási szintjeivel a megjelölt kategóriákat úgy finomhangolhatja a naplózás kimenetét. Ha nem talál egyezést belül a `CategoryLevels` szótárban, a szűrő visszavált a `Default` értékét, amikor eldönti, hogy az üzenet szűrésére.

Az alábbi példa létrehoz egy szűrőt, amely alapértelmezés szerint minden naplókat, szűri a `Warning` szintjét. A `Function` és `Host.Results` kategóriák szűr a rendszer a `Error` szintjét. A `LogCategoryFilter` hasonlítja össze az aktuális kategória minden regisztrált `CategoryLevels` és úgy dönt, a leghosszabb egyezést. Ezért a `Debug` szint regisztrált `Host.Triggers` egyezni fog `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi, hogy szabályozza a szélesebb körű kategóriák anélkül, hogy mindegyikhez adjon hozzá.

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

Az egyéni telemetriája megvalósításának folyamata [Application Insights](../azure-monitor/app/app-insights-overview.md) SDK verziójától függ. Az Application Insights konfigurálása kapcsolatban lásd: [Application Insights hozzáadása naplózási](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>3. verziója. *x*

Mivel a 3-as verziójú. *x* a WebJobs SDK a .NET Core, már nem nyújt általános gazdagépre, egy egyéni telemetriai adatokat előállító támaszkodik. De adhat hozzá egyéni telemetriát a folyamat függőségi beszúrást használatával. Ebben a szakaszban szereplő példák a szükséges `using` utasításokat:

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

Ha a [ `TelemetryConfiguration` ] jön létre, az összes regisztrált típusú [ `ITelemetryInitializer` ] tartalmazza. További tudnivalókért lásd: [Application Insights API egyéni eseményekhez és a metrikák](../azure-monitor/app/api-custom-events-metrics.md).

A 3-as verziójú. *x*, már nem rendelkezik kiüríteni a [ `TelemetryClient` ] amikor a gazdagép leáll. A .NET Core függőségi injektálási rendszer automatikusan elveti, a regisztrált `ApplicationInsightsLoggerProvider`, mely kiürítéseinek száma a [ `TelemetryClient` ].

#### <a name="version-2x"></a>2. verziója. *x*

A 2. verzióban. *x*, a [ `TelemetryClient` ] belsőleg az Application Insights-szolgáltató által létrehozott a WebJobs SDK-t használ a [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Az Application Insights-végpont esetén nem érhető el, vagy a sávszélesség-szabályozási bejövő kérelmek, ez a csatorna [kérelmek menti a webalkalmazás-fájlrendszer, és később újra elküldi](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

A [ `TelemetryClient` ] jön létre, amely megvalósítja az osztályok által `ITelemetryClientFactory`. Ez az alapértelmezés szerint a [ `DefaultTelemetryClientFactory` ](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Ha szeretné módosítani az Application Insights folyamat bármely része, megadhatja a saját `ITelemetryClientFactory`, és a gazdagép létrehozására fogja használni az osztály egy [ `TelemetryClient` ]. Például ez a kód felülírja `DefaultTelemetryClientFactory` egy tulajdonságának módosítása `ServerTelemetryChannel`:

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

A `SamplingPercentageEstimatorSettings` objektum konfigurálja [adaptív mintavételezés](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Ez azt jelenti, hogy bizonyos nagy mennyiségű helyzetekben Applications Insights elküldi a kiválasztott részhalmazát telemetriai adatokat a kiszolgáló.

Miután létrehozta a telemetriai adatokat feldolgozó, adja át azt az Application Insights-naplózás szolgáltatóhoz:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Következő lépések

Ebben a cikkben nyújtott kódrészletek azt mutatják be, hogyan legyen kezelve a gyakori forgatókönyvek és a WebJobs SDK együttes használata. Teljes minta, lásd: [azure-webjobs-sdk-minták](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
