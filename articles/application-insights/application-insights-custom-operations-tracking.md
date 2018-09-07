---
title: Az Azure Application Insights .NET SDK-val egyéni műveletek követése |} A Microsoft Docs
description: Az Azure Application Insights .NET SDK-val egyéni műveletek követése
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8295fb58bdf92ca8688f5f7b6270dc1b48632a73
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057131"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Application Insights .NET SDK-val egyéni műveletek követése

Az Azure Application Insights SDK-k automatikusan nyomon követheti a bejövő HTTP-kérések és a függő szolgáltatások, például HTTP-kérelmekre, és az SQL-lekérdezéseket. Nyomon követését és a kérelmek és a függőségek korrelációs teszik lehetővé a teljes alkalmazáshoz válaszkészségének és megbízhatóság minden mikroszolgáltatás-alapú, amelyek az alkalmazás között. 

Nincs olyan osztályát alkalmazásminták általános nem támogatott. Az ilyen minták a megfelelő figyelési igényel manuális kód kialakítási. Ez a cikk ismerteti a néhány minta, amelyek manuális instrumentation, például az egyéni várólista feldolgozása és a hosszú ideig futó háttérfeladatokat futtató lehet szükség.

Ez a dokumentum útmutatást nyújt az Application Insights SDK-val egyéni műveletek követése való. Ez a dokumentáció fontos lehetőség:

- Az Application Insights .NET (más néven alapszintű SDK) verzió 2.4 +.
- Az Application Insights web applications (ASP.NET fut) verzió 2.4 +.
- Application Insights az ASP.NET Core 2.1-es vagy újabb.

## <a name="overview"></a>Áttekintés
Egy logikai munkákat egy alkalmazás által futtatott művelet. Rendelkezik egy névvel, indítsa el az idő, az időtartam, a eredmény és a egy környezetet a Futtatás mint felhasználónév, a tulajdonságok és az eredmény. Ha a művelet A kezdeményezett művelet B, akkor művelet B értéke adja meg szülőként az A. Művelet csak egy szülő rendelkezhet, de számos gyermek műveletek lehetnek. A műveletek és telemetriai korreláció további információkért lásd: [Azure Application Insights telemetriai korreláció](application-insights-correlation.md).

