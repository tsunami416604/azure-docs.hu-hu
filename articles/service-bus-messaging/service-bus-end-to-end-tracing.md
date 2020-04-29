---
title: Végpontok közötti nyomkövetés és diagnosztika Azure Service Bus | Microsoft Docs
description: Az Service Bus-ügyfél diagnosztika és a végpontok közötti nyomkövetés áttekintése (ügyfél a feldolgozásban részt vevő összes szolgáltatáson keresztül)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294177"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Elosztott nyomkövetés és korreláció Service Bus üzenetkezelésen keresztül

A szolgáltatások fejlesztésének egyik leggyakoribb problémája az, hogy nyomon tudja követni a műveletet egy ügyfélen a feldolgozásban érintett összes szolgáltatáson keresztül. Hibakeresés, teljesítmény-elemzés, A/B tesztelés és egyéb jellemző diagnosztikai forgatókönyvek esetén hasznos.
A probléma egyik része a logikai munkafolyamatok nyomon követése. Magában foglalja az üzenetek feldolgozásának eredményét, valamint a késést és a külső függőségi hívásokat. Egy másik rész a folyamat határain túli diagnosztikai események korrelációja.

Amikor egy gyártó üzenetet küld egy várólistán keresztül, általában egy másik, más ügyfél vagy szolgáltatás által kezdeményezett logikai művelet hatókörében fordul elő. Ugyanezt a műveletet a fogyasztó is folytatja, amint üzenetet kap. Mind a gyártó, mind a fogyasztó (és más, a műveletet feldolgozó szolgáltatások), feltehetően telemetria-események, hogy nyomon kövessék a művelet folyamatát és eredményét. Ahhoz, hogy az ilyen események és nyomkövetési műveletek teljes körűek legyenek, minden, a telemetria-t jelentést tevő szolgáltatásnak meg kell Stamp minden eseményt egy nyomkövetési környezettel.

Microsoft Azure Service Bus üzenetkezelés olyan adattartalom-tulajdonságokat adott meg, amelyeket a gyártóknak és a felhasználóknak az ilyen nyomkövetési környezet továbbítására kell használniuk.
A protokoll a [http korrelációs protokollon](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)alapul.

