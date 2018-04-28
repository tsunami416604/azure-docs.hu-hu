---
title: Nyomon követheti a Azure Application Insights .NET SDK-val egyéni műveleteket |} Microsoft Docs
description: Azure Application Insights .NET SDK-val egyéni műveletek nyomon követése
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz
ms.openlocfilehash: 94424a3d8aad56cf4504cccd8adb1a45523d95e0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Application Insights .NET SDK-val egyéni műveletek nyomon követése

Az Azure Application Insights SDK-k automatikusan követi nyomon a bejövő HTTP-kérelmek és a függő szolgáltatások, például HTTP-kérelmek és az SQL-lekérdezések. Nyomon követését és a kérelmek és a függőségek korrelációs biztosítanak a teljes alkalmazáshoz válaszidejét és megbízhatóság láthatósága összes mikroszolgáltatások létrehozására, az alkalmazás felhasználó között. 

Nincs alkalmazás mintáról olvashat, amelyek nem tudják támogatni az általános osztály. Az ilyen minták megfelelő figyeléshez szükséges manuális kód instrumentation. Ez a cikk manuális instrumentation egyéni várólista feldolgozása és a hosszú futású háttérfeladatok futtatása például szükség lehet néhány mintázatokat tartalmazza.

Ez a dokumentum útmutatást nyújt a történő nyomon követheti az Application Insights SDK-val egyéni műveleteket. Ebben a dokumentációban fontos:

- Az Application Insights .NET (más néven alapvető SDK) verziójának 2.4 +.
- Az Application Insights webes (ASP.NET futó) alkalmazások verziójához 2.4 +.
- Az Application Insights az ASP.NET Core 2.1-es vagy újabb.

## <a name="overview"></a>Áttekintés
Egy művelet egy logikai munkákat az alkalmazások futtatása. Az idő, időtartam, eredmény és olyan környezetben, például a felhasználónevet, a tulajdonságok és az eredmény végrehajtás start neve van. Ha A műveletet kezdeményezett művelet B, majd művelet B be van állítva az A. szülője Egy művelet csak egy szülőhöz lehet, de sok gyermek művelet veheti fel. Műveletek és telemetriai korrelációs további információkért lásd: [Azure Application Insights telemetria korrelációs](application-insights-correlation.md).

