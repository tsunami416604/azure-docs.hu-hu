---
title: Egyéni műveletek nyomon követése az Azure Application Insights .NET SDK-val | Microsoft Docs
description: Egyéni műveletek nyomon követése az Azure Application Insights .NET SDK-val
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: d966ff3bc00d5190ebc163d4f4bfa35ba73d21ab
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087673"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Egyéni műveletek nyomon követése Application Insights .NET SDK-val

Az Azure Application Insights SDK-k automatikusan követik a bejövő HTTP-kéréseket és a függő szolgáltatások (például HTTP-kérések és SQL-lekérdezések) hívásait. A kérések és a függőségek nyomon követése és összekapcsolása révén a teljes alkalmazás rugalmassága és megbízhatósága látható az alkalmazást egyesítő összes szolgáltatásban. 

Az alkalmazás-mintázatok olyan osztálya van, amely nem támogatott általános támogatással. Az ilyen mintázatok megfelelő monitorozásához manuális kódokra van szükség. Ez a cikk néhány olyan mintát tartalmaz, amelyek manuális működést igényelhetnek, például az egyéni várólisták feldolgozását és a hosszan futó háttérben végzett feladatok futtatását.

Ez a dokumentum útmutatást nyújt az egyéni műveletek nyomon követéséhez az Application Insights SDK-val. Ez a dokumentáció a következő anyagokra vonatkozik:

- Application Insights a .NET (más néven Base SDK) 2.4-es vagy újabb verziójára.
- A (ASP.NET-t futtató) webalkalmazások Application Insights a 2.4-es és újabb verzióit.
- Application Insights a 2.1-es ASP.NET Core-verzióhoz.

## <a name="overview"></a>Áttekintés
A művelet egy alkalmazás által futtatott logikai munkadarab. A név, a kezdési idő, az időtartam, az eredmény és a végrehajtás környezete, például a Felhasználónév, a tulajdonságok és az eredmény. Ha A műveletet a B művelet kezdeményezte, akkor A B művelet szülőként van beállítva A számára. Egy művelet csak egy szülővel rendelkezhet, de több alárendelt művelettel is rendelkezhet. A műveletekkel és a telemetria kapcsolatos további információkért lásd: [Azure Application Insights telemetria korreláció](correlation.md).

A Application Insights .NET SDK-ban a műveletet a [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) absztrakt osztálya, valamint a [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) és a [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs)leszármazottai írják le.

## <a name="incoming-operations-tracking"></a>Bejövő műveletek követése 
A Application Insights web SDK automatikusan gyűjt HTTP-kérelmeket az IIS-folyamatokban és az összes ASP.NET Core alkalmazásban futó ASP.NET-alkalmazásokhoz. Más platformokhoz és keretrendszerekhez Közösség által támogatott megoldások tartoznak. Ha azonban a standard vagy a Közösség által támogatott megoldások egyike sem támogatja az alkalmazást, manuálisan is elvégezheti azt.

Egy másik példa, amely egyéni követést igényel, az a feldolgozó, amely a várólistából fogad elemeket. Egyes várólisták esetében az üzenetnek a várólistára való felvételének hívása függőségként lesz nyomon követve. Az üzenetek feldolgozását ismertető magas szintű művelet azonban nem lesz automatikusan begyűjtve.

Lássuk, hogyan követhetik nyomon ezeket a műveleteket.

Magas szinten a feladat az ismert tulajdonságok létrehozása `RequestTelemetry` és beállítása. A művelet befejezése után nyomon követheti a telemetria. A következő példa azt mutatja be, hogy ez a feladat.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-kérelem a Owin saját üzemeltetésű alkalmazásában
Ebben a példában a nyomkövetési környezetet a rendszer a [korrelációhoz tartozó HTTP protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)szerint propagálja. Az itt ismertetett fejléceket kell elvárnia.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
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

A korrelációs HTTP-protokoll a `Correlation-Context` fejlécet is deklarálja. Az egyszerűség kedvéért azonban itt kimarad.

