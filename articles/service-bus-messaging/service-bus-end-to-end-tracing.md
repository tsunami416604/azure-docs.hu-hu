---
title: "Az Azure Service Bus-végpont nyomkövetés és diagnosztikai |} Microsoft Docs"
description: "A Service Bus ügyfél-diagnosztika és végpont nyomkövetés áttekintése"
services: service-bus-messaging
documentationcenter: 
author: lmolkova
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Elosztott nyomkövetés és a Service Bus üzenetkezelés korrelációs

A gyakori problémák a mikroszolgáltatások fejlesztési egyik lehetővé teszi nyomkövetési művelet egy ügyfél a szolgáltatások, amelyek szerepet játszanak a feldolgozása. Hasznos hibakeresési információ teljesítményét elemző, A / B tesztelés és egyéb tipikus diagnosztika forgatókönyvek.
Ez a probléma egyik részét képező logikai darab munka nyomon követéséhez. Ez magában foglalja az üzenetfeldolgozás eredmény és a késleltetés és a külső függőségi hívások esetében. Egy másik korrelációs diagnosztika események folyamat határain kívül.

A gyártó egy várólista keresztül üzenetet küld, amikor ez általában akkor fordul elő hatókörébe tartozó más logikai műveletet, néhány más ügyfél vagy a szolgáltatás által kezdeményezett. Azonos művelet folytatható fogyasztó után egy üzenetet kap. A gyártó és fogyasztói (és más szolgáltatások, amely feldolgozza a műveletet), feltehetően kibocsátás telemetriai események nyomon követésére, a művelet vezérlési és az eredményt. Ahhoz, hogy az ilyen eseményeket és a nyomkövetési művelet-végpontok közötti összefüggéseket, minden egyes jelentések telemetriai szolgáltatás ki minden esemény stamp nyomkövetési környezetet.

