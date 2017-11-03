---
title: "Tranzakció-feldolgozási az Azure Service Bus áttekintése |} Microsoft Docs"
description: "Azure Service Bus atomi tranzakciók és a küldési keresztül áttekintése"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: dac0bf117e56c788adf46bc0647f684eccf8cf42
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="overview-of-service-bus-transaction-processing"></a>A Service Bus tranzakciófeldolgozás áttekintése
A cikk ismerteti az Azure Service Bus tranzakció képességeit. Nagy részét a vitafórum ezt a [elemi tranzakciókat és Service Bus minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Ez a cikk korlátozódik tranzakciófeldolgozás áttekintése és a *küldés* funkció a Service Bus, a helyreállítás alatt a atomi tranzakciók minta szélesebb körben és még bonyolultabbá hatókör.

## <a name="transactions-in-service-bus"></a>A Service Bus-tranzakciók
A [ *tranzakció* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) csoportosítja két vagy több műveletet együtt egy *végrehajtási hatókör*. Természetüknél ilyen tranzakció győződjön meg arról, hogy a műveletek adott csoporthoz tartozó összes művelet sikeres vagy sikertelen lehet közösen. Ebben a tekintetben tranzakciók összekötőként egyetlen egységben, amely gyakran nevezik *atomicity*. 

A Service Bus egy tranzakciós üzenet broker, és biztosítja az összes belső műveleteket az üzenettároló tranzakciós integritását. Az üzenetek belül a Service Bus-üzenetek áthelyezésével valamennyi átvitelt egy [kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md) vagy [automatikus továbbítási](service-bus-auto-forwarding.md) üzenetek entitások közötti, a tranzakciós. Ha a Service Bus fogad egy üzenetet, azt már tárolt és sorozatszáma címkével. Ettől a Service Bus belül bármely üzenet átvitelek koordinált művelet entitások közötti, sem irányítja adatvesztés (forrás sikeres és sikertelen lesz a cél), vagy ismétlődést (sikertelen és a cél sikeres) az üzenet.

A Service Bus egy tranzakció csoportosítása egyetlen üzenetküldési entitás (várólista, témakör, előfizetés) hatókörén belül műveleteket támogatja. Például több üzeneteket küldhetnek a tranzakció hatókörén belül az egy olyan sort, és az üzenetek csak lesz a várólistára naplózási véglegesíteni a tranzakció sikeres befejezéséről.

## <a name="operations-within-a-transaction-scope"></a>Műveletek a tranzakció hatókörén belül
A tranzakció hatókörén belül végrehajtható műveletek a következők:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: küldés, sendasync metódusok párhuzamosan, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: befejeződése után CompleteAsync, Szolgáltatásműveletnek, AbandonAsync, kézbesítetlen levelek, DeadletterAsync, késleltető, DeferAsync, RenewLock, RenewLockAsync 

Fogadási műveletek nem szerepelnek, mivel feltételezzük, hogy az alkalmazás olyan üzenetek szerez be a [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) mód, néhány belül az üzenetfogadási hurok vagy a egy [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) visszahívási, és csak ezután megnyitja a tranzakció hatókörén az üzenet feldolgozására.

Rendezése (teljes, szolgáltatásműveletnek, kézbesítetlen levelek, késleltető) üzenet, majd, és a függő az általános eredményét a tranzakció hatókörén belül következik be.

## <a name="transfers-and-send-via"></a>Átvitel és a "via küldési"
Ahhoz, hogy az adatok az üzenetsorból processzort, és ezután egy másik várólista tranzakciós átadási, Service Bus támogatja *átvitelek*. Átviteli művelettel, a először küldő egy üzenetet, amely egy *átviteli sorban*, és az átviteli sorban azonnal áthelyezése a rendeltetési sorból robusztus átviteli implementáció ugyanaz az üzenet, amely az automatikus továbbítás képesség támaszkodik. Az átviteli sorban napló oly módon, hogy akkor válik láthatóvá az átviteli sorban fogyasztók soha nem elkötelezett az üzenetet.

A teljesítmény, a tranzakciós funkció nyilvánvaló szükségszerűséggé vált az átviteli sorban maga esetén a feladó bemeneti üzenetet forrását. Más szóval a Service Bus vihetők át az üzenet célvárólistába "via" az átviteli sorban teljes végrehajtása során (vagy késleltető, vagy a kézbesítetlen levelek) a bemeneti üzenet egy atomi művelet az összes műveletet. 

### <a name="see-it-in-code"></a>Megtekintés a kódot
Ilyen átvitelek beállításához hozzon létre egy üzenet küldője, amelynek célpontja a célvárólista keresztül az átviteli sorban. Akkor is, amely kéri le, hogy ugyanazon várólistában lévő üzenetek fogadó. Példa:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Egy egyszerű tranzakció használja ezeket az elemeket, az alábbi példában látható módon:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Következő lépések

További információt a Service Bus-üzenetsorok a következő cikkekben talál:

* [A Service Bus-üzenetsorok használata](service-bus-dotnet-get-started-with-queues.md)
* [Automatikus-továbbítást a Service Bus-entitások láncolás](service-bus-auto-forwarding.md)
* [Automatikus továbbítás minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Elemi tranzakciókat és Service Bus-minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Az Azure várólisták és a Service Bus üzenetsorok képest](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