Az Application Insights .NET SDK a művelet leírását a absztrakt osztály [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) és a leszármazottai [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) és [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Bejövő műveletek nyomon követése 
Az Application Insights webes SDK-t automatikusan gyűjti az ASP.NET futtatni egy IIS-feldolgozási folyamat összes ASP.NET Core alkalmazásokat és a HTTP-kérelmekre. Nincsenek megoldások Közösség által támogatott más platformok és -keretrendszerek számára. Azonban ha az alkalmazás nem támogatja a standard vagy a Közösség által támogatott megoldások bármelyikét, hogy állíthatnak be azt manuálisan.

Egy másik, amelyhez az egyéni nyomkövetési: a worker, amely megkapja a cikkek az üzenetsorból. Egyes várólisták üzenet hozzáadása a várólista-hívás a függőség beállításához nyomon követni. Azonban a magas szintű műveletet leíró üzenet feldolgozása nem automatikusan összegyűjtött.

Nézzük meg az ilyen műveletek sikerült nyomon követésének módja.

Magas szinten, a feladatütemezés, ha a `RequestTelemetry` és ismert tulajdonságainak beállítása. A művelet befejezése után a telemetriai adatok nyomon követéséhez. A következő példa bemutatja, ezt a feladatot.

### <a name="http-request-in-owin-self-hosted-app"></a>Önálló üzemeltetett alkalmazás Owin HTTP-kérelem
Ebben a példában a következők szerint propagálja nyomkövetési környezetben a [HTTP protokoll a korrelációs](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Fogadási hiba ismertetett fejlécek kell látnia.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

A HTTP protokoll a korrelációs is deklarálja a `Correlation-Context` fejléc. Azonban ki van hagyva itt az egyszerűség érdekében.

## <a name="queue-instrumentation"></a>Várólista instrumentation
Ha van [HTTP protokoll a korrelációs](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) felelt meg a korrelációs információ a HTTP-kérelem, minden várólista protokollt meghatározásához, hogy ugyanazokat a részleteket az üzenetsorban lévő üzenetet továbbítódnak rendelkezik. Néhány várólista protokollok (például az AMQP) engedélyezése sikeres metaadatokat és más (ilyen Azure Storage Üzenetsorába) igényelnek a környezetben, kódolhatók a a üzenetadatokat.

### <a name="service-bus-queue"></a>Service Bus-üzenetsor
Az Application Insights nyomon követi az új Service Bus üzenetkezelés hívások [Microsoft Azure Szolgáltatásbusz-ügyfél .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) verzió 3.0.0 és magasabb.
Használatakor [üzenet kezelő mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) üzenetek feldolgozásához, végzett: a szolgáltatás által végzett összes Service Bus hívások automatikusan követni, és egyéb telemetriai elemek tartozzanak. Tekintse meg a [nyomkövetés Microsoft Application insights szolgáltatással a Service Bus-ügyfélalkalmazást](../service-bus-messaging/service-bus-end-to-end-tracing.md) Ha manuálisan üzenetek feldolgozásához.

Ha [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) csomag, olvasná - a következő példák bemutatják, hogyan lehet nyomon követéséhez (és összefüggéseket) a Service Bus hívja Service Bus-üzenetsorba AMQP protokollt használja, és az Application Insights nem automatikusan követi nyomon az üzenetsor-műveletet.
Az üzenet tulajdonságai átadott korrelációs azonosítót.

#### <a name="enqueue"></a>Sorba helyezni

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Folyamat
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Az Azure Storage üzenetsorába
A következő példa bemutatja, hogyan nyomon követéséhez a [Azure Storage üzenetsorába](../storage/queues/storage-dotnet-how-to-use-queues.md) műveletek és a gyártó fogyasztói, és az Azure Storage közötti összefüggésbe telemetriai. 

A tároló várólista rendelkezik egy HTTP API-t. A várólistára minden hívást követi az Application Insights függőségi gyűjtő a HTTP-kérelmekre.
Győződjön meg arról, hogy `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` a `applicationInsights.config`. Ha nem rendelkezik, adja hozzá a programozott módon [szűrést és az Azure Application Insights SDK előfeldolgozása](app-insights-api-filtering-sampling.md).

Ha manuálisan adja meg az Application Insights, győződjön meg arról, létrehozása és inicializálása `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` mint:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Érdemes azt is összefüggéseket az Application Insights Műveletazonosító tároló kérelemben azonosítóval. Állítsa be, és egy tároló kérelemben ügyfél és a kiszolgáló Kérelemazonosító információkért lásd: [figyelése, diagnosztizálása és elhárítása az Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Sorba helyezni
Tárolási sorok támogatja a HTTP API-t, mert a sor összes művelet automatikusan követi az Application Insights. Sok esetben elegendő a instrumentation kell lennie. Azonban összefüggéseket készítő nyomkövetési adatokat a fogyasztói oldalon a nyomkövetéseket, át kell korrelációs környezete hasonlóképpen hogyan azt ehhez a HTTP protokoll korrelációhoz. 

Ez a példa bemutatja, hogyan nyomon követheti a `Enqueue` műveletet. A következőket teheti:

 - **Összefüggéseket találni az újrapróbálkozások között (ha van ilyen)**: minden rendelkeznek egy közös szülő, amely rendelkezik a `Enqueue` műveletet. Ellenkező esetben ezek még nyomon követi a bejövő kérelem gyermekeként. Ha a várólista logikai kéréseket, hívást eredményezett újrapróbálkozások található nehéz lehet.
 - **A tárolási naplófájljai összefüggéseket (ha szükséges)**: azok az Application Insights telemetria most tartozzanak.

A `Enqueue` művelet egy szülő műveletet (például egy bejövő HTTP-kérelem) gyermeke. A HTTP-függőségi hívás nem gyermeke a `Enqueue` művelet és a bejövő kérelem unoka:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

A telemetriai adatok mennyiségének csökkentésére az alkalmazás jelentéseket, vagy ha nem kívánja nyomon követni a `Enqueue` más okok miatt, használja a `Activity` API közvetlenül:

- Hozzon létre (és indítása) egy új `Activity` helyett az Application Insights művelet elindítása. Ezt megteheti *nem* kell hozzárendelni kívánt tulajdonságokat rajta a művelet nevén kívül.
- Szerializálható `yourActivity.Id` ahelyett, hogy a üzenetadatokat be `operation.Telemetry.Id`. Is `Activity.Current.Id`.


#### <a name="dequeue"></a>Created
Hasonlóképpen, a `Enqueue`, a tároló várólista tényleges HTTP-kérelem automatikusan követi nyomon az Application Insights. Azonban a `Enqueue` művelet feltehetően történik, a szülő környezetben, például egy bejövő kérelem környezete. Application Insights SDK-k automatikusan összefüggéseket ilyen művelet (és a HTTP része) a szülő kérelem és egyéb telemetriai jelentett ugyanabban a hatókörben.

A `Dequeue` legbonyolultabb művelet. Az Application Insights SDK automatikusan nyomon követi a HTTP-kérelmekre. A korrelációs környezetben azonban azt nem ismert mindaddig, amíg az üzenetet a rendszer értelmezi. Nincs lehetőség a HTTP-kérelem és a telemetriai adatokat a többi üzenet összefüggéseket.

Sok esetben hasznos lehet a más nyomkövetési adatokat, valamint a HTTP-kérelem a várólista összefüggéseket. A következő példa bemutatja, hogyan teheti meg:

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Folyamat

A következő példában bejövő üzenet zajlik a módon hasonlóan a bejövő HTTP-kérelem:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Hasonlóképpen a többi üzenetsor-műveletet tagolva is. Egy betekintés művelet egy dequeue művelet, hasonló módon lesznek tagolva. Üzenetsor-kezelési műveletet tagolása nem szükséges. Az Application Insights nyomon követi a műveletek, például HTTP, és a legtöbb esetben elegendő.

Amikor állíthatnak üzenet törlése, ellenőrizze, hogy beállította a művelet (korrelációs) azonosítót. Másik lehetőségként használhatja a `Activity` API. Majd nincs szükség művelet azonosítók a telemetriai adatok elemek beállítani, mert az Application Insights SDK minderre meg:

- Hozzon létre egy új `Activity` után egy elemet az üzenetsorból.
- Használjon `Activity.SetParentId(message.ParentId)` fogyasztói és gyártó naplók összefüggéseket.
- Indítsa el a `Activity`.
- Track created, feldolgozása és törlési műveletek használatával `Start/StopOperation` segítő. Ehhez a a azonos aszinkron folyamatábrán (a végrehajtási környezet). Ezzel a módszerrel azok még korrelált megfelelően.
- Állítsa le a `Activity`.
- Használjon `Start/StopOperation`, vagy hívja az `Track` telemetriai manuálisan.

### <a name="batch-processing"></a>Kötegelt feldolgozás
Az egyes üzenetsorok egy kérelem több üzenetet is created. Ilyen üzenetek feldolgozása feltételezhetően független, és a különböző logikai műveletek tartozik. Ebben az esetben nincs lehetőség a összefüggéseket a `Dequeue` adott üzenetfeldolgozást művelet.

Minden üzenet fel kell dolgozni a saját aszinkron vezérlési folyamatában. További információkért lásd: a [követési kimenő függőségek](#outgoing-dependencies-tracking) szakasz.

## <a name="long-running-background-tasks"></a>Hosszan futó háttérfeladatok
Egyes alkalmazások start hosszú futású műveleteket, amelyek a felhasználói kérelmek oka lehet. A nyomkövetés/instrumentation szempontjából nincs eltér a kérelem vagy függőségi instrumentation: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Ebben a példában `telemetryClient.StartOperation` hoz létre `RequestTelemetry` és tölti ki a korrelációs környezetben. Tegyük fel, a bejövő kérelmeket, amelyek ütemezett a művelet által létrehozott szülő művelet van. Amennyiben a megjelölt `BackgroundTask` ugyanazon aszinkron elindul egy bejövő kérelem folyamata kontrolljához szülő művelet tartozzanak. `BackgroundTask` és minden beágyazott telemetriai elem mellékel a kérelemhez, amely a, a kérelem befejeződését követően automatikusan közötti kapcsolatot.

A feladat indításakor a háttérben szálból, amely nem tartalmaz semmilyen műveletet (`Activity`) társított, `BackgroundTask` bármelyik szülő nem rendelkezik. Azonban azt is beágyazott műveletek. A feladat jelentett összes telemetriai elemet összefüggő a `RequestTelemetry` létrehozott `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Kimenő függőségek nyomon követése
Nyomon követheti a saját függőségi fajta vagy az Application Insights nem támogatja a műveletet.

A `Enqueue` metódus a Service Bus-üzenetsorba, vagy a tároló várólista ilyen egyéni követési példák lehetnek.

Az általános módszer követési egyéni függőséget, hogy:

- Hívja a `TelemetryClient.StartOperation` (kiterjesztés) módszer, amelynek változó kitöltése a `DependencyTelemetry` összefüggések keresésére és néhány egyéb tulajdonság szükséges tulajdonságok (kezdő időpont stamp, időtartama).
- Egyéb egyéni tulajdonságának beállítása a `DependencyTelemetry`, például a nevét és minden egyéb környezetre van szüksége.
- Ellenőrizze a függőségi hívás, és várjon, amíg az.
- Állítsa le a műveletet a `StopOperation` amikor befejeződött.
- Kivétel kezelése.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Művelet leállt, értékesítésére művelet hatására az, akkor azt a telefonhívás helyett `StopOperation`.

*Figyelmeztetés*: bizonyos esetekben unhanded kivétel is [megakadályozása](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` hívandó, műveletek nem követhető nyomon.

### <a name="parallel-operations-processing-and-tracking"></a>Párhuzamos művelet feldolgozása és nyomon követése

`StopOperation` csak leállítja a műveletet, amely elkezdődött. Ha az aktuális futó művelet nem egyezik meg szeretné szüntetni, azzal `StopOperation` nincs semmi hatása. Ez a helyzet akkor fordulhat elő, ha azonos végrehajtási környezetében párhuzamosan több művelet indítása:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Győződjön meg arról, hogy mindig hívja `StartOperation` és ugyanaz a művelet feldolgozása **aszinkron** különítheti el a párhuzamosan futó műveletek metódust. Ha a művelet szinkron (vagy nem aszinkron), burkolja a folyamat, és nyomon követni `Task.Run`:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>További lépések

- Az alapvető [telemetriai korrelációs](application-insights-correlation.md) az Application insights szolgáltatással.
- Tekintse meg a [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Egyéni jelentést [eseményeket és metrikákat](app-insights-api-custom-events-metrics.md) az Application Insights részére.
- Tekintse meg a standard [konfigurációs](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) környezeti tulajdonságok gyűjtemény.
- Ellenőrizze a [System.Diagnostics.Activity felhasználói útmutató](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) hogyan azt összefüggéseket telemetriai adatok megjelenítéséhez.
