---
title: Azure Functions C# fejlesztői útmutató
description: Ismerje meg, hogyan fejlesztheti az Azure Functions t a C# használatával.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277062"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# fejlesztői útmutató

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ez a cikk az Azure Functions fejlesztésének bemutatkozása a C# használatával a .NET osztálytárakban.

Az Azure Functions támogatja a C# és a C# parancsfájl programozási nyelvek. Ha útmutatást keres a C# használatával kapcsolatosan [az Azure Portalon,](functions-create-function-app-portal.md)olvassa el a [C# script (.csx) fejlesztői útmutatót.](functions-reference-csharp.md)

Ez a cikk feltételezi, hogy már elolvasta a következő cikkeket:

* [Útmutató az Azure Functions fejlesztőinek](functions-reference.md)
* [Az Azure Functions Visual Studio 2019 eszközei](functions-develop-vs.md)

## <a name="supported-versions"></a>Támogatott verziók

A Functions futásidejű verziói a .NET adott verzióival dolgoznak. Az alábbi táblázat a .NET Core és a .NET Framework és a .NET Core legmagasabb szintjét mutatja be, amely a projekt függvényeinek egy adott verziójával használható. 

| Függvények futásidejű verziója | Max .NET verzió |
| ---- | ---- |
| Funkciók 3.x | .NET Core 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET-keretrendszer 4.6 |

További információ: [Azure Functions runtime versions overview](functions-versions.md)

## <a name="functions-class-library-project"></a>Funkciók osztálykönyvtár-projekt

A Visual **Studio-ban** az Azure Functions projektsablon létrehoz egy C# osztálykönyvtár-projektet, amely a következő fájlokat tartalmazza:

* [host.json](functions-host-json.md) – tárolja azokat a konfigurációs beállításokat, amelyek hatással vannak a projekt összes függvényére, amikor helyileg vagy az Azure-ban futnak.
* [local.settings.json](functions-run-local.md#local-settings-file) - tárolja a helyi futtatáskor használt alkalmazásbeállításokat és kapcsolati karakterláncokat. Ez a fájl titkos kulcsokat tartalmaz, és nem teszi közzé a függvényalkalmazásban az Azure-ban. Ehelyett [adja hozzá az alkalmazásbeállításokat a függvényalkalmazáshoz.](functions-develop-vs.md#function-app-settings)

A projekt létrehozásakor a buildkimeneti könyvtárban a következő példához hasonló mappastruktúra jön létre:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ez a könyvtár az, amit kap üzembe helyezése a függvényalkalmazás az Azure-ban. A Functions futásidejű [2.x-es verziójában](functions-versions.md) szükséges kötési kiterjesztések [NuGet csomagokként kerülnek a projektbe.](./functions-bindings-register.md#vs)

> [!IMPORTANT]
> A létrehozási folyamat minden függvényhez létrehoz egy *function.json* fájlt. Ez *a function.json* fájl nem közvetlenül szerkeszthető. A fájl szerkesztésével nem módosíthatja a kötési konfigurációt, és nem tilthatja le a funkciót. Ha tudni szeretné, hogyan tilthatja le a függvényeket, olvassa el [a Funkciók letiltása](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Függvényként felismert metódusok

Az osztálytárban a függvény egy statikus `FunctionName` metódus egy és egy eseményindító attribútummal, ahogy az a következő példában látható:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Az `FunctionName` attribútum a metódust függvénybelépési pontként jelöli meg. A névnek egyedinek kell lennie a projekten belül, `_`betűvel `-`kell kezdődnie, és csak betűket, számokat és legfeljebb 127 karakter hosszúságú analikát. A projektsablonok gyakran hoznak létre egy metódust, `Run`de a metódus neve bármely érvényes C# metódusnév lehet.

Az eseményindító attribútum megadja az eseményindító típusát, és a bemeneti adatokat egy metódusparaméterhez köti. A példa függvényt egy várólista-üzenet váltja ki, és `myQueueItem` a várólista-üzenet a paraméterben lévő metódusnak kerül.

## <a name="method-signature-parameters"></a>Metódus aláírási paraméterei

A metódus aláírása nem az eseményindító attribútummal használt paramétereket tartalmazhat. Íme néhány további paraméter, amelyet felvehet:

* Az ilyenként megjelölt [bemeneti és kimeneti kötések](functions-triggers-bindings.md) attribútumokkal való díszítéssel.  
* A `ILogger` `TraceWriter` [naplózáshoz](#logging)egy vagy ([csak 1.x verziójú)](functions-versions.md#creating-1x-apps)paraméter.
* A `CancellationToken` [szabályos leállítás](#cancellation-tokens)paramétere.
* [Kötési kifejezések paraméterei](./functions-bindings-expressions-patterns.md) az eseményindító metaadatainak leéséhez.

A paraméterek sorrendje a függvény aláírásnem számít. Például más kötések előtt vagy után is elhelyezheti az eseményindító paramétereket, és a naplózó paramétert az eseményindító vagy a kötési paraméterek elé vagy után helyezheti el.

### <a name="output-binding-example"></a>Példa kimenetkötésre

A következő példa módosítja az előzőt egy kimeneti várólista-kötés hozzáadásával. A függvény a várólistára küldött üzenetet írja, amely egy másik várólistában lévő új várólista-üzenetbe indítja a függvényt.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

A kötési referenciacikkek ([például tárolási várólisták ) ismertetik,](functions-bindings-storage-queue.md)hogy mely paramétertípusok használhatók az eseményindító, a bemeneti vagy a kimeneti kötési attribútumokkal.

### <a name="binding-expressions-example"></a>Példa kötési kifejezésekre

A következő kód leadja a figyelni figyelt várólista nevét egy alkalmazásbeállításból, és megkapja a várólista-üzenet létrehozási idejét a `insertionTime` paraméterben.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatikusan generált függvény.json

A létrehozási folyamat *egy function.json* fájlt hoz létre a buildmappa egy függvénymappájában. Amint azt korábban említettük, ez a fájl nem közvetlenül szerkeszthető. A fájl szerkesztésével nem módosíthatja a kötési konfigurációt, és nem tilthatja le a funkciót. 

Ennek a fájlnak az a célja, hogy információt szolgáltasson a méretezési vezérlőnek, amelyet [a Felhasználási tervre vonatkozó döntések méretezéséhez](functions-scale.md#how-the-consumption-and-premium-plans-work)használhat. Emiatt a fájl csak az eseményindító adatokat, nem bemeneti vagy kimeneti kötések.

A létrehozott *function.json* fájl `configurationSource` tartalmaz egy tulajdonságot, amely azt mondja a futásidőnek, hogy *a function.json* konfiguráció helyett .NET attribútumokat használjon a kötésekhez. Például:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

A *function.json* fájlgenerálását a [\.Microsoft NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)nuGet csomag hajtja végre. 

Ugyanez a csomag használatos a Functions futásidejű 1.x és 2.x verziójához is. A célkeretrendszer különbözteti meg az 1.x projektet egy 2.x projekttől. A *.csproj* fájlok megfelelő részei a különböző célkeretrendszereket és ugyanazt `Sdk` a csomagot mutatják:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

A `Sdk` csomagfüggőségek között vannak eseményindítók és kötések. Az 1.x projekt 1.x eseményindítókra és kötésekre utal, mivel ezek az eseményindítók és kötések a .NET keretrendszert célozzák meg, míg a 2.x-ek a .NET Core-t.

A `Sdk` csomag a [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json)tól és közvetve a [WindowsAzure.Storage-tól](https://www.nuget.org/packages/WindowsAzure.Storage)is függ. Ezek a függőségek győződjön meg arról, hogy a projekt a projekt által megcélozott Functions futásidejű verzióval működő csomagok verzióit használja. Például `Newtonsoft.Json` rendelkezik a .NET Framework 4.6.1 11-es verziójával, de a Functions runtime, amely a .NET Framework 4.6.1-et célozza, csak a 9.0.1-es verzióval `Newtonsoft.Json` kompatibilis. Tehát a függvénykódnak a projektben is a 9.0.1-et kell használnia. `Newtonsoft.Json`

A forráskód `Microsoft.NET.Sdk.Functions` a GitHub repo azure [\-\-\-függvényekben és build\-SDK-ben](https://github.com/Azure/azure-functions-vs-build-sdk)érhető el.

## <a name="runtime-version"></a>Futásidejű verzió

A Visual Studio az [Azure Functions Core Tools segítségével](functions-run-local.md#install-the-azure-functions-core-tools) futtatja a Functions-projekteket. A Core Tools a Functions futásidejű parancssori felülete.

Ha a Core Tools eszközt npm használatával telepíti, az nincs hatással a Visual Studio által használt Core Tools verzióra. A Functions runtime 1.x verziójában a Visual Studio a Core Tools verzióit *a %USERPROFILE%\AppData\Local\Azure.Functions.Cli* mappában tárolja, és az ott tárolt legújabb verziót használja. A Functions 2.x esetében a Core Tools az Azure Functions és a **Web Jobs Tools** bővítményrészét tartalmazza. Mind az 1.x, mind a 2.x esetében láthatja, hogy milyen verziót használ a konzolkimenetben a Functions projekt futtatásakor:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Támogatott típusú kötések

Minden kötésnek saját támogatott típusa van; például egy blob trigger attribútum alkalmazható egy karakterlánc-paraméterre, egy `CloudBlockBlob` POCO paraméterre, egy paraméterre vagy számos más támogatott típusra. A [blob-kötések kötési referenciacikke](functions-bindings-storage-blob-trigger.md#usage) felsorolja az összes támogatott paramétertípust. További információ: [Eseményindítók és kötések,](functions-triggers-bindings.md) valamint az [egyes kötéstípusokkötési hivatkozási dokumentumai.](functions-triggers-bindings.md#next-steps)

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötés a metódus visszatérési értékéhez

Az attribútum nak a metódus visszatérési értékére való alkalmazásával metódusvisszatérési értéket használhat a kimenetkötéshez. Példák: [Eseményindítók és kötések](./functions-bindings-return-value.md). 

Csak akkor használja a visszatérési értéket, ha a sikeres függvényvégrehajtás mindig a kimeneti kötésnek átadandó visszatérési értéket eredményez. Ellenkező esetben `ICollector` `IAsyncCollector`használja a vagyat, ahogy az a következő szakaszban látható.

## <a name="writing-multiple-output-values"></a>Több kimeneti érték írása

Ha több értéket szeretne írni egy kimeneti kötésbe, vagy ha egy sikeres függvénymeghívás [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nem [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) eredményez semmit a kimeneti kötésnek, használja a vagy típusokat. Ezek a típusok csak írható gyűjtemények, amelyek a metódus befejezésekor a kimeneti kötésbe íródnak.

Ez a példa több várólista-üzenetet ír ugyanabba a várólistába a következő használatával: `ICollector`

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Naplózás

A c#-ban lévő streamelési naplók kimenetének naplózásához adjon meg egy [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot. Javasoljuk, hogy nevezze `log`el , ahogy az a következő példában is szerepel:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Ne `Console.Write` használja az Azure Functions. További információ: [Naplók írása C# függvényekben](functions-monitoring.md#write-logs-in-c-functions) az **Azure Functions figyelője** cikkben.

## <a name="async"></a>Aszinkron

Ahhoz, hogy egy függvény [aszinkron,](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)használja `async` `Task` a kulcsszót, és vissza egy objektumot.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Az aszinkron `out` függvényekben nem használhatók paraméterek. Kimeneti kötések esetén használja a [függvény visszatérési értékét](#binding-to-method-return-value) vagy egy [gyűjtőobjektumot.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Törlési tokenek

Egy függvény elfogadhatja [az CancellationToken](/dotnet/api/system.threading.cancellationtoken) paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a kódot, amikor a függvény meg fog szűnni. Ezzel az értesítéssel biztosíthatja, hogy a függvény ne fejeződjön be váratlanul úgy, hogy az adatok inkonzisztens állapotban maradnak.

A következő példa bemutatja, hogyan ellenőrizheti a közelgő függvények leállítását.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
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
}
```

## <a name="environment-variables"></a>Környezeti változók

Környezeti változó vagy alkalmazásbeállítási érték `System.Environment.GetEnvironmentVariable`lekérése, használja a használatát, ahogy az a következő kódpéldában látható:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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
}
```

Az alkalmazásbeállítások a környezeti változókból is olvashatók, ha helyileg, és az Azure-ban való futtatáskor is futtatják. Helyi fejlesztés esetén az alkalmazásbeállítások `Values` a *local.settings.json* fájlgyűjteményéből származnak. A helyi és az Azure-környezetben is `GetEnvironmentVariable("<app setting name>")` lekéri a megnevezett alkalmazásbeállítás értékét. Ha például helyileg fut, a "Saját hely neve" értéket adja vissza, ha `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`a *local.settings.json* fájl tartalmazza.

A [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) tulajdonság egy alternatív API az alkalmazásbeállítási értékek `GetEnvironmentVariable` beszerzéséhez, de azt javasoljuk, hogy az itt látható módon használja.

## <a name="binding-at-runtime"></a>Kötés futásidőben

C# és más .NET nyelveken az attribútumok [*deklaratív*](https://en.wikipedia.org/wiki/Declarative_programming) kötéseivel szemben [egy imperatív](https://en.wikipedia.org/wiki/Imperative_programming) kötést is használhat. A minós kötés akkor hasznos, ha a kötési paramétereket futásidőben kell kiszámítani, nem pedig tervezési időben. Ezzel a mintával a függvénykódban menet közben is köthető a támogatott bemeneti és kimeneti kötésekhez.

A következőképpen határozhatja meg a kötelező kötést:

- **Ne** adjon meg attribútumot a függvényaláírásban a kívánt imperatív kötésekhez.
- Adja meg a [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)bemeneti paramétert vagy a lehetőséget.
- Az adatkötés végrehajtásához használja a következő C# mintát.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`A .NET attribútum, amely meghatározza a `T` kötést, és egy bemeneti vagy kimeneti típus, amelyet az adott kötéstípus támogat. `T`nem lehet `out` paramétertípus (például `out JObject`). Például a Mobile Apps tábla kimeneti kötés támogatja [a hat kimeneti típusok,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)de csak akkor használhatja [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [\<IAsyncCollector T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) elengedhetetlen kötés.

### <a name="single-attribute-example"></a>Példa egyetlen attribútumra

A következő példakód létrehoz egy [Storage blob kimeneti kötést](functions-bindings-storage-blob-output.md) a blob elérési útját, amely futásidőben van definiálva, majd egy karakterláncot ír a blobba.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[A BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiálja a [Storage blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötést, és a [TextWriter](/dotnet/api/system.io.textwriter) egy támogatott kimeneti kötéstípus.

### <a name="multiple-attribute-example"></a>Példa több attribútumra

Az előző példa lekéri az alkalmazás beállítását a függvényalkalmazás `AzureWebJobsStorage`fő storage-fiók kapcsolati karakterláncához (amely). A [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) attribútum hozzáadásával és az attribútumtömb nek való átadásával egyéni `BindAsync<T>()`alkalmazásbeállítást adhat meg a StorageAccountAttribute attribútumhozzáadásával. Ne `Binder` a `IBinder`paramétert használja.  Példa:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Eseményindítók és kötések 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítókról és kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ az Azure Functions bevált módszereiről](functions-best-practices.md)
