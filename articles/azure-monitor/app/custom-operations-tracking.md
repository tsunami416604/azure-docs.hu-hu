---
title: Egyéni műveletek nyomon követése az Azure Application Insights .NET SDK segítségével
description: Egyéni műveletek nyomon követése az Azure Application Insights .NET SDK szolgáltatással
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 31c1fb366e7b109ea1fa4977d8e2f908e766e0f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276100"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Egyéni műveletek nyomon követése az Application Insights .NET SDK segítségével

Az Azure Application Insights SDK-k automatikusan nyomon követik a bejövő HTTP-kérelmeket és hívásokat a függő szolgáltatásokhoz, például a HTTP-kérelmekhez és az SQL-lekérdezésekhez. A kérelmek és függőségek nyomon követése és korrelációja betekintést nyújt a teljes alkalmazás válaszképességébe és megbízhatóságába az alkalmazást egyesítő összes mikroszolgáltatásban. 

Van egy osztály az alkalmazás minták, amelyek nem támogatott általánosan. Az ilyen minták megfelelő monitorozása manuális kódműszerezést igényel. Ez a cikk néhány olyan mintát tartalmaz, amelyek manuális instrumentációt igényelhetnek, például az egyéni várólista-feldolgozást és a hosszú ideig futó háttérfeladatok futtatását.

Ez a dokumentum útmutatást nyújt az egyéni műveletek nyomon követéséhez az Application Insights SDK-val. Ez a dokumentáció a következőkre vonatkozik:

- Application Insights a .NET (más néven Base SDK) 2.4+-os verziója.
- Application Insights webes alkalmazásokhoz (ASP.NET) 2.4+-os verziója.
- Application Insights ASP.NET Core 2.1+-os verziója.

## <a name="overview"></a>Áttekintés
A művelet egy alkalmazás által futtatott logikai munka. Nevét, kezdési idejét, időtartamát, eredményét és a végrehajtás környezetét, például a felhasználónevet, a tulajdonságokat és az eredményt rendelkezik. Ha az A műveletet a B művelet kezdeményezte, akkor a B művelet az A szülőjeként van beállítva. Egy műveletnek csak egy szülője lehet, de sok gyermekműveletet is használhat. A műveletekről és a telemetriai adatok korrelációjáról az [Azure Application Insights telemetriai korrelációcímű témakörben](correlation.md)talál további információt.

Az Application Insights .NET SDK, a művelet által leírt absztrakt osztály [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) és leszármazottai [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) és [DependencyTelemetry.](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs)

## <a name="incoming-operations-tracking"></a>Bejövő műveletek nyomon követése 
Az Application Insights webes SDK automatikusan gyűjti a HTTP-kérelmeket az IIS-folyamatban futó ASP.NET alkalmazásokhoz és az összes ASP.NET Core alkalmazáshoz. Vannak közösség által támogatott megoldások más platformokés keretek számára. Ha azonban az alkalmazást a szabványos vagy közösségi legtámogatottabban megoldások egyike sem támogatja, manuálisan is beállíthatja.

Egy másik példa, amely egyéni nyomon követést igényel, az a dolgozó, aki elemeket fogad a várólistából. Egyes várólisták esetén a várólistához üzenet hozzáadására irányuló hívás függőségként lesz nyomon követhető. Az üzenetfeldolgozást leíró magas szintű művelet azonban nem gyűjt automatikusan.

Lássuk, hogyan lehet nyomon követni az ilyen műveleteket.

