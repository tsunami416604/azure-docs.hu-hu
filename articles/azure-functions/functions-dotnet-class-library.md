---
title: Azure Functions C# fejlesztői dokumentáció
description: Ismerje meg, hogyan fejlesztheti C#Azure functions a használatával.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356990"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# fejlesztői dokumentáció

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ez a cikk bevezetést mutat be a .NET C# -es kódtárak használatával történő Azure functions fejlesztéséhez.

Azure Functions támogatja C# és C# parancsfájl-programozási nyelveket támogat. Ha [a Azure Portal használatával C# ](functions-create-function-app-portal.md)kapcsolatos útmutatást keres, tekintse [ C# meg a script (. CSX) fejlesztői referenciáját](functions-reference-csharp.md).

Ez a cikk azt feltételezi, hogy már elolvasta a következő cikkeket:

* [Azure Functions fejlesztői útmutató](functions-reference.md)
* [Azure Functions Visual Studio 2019-eszközök](functions-develop-vs.md)

## <a name="supported-versions"></a>Támogatott verziók

A functions futtatókörnyezet verziói a .NET adott verziójával működnek. Az alábbi táblázat a .NET Core és a .NET-keretrendszer és a .NET Core legmagasabb szintjét mutatja be, amelyek a projektben a függvények adott verziójával használhatók. 

| Functions futtatókörnyezet verziója | .NET-es verzió max. verziója |
| ---- | ---- |
| Függvények 3. x | .NET Core 3,1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET-keretrendszer 4.6 |

További információ: [Azure functions Runtime-verziók áttekintése](functions-versions.md)

## <a name="functions-class-library-project"></a>Functions Class Library-projekt

A Visual Studióban a **Azure functions** Project sablon egy olyan C# osztály-függvénytár-projektet hoz létre, amely a következő fájlokat tartalmazza:

* a [Host. JSON](functions-host-json.md) – olyan konfigurációs beállításokat tárol, amelyek a projekt összes funkcióját érintik helyileg vagy az Azure-ban való futtatáskor.
* [Local. Settings. JSON](functions-run-local.md#local-settings-file) – a helyileg futtatott Alkalmazásbeállítások és kapcsolódási karakterláncok tárolására szolgál. Ez a fájl titkokat tartalmaz, és nincs közzétéve az Azure-beli Function alkalmazásban. Ehelyett [adja hozzá az alkalmazás beállításait a Function alkalmazáshoz](functions-develop-vs.md#function-app-settings).

A projekt létrehozásakor a következő példához hasonló mappastruktúrát jön létre a Build kimeneti könyvtárában:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ez a könyvtár az Azure-beli Function alkalmazás üzembe helyezése. A functions futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési bővítmények a [projekthez NuGet csomagokként lesznek hozzáadva](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> A build folyamat minden függvényhez létrehoz egy *function. JSON* fájlt. A *function. JSON* fájlt nem közvetlenül kell szerkeszteni. Nem módosíthatja a kötési konfigurációt, vagy letilthatja a függvényt a fájl szerkesztésével. A függvények letiltásával kapcsolatos további információkért lásd a [függvények letiltását](disable-function.md)ismertető témakört.


## <a name="methods-recognized-as-functions"></a>Függvényekként felismert metódusok

Az osztályban a függvények statikus metódusok egy `FunctionName` és egy trigger attribútummal, ahogy az alábbi példában látható:

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

A `FunctionName` attribútum a metódust függvény belépési pontként jelöli. A névnek egyedinek kell lennie a projekten belül, betűvel kell kezdődnie, és csak betűket, számokat, `_`és `-`tartalmazhat, legfeljebb 127 karakter hosszú lehet. A Project templates gyakran létrehoz egy `Run`nevű metódust, de a metódus neve lehet C# bármely érvényes metódus neve.

Az trigger attribútum meghatározza az trigger típusát, és a bemeneti adatokat egy metódus paraméteréhez köti. A példa függvényt egy üzenetsor-üzenet indítja el, és a várólista-üzenet a `myQueueItem` paraméterben megadott metódusnak lesz átadva.

## <a name="method-signature-parameters"></a>Metódus-aláírás paraméterei

A metódus aláírása nem az trigger attribútummal használt paramétereket tartalmazhat. Íme néhány további paraméter, amelyet felvehet:

* A [bemeneti és kimeneti kötések](functions-triggers-bindings.md) úgy vannak megjelölve, hogy az attribútumokkal díszítsék őket.  
* Egy `ILogger` vagy `TraceWriter` ([csak 1. x verziójú](functions-versions.md#creating-1x-apps)) paraméter a [naplózáshoz](#logging).
* Egy `CancellationToken` paraméter a [kecses leállításhoz](#cancellation-tokens).
* A [kötési kifejezések](./functions-bindings-expressions-patterns.md) paraméterei trigger-metaadatok beszerzéséhez.

A függvények aláírásában szereplő paraméterek sorrendje nem számít. Például az aktiválási paramétereket más kötések előtt vagy után is elhelyezheti, és a naplózó paramétert trigger vagy kötési paraméterek előtt vagy után is elhelyezheti.

### <a name="output-binding-example"></a>Kimeneti kötési példa

A következő példa egy kimeneti várólista-kötés hozzáadásával módosítja az előzőt. A függvény az üzenetsor-üzenetet írja, amely egy másik várólistában lévő új üzenetsor-üzenetre indítja a függvényt.

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

A kötési útmutató cikkei (például a[tárolási várólisták](functions-bindings-storage-queue.md)) megmutatják, hogy milyen típusú paramétereket használhat trigger-, bemeneti vagy kimeneti kötési attribútumokkal.

### <a name="binding-expressions-example"></a>Példa kötési kifejezésekre

A következő kód beolvassa az Alkalmazásbeállítások által figyelt várólista nevét, és a várólista-üzenet létrehozási idejét a `insertionTime` paraméterben kapja meg.

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

## <a name="autogenerated-functionjson"></a>Automatikusan generált function. JSON

A build folyamat létrehoz egy *function. JSON* fájlt a Build mappában található Function mappában. Amint azt korábban említettük, a fájlt nem közvetlenül kell szerkeszteni. Nem módosíthatja a kötési konfigurációt, vagy letilthatja a függvényt a fájl szerkesztésével. 

Ennek a fájlnak a célja, hogy információt szolgáltasson a méretezési vezérlő számára [a használati tervre vonatkozó döntések skálázásához](functions-scale.md#how-the-consumption-and-premium-plans-work). Emiatt a fájl csak trigger-információkkal, nem bemeneti vagy kimeneti kötésekkel rendelkezik.

A generált *function. JSON* fájl egy `configurationSource` tulajdonságot tartalmaz, amely azt jelzi, hogy a futtatókörnyezet .net-attribútumokat használ a kötésekhez ahelyett, hogy *function. JSON* -konfigurációt alkalmazzon. Például:

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

A *function. JSON* -fájl létrehozását a [Microsoft\.NET\.SDK\.functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)NuGet csomag hajtja végre. 

Ugyanez a csomag a functions futtatókörnyezet 1. x és 2. x verziójára is használható. A cél-keretrendszer az 1. x projekt megkülönböztetése egy 2. x projektből. Az alábbiakban láthatók a *. csproj* -fájlok azon részei, amelyek különböző cél-keretrendszereket és azonos `Sdk` csomagot mutatnak be:

**Függvények 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Függvények 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

A `Sdk` csomag függőségei közé tartozik az eseményindítók és a kötések. Egy 1. x projekt egy 1. x eseményindítóra és kötésre hivatkozik, mivel ezek az eseményindítók és kötések a .NET-keretrendszert célozzák meg, a 2. x eseményindítók és kötések pedig a .NET Core-t célozzák meg.

A `Sdk` csomag a [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json)fájltól, illetve a [WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage)-ban közvetve is függ. Ezek a függőségek gondoskodnak arról, hogy a projekt azokat a csomagokat használja, amelyek a projekt által megcélzott functions futtatókörnyezet-verzióval működnek. Például `Newtonsoft.Json` a .NET-keretrendszer 4.6.1-es verziójának 11-ös verziójával rendelkezik, de a .NET-keretrendszer 4.6.1-t célbaő functions futtatókörnyezet csak `Newtonsoft.Json` 9.0.1 kompatibilis. Így a projekt kódjának is `Newtonsoft.Json` 9.0.1 kell használnia.

`Microsoft.NET.Sdk.Functions` forráskódja elérhető a GitHub-tárház [azure\-functions\-vs\-build\-SDK](https://github.com/Azure/azure-functions-vs-build-sdk)-ban.

## <a name="runtime-version"></a>Futtatókörnyezet verziója

A Visual Studio a [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) használatával futtatja a functions-projekteket. A Core Tools a functions futtatókörnyezet parancssori felülete.

Ha a NPM használatával telepíti az alapvető eszközöket, amelyek nem érintik a Visual Studio által használt alapvető eszközök verzióját. A functions Runtime 1. x verziójában a Visual Studio a *%USERPROFILE%\AppData\Local\Azure.functions.CLI* alapeszközök verzióját tárolja, és az ott tárolt legújabb verziót használja. A 2. x függvények esetében az alapvető eszközök a **Azure functions és a web Jobs Tools** bővítmény részét képezik. Az 1. x és a 2. x esetében egyaránt megtekintheti, hogy a konzol kimenetében milyen verzió van használatban a functions projekt futtatásakor:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusai

Minden kötés saját támogatott típusokkal rendelkezik; például egy blob trigger attribútumot lehet alkalmazni egy karakterlánc-paraméterre, egy POCO paraméterre, egy `CloudBlockBlob` paraméterre vagy számos más támogatott típusra. A [blob-kötések kötési útmutatója](functions-bindings-storage-blob-trigger.md#usage) felsorolja az összes támogatott paraméter-típust. További információ: [triggerek és kötések](functions-triggers-bindings.md) , valamint az [egyes kötési típusok kötési dokumentációja](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötés a metódus visszatérési értékéhez

A metódus visszatérési értékét egy kimeneti kötéshez is használhatja, ha az attribútumot a metódus visszatérési értékére alkalmazza. Példákat az [Eseményindítók és kötések](./functions-bindings-return-value.md)című témakörben talál. 

Csak akkor használja a visszaadott értéket, ha egy sikeres függvény végrehajtása mindig visszatérési értéket ad eredményül a kimeneti kötésnek. Ellenkező esetben használja a `ICollector` vagy `IAsyncCollector`, ahogy az a következő szakaszban látható.

## <a name="writing-multiple-output-values"></a>Több kimeneti érték írása

Ha több értéket szeretne írni egy kimeneti kötésbe, vagy ha egy sikeres függvény meghívása nem eredményez semmit a kimeneti kötéshez, használja a [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típust. Ezek a típusok olyan írásvédett gyűjtemények, amelyek a metódus befejeződése után a kimeneti kötésbe íródnak.

Ez a példa több üzenetsor-üzenetet ír ugyanabba a várólistába `ICollector`használatával:

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

Ha a kimenetét be szeretné jelentkezni a C#folyamatos átviteli naplókba, vegyen fel egy [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot. Javasoljuk, hogy nevezze el `log`, ahogy az alábbi példában is látható:  

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

Kerülje a Azure Functions `Console.Write` használatának elkerülését. További információ: a [naplók írása a C# functions](functions-monitoring.md#write-logs-in-c-functions) szolgáltatásban a **figyelő Azure functions** cikkben.

## <a name="async"></a>Aszinkron

A függvények [aszinkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)végrehajtásához használja a `async` kulcsszót, és egy `Task` objektumot ad vissza.

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

Aszinkron függvények nem használhatnak `out` paramétereket. Kimeneti kötések esetén használja helyette a [függvény visszatérési értékét](#binding-to-method-return-value) vagy egy [gyűjtő objektumot](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Visszavonási tokenek

A függvények elfogadják a [CancellationToken](/dotnet/api/system.threading.cancellationtoken) paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a kódot, ha a függvény hamarosan leáll. Ezzel az értesítéssel meggyőződhet arról, hogy a függvény váratlanul leáll olyan módon, amely inkonzisztens állapotban hagyja az adatvesztést.

Az alábbi példa bemutatja, hogyan ellenőrizhető a közelgő függvények leállítása.

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

Környezeti változó vagy Alkalmazásbeállítások értékének beszerzéséhez használja a `System.Environment.GetEnvironmentVariable`, ahogy az a következő példában látható:

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

Az Alkalmazásbeállítások a helyi fejlesztésekor és az Azure-ban való futtatáskor is beolvashatók környezeti változókból. Helyi fejlesztés esetén az Alkalmazásbeállítások a *Local. Settings. JSON* fájlban lévő `Values` gyűjteményből származnak. A helyi és az Azure-beli környezetekben `GetEnvironmentVariable("<app setting name>")` lekéri az elnevezett alkalmazás értékét. Ha például helyileg fut, a "saját hely neve" értéket adja vissza, ha a *Local. Settings. JSON* fájl `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`tartalmaz.

A [System. Configuration. ConfigurationManager. appSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) tulajdonság egy alternatív API az Alkalmazásbeállítások értékének beolvasásához, de javasoljuk, hogy az itt látható módon használja a `GetEnvironmentVariable`.

## <a name="binding-at-runtime"></a>Kötés futásidőben

A C# -ben és más .net nyelveken a kötelező [kötési mintát](https://en.wikipedia.org/wiki/Imperative_programming) is használhatja, az attribútumok [*deklaratív*](https://en.wikipedia.org/wiki/Declarative_programming) kötéseivel szemben. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. Ezzel a mintával a függvény kódjában a támogatott bemeneti és kimeneti kötésekhez köthető.

A következő módon adjon meg egy kötelező kötést:

- **Ne tartalmazzon** attribútumot a függvény aláírásában a kívánt kényszerített kötésekhez.
- Adjon át egy bemeneti paramétert [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Az adatkötés C# végrehajtásához használja a következő mintát.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` a kötést definiáló .NET-attribútum, a `T` pedig az adott kötési típus által támogatott bemeneti vagy kimeneti típus. `T` nem lehet `out` paraméter típusa (például `out JObject`). Például a Mobile Apps tábla kimeneti kötése [hat kimeneti típust](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)támogat, de csak a [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy a [IAsyncCollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) is használhatja a kényszerített kötéssel.

### <a name="single-attribute-example"></a>Példa egyetlen attribútumra

A következő mintakód létrehoz egy [tárolási blob kimeneti kötést](functions-bindings-storage-blob-output.md) a futási időben definiált blob elérési úttal, majd karakterláncot ír a blobba.

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

A [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) meghatározza a [Storage blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötését, és a [TextWriter](/dotnet/api/system.io.textwriter) egy támogatott kimeneti kötési típus.

### <a name="multiple-attribute-example"></a>Több attribútum – példa

Az előző példában beolvassa a Function alkalmazás fő Storage-fiókjának (`AzureWebJobsStorage`) az alkalmazás beállítását. A Storage-fiókhoz a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) hozzáadásával és az attribútum tömb `BindAsync<T>()`ba való átadásával adhat meg egyéni alkalmazás-beállítást. `Binder` paraméter használata nem `IBinder`.  Például:

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítók és kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ a Azure Functions ajánlott eljárásairól](functions-best-practices.md)
