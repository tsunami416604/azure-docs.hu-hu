---
title: Az Azure WebJobs SDK használatával
description: További tudnivalókért arról, hogyan írhat kódot a WebJobs SDK-ban. Eseményvezérelt háttérben történő feldolgozási feladatok létrehozására, amelyek az Azure-szolgáltatások és harmadik féltől származó szolgáltatással adatok eléréséhez.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 08272ba7d828f744336723f25b482bf06b9e43dc
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234650"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Hogyan használható az Azure WebJobs SDK a eseményvezérelt háttérben történő feldolgozás

Ez a cikk nyújt útmutatást, hogyan írhat kódot [az Azure WebJobs SDK](webjobs-sdk-get-started.md). A dokumentáció foglaltak azokra 2.x és 3.x jelzés hiányában. A fő módosítása 3.x által bevezetett a .NET Core helyett a .NET-keretrendszer használatát.

>[!NOTE]
> [Az Azure Functions](../azure-functions/functions-overview.md) épül, a WebJobs SDK, és ez a cikk hivatkozásokat tartalmaz néhány témaköre az Azure Functions dokumentációjában olvashatók. Megjegyzés: a funkciók és a WebJobs SDK között a következő eltérésekkel:
> * Azure WebJobs SDK-verzió 1.x megfelel-e funkciók-verzió 2.x, és a 2.x WebJobs SDK felel meg az Azure Functions 3.x. Forráskódú adattárakban kövesse a WebJobs SDK számozására, és sok v2.x ágak, a főágba jelenleg a 3.x kóddal rendelkező rendelkezik.
> * Az Azure Functions C# osztálykönyvtárakhoz mintakód hasonlóan a WebJobs SDK-kód kivételével nincs szükség van egy `FunctionName` attribútum sem. a WebJobs SDK projektben.
> * Néhány kötési típusok csak támogatottak a Funkciók, például HTTP webhook vagy esemény rács (alapuló HTTP). 
> 
> További információkért lásd: [hasonlítsa össze a WebJobs SDK és az Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik olvasási [Ismerkedés a WebJobs SDK-val](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

A `JobHost` célja a futtatási tárolóban függvények: elkezdi figyelni a eseményindítók és hívások funkciók. Létrehozhat a `JobHost` a kódot és az írási kód a viselkedés testreszabásához.

Ez fő különbség a WebJobs SDK segítségével közvetlenül és közvetve használata az Azure Functions használatával között. Az Azure Functions, a szolgáltatás szabályozza a `JobHost`, és a kód megírásával nem testreszabhatók. Az Azure Functions lehetővé teszi, hogy a gazdagép viselkedés beállításain keresztül testreszabásához a *host.json* fájlt. Ezek a beállítások olyan karakterláncok, nem kód, amely korlátozza a testreszabások teheti típusú.

### <a name="jobhost-connection-strings"></a>JobHost kapcsolati karakterláncok

A WebJobs SDK keresi a tárolás és a Service Bus kapcsolati karakterláncok *local.settings.json* futtatásakor helyileg, vagy a webjobs-feladat környezetben való futtatásakor az Azure-ban. Ha azt szeretné, a saját nevét a kapcsolati karakterláncok használatára, vagy máshová tárolja őket, állíthatja be azokat a kódban, ahogy az itt látható:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>JobHost fejlesztési beállításai

A `JobHostConfiguration` osztály rendelkezik egy `UseDevelopmentSettings` módszer, amely a helyi fejlesztési hatékonyabbá hívása. Az alábbiakban néhány, a beállításokat, amelyek ezt a módszert módosítja:

| Tulajdonság | Fejlesztési beállítás |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` a kimenet maximalizálása érdekében. |
| `Queues.MaxPollingInterval`  | Győződjön meg arról, várólista módszerek által kiváltott azonnal alacsony érték.  |
| `Singleton.ListenerLockPeriod` | a gyors iteratív fejlesztési támogatási 15 másodperc. |

A következő példa bemutatja, hogyan fejlesztői beállításokat kívánja használni. Hogy `config.IsDevelopment` vissza `true` a helyi futtatás során nevű helyi környezeti változó beállítása `AzureWebJobsEnv` értékű `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>JobHost ServicePointManager beállítások

A .NET-keretrendszer tartalmazza az API-k hívása [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , amely meghatározza, hogy a gazdagép létesített egyidejű kapcsolatok száma. Azt javasoljuk, hogy állítsa ezt az értéket az alapértelmezett 2 a webjobs-feladatok host indítása előtt.

Egy függvény használatával ellenőrizze az összes kimenő HTTP-kérelmek `HttpClient` keresztül folyik a `ServicePointManager`. Miután kattint a `DefaultConnectionLimit`, a `ServicePointManager` mielőtt elküldené várólistázást kérelmek kezdődik. Tegyük fel, hogy a `DefaultConnectionLimit` 2 és a kód teszi 1000 HTTP-kérelmek értékre van állítva. Kezdetben csak 2 kérelmek ténylegesen engedélyezettek keresztül az operációs rendszeren. A más 998 sorba amíg nincs elég hely a számukra. Ez azt jelenti a `HttpClient` előfordulhat, hogy túllépi az időkorlátot, mert azt *úgy értelmezi* történik a kérelmet, de a kérés nem lett elküldve az operációs rendszer által a célkiszolgálón. Ezért úgy tűnik, hogy célszerű működésére láthatja: a helyi `HttpClient` a kérés teljesítéséhez el 10 másodpercet vesz igénybe, de a szolgáltatás minden kérelemnél vissza a 200 ms. 

Az alapértelmezett érték az ASP.NET-alkalmazások `Int32.MaxValue`, és valószínűleg működnek jól az alapszintű vagy magasabb szintű App Service-csomag futó webjobs-feladatok. Webjobs-feladatok általában a mindig bekapcsolva beállítás szükséges, és, amely csak az alapszintű és magasabb szintű App Service-csomagokról által támogatott. 

Ha egy ingyenes vagy közös App Service-csomag a webjobs-feladat fut, az alkalmazás az App Service védőfal, amelynek jelenleg korlátozza a [kapcsolathoz megadott korlátot 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Az egy kötetlen kapcsolathoz megadott korlátot a `ServicePointManager`, valószínűbb, hogy a védőfal kapcsolat küszöbértéket lejár, és a webhely leállítása. Ebben az esetben beállítás `DefaultConnectionLimit` egy alacsonyabb, mint 50 vagy 100, ez megelőzhető és is lehetővé teheti a megfelelő átviteli sebesség eléréséhez.

A beállítás előtt a HTTP-kérelmek kell konfigurálni. Emiatt a WebJobs-gazdagép nem próbál szükség esetén módosítsa a beállítást automatikusan; Előfordulhat, hogy HTTP-kérelmek történnie ahhoz, hogy a gazdagép elindul, és ez nem várt működést eredményezhet. A legjobb módszer is, hogy az érték a azonnal a `Main` metódus inicializálása előtt a `JobHost`, a következő példában látható módon

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

Funkciók metódusnak kell lennie nyilvános, és rendelkeznie kell egy eseményindító vagy a [NoAutomaticTrigger](#manual-trigger) attribútum.

### <a name="automatic-trigger"></a>Automatikusan indítható

Automatikus eseményindítók meghívni a függvényt az adott esemény bekövetkezésekor. Egy vonatkozó példáért lásd: a várólista eseményindító a [Get elindított cikk](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Manuális eseményindító

Egy függvény manuálisan kezdeményezi, használja a `NoAutomaticTrigger` attribútum, az alábbi példában látható módon:

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Bemeneti és kimeneti kötések

Bemeneti kötések nyújtanak olyan deklaratív adatok Azure vagy harmadik féltől származó szolgáltatással számára elérhetővé a kódot. Kimeneti kötések lehetőséget nyújtanak olyan adatainak frissítése. A [Get elindított cikk](webjobs-sdk-get-started.md) minden példáját mutatja be.

A metódus visszatérési érték használható egy kimeneti kötése az attribútum a metódus visszatérési érték alkalmazásával. A következő példa az Azure Functions a [eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) cikk.

## <a name="binding-types"></a>Kötés típusa

A következő indítási és a kötési típusok szerepelnek a `Microsoft.Azure.WebJobs` csomag:

* Blob Storage
* Queue Storage
* Table Storage

Egyéb indítási és a kötéstípust szeretné használni, telepítse a NuGet-csomagot, amely tartalmazza őket, és hívja meg a `Use<binding>` metódust a `JobHostConfiguration` objektum. Például ha egy időzítő indítófeltételt használni kívánt, telepítse `Microsoft.Azure.WebJobs.Extensions` , és hívja meg `UseTimers` a a `Main` módszer, például:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

A csomag telepítéséhez az adott kötési típus található a **csomagok** a kötési típus szakasza [áttekintésével foglalkozó cikkben](#binding-reference-information) az Azure Functions. Egy kivétel ez alól a fájlok eseményindító és kötése (a helyi fájlrendszerben;), amely az Azure Functions által nem támogatott. a fájlok használata kötelező, telepítse `Microsoft.Azure.WebJobs.Extensions` , és hívja meg `UseFiles`.

### <a name="usecore"></a>UseCore

A `Microsoft.Azure.WebJobs.Extensions` a korábban említett csomag is biztosít egy különleges kötési típus, amely meghívásával regisztrálhatja a `UseCore` metódust. A kötés lehetővé teszi, hogy adja meg egy [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) a függvényaláíráshoz paramétere. A környezeti objektumot biztosít hozzáférést a meghívási azonosító, amelyet felhasználhat egy adott funkció hívás által visszaadott összes napló összefüggéseket. Például:

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

## <a name="binding-configuration"></a>Kötési konfigurációja

Számos indíthat, és a kötési típusok segítségével úgy konfigurálhatja a viselkedésük egy konfigurációs objektumot, amelyeket átad tulajdonságainak megadásával a `JobHost`.

### <a name="queue-trigger-configuration"></a>Várólista eseményindító konfigurációja

A tároló várólista eseményindító a konfigurálható beállítások szakaszban találhatók az Azure Functions [host.json hivatkozás](../azure-functions/functions-host-json.md#queues). Hogyan kell őket beállítani a WebJobs SDK projektben van az alábbi példában látható módon:

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

### <a name="configuration-for-other-bindings"></a>Más kötésekben konfigurációja

Néhány eseményindító és a kötési típus a saját egyéni konfiguráció típusa határozza meg. Például a fájl eseményindító megadását teszi lehetővé az útvonalgyökér figyelése:

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

## <a name="binding-expressions"></a>Kötési kifejezésként

Attribútum konstruktorparaméterek különböző forrásokból származó értékek feloldható kifejezéseket is használhat. Például az alábbi kódban, az elérési útját a `BlobTrigger` attribútum létrehoz egy kifejezés nevű `filename`. A kimeneti kötés használatakor `filename` oldja fel az eseményindító blob neve.
 
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

További információ a kötés kifejezések: [kötelező kifejezések és minták](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) az Azure Functions dokumentációjában.

### <a name="custom-binding-expressions"></a>Egyéni kötésben kifejezések

Néha szeretne hozzáadni, adja meg a várólista nevét, a blob neve vagy a tároló, vagy egy táblázatot a merevlemez-kód helyett a kód adjon neki nevet. Például érdemes a várólista nevét adja meg a `QueueTrigger` attribútumot a konfigurációs fájl vagy a környezeti változóban.

A történő ehhez a `NameResolver` az objektum a `JobHostConfiguration` objektum. A trigger és kötés attribútum konstruktorparaméterek helyőrzők is tartalmazó és a `NameResolver` kód ezeket a helyőrzők helyett használandó tényleges értékek szolgál. A helyőrzőket azonosítják százalékjel (%) jelek, az azt körülvevő a következő példában látható módon:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód lehetővé teszi a tesztkörnyezetben logqueuetest és éles környezetben egy elnevezett logqueueprod nevű várólista használatát. A kódolt várólista neve helyett a bejegyzés nevének megadása a `appSettings` gyűjtemény. 

Nincs olyan alapértelmezett NameResolver érvénybe, ha nem ad meg egy egyéni. Az alapértelmezett beállítások vagy a környezeti változók értékeit kapja.

A `NameResolver` osztály lekérdezi a várólista nevét a `appSettings` a következő példában látható módon:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Továbbítsa a `NameResolver` az osztályt a `JobHost` objektum az az alábbi példában látható módon:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Az Azure Functions valósít meg `INameResolver` értékek lekérése Alkalmazásbeállítások, a példában látható módon. Közvetlenül a WebJobs SDK használata esetén a következő egyéni megvalósítása, amely lekérdezi a helyőrző csereértékekre inkább bármilyen forrásból is írhat. 

## <a name="binding-at-runtime"></a>Futásidejű kötés

Ha néhány célra a függvény egy kötelező attribútum például a használata előtt kell `Queue`, `Blob`, vagy `Table`, használhatja a `IBinder` felületet.

Az alábbi példa időt vesz igénybe egy bemeneti várólista-üzenet, és létrehoz egy új üzenetet a kimeneti várólistában lévő ugyanahhoz a tartalomhoz. A kimeneti várólista neve van beállítva, a függvény törzséhez tartozó kóddal.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

További információkért lásd: [futásidőben kötés](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) az Azure Functions dokumentációjában.

## <a name="binding-reference-information"></a>Kötési hivatkozási információ

Minden kötési típus információk az Azure Functions dokumentációjában találhatók. Storage üzenetsorába használ példaként, látni fogja a következő információkat az egyes kötés áttekintésével foglalkozó cikkben:

* [Csomagok](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) -mi csomag telepítenie ahhoz, hogy a WebJobs SDK projekt a kötés támogatja.
* [Példák](../azure-functions/functions-bindings-storage-queue.md#trigger---example) -a WebJobs SDK a C# class library Példa érvényes; csak hagyja el a `FunctionName` attribútum.
* [Attribútumok](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) – a kötési típus használandó attribútumait.
* [Konfigurációs](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -attribútum tulajdonságainak és konstruktorparaméterek magyarázatot.
* [Használati](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - kell kötni, mit kell adnia és információ a kötés működik. Például: algoritmus lekérdezési, elhalt várólista feldolgozása.
  
Kötési hivatkozási cikkek listáját lásd: **kötések támogatott** a a [eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) cikk az Azure Functions. A listában a HTTP, a webhook és az esemény rács-kötések támogatottak csak az Azure Functions, nem pedig a WebJobs SDK által.

## <a name="disable-attribute"></a>Tiltsa le a attribútum 

A [letiltása](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum segítségével szabályozhatja, hogy egy függvény is elindítható. 

A következő példa Ha az Alkalmazásbeállítás `Disable_TestJob` értéke "1" vagy "True" (kis-és nagybetűket), a függvény nem fog futni. Ebben az esetben a futtatókörnyezet létrehoz egy naplófájlüzenetre *"Functions.TestJob" funkció le van tiltva*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Alkalmazás beállítás értékeit az Azure-portálon módosítja, amikor okoz a webjobs-feladat újra kell indítani, az új beállítás fel.

Az attribútum a paraméter, módszer vagy osztály szintjén deklarálható. A Beállításnév kötés kifejezést is tartalmazhat.

## <a name="timeout-attribute"></a>Időtúllépés attribútumot

A [időtúllépés](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribútum okoz a működnek, ha azt nem fejezi be a megadott időn belül. A következő példában a függvény egy napig a időtúllépése nélkül fog futni. Az időkorlát a függvény 15 másodperc után megszakadt.

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

Az időtúllépési attribútumot az osztály vagy a metódus szinten is alkalmazhat, és segítségével megadhatja a globális időtúllépést `JobHostConfiguration.FunctionTimeout`. Osztály vagy a metódus szintű időtúllépések bírálja felül a globális időtúllépést.

## <a name="singleton-attribute"></a>Egypéldányos attribútum

Használja a [egypéldányos](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribútum biztosításához, hogy egy függvény csak egy példánya még akkor is fut, ha a gazdagép webes alkalmazás több példánya van. Ennek érdekében végrehajtási [zárolás elosztott](#viewing-lease-blobs).

A következő példában csak egyetlen példány futhat a `ProcessImage` függvény fut az adott időpontban:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Néhány eseményindítók van építve a feldolgozási felügyelet támogatása:

* **QueueTrigger** - beállított `JobHostConfiguration.Queues.BatchSize` 1.
* **ServiceBusTrigger** - beállított `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 1.
* **FileTrigger** - beállított `FileProcessor.MaxDegreeOfParallelism` 1.

Ezek a beállítások segítségével győződjön meg arról, hogy a függvény fut egypéldányos egyetlen példányán. Annak érdekében, a függvény csak egyetlen példányát futása közben a webes alkalmazás alkalmazkodnak ki több példánya, a függvény egy figyelő szintű egypéldányos zárolási alkalmazni (`[Singleton(Mode = SingletonMode.Listener)]`). Figyelő zárolásokat a JobHost az indításkor beszerzett. Ha minden három kiterjesztett példányok egyszerre indul el, csak egy példányt a zárolás, és csak egy figyelő kezdődik.

### <a name="scope-values"></a>Hatókörök értékeinek

Megadhat egy **kifejezés-érték hatókör** a Singleton, amely biztosítja, hogy az adott hatókörben függvény összes végrehajtások szerializálni lehessen a. Ily módon részletesebb zárolás végrehajtási lehetővé teszik bizonyos szintű párhuzamosság a függvény által előírtak szerint a követelmények más indítások szerializálásakor. Például az alábbi példában a hatókör kifejezés köti a `Region` értéket a bejövő üzenet. Ha a várólista régiók "Kelet", "Kelet" és "Nyugat" osztályban, majd az üzeneteket, amelyek "Keleti" végrehajtja Feladattervek közben az üzenet "Nyugati" végrehajtja azokat párhuzamosan régió régió 3 üzenet tartalmazza.

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

Az alapértelmezett hatókör zárolásra `SingletonScope.Function` tehát a zárolási hatókör (a blob bérleti elérési utat) a teljesen minősített függvény neve van kötve. Zárolása funkciók között, adja meg a `SingletonScope.Host` és használjon, amely ugyanazokat a funkciók nem kívánt egyidejű összes hatókör azonosító nevet. A következő példában csak egy példánya `AddItem` vagy `RemoveItem` egyszerre fut:

```charp
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

### <a name="viewing-lease-blobs"></a>Címbérlet BLOB megtekintése

A WebJobs SDK az [Azure blob-bérletek](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) a színfalak elosztott zárolás végrehajtásához. A bérleti blobok segítségével Singleton itt található: a `azure-webjobs-host` tárolóhoz a `AzureWebJobsStorage` tárfiók "zárolások" elérési úton. Például a címbérlet blob elérési út az első `ProcessImage` korábban bemutatott példa lehet `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Összes elérési út tartalmazza a JobHost Azonosítót, a nagybetűk 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Aszinkron funkciók

A kód aszinkron funkciók kapcsolatos információkért tekintse meg az Azure Functions dokumentáció [aszinkron funkciók](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Megszakítási jogkivonatok

Információ cancellation jogkivonatok kezeléséhez, dokumentációja az Azure Functions [cancellation jogkivonatokat és biztonságos leállításának](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Több példánya

Ha több példányt a webalkalmazás fut, egy folyamatos webjobs-feladat a eseményindítók figyeli és függvények hívása futó minden egyes előfordulás esetén. A különböző eseményindító kötések úgy tervezték, hogy hatékonyan közösen dolgozni elősegítése érdekében különböző példányai, így további példányokra kiterjesztése lehetővé teszi további terhelés kezelésére.

Az üzenetsor és a blob eseményindítók automatikusan megakadályozza, hogy a függvény egy várólista üzenet feldolgozása vagy a blob-egynél többször; funkciók nem kell lennie az idempotent.

Az időzítő indítófeltételt automatikusan az időzítő fut, hogy csak egy példánya lehetővé teszi, hogy egynél több függvény-példány futtatási adott ütemezett időpontban nem.

Ha biztos szeretne lenni abban, hogy csak egy példányát a függvény fut, akkor is, ha a gazdagép webes alkalmazás több példánya van, használja a [egypéldányos](#singleton) attribútum.
    
## <a name="filters"></a>Szűrők 

Függvény szűrők (előzetes verzió) teszik lehetővé a webjobs-feladatok végrehajtási folyamatban saját logikával testreszabása. Szűrők hasonlóak [ASP.NET Core szűrők](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Ezek a funkciók vagy osztályok alkalmazott deklaratív attribútumként kell végrehajtani. További információkért lásd: [függvény szűrők](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Azt javasoljuk, hogy a naplózási keretrendszer kifejlesztett ASP.NET, és a [Ismerkedés](webjobs-sdk-get-started.md) a cikk bemutatja, hogy miképpen lehet vele. 

### <a name="log-filtering"></a>Napló szűrése

Minden naplót hoz létre egy `ILogger` példány tartozik egy `Category` és `Level`. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) felsorolásoknak, és az egész kód relatív fontosságát jelzi:

|LogLevel    |Kód|
|------------|---|
|Nyomkövetés       | 0 |
|Hibakeresés       | 1 |
|Információ | 2 |
|Figyelmeztetés     | 3 |
|Hiba       | 4 |
|Kritikus    | 5 |
|None        | 6 |

Minden kategória egy adott egymástól függetlenül szűrhetők [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Például előfordulhat, hogy kívánt összes blob eseményindító feldolgozása csak a naplófájlokban `Error` és minden más magasabb.

Adja meg a szűrési szabályokat megkönnyítése a WebJobs SDK biztosít a `LogCategoryFilter` , amely azokat a meglévő naplózási szolgáltatókat, beleértve az Application insights szolgáltatással és a konzol számos függvénynek adható át.

A `LogCategoryFilter` rendelkezik egy `Default` tulajdonság a kezdeti értékkel `Information`, azt jelenti, hogy minden üzenet szintű `Information`, `Warning`, `Error`, vagy `Critical` jelentkezett, de szintűüzeneteket`Debug` vagy `Trace` számítógépnél szűrve.

A `CategoryLevels` tulajdonság lehetővé teszi a megjelölt kategóriákat naplózási szintet, úgy finomhangolhatja, a naplózás kimenetét. Ha nincs egyezés belül a `CategoryLevels` szótárban, a szűrő visszaáll a `Default` értéket, amikor eldönti, hogy az üzenet szűrésére.

A következő példa egy működő szűrő alapértelmezés szerint mindaddig naplózásra kerül minden hoz létre a `Warning` szintjét. A kategóriák `Function` vagy `Host.Results` a rendszer a `Error` szintjét. A `LogCategoryFilter` összehasonlítja az aktuális kategória az összes regisztrált `CategoryLevels` , majd a leghosszabb találatra. Ez azt jelenti, hogy a `Debug` szint regisztrált `Host.Triggers` fog egyezni `Host.Triggers.Queue` vagy `Host.Triggers.Blob`. Ez lehetővé teszi, hogy szabályozza a nagyobb kategóriák anélkül, hogy minden egyes hozzáadása.

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

### <a name="custom-telemetry-for-application-insights"></a>Az Application Insights egyéni telemetria

Belsőleg az `TelemetryClient` a WebJobs SDK használja az Application Insights-szolgáltató által létrehozott a [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Az Application Insights végpont esetén nem érhető el vagy szabályozási bejövő kérelmet, ez a csatorna [kérelmek menti a webalkalmazás-fájlrendszer, és később újra elküldi](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

A `TelemetryClient` hozta létre, amely megvalósítja az osztály `ITelemetryClientFactory`. Ez az alapértelmezés szerint a [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Ha szeretné módosítani a Application Insights-feldolgozási folyamat bármely részét, megadhatja a saját `ITelemetryClientFactory`, és a gazdagép összeállításához fogja használni az osztály egy `TelemetryClient`. Ez a kód felülírja például a `DefaultTelemetryClientFactory` tulajdonságának módosítása a `ServerTelemetryChannel`:

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

Konfigurálja a SamplingPercentageEstimatorSettings objektum [adaptív mintavételi](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Ez azt jelenti, hogy bizonyos nagy mennyiségű forgatókönyvekben App Insights telemetriai adatokat kiválasztott részhalmazát által a kiszolgálónak küldött.

Ha létrehozta az telemetriai adat-előállítóban, át kell neki adni a az Application Insights naplózási szolgáltatóhoz:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Következő lépések

Ez az útmutató nyújtott kódrészletek, amelyek bemutatják, hogyan lehet a WebJobs SDK-val kapcsolatos gyakori helyzetek kezelésére. Teljes példákért lásd: [azure-webjobs-sdk-minták](https://github.com/Azure/azure-webjobs-sdk-samples).