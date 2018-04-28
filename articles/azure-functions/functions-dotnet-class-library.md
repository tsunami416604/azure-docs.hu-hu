---
title: Az Azure Functions C# fejlesztői leírás
description: Megtudhatja, hogyan fejleszthet Azure Functions használatával C#.
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
ms.openlocfilehash: c1b04968f83271006240fc0e099175e9017574ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-functions-c-developer-reference"></a>Az Azure Functions C# fejlesztői leírás

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ebben a cikkben megismerkedhet az Azure Functions használatával C# alkalmazás fejlesztése.

Az Azure Functions támogatja a C# és C# programozási nyelvek parancsfájl. Ha a keresett útmutatást [C# segítségével az Azure portálon](functions-create-function-app-portal.md), lásd: [C# a parancsfájlt (.csx) fejlesztői leírás](functions-reference-csharp.md).

Ez a cikk feltételezi, hogy Ön már elolvasta a következő cikkeket:

* [Az Azure Functions fejlesztői útmutatója](functions-reference.md)
* [Azure Functions Visual Studio 2017 eszközök](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Functions hordozhatóosztálytár-projektjének

A Visual Studio a **Azure Functions** projektsablon hoz létre egy C# hordozhatóosztálytár-projektjének, amely a következő fájlokat tartalmazza:

* [Host.JSON](functions-host-json.md) -konfigurációs beállítások, amelyek hatással vannak a projekt összes funkciójának futtatásakor, helyileg vagy az Azure-ban tárolja.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -Alkalmazásbeállítások és a helyi futtatás során használt kapcsolati karakterláncok tárolja.

> [!IMPORTANT]
> Az összeállítási folyamat létrehoz egy *function.json* fájlt minden funkciót. Ez *function.json* fájl nem célja, hogy közvetlenül szerkeszthetők. Nem kötelező konfigurációjának módosítása, vagy tiltsa le a függvény a fájl szerkesztésével. A funkció letiltásához használja a [letiltása](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribútum. Például MY_TIMER_DISABLED beállítás logikai alkalmazás hozzáadása, és alkalmazni `[Disable("MY_TIMER_DISABLED")]` a függvénynek. Ezután engedélyezése és letiltása az alkalmazás beállításainak megváltoztatása.

## <a name="methods-recognized-as-functions"></a>Funkciók értelmezhető módszerek

Egy osztály könyvtárban, a függvény egy statikus metódus egy `FunctionName` és egy eseményindító attribútum, az alábbi példában látható módon:

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

A `FunctionName` attribútummal jelöli meg a metódus egy függvény belépési pontként. A név egy projekten belül egyedinek kell lennie. Projektsablonjai gyakran nevű metódus létrehozása `Run`, de a metódus nevét bármilyen érvényes C# metódus neve lehet.

Az eseményindító attribútum az eseményindító adja meg, és a bemeneti adatok kötődik metódusparaméter. A példa függvény váltja ki egy üzenetsor-üzenetet, és az üzenetsorban lévő üzenetet továbbítódik metódust a `myQueueItem` paraméter.

## <a name="method-signature-parameters"></a>Metódus-aláírás paraméterei

A metódus aláírása nem az eseményindító attribútummal használt paraméterek tartalmazhat. Íme néhány a felvehető paraméterek közül:

* [Bemeneti és kimeneti kötések](functions-triggers-bindings.md) jelölés azonosítja, által dekoráció azokat az attribútumokat.  
* Egy `ILogger` vagy `TraceWriter` paramétere [naplózás](#logging).
* A `CancellationToken` paramétere [szabályos leállítást](#cancellation-tokens).
* [Kötési kifejezésként](functions-triggers-bindings.md#binding-expressions-and-patterns) paramétereket indítás metaadatok.

Paramétereket a függvényaláíráshoz a sorrendje nem lényeges. Például helyezhet el trigger paraméterek előtt vagy után más kötésekben, és adhat meg a tranzakciónaplókat tartalmazó paraméter előtt vagy után eseményindító vagy kötelező paraméterek.

### <a name="output-binding-example"></a>Példa a kimenetre kötés

A következő példa egy kimeneti várólista-kötés hozzáadásával előző egy módosítja. A függvény az üzenetsorban található üzenetet, amely elindítja a működnek, mint egy új üzenetsor-üzenetet egy másik várólistához ír.

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

A kötés útmutatót ([tárüzenetsort](functions-bindings-storage-queue.md), például) mely eseményindító, bemeneti vagy kimeneti attribútumok kötés használható különböző ismertetik.

### <a name="binding-expressions-example"></a>Kötési kifejezésekben – példa

A következő kód jogosultságot kap a várólistában, figyelheti a Alkalmazásbeállítás nevét, és onnan kapta, hogy a várólista üzenet létrehozása idején a `insertionTime` paraméter.

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

Az összeállítási folyamat létrehoz egy *function.json* fájlban egy függvény a build mappában. Ahogy azt korábban említettük, ez a fájl nem célja, hogy közvetlenül szerkeszthetők. Nem kötelező konfigurációjának módosítása, vagy tiltsa le a függvény a fájl szerkesztésével. 

Ezt a fájlt az a célja, hogy kapcsolatban nyújtanak információkat a méretezési vezérlő használandó [döntések méretezésének a fogyasztás terv](functions-scale.md#how-the-consumption-plan-works). Ezért a fájl csak eseményindító adatait nem bemeneti vagy kimeneti kötések rendelkezik.

A létrehozott *function.json* fájl tartalmaz egy `configurationSource` tulajdonság, amely közli a futtatókörnyezet kötések, a .NET-attribútumok használata helyett *function.json* konfigurációs. Például:

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

A *function.json* fájl létrehozása végzi el a NuGet-csomag [Microsoft\.NET\.Sdk\.funkciók](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Ugyanaz a csomag mindkét verziójának használt 1.x és a 2.x funkciók futásidejű. A megcélzott keretrendszer verziószáma, egy 1.x projekt kódjába 2.x projektből. Az alábbiakban vonatkozó részeinek *.csproj* fájlok megjelenítő különböző keretrendszerek és azonos cél `Sdk` csomag:

**1.x működik**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**2.x működik**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Között a `Sdk` csomagfüggőségek eseményindítók és kötések. Egy 1.x projekt hivatkozik 1.x eseményindítók és kötések mert azok a .NET-keretrendszer céloz, amíg 2.x eseményindítók és kötések .NET Core célként.

A `Sdk` csomag is függ [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json), majd a közvetve [windowsazure.Storage kifejezésre](http://www.nuget.org/packages/WindowsAzure.Storage). Ezeket a függőségeket győződjön meg arról, hogy a projekt használja azokat a csomagokat, amelyek működnek együtt a funkciók futásidejű verzióját azon verzióit, amelyek a projekt célokat. Például `Newtonsoft.Json` 11-es verzió a .NET-keretrendszer 4.6.1-es verzióját, de a Functions futtatókörnyezete, amelynek célpontja a .NET-keretrendszer 4.6.1 csak kompatibilis `Newtonsoft.Json` 9.0.1. Így a projekt függvény kód is használata `Newtonsoft.Json` 9.0.1.

Forráskódja `Microsoft.NET.Sdk.Functions` érhető el a GitHub-tárház [azure\-funkciók\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Futtatókörnyezet verziója

A Visual Studio használja a [Azure Functions Core eszközök](functions-run-local.md#install-the-azure-functions-core-tools) funkciók projektek futtatásához. A Core-eszközök a Functions futtatókörnyezete parancssori felület.

A Core telepíti npm segítségével, ha a Visual Studio által használt Core eszközök verziója, amely nincs hatással. A funkciók futásidejű verzióját a 1.x, Visual Studio verziója a Core eszközök tárolja *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* és nincs tárolt legújabb verzióját használja. A 2.x működik, az alapvető eszközök szerepelnek a **Azure Functions és webes feladatok eszközök** bővítmény. 1.x és a 2.x megtekintheti, milyen verzióját használja a konzol kimeneti funkciók projekt futtatásakor:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusok

Minden kötésnek rendelkezik saját támogatott típusok; például egy blob eseményindító attribútum is alkalmazható egy karakterlánc-paramétert, egy POCO paraméter egy `CloudBlockBlob` paraméter, sem annak több más támogatott típusok. A [kötés áttekintésével foglalkozó cikkben blob kötések](functions-bindings-storage-blob.md#trigger---usage) felsorolja az összes támogatott különböző. További információkért lásd: [eseményindítók és kötések](functions-triggers-bindings.md) és a [kötési hivatkozási docs az egyes kötés](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötése metódus visszatérési értéke

A metódus visszatérési érték használható egy kimeneti kötése az attribútum a metódus visszatérési érték alkalmazásával. Tekintse meg a [eseményindítók és kötések](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Több kimeneti értékeinek írása

Több érték írni egy kimeneti kötése, használja a [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típusok. Ezek a típusok csak írható gyűjtemények metódus befejezésekor a kimeneti kötés írt.

Ebben a példában több üzenetek ír be várólista használatával `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Naplózás

A folyamatos átviteli naplók, a C# kimeneti jelentkeznek, tartalmazza a típusú argumentumot `TraceWriter`. Ajánlott nevezze el `log`. Kerülje a `Console.Write` az Azure Functions. 

`TraceWriter` a van definiálva a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). A naplózási szint a `TraceWriter` konfigurálható [host.json](functions-host-json.md).

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
> Egy újabb naplózási keretrendszert, melyekkel ahelyett, hogy információt `TraceWriter`, lásd: [írási naplói a C# funkciók](functions-monitoring.md#write-logs-in-c-functions) a a **figyelése az Azure Functions** cikk.

## <a name="async"></a>Aszinkron

Egy függvényt [aszinkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), használja a `async` kulcsszót, és térjen vissza a `Task` objektum.

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

## <a name="cancellation-tokens"></a>Megszakítási jogkivonatok

A következő függvényt fogad el egy [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) paraméter, amely lehetővé teszi az operációs rendszer, hogy értesítse a kódot a függvény készül, hogy megszűnik. Az értesítés segítségével győződjön meg arról, hogy a függvény nem bontható váratlanul oly módon, hogy az adatok inkonzisztens állapotban hagyja.

A következő példa bemutatja, hogyan közelgő függvény futása kereséséhez.

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

Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `System.Environment.GetEnvironmentVariable`, ahogy az az alábbi példakód:

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

## <a name="binding-at-runtime"></a>Futásidejű kötés

C# és egyéb .NET nyelven, használhat egy [imperatív](https://en.wikipedia.org/wiki/Imperative_programming) kötés mintát, nem pedig a [ *deklaratív* ](https://en.wikipedia.org/wiki/Declarative_programming) attribútumok kötések. Imperatív kötés akkor hasznos, ha a kötési paraméterekhez kell számítani a Tervező helyett futásidejű időpontban. Az ebben a mintában köthető támogatott bemeneti és kimeneti kötések az azonnali a függvény kódban.

Adja meg a következő kötés dolgozik:

- **Ne** fel egy attribútumot a függvényaláíráshoz a kívánt imperatív kötések számára.
- A bemeneti paraméter fázis [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- A következő C# minta segítségével hajtható végre a adatkötés.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` a .NET-attribútum, amely meghatározza a kötés és `T` bemeneti vagy kimeneti típus a kötési típus által támogatott. `T` nem lehet egy `out` típusú paraméter (például `out JObject`). Például a Mobile Apps tábla kimeneti kötése támogatja [hat kimeneti típusok](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), de csak használható [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)imperatív kötést.

### <a name="single-attribute-example"></a>Egyetlen attribútum – példa

Az alábbi példakód létrehoz egy [tárolási blob kimeneti kötése](functions-bindings-storage-blob.md#output) blobbal együtt futási időben megadott elérési út ezután ír egy karakterláncot a blobra mutató.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) határozza meg a [tárolási blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötelező, és [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) támogatott kimeneti kötési típus.

### <a name="multiple-attribute-example"></a>Több attribútum – példa

Az előző példában lekérdezi az alkalmazás beállítása a függvény alkalmazás fő tárolási fiók kapcsolati karakterlánc (amely `AzureWebJobsStorage`). Megadhat egy egyéni alkalmazás beállítást, a tárfiók hozzáadásával a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) , és átadja a attribútum tömb be `BindAsync<T>()`. Használja a `Binder` paraméter nem `IBinder`.  Példa:

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

A következő táblázat a eseményindító és kötés attribútumok, amelyek az Azure Functions hordozhatóosztálytár-projektjének érhető el. A következő névtérbeli attribútumok összes `Microsoft.Azure.WebJobs`.

| Eseményindító | Input (Bemenet) | Kimenet|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [Blob](functions-bindings-storage-blob.md#input---attributes)| [Blob](functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Várólista](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [A Szolgáltatásbusz](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Tábla](functions-bindings-storage-table.md#input---attributes)| [Tábla](functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információk a gyakorlati tanácsok az Azure Functions](functions-best-practices.md)
