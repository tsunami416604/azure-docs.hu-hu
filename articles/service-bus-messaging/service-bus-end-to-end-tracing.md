---
title: Az Azure Service Bus végpontok közötti nyomon követése és diagnosztikája | Microsoft dokumentumok
description: A Service Bus ügyféldiagnosztikájának áttekintése és a végpontok között történő nyomkövetés (az ügyfél a feldolgozásban részt vevő összes szolgáltatáson keresztül.)
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7c2efc9c736097873201505f280af5d47bed4847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294177"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Elosztott nyomkövetés és korreláció a Service Bus üzenetküldése révén

A mikroszolgáltatások fejlesztésének egyik gyakori problémája az, hogy képes nyomon követni a műveletet az ügyféltől a feldolgozásban részt vevő összes szolgáltatáson keresztül. Hasznos hibakereséshez, teljesítményelemzéshez, A/B teszteléshez és más tipikus diagnosztikai forgatókönyvekhez.
Ennek a problémának az egyik része a logikai munka nyomon követése. Ez magában foglalja az üzenet feldolgozási eredmény és a késés és a külső függőségi hívások. Egy másik része a folyamathatárokon túli diagnosztikai események korrelációja.

Amikor egy gyártó üzenetet küld egy várólistán keresztül, az általában egy másik ügyfél vagy szolgáltatás által kezdeményezett logikai művelet hatókörében történik. Ugyanazt a műveletet a fogyasztó folytatja, amint üzenetet kap. Mind a termelő és a fogyasztó (és más szolgáltatások, amelyek feldolgozzák a műveletet), feltehetően telemetriai eseményeket bocsátanak ki a működési folyamat és az eredmény nyomon követéséhez. Az ilyen események és a nyomkövetési művelet végpontok utáni korrelációhoz minden telemetriai jelentést küldő szolgáltatásnak minden eseményt nyomkövetési környezettel kell lebélyegzővel lebélyegzővel.

A Microsoft Azure Service Bus üzenetküldés meghatározott hasznos adat tulajdonságokat, amelyeket a gyártók és a fogyasztók kell használnia, hogy adja át az ilyen nyomkövetési környezetben.
A protokoll a [HTTP korrelációs protokollon](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)alapul.