Magas szinten a feladat ismert `RequestTelemetry` tulajdonságok létrehozása és beállítása. A művelet befejezése után nyomon követheti a telemetriai adatokat. A következő példa bemutatja ezt a feladatot.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-kérelem az Owin saját üzemeltetésű alkalmazásában
Ebben a példában a nyomkövetési környezet propagálása a [HTTP protokoll korrelációhoz](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Az ott leírt fejlécek fogadására számíthat.

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

A HTTP-protokoll korrelációhoz is deklarálja a `Correlation-Context` fejlécet. Azonban ez kimaradt itt az egyszerűség kedvéért.

## <a name="queue-instrumentation"></a>Várólista-műszerezés
Bár vannak [W3C trace context](https://www.w3.org/TR/trace-context/) és HTTP protokoll [korreláció](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) átad korrelációs részleteket HTTP-kérelem, minden várólista protokoll nak meg kell határoznia, hogy ugyanazokat a részleteket továbbítják végig a várólista-üzenet. Egyes várólista-protokollok (például Az AMQP) további metaadatok továbbítását teszik lehetővé, és mások (például az Azure Storage Queue) megkövetelik a környezet kódolását az üzenet hasznos adatába.

> [!NOTE]
> * **A várólisták** esetében még nem támogatott az összetevők közötti nyomkövetés Http-vel, ha a termelő és a fogyasztó telemetriát küld a különböző Application Insights-erőforrások, tranzakciódiagnosztikai tapasztalat és alkalmazástérkép megjelenítése tranzakciók és a leképezés végpontok között. Várólisták esetén ez még nem támogatott. 

### <a name="service-bus-queue"></a>Service Bus-üzenetsor
Az Application Insights nyomon követi a Service Bus Messaging-hívásokat az új [Microsoft Azure ServiceBus ügyféllel a .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 3.0.0-s vagy újabb verziójához.
Ha [az üzenetek feldolgozásához üzenetkezelő mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) használ, akkor a következőket végezheti el: a szolgáltatás által kezdeményezett összes Service Bus-hívás automatikusan nyomon követi és korrelál más telemetriai elemekkel. Ha manuálisan dolgozza fel az üzeneteket, olvassa el a [Service Bus-ügyfél nyomkövetését a Microsoft Application Insights szolgáltatással.](../../service-bus-messaging/service-bus-end-to-end-tracing.md)

Ha [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) csomagot használ, olvassa el tovább – az alábbi példák bemutatják, hogyan lehet nyomon követni (és korrelálni) a Service Bus-várólistába irányuló hívásokat, mivel a Service Bus-várólista AMQP protokollt használ, és az Application Insights nem követi automatikusan a várólista-műveleteket.
A korrelációs azonosítók átkerülnek az üzenet tulajdonságaiba.

#### <a name="enqueue"></a>Várólistára kerül

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

### <a name="azure-storage-queue"></a>Azure Storage-várólista
A következő példa bemutatja, hogyan követheti nyomon az [Azure Storage-várólista-műveleteket,](../../storage/queues/storage-dotnet-how-to-use-queues.md) és korrelálhat a gyártó, a fogyasztó és az Azure Storage közötti telemetriai adatokkal. 

A storage-várólista http API-val rendelkezik. A várólistába irányuló összes hívást az Application Insights függőséggyűjtő http-kérelmekhez követi nyomon.
Alapértelmezés szerint ASP.NET és ASP.NET Core alkalmazásokban van konfigurálva, más típusú alkalmazásokkal a [konzolalkalmazások dokumentációjára](../../azure-monitor/app/console.md) hivatkozhat

Azt is érdemes lehet az Application Insights-műveletazonosító és a storage-kérelem azonosítóját. A Storage-kérelemügyfél és a kiszolgálókérés-azonosító beállításáról és lekéréséről az [Azure Storage figyelése, diagnosztizálása és hibaelhárítása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)című témakörben talál további információt.

#### <a name="enqueue"></a>Várólistára kerül
Mivel a tárolási várólisták támogatják a HTTP API-t, a várólistával rendelkező összes műveletet automatikusan nyomon követi az Application Insights. Sok esetben ez a műszerek elegendőnek kell lennie. Ahhoz azonban, hogy a fogyasztói oldalon lévő nyomokat a gyártói nyomokkal korreláljuk, bizonyos korrelációs környezetet kell átadnia, hasonlóan ahhoz, ahogyan ezt a HTTP-protokollkorrel való összefüggésezéshez csináljuk. 

Ez a példa bemutatja, hogyan lehet nyomon követni a `Enqueue` műveletet. A következőket teheti:

 - **Újrapróbálkozások korrelálása (ha van ilyen)**: `Enqueue` Mindegyiknek van egy közös szülője, amely a művelet. Ellenkező esetben a bejövő kérelem gyermekeiként követi őket nyomon. Ha több logikai kérelmek a várólistába, nehéz lehet megtalálni, amely hívás eredményeként újrapróbálkozások.
 - **A storage-naplók korrelációja (ha és amikor szükséges)**: Korrelálnak az Application Insights telemetriai adataival.

A `Enqueue` művelet egy szülőművelet (például egy bejövő HTTP-kérelem) gyermeke. A HTTP-függőségi hívás a `Enqueue` művelet gyermeke és a bejövő kérelem unokája:

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

Az alkalmazásjelentések telemetriai adatainak csökkentéséhez, vagy ha `Enqueue` más okból nem `Activity` szeretné nyomon követni a műveletet, használja közvetlenül az API-t:

- Hozzon létre (és `Activity` indítsa el) az Application Insights-művelet helyett egy újat. A művelet neve kivételével *nem* kell hozzárendelnie rajta egyetlen tulajdonságot sem.
- Szerializálja `yourActivity.Id` az üzenet `operation.Telemetry.Id`hasznos adatát a helyett. Használhatja a `Activity.Current.Id`használatát is.


#### <a name="dequeue"></a>Várósor törlése
A példához `Enqueue`hasonlóan a storage-várólistába irányuló tényleges HTTP-kérést az Application Insights automatikusan nyomon követi. A `Enqueue` művelet azonban feltehetően a szülő környezetben történik, például egy bejövő kérelem környezetben. Az Application Insights SDK-k automatikusan korrelálnak egy ilyen műveletet (és annak HTTP-részét) a fölérendelt kérelemmel és az azonos hatókörben jelentett egyéb telemetriai adatokkal.

A `Dequeue` műtét trükkös. Az Application Insights SDK automatikusan nyomon követi a HTTP-kérelmeket. Azonban nem ismeri a korrelációs környezetet, amíg az üzenet nincs elemezve. Nem lehet korrelálni a HTTP-kérelmet az üzenet lekéréséhez a többi telemetriai adatokkal, különösen akkor, ha egynél több üzenet érkezik.

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
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
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

A következő példában a bejövő üzeneteket a bejövő HTTP-kéréshez hasonlóan követi a rendszer:

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

Hasonlóképpen más várólista-műveletek is műszerezhetők. A betekintési műveletet a várólistán állásmentesítési művelethez hasonló módon kell elhangzálni. Nincs szükség a várólista-kezelési műveletek műszerezésére. Az Application Insights nyomon követi az olyan műveleteket, mint a HTTP, és a legtöbb esetben ez elég.

Az üzenetek törlésének műszere, győződjön meg arról, hogy beállította a művelet (korrelációs) azonosítókat. Másik lehetőségként használhatja `Activity` az API-t. Ezután nem kell beállítania a telemetriai elemek működési azonosítóit, mert az Application Insights SDK ezt az Ön számára:

- Hozzon `Activity` létre egy újat, miután egy elemet a várólistából kapott.
- A `Activity.SetParentId(message.ParentId)` fogyasztói és a gyártói naplók korrelációja.
- Indítsa `Activity`el a t.
- Kövesse nyomon a dequeue, a `Start/StopOperation` folyamat és a műveletek törlése segítségével segítők. Végezze el ugyanabból az aszinkron vezérlési folyamatból (végrehajtási környezet). Ily módon, ők megfelelően összefügg.
- Állítsa `Activity`le a.
- Használja `Start/StopOperation`a `Track` telemetriai adatokat manuálisan.

### <a name="dependency-types"></a>Függőségi típusok

Az Application Insights függőségi típust használ a felhasználói felület élményének cusomizálásához. A várólisták esetében a `DependencyTelemetry` következő típusú típusokat ismeri fel, amelyek javítják [a tranzakciódiagnosztika élményét:](/azure/azure-monitor/app/transaction-diagnostics)
- `Azure queue`Azure storage-várólistákhoz
- `Azure Event Hubs`Azure Event Hubs-hoz
- `Azure Service Bus`az Azure Service Bus-hoz

### <a name="batch-processing"></a>Kötegelt feldolgozás
Egyes várólisták esetén egyetlen kéréssel több üzenet várólistájának törlését is lehetővé teheti. Az ilyen üzenetek feldolgozása feltehetően független, és a különböző logikai műveletekhez tartozik. A `Dequeue` műveletet nem lehet egy adott, a feldolgozás alatt lévő üzenethez kapcsolni.

Minden üzenetet a saját aszinkron vezérlési folyamatában kell feldolgozni. További információt a [Kimenő függőségek nyomon követése](#outgoing-dependencies-tracking) című szakaszban talál.

## <a name="long-running-background-tasks"></a>Hosszú ideig futó háttérfeladatok

Egyes alkalmazások hosszú ideig futó műveleteket indítanak, amelyeket a felhasználói kérelmek okozhatnak. A nyomkövetés/instrumentation szempontjából ez nem különbözik a kérelem vagy a függőség műszerek: 

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

Ebben a `telemetryClient.StartOperation` példában létrehozza `DependencyTelemetry` és kitölti a korrelációs környezetet. Tegyük fel, hogy van egy szülőművelete, amelyet a műveletet ütemező bejövő kérelmek hoztak létre. Mindaddig, `BackgroundTask` amíg ugyanabban az aszinkron vezérlési folyamatban kezdődik, mint egy bejövő kérelem, az összefügg az adott szülőművelettel. `BackgroundTask`és az összes beágyazott telemetriai elemek automatikusan korrelál a kérelmet, amely okozta, még a kérelem befejezése után is.

Amikor a feladat a háttérszálról indul, amelyhez nincs hozzá tartozó művelet (`Activity`) nincs hozzátartozója, `BackgroundTask` nincs szülője. Azonban lehet beágyazott műveleteket. A feladatból jelentett összes telemetriai `DependencyTelemetry` elem `BackgroundTask`korrelál a.-ban létrehozott elemhez.

## <a name="outgoing-dependencies-tracking"></a>Kimenő függőségek nyomon követése
Nyomon követheti a saját függőségi fajtát, vagy egy műveletet, amely nem támogatja az Application Insights.

A `Enqueue` Service Bus-várólistában vagy a Storage várólistában lévő metódus példaként szolgálhat az ilyen egyéni nyomon követéshez.

Az egyéni függőségek nyomon követésének általános megközelítése a következő:

- Hívja `TelemetryClient.StartOperation` meg a (kiterjesztés) `DependencyTelemetry` metódust, amely kitölti a korrelációhoz szükséges tulajdonságokat és néhány más tulajdonságot (kezdési időbélyegző, időtartam).
- Állítson be más `DependencyTelemetry`egyéni tulajdonságokat a en, például a név és bármely más környezetben, amire szüksége van.
- Függőségi hívást kezdeményezni, és várni rá.
- Állítsa le `StopOperation` a műveletet, ha elkészült.
- Kivételek kezelése.

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

A művelet ártalmatlanítása a művelet leállítását eredményezi, `StopOperation`ezért a hívás helyett megteheti.

*Figyelmeztetés:* bizonyos esetekben a kinemadható kivétel [megakadályozhatja,](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` hogy hívják, így a műveletek nem követhetők nyomon.

### <a name="parallel-operations-processing-and-tracking"></a>Párhuzamos műveletek feldolgozása és nyomon követése

`StopOperation`csak leállítja az elindított műveletet. Ha az aktuálisan futó művelet nem egyezik `StopOperation` meg a leállítani kívánt művelettel, nem tesz semmit. Ez a helyzet akkor fordulhat elő, ha több műveletet indít el párhuzamosan ugyanabban a végrehajtási környezetben:

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

Győződjön meg `StartOperation` arról, hogy mindig hívja és dolgozza fel a műveletet ugyanabban az **aszinkron** metódusban a párhuzamosan futó műveletek elkülönítéséhez. Ha a művelet szinkron (vagy nem aszinkron), körbefolyatja a folyamatot és nyomon követheti a következőt: `Task.Run`

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights-műveletek vs System.Diagnostics.Activity
`System.Diagnostics.Activity`az elosztott nyomkövetési környezetet jelöli, és a keretrendszerek és kódtárak a folyamaton belüli és kívüli környezetek létrehozására és terjesztésére, valamint a telemetriai elemek korrelációjára használják. A tevékenység `System.Diagnostics.DiagnosticSource` együttműködik - a keretrendszer/könyvtár közötti értesítési mechanizmussal, hogy értesítse az érdekes eseményeket (bejövő vagy kimenő kérelmek, kivételek stb.).

A tevékenységek első osztályú polgárok az Application Insightsban, és az automatikus `DiagnosticSource` függőségi és kérelemgyűjtés nagymértékben támaszkodik rájuk az eseményekkel együtt. Ha az alkalmazásban hozzon létre tevékenységet, az nem eredményezi az Application Insights telemetriai adatok létrehozását. Az Application Insights nak meg kell kapnia a DiagnosticSource-eseményeket, és ismernie kell az események nevét és a rakományt, hogy a tevékenységtelemetriává lefordítsa.

Minden Application Insights-művelet (kérés vagy `Activity` függőség) magában foglalja – ha `StartOperation` hívják, tevékenységet hoz létre alatta. `StartOperation`az ajánlott módja a kérelmek vagy függőségi telemetriai adatok manuális nyomon követésére, és győződjön meg arról, hogy minden korrelál.

## <a name="next-steps"></a>További lépések

- Ismerje meg a [telemetriai adatok korreláció](correlation.md) alapjait az Application Insightsban.
- Nézze meg, hogyan korrelált adatok hatáskörök [tranzakciódiagnosztika tapasztalat](../../azure-monitor/app/transaction-diagnostics.md) és [alkalmazástérkép](../../azure-monitor/app/app-map.md).
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](../../azure-monitor/app/data-model.md)
- Jelentse az egyéni [eseményeket és mutatókat](../../azure-monitor/app/api-custom-events-metrics.md) az Application Insightsnak.
- Tekintse meg a környezettulajdonság-gyűjtemény szabványos [konfigurációját.](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)
- Tekintse meg a [System.Diagnostics.Activity felhasználói útmutató,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) hogy hogyan korrelál telemetriai adatok.
