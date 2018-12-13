---
title: Az Azure WebJobs SDK - használata
description: További információ a WebJobs SDK-hoz készült kód írásával. Hozzon létre eseményvezérelt háttér-feldolgozási feladataikat az Azure-szolgáltatások és a külső szolgáltatások által adatok elérésére.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 6df707f0315603d30446a17bf941d5ab6028dde4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270467"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK használata az eseményvezérelt háttérben történő feldolgozás

Ez a cikk útmutatást, hogy hogyan írhat kódot [az Azure WebJobs SDK](webjobs-sdk-get-started.md). A dokumentáció verzió érvényes 2.x és 3.x, kivéve, ha nincs másként jelölve. A fő változás 3.x bevezetett, a .NET-keretrendszer helyett a .NET Core használata.

>[!NOTE]
> [Az Azure Functions](../azure-functions/functions-overview.md) épül, a WebJobs SDK-val, és ez a cikk hivatkozásokat tartalmaz olyan témakörök, az Azure Functions dokumentációját. Megjegyzés: a Functions és a WebJobs SDK között a következő eltérésekkel:
> * 1.x WebJobs SDK-verziójának felel meg az Azure Functions-verzió 2.x-es és a WebJobs SDK 2.x felel meg az Azure Functions 3.x. Forráskódtárházak kövesse a WebJobs SDK számozása, és számos 2.x ágak jelenleg a 3.x kódot kellene master ággal.
> * Az Azure Functions C#-osztálykódtárakat mintakód van, a WebJobs SDK-kód, nem kell azzal a különbséggel a `FunctionName` attribútum a WebJobs SDK-projektben.
> * Bizonyos kötési típusok csak az funkciók, például a HTTP, a webhook és az Event Grid (amely a HTTP-alapú) támogatottak. 
> 
> További információkért lásd: [összehasonlítása a WebJobs SDK-val és az Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy elolvasta [a WebJobs SDK használatának első lépései](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

A `JobHost` objektum a Functions runtime tároló: figyeli az eseményindítók és a hívások függvény. Hoz létre a `JobHost` a kód és az írási kód szabhatja annak működését.

Ez a fő különbség a közvetlenül a WebJobs SDK-val és közvetve használatával az Azure Functions szolgáltatással. Az Azure Functions, a szolgáltatás szabályozza a `JobHost`, és nem lehet testre szabni kód írásával. Azure Functions segítségével testre szabhatja a gazdagép viselkedésének beállításaival a *host.json* fájlt. Ezek a beállítások olyan karakterláncok, nem a kódban, ami a bármilyen típusú testreszabásokat is végezhet.

### <a name="jobhost-connection-strings"></a>JobHost kapcsolati karakterláncok

A WebJobs SDK-t keresi a Storage és Azure Service Bus kapcsolati karakterláncokat a *local.settings.json* futtatásakor helyileg, illetve a webjobs-feladat környezetben való futtatásakor az Azure-ban. Ha szeretné használni ezeket a kapcsolati karakterláncokat a saját nevét, vagy máshol tárolhatja őket, állíthatja be azokat a kódban, itt látható módon:

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

### <a name="jobhost-development-settings"></a>JobHost fejlesztői beállítások

A `JobHostConfiguration` osztálya rendelkezik egy `UseDevelopmentSettings` metódushoz, amely hatékonyabb helyi fejlesztés hívhatja. Íme néhány a beállítást, ez a módszer változik:

| Tulajdonság | Fejlesztési beállítás |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` maximalizálhatja a napló kimenetét. |
| `Queues.MaxPollingInterval`  | Alacsony értéket annak biztosítása érdekében az üzenetsor-módszereket a azonnal aktiválódnak.  |
| `Singleton.ListenerLockPeriod` | 15 másodperc, ezzel elősegítve az gyors, iteratív fejlesztés. |

Az alábbi példa bemutatja, hogyan fejlesztői beállítások használatára. Győződjön meg arról, hogy `config.IsDevelopment` vissza `true` helyi futtatáskor nevű helyi környezeti változó beállítása `AzureWebJobsEnv` értékkel `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>JobHost ServicePointManager beállításai

A .NET-keretrendszer tartalmaz egy API-t nevű [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , amely meghatározza, hogy az egyidejű kapcsolatok száma egy gazdagépre. Azt javasoljuk, hogy ezt az értéket az alapértelmezett 2. növelje a webjobs-feladatok gazdagép indítása előtt.

Az összes kimenő HTTP-kéréseket, amelyek egy függvény használatával teszik `HttpClient` keresztül érhetők el a `ServicePointManager`. Ha eléri a `DefaultConnectionLimit`, a `ServicePointManager` üzenetsor kérelmek elindítja őket elküldése előtt. Tegyük fel, hogy a `DefaultConnectionLimit` értéke 2. és a kód révén az 1000 HTTP-kérelmekre. Kezdetben csak 2 kérelmek ténylegesen engedélyezettek keresztül az operációs rendszerre. A többi 998 a rendszer várólistára helyezi addig, amíg nincs hely a számukra. Azt jelenti, hogy a `HttpClient` időtúllépés, lehet, mert azt *fenyegetésként észlel* , rendelkezik a kérést, de a kérést küldött az operációs rendszer soha nem a célkiszolgálón. Ezért előfordulhat, hogy nem Microsoft-jelentéssel bírnak viselkedés: a helyi `HttpClient` teljesítse a kérelmet 10 másodpercet vesz igénybe, de a szolgáltatás minden kérelemnél adja vissza a 200 ms. 

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

Automatikus eseményindítók egy esemény bekövetkeztekor függvény hívása. Egy vonatkozó példáért tekintse meg az üzenetsor eseményindító a [Get lépéseket bemutató cikkben](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Manuális eseményindító

Egy függvény manuális aktiválásához használhatja az `NoAutomaticTrigger` attribútum, az alábbi példában látható módon:

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

Bemeneti adatok az Azure-ban vagy külső szolgáltatások számára elérhetővé szeretné tenni a kód deklaratív módszert biztosítanak. Kimeneti kötések lehetővé teszik egy frissítheti az adatokat. A [Get lépéseket bemutató cikkben](webjobs-sdk-get-started.md) egyes egy példát mutat be.

A metódus visszatérési értékek használható egy kimeneti kötést az attribútum a metódus visszatérési érték alkalmazásával. A példa az Azure Functions szolgáltatásban [eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) cikk.

## <a name="binding-types"></a>Kötési típus

A következő eseményindítót és a kötési típusok szerepelnek a `Microsoft.Azure.WebJobs` csomag:

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

Megtalálhatja a csomag telepítéséhez az adott kötési típus a **csomagok** a kötési típus szakaszában [áttekintésével foglalkozó cikkben](#binding-reference-information) az Azure Functions szolgáltatáshoz. Kivétel ez alól a fájlok trigger és kötése (a helyi fájlrendszerben;), ami nem támogatott az Azure Functions. a fájlok telepítése kötelező, `Microsoft.Azure.WebJobs.Extensions` hívja `UseFiles`.

### <a name="usecore"></a>UseCore

A `Microsoft.Azure.WebJobs.Extensions` a korábban említett csomag is biztosít egy speciális kötési típus, amely meghívásával regisztrálhatja a `UseCore` metódust. Ez a kötés lehetővé teszi, hogy meghatározhatja egy [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) parametr obecného a függvényt. A környezeti objektumon a Hívásazonosítót, amellyel korrelációját, ha egy adott függvény meghívási által előállított összes napló hozzáférést biztosít. Például:

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

## <a name="binding-configuration"></a>Kötés konfigurálása

Számos aktiválása és a kötési típus segítségével konfigurálhatja a felhasználók viselkedését egy konfigurációs objektumot, amelyeket átad a tulajdonságainak beállításával a `JobHost`.

### <a name="queue-trigger-configuration"></a>Üzenetsor eseményindító konfigurációja

A beállítások konfigurálhatók a tárolási üzenetsor eseményindító mutatjuk be az Azure Functions [host.json referencia](../azure-functions/functions-host-json.md#queues). Hogyan állítható be őket a WebJobs SDK-projektben a következő példában látható:

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

### <a name="configuration-for-other-bindings"></a>Konfigurációs más kötések

Bizonyos eseményindítót és a kötési típusok saját egyéni konfiguráció típusa határozza meg. Például a fájl eseményindító megadhatja a gyökér elérési úthoz figyelése:

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

Kötési kifejezésekben kapcsolatos további információkért lásd: [kifejezések és a minták](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) az Azure Functions dokumentációjában.

### <a name="custom-binding-expressions"></a>Egyéni kötési kifejezésekben

Néha szeretne hozzáadni egy üzenetsor neve, a blob nevét vagy a tároló, vagy egy Táblanév rögzítse szoftveresen helyett a kód azt. Például érdemes a várólista nevét adja meg a `QueueTrigger` attribútum egy konfigurációs fájlban vagy környezeti változóban.

Úgy teheti meg, amely megadásának egy `NameResolver` az objektum a `JobHostConfiguration` objektum. A trigger vagy kötési attribútum konstruktor paraméterek helyőrzőket tartalmaznak, és a `NameResolver` kódot biztosít a tényleges értékek ezeket a helyőrzők helyett használható. A helyőrzők azonosítja téve őket a százalék (%) jelentkezik, az alábbi példában látható módon:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ez a kód a tesztelési környezetben logqueuetest és a egy elnevezett logqueueprod éles környezetben nevű üzenetsor használatát teszi lehetővé. Egy szokott várólista neve helyett nevét adja meg, egy bejegyzés a `appSettings` gyűjtemény. 

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
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
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

Megadhat egy **hatókör-érték kifejezés** az egyedülálló, amely biztosítja, hogy az adott hatókörben függvény az összes végrehajtás szerializálható lesz a. Részletesebb zárolás ezzel a módszerrel a végrehajtási lehetővé teszik bizonyos szintű párhuzamosság a függvény által igényeinek más indítások szerializálásakor. Ha például az alábbi példában a hatókör-kifejezés van kötve a `Region` értéket a bejövő üzenet. Ha a várólista a régiók "Keleti régiója", "Keleti régiója" és "Nyugati régiója" jelölik, majd az üzeneteket, amelyeken a régióban "Kelet" lesz végrehajtva tárolókonfigurációhoz közben az üzenetre az "West" hajtani, és azok régió 3 üzenet tartalmazza.

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

Ha azt szeretné, hogy csak egy példánya, a függvény akkor fut, akkor is, ha a gazdagép webes alkalmazás több példánya, használja a [egyszeres](#singleton) attribútum.
    
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

Könnyebb adja meg a szűrési szabályokat, a WebJobs SDK biztosítja a `LogCategoryFilter` , amelyek átadhatók be számos, a meglévő naplózási-szolgáltatók, például az Application Insights és a konzol.

A `LogCategoryFilter` rendelkezik egy `Default` tulajdonság a kezdeti értékkel `Information`, azt jelenti, hogy olyan üzeneteket szintű `Information`, `Warning`, `Error`, vagy `Critical` jelentkezett, de olyan üzeneteket szintű`Debug` vagy `Trace` azonnal vannak szűrve.

A `CategoryLevels` tulajdonság lehetővé teszi, hogy adja meg a naplózási szintjeivel a megjelölt kategóriákat úgy finomhangolhatja a naplózás kimenetét. Ha nem talál egyezést belül a `CategoryLevels` szótárban, a szűrő visszavált a `Default` értékét, amikor eldönti, hogy az üzenet szűrésére.

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

Belsőleg az `TelemetryClient` a WebJobs SDK-t használja az Application Insights-szolgáltató által létrehozott a [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Az Application Insights-végpont esetén nem érhető el, vagy a sávszélesség-szabályozási bejövő kérelmek, ez a csatorna [kérelmek menti a webalkalmazás-fájlrendszer, és később újra elküldi](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

A `TelemetryClient` jön létre, amely megvalósítja az osztályok által `ITelemetryClientFactory`. Ez az alapértelmezés szerint a [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Ha szeretné módosítani az Application Insights folyamat bármely része, megadhatja a saját `ITelemetryClientFactory`, és a gazdagép létrehozására fogja használni az osztály egy `TelemetryClient`. Például ez a kód felülírja az `DefaultTelemetryClientFactory` egy tulajdonságának módosítása a `ServerTelemetryChannel`:

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