| Tulajdonság neve        | Leírás                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnosztikai azonosító       | A gyártótól a várólistába irányuló külső hívás egyedi azonosítója A [HTTP protokoll kérelemazonosítója](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) című részben tájékforalamizhat az indoklásról, a szempontokról és a formátumról |
|  Korrelációs-környezet | Műveleti környezet, amely a műveletfeldolgozásban részt vevő összes szolgáltatásra propagálva történik. További információ: [Correlation-Context in HTTP protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET ügyfél automatikus követése

A 3.0.0-s verziótól kezdve [a Microsoft Azure ServiceBus Client for .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) olyan nyomkövetési műszerpontokat biztosít, amelyek nyomkövetési rendszerekkel vagy ügyfélkód-darabbal kapcsolhatók össze.
A műszerek lehetővé teszik a Service Bus üzenetküldő szolgáltatása összes hívásának nyomon követését az ügyféloldalról. Ha az üzenetfeldolgozás az [üzenetkezelő mintával](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)történik, az üzenetfeldolgozás is

### <a name="tracking-with-azure-application-insights"></a>Nyomon követés az Azure Application Insights segítségével

[A Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) gazdag teljesítményfigyelési lehetőségeket biztosít, beleértve az automatikus mágikus kérelmeket és a függőségek nyomon követését.

A projekt típusától függően telepítse az Application Insights SDK-t:
- [ASP.NET](../azure-monitor/app/asp-net.md) - telepítse a 2.5-beta2 vagy újabb verziót
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - telepítse verzió 2.2.0-beta2 vagy újabb.
Ezek a hivatkozások részletesen ismertetik az SDK telepítését, az erőforrások létrehozását és az SDK konfigurálását (ha szükséges). A non-ASP.NET alkalmazások, tekintse meg az [Azure Application Insights for Console Applications](../azure-monitor/app/console.md) cikket.

Ha [az üzenetek feldolgozásához üzenetkezelő mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) használ, akkor a következőket végezheti el: a szolgáltatás által kezdeményezett összes Service Bus-hívás automatikusan nyomon követi és korrelál más telemetriai elemekkel. Ellenkező esetben olvassa el a következő példát a manuális üzenetfeldolgozás nyomon követéséhez.

#### <a name="trace-message-processing"></a>Nyomkövetési üzenetek feldolgozása

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

Ebben a `RequestTelemetry` példában minden feldolgozott üzenetről, időbélyeggel, időtartammal és eredménnyel (sikeres) jelent a rendszer. A telemetriai adatok korrelációs tulajdonságokat is rendelkezik.
Az üzenetfeldolgozás során jelentett beágyazott nyomkövetések és kivételek szintén a korrelációs tulajdonságokkal vannak ellátva, amelyek a `RequestTelemetry`rendszer "gyermekeiként" jelölik őket.

Abban az esetben, ha az üzenetfeldolgozás során kezdeményezett hívásokat a támogatott külső összetevőkhöz, a rendszer automatikusan nyomon követi és korrelál. Olvassa el [az Egyéni műveletek nyomon követése az Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) kézi nyomon követés és korreláció.

Ha az Application Insights SDK mellett bármilyen külső kódot futtat, az Application Insights-naplók megtekintésekor várhatóan hosszabb **időtartamot** fog látni. 

![Hosszabb időtartam az Application Insights-naplóban](./media/service-bus-end-to-end-tracing/longer-duration.png)

Ez nem jelenti azt, hogy késedelmesen fogadta az üzenetet. Ebben az esetben az üzenet már érkezett, mivel az üzenet az SDK-kód paraméterként való átadása óta érkezett. És az App Insights naplók (**Folyamat)** **névcímke** azt jelzi, hogy az üzenet feldolgozása folyamatban van a külső esemény feldolgozási kód. Ez a probléma nem azure-ral kapcsolatos. Ehelyett ezek a metrikák a külső kód hatékonyságára vonatkoznak, mivel az üzenet már kapott a Service Bustól. Tekintse meg [ezt a fájlt a GitHubon,](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) hogy lássa, hol jön létre és rendeli hozzá a **folyamatcímke,** miután az üzenet megkapta a Service Bustól. 

### <a name="tracking-without-tracing-system"></a>Nyomon követés nyomkövetési rendszer nélkül
Abban az esetben, ha a nyomkövetési rendszer nem támogatja az automatikus Service Bus hívások nyomon követését, előfordulhat, hogy ilyen támogatást szeretne hozzáadni egy nyomkövetési rendszerhez vagy az alkalmazáshoz. Ez a szakasz a Service Bus .NET ügyfél által küldött diagnosztikai eseményeket ismerteti.  

A Service Bus .NET ügyfél a .NET nyomkövetési primitívek [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) és [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)használatával történik.

`Activity`nyomon követési környezetként `DiagnosticSource` szolgál, míg az értesítési mechanizmus. 

Ha nincs figyelő a DiagnosticSource események, műszerek ki van kapcsolva, megtartva nulla műszerezési költségek. DiagnosticSource ad minden ellenőrzést a figyelő:
- a hallgató szabályozza, hogy mely forrásokés események hallatszolják
- a hallgató szabályozza az eseményarányt és a mintavételezést
- az események küldése olyan hasznos adattal lesz elküldve, amely teljes környezetet biztosít, így az esemény során elérheti és módosíthatja az Üzenet objektumot

Ismerkedjen meg a [DiagnosticSource felhasználói útmutatóval,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) mielőtt folytatná a megvalósítást.

Hozzon létre egy figyelőt a Service Bus-eseményekhez ASP.NET Core alkalmazásban, amely naplókat ír a Microsoft.Extension.Logger alkalmazással.
Használja [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) könyvtár feliratkozni DiagnosticSource (ez is könnyen feliratkozni DiagnosticSource nélküle)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

Ebben a példában a figyelő naplózza az időtartamot, az eredményt, az egyedi azonosítót és az egyes Service Bus-műveletek kezdési idejét.

#### <a name="events"></a>Események

Minden művelethez két esemény kerül elküldésre: "Start" és "Stop". Valószínűleg csak a "Stop" események érdeklik. A működés eredményét, valamint a kezdési időt és az időtartamot tevékenységi tulajdonságokként biztosítják.

Esemény hasznos adat biztosít a figyelő a művelet környezetében, replikálja az API bejövő paramétereket és a visszatérési értéket. A "Stop" esemény hasznos adata a "Start" esemény hasznos adatának összes tulajdonságával rendelkezik, így teljesen figyelmen kívül hagyhatja a "Start" eseményt.

Minden esemény "Entitás" és "Végpont" tulajdonságokkal is rendelkezik, ezek az alábbi táblázatban maradnak.
  * `string Entity`- - Az entitás neve (várólista, téma stb.)
  * `Uri Endpoint`- A Service Bus végpontjának URL-címe

Minden "Stop" `Status` esemény `TaskStatus` aszinkron művelettel rendelkező tulajdonsággal rendelkezik, amely az egyszerűség kedvéért az alábbi táblázatban is kimarad.

Itt a műszeres műveletek teljes listája:

| Művelet neve | Nyomon követett API | Adott hasznos adat tulajdonságai|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`- Az elküldött üzenetek listája |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`- Üzenet feldolgozása folyamatban<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`- Ütemezett üzenet eltolás<br/>`long SequenceNumber`- Az ütemezett üzenet sorszáma ("Stop" esemény hasznos teher) |
| Microsoft.Azure.ServiceBus.Mégse | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`- Sorszám te üzenetet meg kell szüntetni | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`- A fogadott üzenetek maximális száma.<br/>`IList<Message> Messages`- A fogadott üzenetek listája ("Stop" esemény hasznos teher) |
| Microsoft.Azure.ServiceBus.Betekintés | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`- A kiindulási pont, ahonnan böngészhet egy köteg üzenetet.<br/>`int RequestedMessageCount`- A beolvasandó üzenetek száma.<br/>`IList<Message> Messages`- A fogadott üzenetek listája ("Stop" esemény hasznos teher) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`- A felkapandó sorszámokat tartalmazó lista.<br/>`IList<Message> Messages`- A fogadott üzenetek listája ("Stop" esemény hasznos teher) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`- A listát, amely tartalmazza a zár tokenek a megfelelő üzeneteket kell kitölteni.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`- A zár token a megfelelő üzenetet elhagyni. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`- A zár token a megfelelő üzenetet, hogy elhalasztja. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`- A zárolási token a megfelelő üzenet holtüzenethez. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`- A zárolási token a megfelelő üzenetet megújítani zár.<br/>`DateTime LockedUntilUtc`- Új zár token lejárati dátum és idő UTC formátumban. ("Stop" esemény hasznos teher)|
| Microsoft.Azure.ServiceBus.Process | Az [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) szolgáltatásban megadott Message Handler lambda függvény | `Message Message`- Az üzenet feldolgozása folyamatban van. |
| Microsoft.Azure.ServiceBus.ProcessSession | Az [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) nyelven megadott üzenetmunkamenet-kezelő lambda függvény | `Message Message`- Az üzenet feldolgozása folyamatban van.<br/>`IMessageSession Session`- A kezelés alatt álló munkamenet |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`- A hozzáadandó szabályt tartalmazó szabályleírás. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`- Az eltávolítandó szabály neve. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`- Az előfizetéshez kapcsolódó összes szabály. ("Stop" hasznos teher) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`- A sessionId jelen van az üzenetekben. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`- A sessionId jelen van az üzenetekben.<br/>`byte [] State`- Munkamenet állapota ("Stop" esemény hasznos adat) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`- A sessionId jelen van az üzenetekben.<br/>`byte [] State`- Munkamenet állapota |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`- A sessionId jelen van az üzenetekben. |
| Microsoft.Azure.ServiceBus.Exception | bármilyen műszeres API| `Exception Exception`- Kivétel példány |

Minden esetben elérheti `Activity.Current` az aktuális műveleti környezetet tartalmazó t.

#### <a name="logging-additional-properties"></a>További tulajdonságok naplózása

`Activity.Current`részletes összefüggésben a jelenlegi működés és a szülők. További információ: [Tevékenység dokumentáció](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) további részletekért.
A Service Bus műszerek `Activity.Current.Tags` további információkat `MessageId` `SessionId` tartalmaznak a - rendelkeznek, és amikor rendelkezésre állnak.

A "Fogadás", "Betekintés" és "ReceiveDeferred" `RelatedTo` eseményt nyomon követő tevékenységekhez is lehet címke. Az eredményként `Diagnostic-Id`fogadott üzenetek különálló listáját tartalmazza.
Az ilyen művelet több, egymástól független üzenet fogadását eredményezheti. Emellett a `Diagnostic-Id` művelet indításakor nem ismert, így a "Fogadás" műveletek korrelálhatnak a "Process" műveletekkel, amelyek csak ezt a címkét használják. Ez akkor hasznos, ha a teljesítményproblémák elemzése, hogy mennyi ideig tartott az üzenet fogadása.

A Címkék naplózásának hatékony módja az, ha végighalad rajtuk, így a Címkék hozzáadása az előző példához így néz ki: 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Szűrés és mintavétel

Bizonyos esetekben kívánatos, hogy az események nek csak egy részét naplózza a teljesítménytöbblet vagy a tárolási felhasználás csökkentése érdekében. Naplózhatja csak a "Stop" eseményeket (mint az előző példában) vagy az események mintaszázalékát. 
`DiagnosticSource`lehetőséget biztosítanak annak `IsEnabled` predikátummal való elérésére. További információ: [Környezetalapú szűrés a DiagnosticSource alkalmazásban.](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)

`IsEnabled`a teljesítményhatás minimalizálása érdekében többször is meglehet hívni egyetlen művelethez.

`IsEnabled`a következő sorrendben hívják fel:

1. `IsEnabled(<OperationName>, string entity, null)`például `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Ne feledje, hogy nincs "Start" vagy "Stop" a végén. Segítségével kiszűrheti az egyes műveleteket vagy várólistákat. Ha a `false`visszahívás visszaadja, a művelet eseményei nem lesznek elküldve

   * A "Process" és a "ProcessSession" műveletek `IsEnabled(<OperationName>, string entity, Activity activity)` hez visszahívás is érkezik. Segítségével szűrheti az `activity.Id` eseményeket a vagy címkék alapján.
  
2. `IsEnabled(<OperationName>.Start)`például `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Ellenőrzi, hogy a "Start" eseményt el kell-e indítani. Az eredmény csak a "Start" eseményt érinti, de a további instrumentation nem függ tőle.

Nincs `IsEnabled` "Stop" esemény.

Ha valamilyen művelet eredménye `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` kivétel, a neve. Csak "Kivétel" eseményekre iratkozhat fel, és megakadályozhatja a műszerek többi részét. Ebben az esetben továbbra is kezelnie kell az ilyen kivételeket. Mivel más instrumentation le van tiltva, nem várható nyomkövetési környezet áramlását az üzeneteket a fogyasztótól a gyártóig.

Mintavételi `IsEnabled` stratégiákat is alkalmazhat. A vagy `Activity.RootId` `Activity.Id` biztosítja a konzisztens mintavételt az összes gumiabroncson (feltéve, hogy azt nyomon követési rendszer vagy saját kód jaalapján terjesztik).

Jelenlétében több `DiagnosticSource` hallgató ugyanazon a forrás, ez elég csak egy hallgató, `IsEnabled` hogy elfogadja az eseményt, így nem garantált, hogy hívják,

## <a name="next-steps"></a>További lépések

* [Application Insights korreláció](../azure-monitor/app/correlation.md)
* [Az Application Insights figyelheti a függőségeket,](../azure-monitor/app/asp-net-dependencies.md) hogy lássa, hogy a REST, az SQL vagy más külső erőforrások lassítják-e a függőséget.
* [Egyéni műveletek nyomon követése az Application Insights .NET SDK segítségével](../azure-monitor/app/custom-operations-tracking.md)