| Tulajdonság neve        | Leírás                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnosztikai azonosító       | Egy külső hívás egyedi azonosítója a termelőről a várólistára. A logika, a szempontok és a formátum érdekében olvassa el a [HTTP protokollon keresztüli kérelem-azonosítót](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) . |
|  Korreláció – környezet | A műveleti környezet, amelyet a rendszer a művelet-feldolgozásban részt vevő összes szolgáltatás között propagál. További információ: [korrelációs környezet a http protokollban](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET-ügyfél autonyomkövetése

A Microsoft Azure 3.0.0-es verziótól kezdődően a [.net-hez készült ServiceBus-ügyfél a](/dotnet/api/microsoft.azure.servicebus.queueclient) nyomkövetési rendszerek vagy az ügyfél kódjának összekapcsolására szolgáló rendszerállapot-figyelési pontokat biztosít.
A Instrumentation lehetővé teszi az Service Bus Messaging szolgáltatás összes hívásának nyomon követését az ügyfél oldaláról. Ha az üzenet feldolgozása az [üzenetkezelő mintával](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)történik, az üzenet feldolgozására is szükség van

### <a name="tracking-with-azure-application-insights"></a>Nyomon követés az Azure Application Insights

A [Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) sokoldalú teljesítmény-figyelési funkciókat biztosít, beleértve az AUTOMAGIC-kérelmeket és a függőségek nyomon követését.

A projekt típusától függően telepítse a Application Insights SDK-t:
- [ASP.net](../azure-monitor/app/asp-net.md) – install 2,5-Beta2 vagy újabb verzió
- [ASP.net Core](../azure-monitor/app/asp-net-core.md) – telepítse a 2.2.0-Beta2 vagy újabb verziót.
Ezek a hivatkozások részletesen ismertetik az SDK telepítését, az erőforrások létrehozását és az SDK konfigurálását (ha szükséges). Non-ASP.NET-alkalmazások esetében tekintse meg az [Azure Application Insights for Console Applications](../azure-monitor/app/console.md) című cikket.

Ha az üzenetkezelési [mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) használja az üzenetek feldolgozásához, a rendszer a szolgáltatás által végzett összes Service Bus automatikusan nyomon követi és korrelálja a többi telemetria elemmel. Ellenkező esetben az alábbi példát tekintheti meg a manuális üzenetek feldolgozásának nyomon követéséhez.

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

Ebben a példában `RequestTelemetry` minden feldolgozott üzenetről jelentés készül, amely időbélyeggel, időtartammal és eredménnyel (sikeres) rendelkezik. A telemetria korrelációs tulajdonságokkal is rendelkezik.
Az üzenetek feldolgozásakor jelentett beágyazott nyomkövetéseket és kivételeket a `RequestTelemetry`rendszer "Children"-ként jelképező korrelációs tulajdonságokkal is lepecsételi.

Ha az üzenetek feldolgozása során kezdeményezi a támogatott külső összetevőket, azokat a rendszer automatikusan nyomon követi és korrelálja is. A manuális nyomon követéshez és korrelációhoz tekintse meg az [Egyéni műveletek nyomon követését Application Insights .net SDK-val](../azure-monitor/app/custom-operations-tracking.md) .

Ha a Application Insights SDK mellett bármilyen külső kódot futtat, a Application Insights naplók megtekintésekor várhatóan hosszabb **időtartamot** kell látnia. 

![Hosszabb időtartam Application Insights naplóban](./media/service-bus-end-to-end-tracing/longer-duration.png)

Ez nem jelenti azt, hogy késés történt az üzenet fogadásakor. Ebben az esetben az üzenet már meg lett fogadva, mert az üzenet az SDK-kód paraméterként lett átadva. Az alkalmazás-elemzési naplók (**folyamat**) **neve** címkéje pedig azt jelzi, hogy az üzenetet most a külső esemény-feldolgozási kód dolgozza fel. Ez a probléma nem kapcsolódik az Azure-hoz. Ezek a metrikák Ehelyett a külső kód hatékonyságára utalnak, mivel az üzenet már a Service Bustól érkezett. Tekintse [meg ezt a fájlt a githubon](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) , ahol megtekintheti, hogy a rendszer hol hozza létre és rendelje hozzá a **folyamat** címkéjét, ha az üzenet Service Busról érkezett 

### <a name="tracking-without-tracing-system"></a>Nyomkövetési rendszerek nélküli követés
Ha a nyomkövetési rendszer nem támogatja az automatikus Service Bus a hívások követését, előfordulhat, hogy az ilyen támogatást egy nyomkövetési rendszerbe vagy az alkalmazásba kívánja hozzáadni. Ez a szakasz a Service Bus .NET-ügyfél által eljuttatott diagnosztikai eseményeket ismerteti.  

Service Bus .NET-ügyfél a .NET nyomkövetési primitívek [System. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) és [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)használatával lett kialakítva.

`Activity`nyomkövetési kontextusként szolgál, `DiagnosticSource` miközben egy értesítési mechanizmus. 

Ha nincs figyelő a DiagnosticSource eseményekhez, a rendszer kikapcsolja a rendszerállapot-figyelési költségeket. A DiagnosticSource minden vezérlést biztosít a figyelőnek:
- a figyelő vezérli, hogy mely forrásokhoz és eseményekhez kell figyelni
- a figyelő az események arányát és a mintavételezést vezérli
- az események olyan adattartalommal lesznek elküldve, amely teljes kontextust biztosít, hogy az üzenet objektuma az esemény során hozzáférhessen és módosítható legyen.

Ismerkedjen meg a [DiagnosticSource felhasználói útmutatóval](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) , mielőtt folytatná a megvalósítást.

Hozzon létre egy figyelőt a ASP.NET Core alkalmazásban Service Bus eseményekhez, amelyek a Microsoft. Extension. naplózó naplókat írnak.
A [System. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) függvénytárat használja a DiagnosticSource-re való előfizetésre (az DiagnosticSource nélkül is egyszerűen előfizethet)

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

Ebben a példában a figyelő naplózza az időtartamot, az eredményt, az egyedi azonosítót és a kezdési időpontot az egyes Service Bus műveletekhez.

#### <a name="events"></a>Események

Minden művelethez két eseményt kell elküldeni: "Start" és "Leállítás". Legvalószínűbb, hogy csak a "stop" események érdeklik. A művelet eredményét, valamint a kezdési időt és az időtartamot adja meg tevékenység tulajdonságaiként.

Az esemény-adattartalom egy figyelőt biztosít a művelet kontextusában, és replikálja az API bejövő paramétereit és a visszatérési értéket. A "stop" esemény adattartalma rendelkezik a "Start" esemény hasznos adataival, így teljesen figyelmen kívül hagyhatja a "Start" eseményt.

Az összes esemény "Entity" és "Endpoint" tulajdonságokkal is rendelkezik, ezeket a rendszer az alábbi táblázatban hagyja ki
  * `string Entity`– Az entitás neve (Üzenetsor, témakör stb.)
  * `Uri Endpoint`– Service Bus végpont URL-címe

Minden "Leállítás" eseménynek `Status` van olyan `TaskStatus` tulajdonsága, amely aszinkron művelettel fejeződött be, és az egyszerűség kedvéért a következő táblázatban is kimarad.

Itt látható a műszeres műveletek teljes listája:

| Művelet neve | Követett API | Adott hasznos adat tulajdonságai|
|----------------|-------------|---------|
| Microsoft. Azure. ServiceBus. Send | [MessageSender. SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`– Küldendő üzenetek listája |
| Microsoft. Azure. ServiceBus. ScheduleMessage | [MessageSender. ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`– Feldolgozott üzenet<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`– Ütemezett üzenet eltolása<br/>`long SequenceNumber`– Ütemezett üzenet sorszáma ("stop" esemény hasznos adat) |
| Microsoft. Azure. ServiceBus. cancel | [MessageSender. CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`– A megszakítani kívánt üzenet sorszáma | 
| Microsoft. Azure. ServiceBus. Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`– A fogadott üzenetek maximális száma.<br/>`IList<Message> Messages`– Fogadott üzenetek listája ("stop" esemény hasznos adat) |
| Microsoft. Azure. ServiceBus. Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`– A kiindulási pont, amelyből a rendszer megkeresi az üzenetek kötegét.<br/>`int RequestedMessageCount`– A lekérdezni kívánt üzenetek száma.<br/>`IList<Message> Messages`– Fogadott üzenetek listája ("stop" esemény hasznos adat) |
| Microsoft. Azure. ServiceBus. ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`– A Beérkezendő sorozatszámokat tartalmazó lista.<br/>`IList<Message> Messages`– Fogadott üzenetek listája ("stop" esemény hasznos adat) |
| Microsoft. Azure. ServiceBus. Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`– A megfelelő üzenetek zárolási jogkivonatait tartalmazó lista.|
| Microsoft. Azure. ServiceBus. elhagyása | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`– A felhagyni kívánt üzenet zárolási jogkivonata. |
| Microsoft. Azure. ServiceBus. Elhalasztva | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`– Az elhalasztáshoz tartozó üzenet zárolási jogkivonata. | 
| Microsoft. Azure. ServiceBus. kézbesítetlen levelek | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`– A megfelelő üzenet zárolási jogkivonata a kézbesítetlen levelekhez. | 
| Microsoft. Azure. ServiceBus. RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`– A zárolás megújításához tartozó üzenet zárolási jogkivonata.<br/>`DateTime LockedUntilUtc`-Új zárolási token lejárati dátuma és időpontja UTC formátumban. ("Stop" esemény hasznos adat)|
| Microsoft. Azure. ServiceBus. Process | Az [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) üzenet-kezelő lambda függvénye | `Message Message`– Az üzenet feldolgozása folyamatban van. |
| Microsoft. Azure. ServiceBus. ProcessSession | Az [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) üzenet munkamenet-kezelője lambda-funkciója | `Message Message`– Az üzenet feldolgozása folyamatban van.<br/>`IMessageSession Session`– Feldolgozás alatt álló munkamenet |
| Microsoft. Azure. ServiceBus. AddRule | [SubscriptionClient. AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`– A szabály által felvenni kívánt szabály leírása. |
| Microsoft. Azure. ServiceBus. RemoveRule | [SubscriptionClient. RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`– Az eltávolítandó szabály neve. |
| Microsoft. Azure. ServiceBus. GetRules | [SubscriptionClient. GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`– Az előfizetéshez társított összes szabály. (Csak az adattartalom leállítása) |
| Microsoft. Azure. ServiceBus. AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`– Az üzenetekben megtalálható munkamenet-azonosító. |
| Microsoft. Azure. ServiceBus. GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`– Az üzenetekben megtalálható munkamenet-azonosító.<br/>`byte [] State`-Munkamenet állapota ("stop" esemény hasznos adat) |
| Microsoft. Azure. ServiceBus. SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`– Az üzenetekben megtalálható munkamenet-azonosító.<br/>`byte [] State`-Munkamenet állapota |
| Microsoft. Azure. ServiceBus. RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`– Az üzenetekben megtalálható munkamenet-azonosító. |
| Microsoft. Azure. ServiceBus. Exception | bármilyen műszeres API| `Exception Exception`– Kivétel példánya |

Minden esetben elérheti `Activity.Current` a jelenlegi műveleti környezetet.

#### <a name="logging-additional-properties"></a>További tulajdonságok naplózása

`Activity.Current`részletes kontextust biztosít a jelenlegi művelet és a szülei számára. További információt a [tevékenység dokumentációjában](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) talál.
Service Bus a rendszerállapot-kialakítás további információkat `Activity.Current.Tags` biztosít a- `MessageId` ban `SessionId` , és ha elérhetővé válik.

A "Receive", "Peek" és "ReceiveDeferred" eseményt nyomon követő tevékenységek `RelatedTo` címkével is rendelkezhetnek. A szolgáltatás az eredményként `Diagnostic-Id`kapott üzenetek különböző listáját tartalmazza.
Ez a művelet több nem kapcsolódó üzenetet is eredményezhet. Emellett a nem `Diagnostic-Id` ismeri a művelet elindulását, így a "Receive" művelet korrelálhat a "Process" műveletekkel, csak ezt a címkét használva. A teljesítménnyel kapcsolatos problémák elemzésekor hasznos, hogy meggyőződjön arról, hogy mennyi ideig tartott az üzenet fogadása.

A címkék naplózásának hatékony módja, ha megismétli őket, így a fenti példához hasonló címkéket adhat hozzá. 

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

#### <a name="filtering-and-sampling"></a>Szűrés és mintavételezés

Bizonyos esetekben érdemes az események egy részét naplózni, hogy csökkentse a teljesítmény terhelését vagy a tárterület felhasználását. A "Leállítás" eseményeket csak (az előző példában látható módon) vagy az események mintájának százalékában állíthatja be. 
`DiagnosticSource`a `IsEnabled` predikátummal való megvalósításának módja. További információ: [környezetfüggő szűrés a DiagnosticSource-ben](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`a teljesítményre gyakorolt hatás csökkentése érdekében többször is meghívható egyetlen műveletre.

`IsEnabled`a következő sorba van meghívva:

1. `IsEnabled(<OperationName>, string entity, null)`például: `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Vegye figyelembe, hogy a végén nincs "Start" vagy "stop". Használatával kiszűrheti az adott műveleteket vagy várólistákat. Ha visszahívást ad vissza `false`, a rendszer nem küldi el az eseményeket a művelethez.

   * A "Process" és a "ProcessSession" műveletekhez visszahívást `IsEnabled(<OperationName>, string entity, Activity activity)` is kap. Használatával szűrheti az eseményeket a vagy a `activity.Id` címkék tulajdonságai alapján.
  
2. `IsEnabled(<OperationName>.Start)`például: `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Ellenőrzi, hogy a "Start" eseményt el kell-e indítani. Az eredmény csak a "Start" eseményt érinti, azonban a további rendszerállapotok nem függenek tőle.

A "Stop `IsEnabled` " esemény nem érhető el.

Ha valamilyen művelet eredménye kivétel, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` a rendszer meghívja a metódust. Csak a "kivétel" eseményekre fizethet elő, és megakadályozhatja a kiépítés további részét. Ebben az esetben továbbra is az ilyen kivételeket kell kezelnie. Mivel más rendszerállapotok le vannak tiltva, nem várható, hogy a nyomkövetési környezet a fogyasztótól a termelő felé irányuló üzenetekkel áramlik.

`IsEnabled` Emellett mintavételi stratégiákat is alkalmazhat. Mintavétel az alapján, `Activity.Id` vagy `Activity.RootId` biztosítja az egységes mintavételezést az összes gumiabroncson belül (feltéve, hogy a rendszert a rendszer vagy a saját kód nyomon követésével propagálja).

Több `DiagnosticSource` figyelő jelenléte ugyanazon a forrásnál elég ahhoz, hogy csak egy figyelő fogadja el az eseményt, ezért `IsEnabled` nem biztos, hogy meghívja,

## <a name="next-steps"></a>További lépések

* [Korreláció Application Insights](../azure-monitor/app/correlation.md)
* [Application Insights a függőségek figyelésével](../azure-monitor/app/asp-net-dependencies.md) ellenőrizheti, hogy a REST, az SQL vagy más külső erőforrások lassulnak-e.
* [Egyéni műveletek nyomon követése Application Insights .NET SDK-val](../azure-monitor/app/custom-operations-tracking.md)
