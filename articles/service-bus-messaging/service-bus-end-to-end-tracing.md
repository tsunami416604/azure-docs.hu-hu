---
title: Az Azure Service Bus-végpontok nyomkövetés és diagnosztika |} A Microsoft Docs
description: A Service Bus-ügyfél-diagnosztika és teljes körű nyomkövetés áttekintése
services: service-bus-messaging
documentationcenter: ''
author: lmolkova
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: lmolkova
ms.openlocfilehash: 12f9f55544f46bc9c88cab7234f78ad7ee7de2d2
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790894"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Elosztott nyomkövetést és korrelációs révén a Service Bus-üzenetkezelés

Lehetővé teszi a gyakori problémák mikroszolgáltatás fejlesztés alatt egyik, hogy nyomkövetési művelet egy ügyfél a szolgáltatások, amelyek szerepet játszanak feldolgozási keresztül. Hasznos lehet a hibakeresés, a teljesítmény elemzése, A / B tesztelés és egyéb általános diagnosztikai forgatókönyvek.
Logikai darab munka egy részét a probléma nyomon követéséhez. Üzenetfeldolgozás eredmény és a késés és külső függőségi hívások tartalmazza. Egy másik része korrelációs diagnosztikai események folyamat határokon túl.

Előállító keresztül egy üzenetsor üzenetet küld, amikor azt általában akkor fordul elő egy másik logikai olyan művelet, néhány más ügyfél vagy a szolgáltatás által kezdeményezett hatókörében. Ugyanazt a műveletet most fogyasztói után egy üzenetet kap. Mind előállítói és fogyasztói (és egyéb szolgáltatások arról, hogy a művelet folyamat), valószínűleg gridre bocsáthatja ki az telemetrikus eseményeket, hogy nyomon tudja követni a művelet a folyamat és az eredmény. Az ilyen események és a nyomkövetési művelet – teljes körű összefüggéseinek minden szolgáltatás, amely telemetriai blokkhoz minden esemény egy nyomkövetési környezettel rendelkezik.

A Microsoft Azure Service Bus-üzenetkezelés hasznos adat tulajdonságait, amely előállítók és fogyasztók csatlakoznia kell az ilyen nyomkövetési környezet átadása definiálva van.
A protokoll alapján a [korrelációs HTTP protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Tulajdonság neve        | Leírás                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnosztika-azonosító       | Az üzenetsorba a gyártó egy külső hívás egyedi azonosítója. Tekintse meg [Request-Id HTTP-protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) a közösségértékek, szempontok és formátum |
|  Korrelációs-környezet | Műveleti környezet, amely propagálja összes szolgáltatásban részt vevő művelet feldolgozása. További információkért lásd: [korrelációs-környezet a HTTP protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Service Bus .NET ügyféloldali auto-nyomkövetés

3.0.0-s verziójával kezdődően [a Microsoft Azure Szolgáltatásbusz-ügyfél .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) nyomkövetés rendszerállapot-figyelési pontjaihoz, amely a nyomkövetés rendszerek vagy ügyfél kódrészleteket is kell akadályoznia biztosít.
A rendszerállapot lehetővé teszi, hogy nyomon követése az összes híváshoz a Service Bus üzenetkezelési szolgáltatás az ügyfél oldaláról. Ha az üzenet feldolgozása történik a [üzenetet kezelő minta](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), üzenetfeldolgozás is van kialakítva.

### <a name="tracking-with-azure-application-insights"></a>Az Azure Application Insights nyomon követése

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) figyelési képességek, beleértve a automagical kérés és a függőségi nyomkövetés gazdag teljesítményt nyújt.

A projekt típusától függően az Application Insights SDK telepítése:
- [Az ASP.NET](../azure-monitor/app/asp-net.md) -telepítése beta2 2.5-ös verzió vagy újabb
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) -verzió 2.2.0-beta2 telepítése vagy újabb verziója.
Ezeket a hivatkozásokat az SDK telepítése, az erőforrások létrehozása és konfigurálása a SDK-t (ha szükséges) adja meg az adatokat. -ASP.NET alkalmazások, tekintse meg [Konzolalkalmazást az Azure Application Insights](../azure-monitor/app/console.md) cikk.

Ha [üzenetet kezelő minta](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) -üzenetek feldolgozása kész: automatikusan nyomon követi és egyéb telemetriát elemeket is vonatkozhatnak, és a szolgáltatás által végzett összes Service Bus-hívás. Ellenkező esetben tekintse meg az alábbi példa manuális üzenetfeldolgozás nyomon követésére.

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

Ebben a példában `RequestTelemetry` feldolgozott üzenetek, egy időbélyeg, időtartama és (siker) eredményt kellene jelentett. A telemetriai korreláció tulajdonságait is tartalmaz.
Beágyazott hívásláncokat és kivételeket jelentett üzenet feldolgozása közben is megjelölve őket képviselő "gyermekeként" korrelációs tulajdonságokkal a `RequestTelemetry`.

