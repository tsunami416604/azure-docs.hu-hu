---
title: "Az Azure Functions C# fejlesztői leírás"
description: "Megtudhatja, hogyan fejleszthet Azure Functions használatával C#."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 8a098d2ecc004b1593310579c47c53778858e799
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
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

### <a name="functionname-and-trigger-attributes"></a>Eseményindító és függvénynév attribútumok

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

A `FunctionName` attribútummal jelöli meg a metódus egy függvény belépési pontként. A név egy projekten belül egyedinek kell lennie.

Az eseményindító attribútum az eseményindító adja meg, és a bemeneti adatok kötődik metódusparaméter. A példa függvény váltja ki egy üzenetsor-üzenetet, és az üzenetsorban lévő üzenetet továbbítódik metódust a `myQueueItem` paraméter.

### <a name="additional-binding-attributes"></a>További kötés attribútumok

További bemeneti és kimeneti kötelező attribútum is használható. A következő példa egy kimeneti várólista-kötés hozzáadásával előző egy módosítja. A függvény a bemeneti várólista üzenetet ír egy új üzenetsor-üzenetet egy másik várólistához.

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

### <a name="conversion-to-functionjson"></a>Function.json átalakítása

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

A *function.json* fájl létrehozása végzi el a NuGet-csomag [Microsoft\.NET\.Sdk\.funkciók](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). A forráskód érhető el a GitHub-tárház [azure\-funkciók\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusok

Minden kötésnek rendelkezik saját támogatott típusok; például egy blob eseményindító attribútum is alkalmazható egy karakterlánc-paramétert, egy POCO paraméter egy `CloudBlockBlob` paraméter, sem annak több más támogatott típusok. A [kötés áttekintésével foglalkozó cikkben blob kötések](functions-bindings-storage-blob.md#trigger---usage) felsorolja az összes támogatott különböző. További információkért lásd: [eseményindítók és kötések](functions-triggers-bindings.md) és a [kötési hivatkozási docs az egyes kötés](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötése metódus visszatérési értéke

A metódus visszatérési érték használhat egy kimeneti kötése a következő példában látható módon:

```csharp
public static class ReturnValueOutputBinding
{
    [FunctionName("CopyQueueMessageUsingReturnValue")]
    [return: Queue("myqueue-items-destination")]
    public static string Run(
        [QueueTrigger("myqueue-items-source-2")] string myQueueItem,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        return myQueueItem;
    }
}
```

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

`TraceWriter`a van definiálva a [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). A naplózási szint a `TraceWriter` konfigurálható [host.json](functions-host-json.md).

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

## <a name="async"></a>Async

Ahhoz, hogy egy függvény aszinkron, használja a `async` kulcsszót, és térjen vissza a `Task` objektum.

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

Egyes műveletek szabályos leállítást igényel. Mindig is írhat kódot, amelyet kezelni tud összeomló ajánlott, azokban az esetekben, ahol szeretné kezelni leállítási kérelmeket, adja meg a [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) megadott argumentum.  A `CancellationToken` valósul meg, hogy jelezze, hogy a gazdagép leállítása elindul.

```csharp
public static class CancellationTokenExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
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

  `BindingTypeAttribute`a .NET-attribútum, amely meghatározza a kötés és `T` bemeneti vagy kimeneti típus a kötési típus által támogatott. `T`nem lehet egy `out` típusú paraméter (például `out JObject`). Például a Mobile Apps tábla kimeneti kötése támogatja [hat kimeneti típusok](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), de csak használható [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)imperatív kötést.

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
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Queue](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [ServiceBus](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Table](functions-bindings-storage-table.md#input---attributes)| [Table](functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információk a gyakorlati tanácsok az Azure Functions](functions-best-practices.md)
