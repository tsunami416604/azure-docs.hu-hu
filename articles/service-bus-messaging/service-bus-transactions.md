---
title: Tranzakciófeldolgozás áttekintése az Azure Service Busban
description: Ez a cikk áttekintést nyújt a tranzakciófeldolgozásról és a küldési funkcióról az Azure Service Busban.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 22744ecbced40b3195f4d047227b1e2a37228102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260903"
---
# <a name="overview-of-service-bus-transaction-processing"></a>A Service Bus tranzakciófeldolgozásának áttekintése

Ez a cikk a Microsoft Azure Service Bus tranzakciós képességeit ismerteti. A vita nagy részét a [Service Bus-mintával rendelkező AMQP-tranzakciók illusztrálják.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) Ez a cikk a tranzakciófeldolgozás és a *send via* funkció áttekintésére korlátozódik a Service Busban, míg az Atomic transactions minta szélesebb és összetettebb hatókörű.

## <a name="transactions-in-service-bus"></a>Tranzakciók a Service Bus-ban

Egy *tranzakció* két vagy több műveletet csoportosít egy *végrehajtási hatókörbe*. Az ilyen ügyletnek természeténél fogva biztosítania kell, hogy egy adott műveletcsoporthoz tartozó valamennyi művelet sikeres legyen, vagy együttesen sikertelen legyen. E tekintetben a tranzakciók egy egységként működnek, amelyet gyakran *atomiságnak*neveznek.

A Service Bus egy tranzakciós üzenetközvetítő, és biztosítja a tranzakciós integritást az üzenettárolókkal szembeni összes belső művelethez. A Service Bus-on belüli üzenetek minden átvitele, például az üzenetek [kézbesítetlen levelek várólistába](service-bus-dead-letter-queues.md) való áthelyezése vagy az üzenetek entitások közötti [automatikus továbbítása](service-bus-auto-forwarding.md) tranzakciós. Mint ilyen, ha a Service Bus elfogadja az üzenetet, azt már tárolták, és egy sorszám címkével van ellátva. Ettől kezdve a Service Bus-on belüli üzenetátvitelek koordinált műveletek entitások között, és nem vezetnek az üzenet elvesztéséhez (a forrás sikeres és a cél sikertelen), sem az üzenet duplikálásához (a forrás sikertelen és a cél sikeres).

A Service Bus támogatja az egyetlen üzenetküldő entitásra (üzenetsor, témakör, előfizetés) irányuló csoportosítási műveleteket egy tranzakció hatáskörén belül. Például több üzenetet is küldhet egy várólistába egy tranzakcióhatókörből, és az üzenetek csak akkor lesznek véglegesítve a várólista naplójában, amikor a tranzakció sikeresen befejeződik.

## <a name="operations-within-a-transaction-scope"></a>Tranzakcióhatókörön belüli műveletek

A tranzakcióhatókörön belül végrehajtható műveletek a következők:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Küldés, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

A fogadási műveletek nem szerepelnek, mert feltételezhető, hogy az alkalmazás üzeneteket szerez be a [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) módban, néhány fogadási cikluson belül vagy egy [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) visszahívással, és csak ezután nyit meg egy tranzakcióhatókört az üzenet feldolgozásához.

Az üzenet elhelyezése (teljes, elhagyás, kézbesítés, halasztás), majd a tranzakció általános kimenetelének hatókörén belül történik, és attól függ.

## <a name="transfers-and-send-via"></a>Átutalások és "küldés keresztül"

Az adatok tranzakciós átadásának engedélyezéséhez egy várólistából egy processzornak, majd egy másik várólistának, a Service Bus támogatja az *átvitelt.* Az átviteli műveletben a feladó először üzenetet küld az *átviteli várólistának,* és az átviteli várólista azonnal áthelyezi az üzenetet a kívánt célvárólistába ugyanazzal a robusztus átviteli implementációval, amelyen az automatikus továbbítási képesség támaszkodik. Az üzenet soha nem lesz véglegesítve az átviteli várólista naplójában oly módon, hogy az láthatóvá váljon az átviteli várólista fogyasztói számára.

Ennek a tranzakciós képességnek a teljesítménye akkor válik nyilvánvalóvá, amikor maga az átviteli várólista a feladó bemeneti üzeneteinek forrása. Más szóval a Service Bus átviheti az üzenetet a célvárólistába "keresztül" az átviteli várólistába, miközben teljes (vagy elhalasztása vagy holtponti) műveletet hajt végre a bemeneti üzeneten, mindezt egy atomi műveletben. 

### <a name="see-it-in-code"></a>Lásd kódolva

Az ilyen átvitelek beállításához hozzon létre egy üzenetküldőt, amely az átviteli várólistán keresztül célozza meg a célvárólistát. Van egy fogadója is, amely ugyanabból a várólistából kéri le az üzeneteket. Példa:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Egy egyszerű tranzakció ezután ezeket az elemeket használja, mint a következő példában. A teljes példa hivatkozásához olvassa el a [Forráskódot a GitHubon:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>További lépések

A Service Bus-várólistákról az alábbi cikkekben talál további információt:

* [A Service Bus-várólisták használata](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus entitások láncolása automatikus továbbítással](service-bus-auto-forwarding.md)
* [Automatikus továbbítási minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomi tranzakciók a Service Bus mintával](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Az Azure-várólisták és a Service Bus-várólisták összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