A Microsoft Azure Service Bus üzenetkezelés létrehozói és felhasználói által használandó ilyen nyomkövetési környezet átadása hasznos tulajdonságok definiálva van.
A protokoll alapul a [HTTP korrelációs protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Tulajdonság neve        | Leírás                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnosztikai-azonosító       | A várólistára termelő egy külső hívás egyedi azonosítója. Tekintse meg [-azonosító a HTTP protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) a profilkategóriák, szempontjait és formátuma |
|  Korreláció-környezet | Az összes szolgáltatásban a művelet feldolgozása érintett propagálja művelet környezetben. További információkért lásd: [korreláció-környezet a HTTP protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Service Bus .NET ügyfél automatikus-nyomkövetés

3.0.0 verziójától kezdve [Microsoft Azure Szolgáltatásbusz-ügyfél .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) nyomkövetés instrumentation pontokat is kell akadályoznia a nyomkövetés rendszerek és kódrészletek, ügyfél biztosítja.
A rendszerállapot lehetővé teszi, hogy követési összes hívás a Service Bus messaging szolgáltatással való ügyféloldali. Ha az üzenet feldolgozása történik a [üzenet kezelő mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), üzenetfeldolgozást is van tagolva

### <a name="tracking-with-azure-application-insights"></a>Az Azure Application insights szolgáltatással nyomon követése

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) figyelési képességek, többek között a automagical kérelmet, és nyomon követését függőségi gazdag teljesítményt biztosít.

A projekt típusától függően Application Insights SDK telepítése:
- [ASP.NET](../application-insights/app-insights-asp-net.md) 2.5 beta2 verzió vagy újabb
- [Az ASP.NET Core](../application-insights/app-insights-asp-net-core.md) 2.2.0-beta2 verzió vagy újabb verzióját.
Ezek a hivatkozások részletekkel szolgálnak SDK telepítése, az erőforrások létrehozása és SDK konfigurálása (ha szükséges). Az-ASP.NET alkalmazások, tekintse meg [Azure Application Insights konzol alkalmazások](../application-insights/application-insights-console.md) cikk.

Használatakor [üzenet kezelő mintát](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) üzenetek feldolgozásához, végzett: a szolgáltatás által végzett összes Service Bus hívások automatikusan követni, és egyéb telemetriai elemek tartozzanak. Ellenkező esetben tekintse meg a következő példa manuális üzenetfeldolgozást nyomon követésére.

#### <a name="trace-message-processing"></a>Nyomkövetési üzenet feldolgozása

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

Ebben a példában `RequestTelemetry` esetén jelentést kap minden feldolgozott üzenet, a timestamp, időtartama és eredményét (sikeres). A telemetriai adatok is van korrelációs tulajdonságait.
Beágyazott nyomkövetéseket és kivételeket üzenet feldolgozása során is megjelölve őket képviselő gyermekeként"" korrelációs tulajdonságokkal a `RequestTelemetry`.

Abban az esetben, ha elvégezte a támogatott külső összetevők hívásainak üzenet feldolgozása során, azok is nyomon követheti és korrelált automagically. Tekintse meg [nyomon követheti az Application Insights .NET SDK-val egyéni műveleteket](../application-insights/application-insights-custom-operations-tracking.md) manuális nyomon követésére és megfelelési.

### <a name="tracking-without-tracing-system"></a>Nyomkövetési rendszer nélkül nyomon követése
Abban az esetben, ha a nyomkövetési rendszer nem támogatja a automagical Service Bus hívások követési keresheti az ilyen támogatásával nyomkövetés rendszerben vagy az alkalmazásba. Ez a szakasz ismerteti a Service Bus .NET-ügyfél által küldött diagnosztika események.  

Service Bus .NET ügyfél van tagolva használata a .NET-nyomkövetés primitívek [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) és [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`a nyomkövetési környezetét, miközben funkcionál `DiagnosticSource` egy értesítési mechanizmus. 

Ha nem a DiagnosticSource eseményeket figyelő, instrumentation ki van kapcsolva, nulla instrumentation költségek. A figyelő minden vezérlő DiagnosticSource biztosít:
- figyelő szabályozza, amely adatforrásokat és események figyelésére
- figyelő vezérlők események száma és a mintavételi
- a hasznos adatok között, így elérheti és Message objektumot módosítása közben az esemény teljes biztosító rendelkező események kerülnek

Ismerje meg a [DiagnosticSource felhasználói útmutató](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) végrehajtása előtt.

A naplók és a Microsoft.Extension.Logger írja az ASP.NET Core app hozzon létre egy Service Bus eseményeket figyelő.
Használja [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) könyvtár előfizetni DiagnosticSource (egyben könnyen előfizetni DiagnosticSource nélkül)

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

Ebben a példában a figyelő az időtartam, eredményt, egyedi azonosítóját és kezdő művelet mennyi időt tölt a Service Bus naplózza.

#### <a name="events"></a>Események

Minden művelethez két események kerülnek: "Start" és "Stop". Valószínűleg érdekli csak "Stop" események. Akkor adja meg a művelet eredményét, valamint egy tevékenység tulajdonságként időpontja és időtartama indítása.

Eseménytartalom figyelő, amelynek az a környezetben, a művelet biztosítja, az API bejövő paraméterek replikálja, és a visszatérési érték. "Stop" eseménytartalom tulajdonságai minden a eseménytartalom "Start", "Start" esemény teljesen figyelmen kívül.

Összes esemény is "Entity" és "Végpont" tulajdonság tartozik, hogy hiányoznak a táblázat alatti
  * `string Entity`– Név az entitás (várólista, témakör, stb.)
  * `Uri Endpoint`-A Service Bus-végpont URL-címe

Minden "Stop" esemény `Status` tulajdonság `TaskStatus` aszinkron művelet már befejeződött, amely még nincs megadva az egyszerűség érdekében az alábbi táblázatban.

A felműszerezett műveletek teljes listája itt található:

| Művelet neve | A nyomon követett API | Megadott tartalom tulajdonságait|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> üzenetek - küldött üzenetek listája |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Üzenet üzenetei – feldolgozott üzenet<br/>DateTimeOffset ScheduleEnqueueTimeUtc - ütemezett üzenet eltolása<br/>hosszú SequenceNumber - sorszáma ütemezett üzenet (eseménytartalom "Stop") |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | hosszú SequenceNumber - megszakítandó te üzenet sorozatszáma | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount - sikerült fogadható üzenetek maximális száma.<br/>IList<Message> állapotüzenetek – fogadott üzenetben ("Stop" eseménytartalom) listája |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber - a kiindulási pont, amelyből az üzenetkötegek tallózásához.<br/>int RequestedMessageCount - beolvasása üzenetek száma.<br/>IList<Message> üzenetek - (eseménytartalom "Stop") kapott üzenetek listája |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | IEnumerable<long> SequenceNumbers - fogadására a sorozatszámok tartalmazó lista.<br/>IList<Message> üzenetek - (eseménytartalom "Stop") kapott üzenetek listája |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens - fejezze be a megfelelő üzenetek a zárolás jogkivonatok tartalmazó lista.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | karakterlánc LockToken - kénytelen volt megszakítani ezt a megfelelő üzenetet a zárolási jogkivonatát. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | karakterlánc LockToken - késlelteti a megfelelő üzenetet a zárolási jogkivonatát. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | a zárolás jogkivonat a megfelelő üzenet halottlevél - a LockToken karakterlánc. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | karakterlánc LockToken - lock megújul az üzenet a zárolás jogkivonatát.<br/>Dátum és idő LockedUntilUtc - új zárolási jogkivonat lejáratának dátuma és időpontja UTC formátumban. ("Stop" eseménytartalom)|
| Microsoft.Azure.ServiceBus.Process | Üzenet kezelő lambda függvény a megadott [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Üzenet üzenetei – feldolgozott üzenet. |
| Microsoft.Azure.ServiceBus.ProcessSession | Üzenet munkamenet kezelő lambda függvény a megadott [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Üzenet üzenetei – feldolgozott üzenet.<br/>IMessageSession munkamenet - munkamenetben feldolgozott |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | RuleDescription szabály – a szabály leírása, amely a szabály hozzáadásához. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | karakterlánc RuleName - eltávolítása a szabály nevét. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | IEnumerable<RuleDescription> az előfizetéshez társított szabályok – minden szabályok. (Csak "Stop" hasznos) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | munkamenet-azonosító – a munkamenet-azonosító szerepel az üzenetek karakterlánc. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | munkamenet-azonosító – a munkamenet-azonosító szerepel az üzenetek karakterlánc.<br/>byte [] állapot - munkamenet-állapot (eseménytartalom "Stop") |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | munkamenet-azonosító – a munkamenet-azonosító szerepel az üzenetek karakterlánc.<br/>byte [] állapot - munkamenet-állapot |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | munkamenet-azonosító – a munkamenet-azonosító szerepel az üzenetek karakterlánc. |
| Microsoft.Azure.ServiceBus.Exception | bármely tagolva API| Kivétel kivétel - kivétel példány |

Minden esetben van-e hozzáférési `Activity.Current` , amely tárolja a jelenlegi műveletet a környezetben.

#### <a name="logging-additional-properties"></a>További naplózási tulajdonságait:

`Activty.Current`az aktuális művelet részletes környezet és a szülők biztosít. További információkért lásd: [tevékenység dokumentáció](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) további részleteket.
A Service Bus instrumentation nyújt a további információkat a `Activity.Current.Tags` -tartanak fenn `MessageId` és `SessionId` ha ilyenek.

Követő "Kap" tevékenységek "Betekintés" és "ReceiveDeferred" esemény is lehet `RelatedTo` címke. Tárolja a különböző listája `Diagnostic-Id`(s) eredményeként fogadott üzenetek.
Ilyen művelet azt eredményezheti, több nem kapcsolódó üzenetek fogadását. Emellett a `Diagnostic-Id` Ismeretlen művelet indításakor, a "Receive" műveletek "Folyamat" műveletekbe csak ezt a címkét összefüggő sikerült lehet. Ez akkor hasznos, ha előfordulhat, hogy ellenőrizze, hogy mennyi ideig tartott a a üzenet elemzése.

Hatékony módot kínál a címkék bejelentkezni az ismétlés őket, vagy, a címkék hozzáadását az előző példában a következőképpen néz 

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

Bizonyos esetekben kívánatos jelentkezzen az események, teljesítmény terhelés vagy tárolási felhasználás csökkentése érdekében csak egy részét. "Stop" események csak (ahogy előző példában) vagy a minta százalékos az események tudta naplózni. 
`DiagnosticSource`lehetőséget nyújtanak a elérése `IsEnabled` predikátum. További információkért lásd: [környezetfüggő szűrése a DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`Előfordulhat, hogy teljesítményre gyakorolt hatás minimalizálása érdekében egy művelettel több alkalommal hívható.

`IsEnabled`neve a következő sorrendben:

1. `IsEnabled(<OperationName>, string entity, null)`például `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Vegye figyelembe, hogy nincs "Start" vagy "Stop" végén. Adott műveletek vagy várólisták szűréséhez használja azt. Ha a visszahívási függvény `false`, a művelet eseményeket a rendszer nem küldi

  * A "Folyamat" és "ProcessSession" műveletek is kap `IsEnabled(<OperationName>, string entity, Activity activity)` visszahívás. Ezen események alapján szűrni `activity.Id` vagy tulajdonságok címkéket.
  
2. `IsEnabled(<OperationName>.Start)`például `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Ellenőrzi, hogy a "Start" esemény kell-e indítva. Az eredmény csak a "Start" esemény érinti, de további instrumentation nem függnek tőle.

Nincs nincs `IsEnabled` "Stop" esemény.

Ha néhány művelet eredménye kivétel, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` nevezzük. Sikerült csak "Kivétel" események előfizetni, és megakadályozza a instrumentation részeinek. Ebben az esetben továbbra is meg kell kezelni ilyen kivételek. Mivel más le van tiltva, nem fognak nyomkövetési környezetben készítő ügyféltől az üzeneteket a rendszer.

Használhat `IsEnabled` mintavételi stratégiák is megvalósíthatja. Mintavételi alapján a `Activity.Id` vagy `Activity.RootId` biztosítja az egységes mintavételi közötti összes gumikat (feltéve, propagálja nyomkövetési rendszer vagy a saját kódot).

A több jelenléte `DiagnosticSource` azonos a forrás-figyelőihez elég csak egy figyelőt, hogy fogadja el az esemény, így `IsEnabled` nem biztos, hogy hívható meg,

## <a name="next-steps"></a>További lépések

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Application Insights korrelációs](../application-insights/application-insights-correlation.md)
* [Application Insights figyelő függőségek](../application-insights/app-insights-asp-net-dependencies.md) megjelenítéséhez, ha a többi, SQL vagy más külső erőforrások lassítja meg.
* [Application Insights .NET SDK-val egyéni műveletek nyomon követése](../application-insights/application-insights-custom-operations-tracking.md)
