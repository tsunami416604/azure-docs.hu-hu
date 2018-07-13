---
title: Az Azure Functions C# – fejlesztői referencia
description: Megismerheti, hogyan hozhat létre az Azure Functions C# használatával.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: 2308419ba79f6b482df6f68e865aafd0152ae090
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001888"
---
# <a name="azure-functions-c-developer-reference"></a>Az Azure Functions C# – fejlesztői referencia

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ez a cikk bevezetést fejlesztése az Azure Functions használatával C# .NET-osztálytárak.

Az Azure Functions C# és a C#-szkript programozási nyelveket támogatja. Ha a keresett útmutatást [C# használatával az Azure Portalon](functions-create-function-app-portal.md), lásd: [C#-szkript (.csx) – fejlesztői referencia](functions-reference-csharp.md).

Ez a cikk azt feltételezi, hogy már elolvasta a következő cikkeket:

* [Az Azure Functions fejlesztői útmutatója](functions-reference.md)
* [Az Azure Functions Visual Studio 2017-eszközök](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Függvények hordozhatóosztálytár-projektjének

A Visual Studióban a **Azure Functions** projektsablonnal hoz létre egy C# hordozhatóosztálytár-projektjének, amely a következő fájlokat tartalmazza:

* [Host.JSON](functions-host-json.md) -tárolja a konfigurációs beállításokat, amelyek hatással vannak a projekt összes függvényt helyileg vagy az Azure-beli futtatásához.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -Alkalmazásbeállítások és a helyi futtatás során használt kapcsolati karakterláncokat tárolja.

> [!IMPORTANT]
> A létrehozási folyamat létrehoz egy *function.json* fájlban találhatók. Ez *function.json* fájl nem célja, hogy közvetlenül szerkeszthetők. Nem kötelező konfigurációjának módosítása, vagy tiltsa le a függvény a fájl szerkesztésével. A funkció letiltásához használja a [letiltása](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum. Például MY_TIMER_DISABLED beállítás logikai alkalmazás hozzáadása, és a alkalmazni `[Disable("MY_TIMER_DISABLED")]` a függvényt. Ezután engedélyezése és letiltása az alkalmazás beállításainak megváltoztatása.

## <a name="methods-recognized-as-functions"></a>Függvények végfelhasználóként módszerek

Osztálytár, a függvény statická metoda. a rendszer egy `FunctionName` és a egy eseményindító attribútumon, az alábbi példában látható módon:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

A `FunctionName` attribútumot jelöli meg a metódus egy függvény belépési pontként. A névnek kell projekt egyedinek lennie, betűvel kezdődhet és csak betűket, számokat, `_` és `-`, legfeljebb 127 karakternél hosszabb. Projektsablonok gyakran létre kellett hozni egy metódust `Run`, de a metódus nevét is meg lehet bármely érvényes C# metódus nevét.

Az eseményindító attribútum határozza meg az eseményindító, és van kötve bemeneti adatokat egy metódus-paramétert. A példa függvény üzenetsori üzenetek által aktivált, és a metódus az átadott az üzenetsorban található üzenet a `myQueueItem` paraméter.

## <a name="method-signature-parameters"></a>A módszerparaméterekre aláírás

A metódus aláírásához nem az a eseményindító attribútumon használt paraméterek tartalmazhat. Íme néhány a további paraméterek, amelyeket megadhat:

* [Bemeneti és kimeneti kötések](functions-triggers-bindings.md) jelölt ilyen dekorálása azokat az attribútumokat.  
* Egy `ILogger` vagy `TraceWriter` paramétere [naplózás](#logging).
* A `CancellationToken` paramétere [szabályos leállítást](#cancellation-tokens).
* [Kötési kifejezésekben](functions-triggers-bindings.md#binding-expressions-and-patterns) paramétereket aktiválása metaadatait.

A paramétereket a függvényaláíráshoz a sorrendje nem számít. Például előtt vagy után más kötések eseményindító paraméterei helyezheti, és a naplózó paraméter előtt vagy után eseményindítóval vagy kötéssel paraméterek helyezheti.

### <a name="output-binding-example"></a>Kimeneti kötés példa

Az alábbi példa módosítja az előző egy üzenetsor kimeneti kötés hozzáadásával. A függvény az üzenetsorban található üzenet, amely a függvény, amely egy másik üzenetsorban lévő új üzenetsor üzenetet ír.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

A kötés referencia cikkek ([tárolási üzenetsorok](functions-bindings-storage-queue.md), például) azt ismertetik, mely paramétertípusok eseményindító, bemeneti vagy kimeneti attribútumok kötését is használhatja.

### <a name="binding-expressions-example"></a>Kötési kifejezésekben példa

A következő kód lekéri a figyelése, az alkalmazás beállítását az üzenetsor nevére, és lekéri a várólista üzenet létrehozásának ideje a `insertionTime` paraméter.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatikusan létrehozott function.json

A létrehozási folyamat létrehoz egy *function.json* egy függvény mappában, a build-mappában fájlt. Korábban feljegyzett ezt a fájlt nem hivatott közvetlenül szerkeszthetők. Nem kötelező konfigurációjának módosítása, vagy tiltsa le a függvény a fájl szerkesztésével. 

Ez a fájl az a célja, hogy kapcsolatban nyújtanak információkat a méretezési csoport vezérlő használandó [döntések méretezése a használatalapú csomag](functions-scale.md#how-the-consumption-plan-works). Ebből kifolyólag a fájl csak az eseményindító adatait nem bemeneti vagy kimeneti kötés rendelkezik.

A generált *function.json* fájl tartalmaz egy `configurationSource` tulajdonságot, amely a futtatókörnyezet kötések, .NET-attribútumok használata helyett *function.json* konfigurációja. Például:

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

A *function.json* fájlt végzi a NuGet-csomag [Microsoft\.NET\.Sdk\.funkciók](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Ugyanaz a csomag mindkét verziójának szolgál az 1.x és a 2.x verzióját a Functions-futtatókörnyezetben. Cílová architektura egy 1.x jellegű parancsot a(z) projekt kódjába a 2.x projektből. Az alábbiakban a vonatkozó részeinek *.csproj* fájlok megjelenítése a különböző keretrendszerek és azonos cél `Sdk` csomag:

**1.x függvények**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**2.x függvények**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Többek között a `Sdk` csomagfüggőségek eseményindítók és kötések. Mivel azokat, a .NET-keretrendszer célként, miközben 2.x eseményindítók és kötések cél .NET Core egy 1.x jellegű parancsot a(z) projekt 1.x eseményindítók és kötések hivatkozik.

A `Sdk` csomag is függ [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json), majd a közvetett módon [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). A függőségek győződjön meg arról, hogy a projekt azokat a csomagokat, amelyek együttműködnek a Functions futtatókörnyezet verziójának verzióit használja, amely a projekt célokat. Ha például `Newtonsoft.Json` 11-es verzió a .NET-keretrendszer 4.6.1-es verziója, de a Functions futtatókörnyezete .NET-keretrendszer 4.6.1-es célzó csak kompatibilis `Newtonsoft.Json` 9.0.1. Ezért is rendelkezik a függvénykódot a projektben használandó `Newtonsoft.Json` 9.0.1.

Forráskódja `Microsoft.NET.Sdk.Functions` érhető el a GitHub-adattárat [azure\-funkciók\-vs\-összeállítása\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Futtatókörnyezet verziója

Visual Studio használja a [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) Functions-projektek futtatásához. A Core Tools olyan parancssori felületet, a Functions futtatókörnyezetének.

A Core Tools telepítése az npm-et, a Visual Studio által használt Core Tools-verzió, amely nem érinti. A Functions futtatókörnyezet verziójának 1.x, a Visual Studio tárolja a Core Tools verziók *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* és van tárolt legújabb verzióját használja. A 2.x-függvények, a Core Tools szerepelnek a **Azure Functions és Webjobs-eszközök** bővítmény. Az 1.x és a 2.x, láthatja, melyik verziót használja a konzol kimenete a Functions-projekt futtatásakor:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Kötések támogatott típusai

Minden egyes kötés rendelkezik a saját támogatott típusok; például egy blob eseményindító attribútumon is alkalmazható egy karakterlánc-paramétert, egy POCO paraméter egy `CloudBlockBlob` paraméter, vagy számos egyéb bármelyik támogatott típusokat. A [kötés áttekintésével foglalkozó cikkben blobkötések](functions-bindings-storage-blob.md#trigger---usage) felsorolja az összes támogatott paramétertípusok. További információkért lásd: [eseményindítók és kötések](functions-triggers-bindings.md) és a [egyes kötési típus kötés referenciadokumentumai](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötelező érvényű, a metódus visszatérési értéke

A metódus visszatérési értékek használható egy kimeneti kötést az attribútum a metódus visszatérési érték alkalmazásával. Példák: [eseményindítók és kötések](functions-triggers-bindings.md#using-the-function-return-value). 

Akkor használja a visszaadott érték, ha mindig egy sikeres függvény végrehajtása a kimeneti kötés átadása visszatérési értéket eredményez. Ellenkező esetben használjon `ICollector` vagy `IAsyncCollector`, a következő szakaszban látható módon.

## <a name="writing-multiple-output-values"></a>Több kimeneti értékeinek írása

Több értéket írni egy kimeneti kötést, vagy ha sikeres függvényhívási nem azt eredményezheti, hogy semmit sem kell átadni a kimeneti kötés használja a [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típusokat. Ezek a típusok gyűjteményei csak írási a metódus befejezésekor a kimeneti kötés írt.

Ebben a példában több üzenetsorbeli üzenetek írja az üzenetsor használatával `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Naplózás

Kimeneti jelentkeznek a C#-ban a folyamatos átviteli naplók, például a típusú argumentumot `TraceWriter`. Javasoljuk, hogy nevezze el `log`. Kerülje a `Console.Write` az Azure Functions szolgáltatásban. 

`TraceWriter` van definiálva a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). A naplózási szint a `TraceWriter` konfigurálható [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Egy újabb naplózási keretrendszer helyett használható információ `TraceWriter`, lásd: [írási bejelentkezik a C#-függvények](functions-monitoring.md#write-logs-in-c-functions) a a **figyelése az Azure Functions** cikk.

## <a name="async"></a>Az aszinkron

Egy függvényt [aszinkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), használja a `async` kulcsszót, és lépjen vissza a `Task` objektum.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Megszakítási tokeneket

Egy függvény elfogadhatja a [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) paraméter, amely lehetővé teszi a kód értesítése, ha a funkció arra készül, hogy állítható le, az operációs rendszer. Használhatja ezt az értesítést, hogy a funkció váratlanul leáll nem úgy, hogy az adatok inkonzisztens állapotban hagyja.

Az alábbi példa bemutatja, hogyan közelgő függvény lezárást kereséséhez.

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

Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `System.Environment.GetEnvironmentVariable`, ahogyan az az alábbi példakód:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Alkalmazásbeállítások környezeti változókat is olvasható be helyileg fejlesztése során mind a futtatáskor az Azure-ban. Helyileg fejlesztésekor Alkalmazásbeállítások származnak az `Values` gyűjteményt a *local.settings.json* fájlt. Mindkét környezetben a helyi és az Azure, `GetEnvironmentVariable("<app setting name>")` nevű Alkalmazásbeállítás értékét kérdezi le. Például, ha helyileg futtatja, "Saját Site-Name" rendszer visszaadna, ha a *local.settings.json* fájl tartalmaz `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

A [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) tulajdonság egy másik API-t az első alkalmazás beállítás értékeit, de javasoljuk, hogy használjon `GetEnvironmentVariable` itt látható módon.

## <a name="binding-at-runtime"></a>Kötés futásidőben

C# és az egyéb .NET nyelven, használhat egy [imperatív](https://en.wikipedia.org/wiki/Imperative_programming) kötés minta, nem pedig a [ *deklaratív* ](https://en.wikipedia.org/wiki/Declarative_programming) attribútumok kötése. Imperatív kötés akkor hasznos, ha a kötési paramétereket kell futásidejű kialakítása helyett időpontjában a következő időpontban számítja. Ezzel a mintával kell kötni támogatott bemeneti és kimeneti kötések a működés közbeni a függvénykódban.

Adja meg a versenyképesség kötés az alábbiak szerint:

- **Ne** fel egy attribútumot a függvényaláíráshoz a kívánt imperatív kötések.
- Adja meg a bemeneti paraméterek [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Az alábbi C# minta segítségével végezze el az adatkötés.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` a .NET-attribútum, amely meghatározza a kötés, és `T` a kötési típus által támogatott bemeneti vagy kimeneti típus. `T` nem lehet egy `out` paraméter típusa (például `out JObject`). Például, a Mobile Apps-tábla kimeneti kötés által támogatott [hat kimeneti típusokat](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), de csak [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)imperatív kötéssel.

### <a name="single-attribute-example"></a>Egyetlen attribútum példa

Az alábbi példakód létrehoz egy [Storage-blobból a kimeneti kötés](functions-bindings-storage-blob.md#output) blob a futási időben meghatározott elérési majd ír egy karakterláncot a blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) határozza meg a [tárolóblob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötést, és [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) egy támogatott kimeneti kötés típusa.

### <a name="multiple-attribute-example"></a>Több attribútum példa

Az előző példában a függvényalkalmazás fő Tárfiók kapcsolati sztringje alkalmazás beállításának beolvasása (amely `AzureWebJobsStorage`). Egy egyéni alkalmazást beállítást, a Storage-fiók hozzáadásával is megadhat a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) és való átadásához attribútum tömböt `BindAsync<T>()`. Használja a `Binder` paraméter nem `IBinder`.  Példa:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
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
> [További információ az eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Tudjon meg többet a gyakorlati tanácsok az Azure Functions szolgáltatáshoz](functions-best-practices.md)