## <a name="queue-instrumentation"></a>Üzenetsor-kialakítás
Míg a [W3C nyomkövetési kontextus](https://www.w3.org/TR/trace-context/) és a [http protokoll a](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) korrelációs adatoknak a http-kérelemmel való átadására szolgál, minden üzenetsor-protokollnak meg kell határoznia, hogyan adja meg ugyanazokat az adatokat az üzenetsor-üzenetben. Egyes üzenetsor-protokollok (például a AMQP) lehetővé teszik a további metaadatok átadását és mások (például az Azure Storage-üzenetsor) használatát, hogy a környezet kódolva legyen az üzenet tartalmába.

> [!NOTE]
> * **Az összetevők közötti nyomkövetés még nem támogatott a várólisták esetében** Ha a gyártó és a fogyasztó a telemetria különböző Application Insights-erőforrásokra küldi, a tranzakciós diagnosztika és az alkalmazás-hozzárendelés a tranzakciókat jeleníti meg, és leképezi a teljes végpontot. Várólisták esetén ez még nem támogatott. 

### <a name="service-bus-queue"></a>Service Bus-üzenetsor
Application Insights nyomon követi az üzenetkezelési hívásokat Service Bus az új [Microsoft Azure ServiceBus-ügyféllel a .net](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) -es vagy újabb verzióhoz.
Ha az üzenetkezelési [mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) használja az üzenetek feldolgozásához, a rendszer a szolgáltatás által végzett összes Service Bus automatikusan nyomon követi és korrelálja a többi telemetria elemmel. Ha manuálisan dolgozza fel az üzeneteket, tekintse meg a [Service Bus-ügyfél nyomkövetését a Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) használatával.

Ha a [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) csomagot használja, olvassa el az alábbi példákat: bemutatjuk, hogyan követheti nyomon (és korrelálhatja) a hívásokat a Service Bus Service Bus ÜZENETSOR a AMQP protokollt használja, és a Application Insights nem követi automatikusan a várólistát Operations.
A korrelációs azonosítók az üzenet tulajdonságaiban lesznek átadva.

#### <a name="enqueue"></a>Sorba helyezni

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
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
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

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

### <a name="azure-storage-queue"></a>Azure Storage-üzenetsor
Az alábbi példa bemutatja, hogyan követheti nyomon az [Azure Storage-várólista](../../storage/queues/storage-dotnet-how-to-use-queues.md) műveleteit, és hogyan korrelálhat telemetria a gyártó, a fogyasztó és az Azure Storage között. 

A tárolási várólista HTTP API-val rendelkezik. A várólista összes hívását nyomon követheti a HTTP-kérelmek Application Insights függőségi gyűjtője.
Alapértelmezés szerint a ASP.NET és ASP.NET Core alkalmazásokban más típusú alkalmazásokkal van konfigurálva, a [konzolon futó alkalmazások dokumentációjában](../../azure-monitor/app/console.md) olvashat

Érdemes lehet összekapcsolni a Application Insights műveleti AZONOSÍTÓját is a Storage-kérelem azonosítójával. A Storage-kérelmek ügyfelének és a kiszolgálói kérelmek AZONOSÍTÓjának beállításával és lekérésével kapcsolatos információkért lásd: az [Azure Storage figyelése, diagnosztizálása és hibáinak megoldása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Sorba helyezni
Mivel a tárolási várólisták támogatják a HTTP API-t, a Application Insights automatikusan nyomon követ minden, a várólistával kapcsolatos műveletet. Sok esetben ez a rendszerállapot elég. Ahhoz azonban, hogy a nyomon követéseket a gyártó által követett nyomkövetéssel társítsa a felhasználói oldalon, hasonló korrelációs kontextust kell átadnia a HTTP protokoll korrelációs működéséhez. 

Ez a példa bemutatja, hogyan követheti nyomon a `Enqueue` műveletet. A következőket teheti:

 - **Újrapróbálkozások korrelációja (ha van)** : Mindegyiknek van egy közös szülője, amely `Enqueue` a művelet. Ellenkező esetben a rendszer a bejövő kérelem gyermekeiként követi nyomon. Ha több logikai kérelem van a várólistához, nehéz lehet megállapítani, hogy melyik hívás eredményezte az újrapróbálkozásokat.
 - **A tárolási naplók korrelációja (ha szükséges)** : Application Insights telemetria korrelálnak.

A `Enqueue` művelet a szülő művelet gyermeke (például egy bejövő HTTP-kérelem). A http-függőségi hívás a `Enqueue` művelet gyermeke és a bejövő kérelem unokája:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
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

Ha csökkenteni szeretné az telemetria mennyiségét, vagy ha más okból nem kívánja nyomon követni `Enqueue` a műveletet, használja közvetlenül az `Activity` API-t:

- A Application Insights művelet elindítása helyett hozzon `Activity` létre (és kezdjen el) egy újat. Semmilyen tulajdonságot *nem* kell hozzárendelni, kivéve a művelet nevét.
- Szerializálja az üzenet adattartalmát a `operation.Telemetry.Id`helyett. `yourActivity.Id` Használhatja a t is `Activity.Current.Id`.


#### <a name="dequeue"></a>Sorból
`Enqueue`Ehhez hasonlóan a Application Insights automatikusan nyomon követik a tárolási üzenetsor tényleges http-kérelmét. Azonban a `Enqueue` művelet valószínűleg a szülő kontextusban történik, például egy bejövő kérelmek környezetében. Application Insights SDK-k automatikusan korrelálnak egy ilyen műveletet (és annak HTTP-részét) a szülő kérelemmel és az ugyanazon a hatókörben jelentett más telemetria.

A `Dequeue` művelet trükkös. A Application Insights SDK automatikusan nyomon követi a HTTP-kérelmeket. Azonban az üzenet elemzése előtt nem ismeri a korrelációs környezetet. A HTTP-kérést nem lehet összekapcsolni, hogy az üzenetet a többi telemetria kapja, különösen akkor, ha egynél több üzenet érkezik.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
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
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Folyamat

A következő példában a bejövő üzenetek a bejövő HTTP-kérésekhez hasonlóan követik nyomon:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
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

Hasonlóképpen, a többi üzenetsor-művelet is kialakítható. A betekintés műveletet hasonló módon kell kiépíteni, mint a dequeuing műveletet. Nincs szükség a rendszerállapot-kezelő várólista-kezelési műveletekre. Application Insights nyomon követi a műveleteket, például a HTTP-t, és a legtöbb esetben ez elég.

Ha a hangszer-üzenetek törlését végzi, ügyeljen rá, hogy a művelet (korreláció) azonosítóit állítsa be. Másik lehetőségként használhatja az `Activity` API-t is. Ezután nem kell megadnia a telemetria elemekben a műveleti azonosítókat, mert a Application Insights SDK ezt teszi:

- Hozzon létre `Activity` egy újat, miután megkapta az elemet a várólistából.
- A `Activity.SetParentId(message.ParentId)` használatával összekapcsolhatja a fogyasztói és a termelői naplókat.
- Indítsa el `Activity`a t.
- A deüzenetsor-, folyamat-és törlési műveletek `Start/StopOperation` nyomon követése segítők használatával. Ugyanezt az aszinkron vezérlési folyamat (végrehajtási környezet) alapján végezze el. Így megfelelően korrelálnak.
- Állítsa le `Activity`a t.
- Manuálisan `Start/StopOperation`is használhatja vagy `Track` meghívhatja a telemetria.

### <a name="dependency-types"></a>Függőségi típusok

A Application Insights függőségi típus használatával cusomize a felhasználói felületi élményeket. A várólisták esetében a következő típusokat `DependencyTelemetry` ismeri fel, amelyek javítják a [tranzakciós diagnosztika élményét](/azure/azure-monitor/app/transaction-diagnostics):
- `Azure queue`Azure Storage-várólisták esetén
- `Azure Event Hubs`Azure-Event Hubs
- `Azure Service Bus`Azure Service Bus

### <a name="batch-processing"></a>Kötegelt feldolgozás
Egyes várólisták esetében több üzenetet is elhelyezhet egy kérelemmel. Az ilyen üzenetek feldolgozása valószínűleg független, és a különböző logikai műveletekhez tartozik. A `Dequeue` műveletet nem lehet korrelálni egy adott üzenet feldolgozásakor.

Minden üzenetet a saját aszinkron vezérlési folyamatában kell feldolgozni. További információ: a [kimenő függőségek követése](#outgoing-dependencies-tracking) szakasz.

## <a name="long-running-background-tasks"></a>Hosszan futó háttérbeli feladatok

Egyes alkalmazások olyan hosszan futó műveleteket indítanak el, amelyeket a felhasználói kérések okozhatnak. A nyomkövetés/rendszerállapot szempontjából nem különbözik a kérelemtől vagy a függőségi állapottól: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
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

Ebben a példában `telemetryClient.StartOperation` a létrehozza `DependencyTelemetry` és betölti a korrelációs környezetet. Tegyük fel, hogy rendelkezik egy fölérendelt művelettel, amelyet a műveletet ütemezett bejövő kérelmek hoztak létre. Ha a rendszer ugyanabban az aszinkron vezérlési folyamatban indulelbejövőkérelemként,akkorazafölérendeltművelettelösszefügg.`BackgroundTask` `BackgroundTask`és az összes beágyazott telemetria elem automatikusan összefügg azzal a kéréssel, amelyik azt okozta, még a kérelem befejeződése után is.

Ha a feladat olyan háttérbeli szálból indul el, amelynek nincs művelete (`Activity`) társítva van, `BackgroundTask` nem rendelkezik szülővel. Azonban rendelkezhet beágyazott műveletekkel. A feladatból jelentett összes telemetria-elem összefügg a `DependencyTelemetry` `BackgroundTask`létrehozásával.

## <a name="outgoing-dependencies-tracking"></a>Kimenő függőségek követése
Nyomon követheti a saját függőségi típusát vagy a Application Insights által nem támogatott műveleteket.

Az Service Bus üzenetsor vagy a Storage-várólista metódusapéldakéntszolgálhatazegyéninyomonkövetéshez.`Enqueue`

Az egyéni függőségi követés általános megközelítése a következő:

- Hívja meg `TelemetryClient.StartOperation` a (kiterjesztés) metódust, `DependencyTelemetry` amely kitölti a korrelációhoz és más tulajdonságokhoz szükséges tulajdonságokat (Kezdési idő bélyegzője, időtartam).
- Adja meg az `DependencyTelemetry`egyéb egyéni tulajdonságokat, például a nevet és a szükséges egyéb környezeteket.
- Hozzon el egy függőségi hívást, és várjon rá.
- Állítsa le a műveletet `StopOperation` , ha befejeződött.
- Kezeli a kivételeket.

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

Az ártalmatlanítási művelet leállítja a műveletet, így nem kell meghívnia `StopOperation`.

*Figyelmeztetés*: bizonyos esetekben a nem kezelt `finally` kivételek miatt [Előfordulhat, hogy a](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) rendszer nem tudja követni a műveleteket.

### <a name="parallel-operations-processing-and-tracking"></a>Párhuzamos műveletek feldolgozása és nyomon követése

`StopOperation`csak az elindított műveletet állítja le. Ha az aktuálisan futó művelet nem egyezik meg azzal, amelyet le szeretne `StopOperation` állítani, akkor semmit sem tesz. Ez a helyzet akkor fordulhat elő, ha párhuzamosan több műveletet indít el ugyanabban a végrehajtási környezetben:

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

Győződjön meg arról, hogy `StartOperation` a párhuzamosan futó műveletek elkülönítése érdekében mindig ugyanazt az **aszinkron** módszerrel hívja meg és dolgozza fel a műveletet. Ha a művelet szinkron (vagy nem aszinkron), a folyamat és a nyomon `Task.Run`követés a következővel történik:

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights-műveletek vs System. Diagnostics. Activity
`System.Diagnostics.Activity`az elosztott nyomkövetési környezetet jelöli, és a keretrendszerek és könyvtárak használják a folyamaton belüli és kívüli környezet létrehozására és propagálására, valamint a telemetria-elemek korrelációját. `System.Diagnostics.DiagnosticSource` A tevékenység együttműködik a keretrendszer/könyvtár közötti értesítési mechanizmussal, amely értesíti az érdekes eseményekről (bejövő vagy kimenő kérelmek, kivételek stb.).

A tevékenységek a Application Insights és az automatikus függőségek, valamint a kérések gyűjteménye az `DiagnosticSource` eseményekkel együtt erősen támaszkodik rájuk. Ha tevékenységet hoz létre az alkalmazásban, akkor nem eredményezi Application Insights telemetria létrehozását. Application Insights meg kell kapnia a DiagnosticSource eseményeket, és ismernie kell az események nevét és a hasznos adatokat, hogy lefordítsa a tevékenységeket a telemetria.

Minden Application Insights művelet (kérelem vagy függőség) magában foglalja `Activity` a- `StartOperation` ha a neve, a tevékenység a következőt hozza létre:. `StartOperation`a kérések vagy függőségi telemetriáiról manuális nyomon követése ajánlott módszer, és minden összefügg.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg a [telemetria korrelációjának](correlation.md) alapjaival Application Insightsban.
- Tekintse át a korrelált adatkezelési [tranzakciós diagnosztika](../../azure-monitor/app/transaction-diagnostics.md) és az [alkalmazás-hozzárendelés](../../azure-monitor/app/app-map.md)módját.
- Tekintse meg az [adatmodellt](../../azure-monitor/app/data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Egyéni [eseményeket és mérőszámokat](../../azure-monitor/app/api-custom-events-metrics.md) jelenthet a Application Insights.
- Tekintse meg a környezeti tulajdonságok gyűjteményének szabványos [konfigurációját](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) .
- Tekintse meg a [System. Diagnostics. Activity felhasználói útmutatót](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) , és nézze meg, hogyan korreláljuk a telemetria.