Az Application Insights .NET SDK a művelet az absztrakt osztály által leírt [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) és a leszármazottai [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) és [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Bejövő műveletek nyomon követése 
Az Application Insights webes SDK automatikusan gyűjti a HTTP-kérelmeket az IIS-folyamatban futó ASP.NET alkalmazások és az összes ASP.NET Core-alkalmazás. Nincsenek megoldások Közösség által támogatott más platformokra és keretrendszerekre. Azonban ha az alkalmazás nem támogatja a standard vagy a Közösség által támogatott megoldások bármelyikét, is kialakíthat azt manuálisan.

Egyéni követési igénylő egy másik példa, amely a várólistában lévő elemek kap a feldolgozó. Bizonyos üzenetsorok meghívásához adjon meg egy üzenetet az üzenetsorba minősülnek függőség. Ugyanakkor a magas szintű művelet, amely leírja az üzenet feldolgozása nem automatikusan lesznek begyűjtve.

Nézzük meg, hogyan sikerült követni ezeket a műveleteket.

Magas szinten, a feladat, ha a `RequestTelemetry` és ismert tulajdonságainak beállítása. A művelet befejezése után nyomon követheti a telemetriát. A következő példa bemutatja ezt a feladatot.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-kérelem Owin helyi alkalmazás
Ebben a példában a nyomkövetési környezet propagálja a következők szerint a [HTTP-protokoll Összekapcsoláshoz](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). A fejlécek, amelyek nem szerepeltek kap kell látnia.

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

A HTTP-protokoll, a korrelációs is deklarálja a `Correlation-Context` fejléc. Azonban ezt kihagyja itt az egyszerűség kedvéért.

## <a name="queue-instrumentation"></a>Várólista rendszerállapot
Amíg [HTTP-protokoll Összekapcsoláshoz](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) átadandó HTTP-kérés a korreláció részletei, minden várólista protokollt meghatározásához, hogy ugyanazokat a részleteket az üzenetsorban található üzenet továbbítódnak rendelkezik. Néhány várólista protokollok (például AMQP) engedélyezése megadásának további metaadatokat, és mások (például Azure Storage-üzenetsor) van szükség a környezet, az üzenet hasznos adattartalmából értéket kódolni kell.

### <a name="service-bus-queue"></a>Service Bus-üzenetsor
Az Application Insights nyomon követi az új Service Bus-üzenetkezelés hívások [a Microsoft Azure Szolgáltatásbusz-ügyfél .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 3.0.0-s verziójával és újabb verzióit.
Ha [üzenetet kezelő minta](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) -üzenetek feldolgozása kész: automatikusan nyomon követi és egyéb telemetriát elemeket is vonatkozhatnak, és a szolgáltatás által végzett összes Service Bus-hívás. Tekintse meg a [nyomkövetés Microsoft Application Insights a Service Bus-ügyfélalkalmazást](../service-bus-messaging/service-bus-end-to-end-tracing.md) Ha manuálisan üzenetek feldolgozását.

Ha [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) csomag, további – a következő példák bemutatják, hogyan lehet nyomon követéséhez (és korrelálni) meghívja a Service Bus, Service Bus-üzenetsorba AMQP protokollt használja, és nem az Application Insights üzenetsor-műveletek automatikusan nyomon.
Korrelációs azonosítók adja át a rendszer az üzenet tulajdonságait.

#### <a name="enqueue"></a>Sorba

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

### <a name="azure-storage-queue"></a>Az Azure Storage-üzenetsor
Az alábbi példa bemutatja, hogyan nyomon követheti a [Azure Storage-üzenetsor](../storage/queues/storage-dotnet-how-to-use-queues.md) műveletek és összevetését telemetriát az előállítói, a fogyasztói és az Azure Storage között. 

A tárolási üzenetsort rendelkezik HTTP API-val. Összes hívás az üzenetsorba kötetblokkok az Application Insights függőségi gyűjtő a HTTP-kéréseket.
Ellenőrizze, hogy `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` a `applicationInsights.config`. Ha nem rendelkezik, adja hozzá a programozott módon [szűrése és előfeldolgozása a az Azure Application Insights SDK](app-insights-api-filtering-sampling.md).

Ha az Application Insights manuális beállítása, ellenőrizze, hogy hozzon létre inicializálása `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` hasonlóan:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Érdemes azt is korrelációját, ha az Application Insights Műveletazonosító tárolási kérelem azonosítóval. Információk a beállítása és lekérése a tárolási kérelem ügyfél és a egy kiszolgálói kérelem azonosítója: [figyelése, diagnosztizálása és hibaelhárítása az Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Sorba
Tároló-üzenetsorok támogatják a HTTP API-t, mert az üzenetsorhoz minden művelet automatikusan nyomon követi az Application Insights. Sok esetben elegendő az ebben a kialakítási kell lennie. Azonban korrelációját, ha a feldolgozói oldalon nyomkövetéseket az előállítói nyomkövetések, meg kell kontextust korrelációs hasonlóan, hogyan tesszük azt a HTTP protokoll a korrelációs. 

Ez a példa bemutatja, hogyan nyomon követheti a `Enqueue` műveletet. A következőket teheti:

 - **Újrapróbálkozások összekapcsolását (ha vannak)**: összes rendelkeznek egy közös szülő, amely rendelkezik a `Enqueue` műveletet. Ellenkező esetben ezek még nyomon követi a bejövő kérelem gyermekeiként. Ha több logikai kérelmeket az üzenetsorban, nehéz megtalálni a hívást eredményezett az újrapróbálkozásokat lehet.
 - **Vesse össze a Storage-naplók (ha szükséges)**:, Ön is vonatkozhatnak, és az Application Insights telemetria.

A `Enqueue` művelet egy szülő művelet (például a bejövő HTTP-kérést) gyermekét. A HTTP-függőségi hívás gyermekobjektuma a `Enqueue` művelet és a unoka a bejövő kérelem:

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

A telemetriai adatok mennyiségének csökkentésére az alkalmazás jelentéseket, vagy ha nem kívánja nyomon követni a `Enqueue` művelet más okok miatt, használja a `Activity` API közvetlenül:

- Hozzon létre (és start) egy új `Activity` helyett az Application Insights művelet indítása. Mégis *nem* hozzá kell rendelni, kivéve a művelet nevét, a tulajdonságokat.
- Szerializálható `yourActivity.Id` helyett a üzenet hasznos adatok `operation.Telemetry.Id`. Is `Activity.Current.Id`.


#### <a name="dequeue"></a>Eltávolítása a sorból
Hasonlóan `Enqueue`, a tárterület üzenetsorával tényleges HTTP-kérelem automatikusan nyomon követi az Application Insights. Azonban a `Enqueue` művelet valószínűleg történik, a szülő a környezetben, például egy bejövő kérelem környezetéből. Application Insights SDK-k automatikusan korrelációját, ha ilyen művelet (és a HTTP-része) a szülő kérelem és egyéb telemetriát jelentett ugyanabban a hatókörben.

A `Dequeue` meglehetősen műveletet. Az Application Insights SDK automatikusan nyomon követi a HTTP-kérelmekre. A korrelációs környezetben azonban azt nem ismert mindaddig, amíg a rendszer elemzi az üzenetet. Nem alkalmas korrelációját, ha a HTTP-kérelem a telemetriát a többi üzenet jelenik meg.

Sok esetben hasznos lehet más nyomkövetéseket, valamint a korrelációját, ha a HTTP-kérelem az üzenetsorba. Az alábbi példa bemutatja, hogyan teheti meg:

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

A következő példában egy bejövő üzenet követi nyomon módon hasonlóan a bejövő HTTP-kérelem:

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

Ehhez hasonlóan más üzenetsor-műveletek lesznek tagolva. A betekintési művelet eltávolítási műveletként hasonló módon lesznek tagolva. Üzenetsor-kezelési műveletek szándékkal nem szükséges. Az Application Insights nyomon követi a műveletek, például a HTTP és a legtöbb esetben elegendő.

Üzenet törlése, ezenkívül, ha mindenképpen állítsa be a műveletet (korrelációs) azonosítók. Másik lehetőségként használhatja a `Activity` API-t. Ezután nincs szükség művelet azonosítók a telemetriai adatok elemek be, mert az Application Insights SDK mindezt megteszi Ön helyett:

- Hozzon létre egy új `Activity` után egy elemet az üzenetsorból.
- Használat `Activity.SetParentId(message.ParentId)` korrelációját, fogyasztói és a gyártó naplókat.
- Indítsa el a `Activity`.
- Track eltávolítása a sorból, feldolgozása és törlési műveletek használatával `Start/StopOperation` segítők. Megteheti azt az azonos aszinkron átvitelvezérlés (végrehajtási környezet). Így azok még korrelált megfelelően.
- Állítsa le a `Activity`.
- Használat `Start/StopOperation`, vagy hívja az `Track` telemetriai adatokat manuálisan.

### <a name="batch-processing"></a>Kötegelt feldolgozás
Az egyes üzenetsorok sorból eltávolítás több üzenetet egy kéréssel. Ilyen üzenetek feldolgozása elvileg független, és a különböző logikai művelethez tartozik. Ebben az esetben már nem korrelációját, ha a `Dequeue` adott üzenet feldolgozási művelet.

Mindegyik üzenet a saját aszinkron átvitelvezérlés fel. További információkért lásd: a [kimenő függőségek követése](#outgoing-dependencies-tracking) szakaszban.

## <a name="long-running-background-tasks"></a>Hosszan futó háttérfeladatokat
Egyes alkalmazások indítása hosszú ideig futó műveletek, amelyek a felhasználói kérelmek oka lehet. A nyomkövetés/rendszerállapot szempontjából, nem tér kérés és a függőségi rendszerállapot: 

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

Ebben a példában `telemetryClient.StartOperation` hoz létre `RequestTelemetry` , és kitölti vele a korrelációs környezetet. Tegyük fel, hogy egy szülő művelet bejövő kérelmek, amelyek ütemezve a művelet által létrehozott-e meg. Mindaddig, `BackgroundTask` átvitelvezérlés azonos aszinkron indul el, egy bejövő kérésnek, azt, hogy szülőművelet összefügg. `BackgroundTask` és minden beágyazott telemetriai elem is automatikusan is vonatkozhatnak, és a kérés által okozott, a kérelem befejezése után is.

A feladat indításakor a háttér-szálból, amely nem rendelkezik minden olyan művelet (`Activity`) tartozik, `BackgroundTask` bármelyik szülő nem rendelkezik. Azonban azt is beágyazott műveleteket. A feladat által jelentett összes telemetriai elem lebontva a `RequestTelemetry` létrehozott `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Kimenő függőségek nyomon követése
Követheti, hogy a saját függőség típusa vagy az Application Insights által nem támogatott művelet.

A `Enqueue` metódus a Service Bus-üzenetsor vagy a tárolási üzenetsort az ilyen egyéni követési példák szolgálhat.

Az általános megközelítés az egyéni függőségi nyomkövetés, hogy:

- Hívja a `TelemetryClient.StartOperation` (kiterjesztés) módszer, amely alapján tölti ki a `DependencyTelemetry` tulajdonságai, a korrelációs és néhány egyéb tulajdonságok szükséges (kezdési idő bélyeg, időtartama).
- Állítsa be az egyéb egyéni tulajdonságait a `DependencyTelemetry`, például a nevét és minden más környezetre van szüksége.
- Győződjön meg arról, egy függőségi hívás, és várjon, amíg.
- Állítsa le a műveletet `StopOperation` amikor befejeződött.
- Kivételek kezelésére.

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

Művelet leállt, értékesítésére művelet okozza, így meghívása nélkül előfordulhat, hogy tegye `StopOperation`.

*Figyelmeztetés*: bizonyos esetekben unhanded kivétel előfordulhat, hogy [megakadályozása](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` kell meghívni, ezért nem lehetséges, hogy követi nyomon a műveleteket.

### <a name="parallel-operations-processing-and-tracking"></a>Párhuzamos műveletek feldolgozása és nyomon követése

`StopOperation` csak leállítja a műveletet, amely lett elindítva. Ha az aktuális futó művelet nem felel meg a megfelelőt, leállításához `StopOperation` nem csinál semmit. Ez a helyzet akkor fordulhat elő, ha több műveletet elindítani a párhuzamos végrehajtás ugyanabban a környezetben:

```csharp
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

Győződjön meg arról, hogy mindig hívja `StartOperation` és ugyanaz a művelet feldolgozása **aszinkron** metódus elkülöníteni a párhuzamosan futó műveletek. Ha a művelet szinkron (vagy nem aszinkron), wrap folyamat, és nyomon követheti a `Task.Run`:

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

- Alapismeretek a Mobilfunkciók [telemetriai korreláció](application-insights-correlation.md) az Application Insightsban.
- Tekintse meg a [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- Egyéni jelentés [eseményeket és mérőszámokat](app-insights-api-custom-events-metrics.md) az Application Insightsba.
- Tekintse meg a standard [konfigurációs](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) környezeti tulajdonságok a gyűjteményhez.
- Ellenőrizze a [System.Diagnostics.Activity felhasználói útmutató](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) megtekintéséhez, hogyan telemetria korrelációját, ha azt.