Abban az esetben támogatott külső összetevők hívásokat hajt végre üzenet feldolgozása közben, ezeket is automatikusan nyomon követi és korrelált. Tekintse meg [Application Insights .NET SDK-val egyéni műveletek követése](../azure-monitor/app/custom-operations-tracking.md) , manuális követési és összehasonlítás céljából.

### <a name="tracking-without-tracing-system"></a>Nyomkövetési rendszert nélkül nyomon követése
Abban az esetben a nyomkövetési rendszer nem támogatja az automatikus Service Bus-hívások nyomon követése előfordulhat, hogy információra van szüksége, az ilyen támogatást egy nyomkövetési rendszert vagy az alkalmazás. Ez a szakasz ismerteti a Service Bus .NET-ügyfél által küldött diagnosztikai események.  

Service Bus .NET ügyféloldali van kialakítva használata a .NET-nyomkövetés primitívek [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) és [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` egy nyomkövetési környezetét, miközben bevezetőként `DiagnosticSource` értesítési mechanizmusok. 

Ha nincs a figyelő a DiagnosticSource események, rendszerállapot ki van kapcsolva, nulla instrumentation dokumentumkezelésben. A figyelő minden vezérlő DiagnosticSource biztosít:
- figyelő szabályozza, amely adatforrásokat és események figyelésére
- figyelő vezérlők események száma és a mintavétel
- események lesznek elküldve a hasznos adat, amely teljes körű kontextust biztosít, így eléréséhez, és az esemény során üzenet objektum módosítása

Ismerje meg az [DiagnosticSource felhasználói útmutató](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) végrehajtása előtt.

Hozzunk létre egy Service Bus-eseményekre figyelő Microsoft.Extension.Logger naplók írja az ASP.NET Core-alkalmazás.
Használ [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) könyvtár előfizetni a DiagnosticSource (Ez Gyerekjáték is DiagnosticSource előfizetni, e nélkül)

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

Ebben a példában a figyelő naplózza időtartama, eredmény, egyedi azonosítója és minden egyes Service Bus-művelet kezdési idejét.

#### <a name="events"></a>Események

Minden művelet esetén két események küldhetők: "Indítás" és "Stop". Valószínűleg érdekli csak a "Stop" eseményeket. Akkor adja meg a művelet eredményét, valamint időpontja és időtartama indítható el, mert egy tevékenység tulajdonságai.

Eseménytartalom figyelő, amelynek keretein belül a művelet biztosítja, az API bejövő paraméterek replikálja, és a visszatérési érték. "Stop" eseménytartalom eseménytartalom "Indítás" tulajdonságainak rendelkezik, így a "Kezdő" esemény teljesen figyelmen kívül.

Az összes esemény is lehet "Entitás" és "Végpont" tulajdonság, azok hiányoznak az alábbi táblázat
  * `string Entity` --Nevét az entitás (üzenetsor, témakör, stb.)
  * `Uri Endpoint` – A Service Bus-végpont URL-címe

Minden egyes "Stop" esemény `Status` tulajdonság `TaskStatus` aszinkron művelet befejeződött, amely nincs megadva, az egyszerűség kedvéért az alábbi táblázatban is.

A következő finomhangolt műveletek teljes listáját:

| Művelet neve | A nyomon követett API | Konkrét tartalom tulajdonságait|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Küldött üzenetek listája |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Üzenet feldolgozása folyamatban<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` – Ütemezett üzenet eltolása<br/>`long SequenceNumber` – Ütemezett üzenet sorszáma (eseménytartalom "Stop") |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` – Te üzenet sorszáma kell lennie | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` – A kapott sikerült üzenetek maximális száma.<br/>`IList<Message> Messages` -(Eseménytartalom "Stop"). a fogadott üzenetek listája |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` – A kiindulási pont, amelyből egy üzenetköteget Tallózás.<br/>`int RequestedMessageCount` – A beolvasandó üzenetek száma.<br/>`IList<Message> Messages` -(Eseménytartalom "Stop"). a fogadott üzenetek listája |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` -A fogadása a feladatütemezési számokat tartalmazó lista.<br/>`IList<Message> Messages` -(Eseménytartalom "Stop"). a fogadott üzenetek listája |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` – A végrehajtásához a megfelelő üzenetek zárolási jogkivonatok tartalmazó lista.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` – A megfelelő a feloldandó zárolású üzenet zárolási jogkivonata. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` – A megfelelő késleltetendő üzenet zárolási jogkivonata. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` – A kézbesítetlen levelek megfelelő üzenet zárolási jogkivonata. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` – A megfelelő üzenet zárolásának megújítása zárolási jogkivonat.<br/>`DateTime LockedUntilUtc` -Új zárolási jogkivonat lejárati dátuma és ideje UTC formátumban. ("Stop" eseménytartalom)|
| Microsoft.Azure.ServiceBus.Process | A megadott üzenetet kezelő lambda függvény [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Üzenet feldolgozása folyamatban. |
| Microsoft.Azure.ServiceBus.ProcessSession | Üzenet munkamenet kezelő lambda függvény a megadott [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Üzenet feldolgozása folyamatban.<br/>`IMessageSession Session` -Munkamenet feldolgozása folyamatban |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` -A szabály leírása, amely biztosítja a szabály hozzáadásához. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` -A szabály eltávolításához nevét. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` -Az előfizetéshez tartozó összes szabályok. (Csak "Stop" tartalom) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` – A munkamenet-azonosító szerepel az üzeneteket. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` – A munkamenet-azonosító szerepel az üzeneteket.<br/>`byte [] State` -Munkamenet állapot (eseménytartalom "Stop") |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` – A munkamenet-azonosító szerepel az üzeneteket.<br/>`byte [] State` -Munkamenet állapot |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` – A munkamenet-azonosító szerepel az üzeneteket. |
| Microsoft.Azure.ServiceBus.Exception | bármelyik meghatározhatóak API| `Exception Exception` -Kivétel példány |

Minden esemény elérheti `Activity.Current` , amely tartalmazza az aktuális műveleti környezet.

#### <a name="logging-additional-properties"></a>További naplózási tulajdonságok

`Activity.Current` részletes kontextusában a jelenlegi műveletet és annak szülői biztosít. További információkért lásd: [tevékenységek dokumentációja](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) további részletekért.
A Service Bus instrumentation az további információkkal szolgál a `Activity.Current.Tags` -azok `MessageId` és `SessionId` , ha elérhetők legyenek.

"Kapnak" nyomon követő tevékenység, "Betekintési" és "ReceiveDeferred" esemény is lehet `RelatedTo` címke. Különböző listája magánál tartja `Diagnostic-Id`(s) az üzenetek eredményeként kapott.
Az ilyen művelet több nem kapcsolódó üzeneteket kapjon eredményezhet. Emellett a `Diagnostic-Id` Ismeretlen művelet indításakor, így sikerült megfeleltetendő "Receive" műveletek "Folyamat" műveletek csak ez a címke használatával. Ez akkor hasznos, ha elemzése a teljesítménnyel kapcsolatos problémák ellenőrzése, hogy mennyi ideig tartottak az üzenetet.

Címkék bejelentkezni hatékony módja az, hogy őket, így néz ki az előző példában címkézésével megismételheti 

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

#### <a name="filtering-and-sampling"></a>Szűrés és a mintavételi

Bizonyos esetekben célszerű jelentkezzen az események, teljesítmény terhelés vagy tárolási felhasználás csökkentése érdekében csak egy részét. "Stop" események csak (ahogy a fenti példában) vagy az események minta százalékos sikerült jelentkezik. 
`DiagnosticSource` Adja meg a oldható meg azt az `IsEnabled` predikátum. További információkért lásd: [környezet-alapú szűrés a DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` Előfordulhat, hogy hívható meg többször a teljesítményre gyakorolt hatás minimalizálása érdekében egyetlen műveletben.

`IsEnabled` neve a következő sorrendben:

1. `IsEnabled(<OperationName>, string entity, null)` Ha például `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Vegye figyelembe, hogy ne legyen "Indítás" vagy "Stop" végén. Használatával kiszűrhetők bizonyos műveletek, vagy várólisták. Ha visszahívást függvény `false`, események, a művelet nem kap

  * A 'Process' és "ProcessSession" műveletek is kapnak `IsEnabled(<OperationName>, string entity, Activity activity)` visszahívás. Ezzel használatával szűrhetők az események alapján `activity.Id` vagy címkék tulajdonságait.
  
2. `IsEnabled(<OperationName>.Start)` Ha például `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Ellenőrzi, hogy "Kezdő" eseményt kell fired. Az eredmény csak a "Kezdő" eseményt van hatással, de további kialakítási nem függnek tőle.

Nincs nem `IsEnabled` "Stop" esemény.

Ha néhány művelet eredményének ez alól kivétel, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` nevezzük. Ön sikerült megakadályozása a többi a rendszerállapot és előfizetni a "Kivétel" események csak. Ebben az esetben még az ilyen kivételek kezelésére. Rendszerállapotokat le van tiltva, mivel nem fognak nyomkövetési környezetét az üzenetek felhasználóknak előállítói tartalmazó folyamat.

Használhat `IsEnabled` mintavételezési stratégia is megvalósíthatja. Mintavétel alapján a `Activity.Id` vagy `Activity.RootId` biztosítja az egységes mintavétel az összes gumikat között (feltéve, propagálja nyomkövetési rendszert, vagy saját kód).

A több jelenléte `DiagnosticSource` -figyelője ugyanazt adatforrást, akkor is csak egy figyelőt, hogy fogadja el az eseményre, így `IsEnabled` nem garantált, která bude volána,

## <a name="next-steps"></a>További lépések

* [Application Insights Korelace](../azure-monitor/app/correlation.md)
* [Application Insights függőségek figyelése](../azure-monitor/app/asp-net-dependencies.md) megtekintheti, ha a REST, SQL és más külső erőforrások okoznak lassulást.
* [Application Insights .NET SDK-val egyéni műveletek követése](../azure-monitor/app/custom-operations-tracking.md)
