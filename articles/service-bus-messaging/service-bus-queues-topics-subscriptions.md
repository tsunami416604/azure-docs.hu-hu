---
title: "Az Azure Service Bus üzenetkezelés üzenetsorok, témakörök és előfizetések áttekintése |} Microsoft Docs"
description: "Service Bus üzenetküldési entitásokról áttekintése."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)

A Microsoft Azure Service Bus felhőalapú, üzenet célú köztes technológiák – például a megbízható üzenetsor és a tartós közzétételi/előfizetési üzenetküldési támogat. A "közvetítőalapú" üzenettovábbítási képességeket-re, leválasztott üzenetküldési funkcióknak, amely a támogatási közzétételi-feliratkozási, az időalapú leválasztási és a terheléselosztási forgatókönyveket a Service Bus üzenetküldési munkaterhelés használatával. A leválasztott kommunikációnak számos előnye van: az ügyfelek és a kiszolgálók például szükség szerint kapcsolódhatnak, és aszinkron módon hajthatják végre a műveleteiket.

Az üzenetküldési entitások, amely az alapvető üzenetkezelési képességeket alkotják a Service Bus üzenetsorok, témakörök és előfizetések és szabályok/művelet.

## <a name="queues"></a>Üzenetsorok

Az üzenetsorok elsőnek *First In, első kimenő* (FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Ez azt jelenti, hogy üzeneteket általában az érzékelők fogadják és dolgozza fel a hozzáadásuk sorrendjében, amelyben addig adták hozzá, a várakozási sorba, és minden üzenetet kapott, és csak egy üzenetfogyasztó által feldolgozott. Üzenetsorok használata egyik legfontosabb előnye értékre a "időbeli elválasztás" alkalmazás-összetevő. Más szóval az adatalkotóknak (küldőknek) és a fogyasztóknak (fogadóknak) nem kell küldeniük és fogadniuk az üzeneteket egy időben, mivel üzenetek tartósan tárolja őket a várólista. Továbbá a küldőnek nem kell ahhoz, hogy a feldolgozásához, és üzenetek küldése egy a fogyasztó válaszára a Várakozás.

Egy kapcsolódó előny, "Terheléskiegyenlítés," amely lehetővé teszi a gyártó és a felhasználó számára a különböző ütemben üzeneteket küldjön és fogadjon. Számos alkalmazásban a rendszerterhelés időnként; eltérő azonban az egyes Munkaegységek szükséges feldolgozási idő jellemzően állandó marad. Közé üzenetek létrehozói és felhasználói üzenetsorokat jelenti, hogy a fogyasztó alkalmazás csak úgy kell létrehozni, hogy tudja kezelni az átlagos terheléssel csúcsterhelés helyett. A bejövő terhelés változásával az üzenetsor hossza nő vagy csökken. Ez közvetlen megtakarításokkal pénz alkalmazásterhelés kiszolgálásához szükséges infrastruktúraméret mennyiségének tekintetében. Ha a terhelés növekszik, további feldolgozó folyamatok adhatók olvasni az üzenetsorból. Az egyes üzeneteket a feldolgozó folyamatoknak csak az egyike dolgozza fel. Ezenkívül a lekérésalapú terheléselosztás lehetővé teszi a munkavégző számítógépek optimális használatára akkor is, ha a munkavégző számítógépek feldolgozási teljesítmény tekintetében különböznek, azok lekéréses üzeneteket a saját maximális díj. Ezt a mintát gyakran nevezik "versengő fogyasztó" mintát.

Várólisták kiválasztásával közötti üzenetek létrehozói és felhasználói segítségével biztosítja az összetevők közötti rejlő laza kapcsoló. Mivel létrehozói és felhasználói nem kompatibilis, minden más, a fogyasztó anélkül, hogy a hatása, ha a gyártó a frissítése.

A várólista létrehozása folyamat. Felügyeleti műveletek a Service Bus üzenetküldési entitásokról (üzenetsorok és témakörök) keresztül hajtsa végre a [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) osztályt, amely úgy, hogy megadja a Service Bus-névtér és a felhasználói hitelesítő adatok alapszintű címéből épül. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) létrehozásához, enumerálásához és üzenetküldési entitások törlése metódusokat biztosít. Létrehozása után egy [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) a SAS-nevet és a kulcs és a szolgáltatási névtér felügyeleti objektum object, használhatja a [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) módszer a várólista létrehozásához. Példa:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Ezután létrehozhat várólista-objektum és egy üzenetkezelési gyárból a Service Bus URI-azonosítójú argumentumként. Példa:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Majd üzeneteket küldhetnek a várólistába. Például, ha a lista a közvetítőalapú üzenetek nevű `MessageList`, a kódot az alábbi példához hasonlóan jelenik meg:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Majd üzenetek fogadása az üzenetsorból az alábbiak szerint:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Az a [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) módban, a fogadási művelet egylépéses; Ez azt jelenti, hogy a Service Bus a kérelmet kap, ha azt az üzenetet, feldolgozottként jelöli meg, és visszaadja az alkalmazásnak. **ReceiveAndDelete** mód a legegyszerűbb modell, és működik a legjobban forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. A Service Bus jelöli meg az üzenetet, feldolgozottként, amikor az alkalmazás újraindításakor és megkezdése az üzenetek újra fel, mert ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

A [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) módban, a fogadási művelet válik kétszakaszos, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus a kérést kap, azt talál a következő feldolgozandó üzenetet, zárolja azt, hogy más fogyasztók ne fogadjon azt, és majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a fogadási folyamat második a [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) meghívásával a fogadott üzenethez. Amikor a Service Bus látja a **Complete** hívás, azt az üzenetet, feldolgozottként jelöli.

Ha az alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, akkor meghívhatja a [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) metódust a fogadott üzenethez (ahelyett, hogy [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Ez lehetővé teszi, hogy a Service Bus feloldja az üzenet zárolását, és lehetővé teszi az ugyanazon felhasználó vagy egy másik versengő felhasználó ismételt fogadását. Másodszor, nincs társítva a zárolási időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási előtt időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus feloldja az üzenet, és lehetővé teszi az újbóli fogadását (lényegében hajt végre egy [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) művelet alapértelmezés szerint).

Vegye figyelembe, hogy arra az esetre, ha az alkalmazás összeomlik az üzenet, de előtte feldolgozása után a **Complete** kérés kiadása, az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ezt gyakran nevezik *legalább egyszeri* feldolgozása; Ez azt jelenti, hogy minden üzenet legalább egyszer fel. Azonban bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újból kézbesítve. Ha ismétlődő feldolgozás, a forgatókönyvben nem lehetségesek, akkor további logikát azért van szükség az alkalmazás ismétlődések észlelése, amely elérhető alapján a **MessageId** tulajdonság az üzenet, amely több állandó marad kézbesítési kísérletek során. Ez más néven *pontosan egyszer* feldolgozása.

## <a name="topics-and-subscriptions"></a>Témakörök és előfizetések
Várólisták, amelyben minden üzenetet dolgoz fel egy-egy fogyasztó, ellentétben *témakörök* és *előfizetések* egy egy-a-többhöz típusú kommunikációt biztosítanak a egy *közzétételi/előfizetési* mintát. Hasznos címzettek nagyon nagy számú méretezését, minden egyes közzétett üzenetek szeretné elérhetővé tenni az adott témakörre regisztrált összes előfizetéshez. Üzenetek elküldenek egy témakörbe, és hogy egy vagy több kapcsolódó előfizetések, attól függően, hogy a szűrési szabályokat, amelyek egy előfizetésenként történő beállítására is beállítható. Az előfizetések további szűrőkkel korlátozza az üzeneteket, amelyeket be szeretne kapni. Üzenetek küldése történik, a témakör ugyanúgy azok küldik annak a várólistára, de nem érkezik üzenet témakörben közvetlenül. Ehelyett azok érkező előfizetések. Egy üzenettémakör-előfizetésben hasonlít egy virtuális üzenetsorra, amelyik a témakörbe küldött üzenetek példányait megkapja. Üzenetek érkező előfizetés azonos az üzenetsorból érkező felügyeletéhez.

Összehasonlításképpen az üzenet küldése funkciókat várólista közvetlenül leképezve a témakör és előfizetés üzenet-fogadás funkciókat van leképezve. Többek között ez azt jelenti, hogy előfizetések támogatja-e a várólisták tekintetében ebben a szakaszban bemutatott azonos minták: versengő felhasználó, az időalapú leválasztási, terhelés simítás és terheléselosztás.

A témakör létrehozása hasonlít egy üzenetsort szeretne létrehozni az előző szakaszban példában látható módon. A szolgáltatás URI létrehozásához, és használja a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) osztály hozhat létre a névteret. Ezután létrehozhat egy témakör segítségével a [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) metódust. Példa:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Ezután adja hozzá az előfizetés tetszés szerint:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Ezután létrehozhat egy témakör ügyfél. Példa:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Az üzenet küldője használ, akkor is üzeneteket küldjön és fogadjon irányuló és onnan a témakör az előző szakaszban ismertetett módon. Példa:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Várólisták hasonló üzenetek fogadása egy előfizetés használatával egy [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) ahelyett, hogy az objektum egy [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) objektum. Az előfizetés ügyfél, a témakör a neve, a az előfizetést, és (opcionálisan) a fogadás mód átadása paraméterként létrehozása. Például a **készlet** előfizetést:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Szabályok és műveletek
A sok esetben meghatározott jellemzőkkel rendelkező üzenetek különböző módon kell feldolgozni. Ennek engedélyezéséhez konfigurálhatja az előfizetések található üzeneteket tulajdonságok van szükség, és hajtsa végre az egyes módosítások azokat a tulajdonságokat. Miközben Service Bus-előfizetések megtekintéséhez a témakörbe küldött üzenetek csak másolhatja azokat az üzeneteket egy részét az virtuális előfizetés üzenetsorába. Mindez előfizetés-szűrők használata. Ezek a módosítások nevezzük *szűrési műveletek*. Előfizetés létrehozásakor megadhat egy kifejezést, amely az üzenet, a rendszer tulajdonságai tulajdonságai (például **címke**) és az egyéni tulajdonságok (például **storename –**.) Az SQL szűrőkifejezés nem kötelező ebben az esetben; egy SQL-kifejezést, nélkül bármely előfizetés definiált szűrőművelet az adott előfizetéshez tartozó összes üzenet érvényesül.

Az előző példát, csak az érkező üzenetek szűrésére **Store1**, akkor kell létrehoznia az irányítópult-előfizetés az alábbiak szerint:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Az előfizetés szűrővel helyen, csak olyan üzeneteket, amelyek rendelkeznek a `StoreName` tulajdonsága `Store1` kerülnek a virtuális várakozási sorban a `Dashboard` előfizetés.

További információ a lehetséges szűrőértékek dokumentációjában a [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) és [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) osztályok. További tájékoztatás a [Brokered Messaging: speciális szűrők](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) és [témakör szűrők](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) minták.

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi témakörök további információt és példákat használatával a Service Bus üzenetkezelés speciális.

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [Service Bus brokered messaging .NET tutorial](service-bus-brokered-tutorial-dotnet.md) (A Service Bus által felügyelt üzenettovábbítás .NET oktatóanyaga)
* [A Service Bus által felügyelt üzenettovábbítás REST oktatóanyaga](service-bus-brokered-tutorial-rest.md)
* [A közvetítőalapú üzenetek: Speciális szűrők minta](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

