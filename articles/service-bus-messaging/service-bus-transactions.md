---
title: Tranzakció-feldolgozási az Azure Service Bus – áttekintés |} A Microsoft Docs
description: Azure Service Bus elemi tranzakciókat és keresztül történő küldési áttekintése
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2018
ms.author: aschhab
ms.openlocfilehash: a839a4cad824a74bde388317cf3aaddf9c5bd47f
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588754"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Tranzakciófeldolgozás a Service Bus – áttekintés

Ez a cikk a Microsoft Azure Service Bus tranzakció képességeit ismerteti. A hozzászólás számos ezt a [példa Service Bus AMQP tranzakció](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Ez a cikk áttekintést tranzakció-feldolgozás korlátozódik, és a *protokollon keresztül történő küldéshez* szolgáltatást a Service Bus, pedig elemi tranzakciókat minta nagyobb és összetettebb hatókörében.

## <a name="transactions-in-service-bus"></a>A Service Bus-tranzakciók

A *tranzakció* két vagy több művelet összevonja azokat egy *végrehajtási hatókör*. Természetéből ilyen egy tranzakció biztosítania kell, hogy a műveletek egy adott csoporthoz tartozó összes művelet sikeres vagy sikertelen lehet közösen. Ebben a tekintetben tranzakciók szerepét egy egységet, amelyek gyakran nevezik *atomitást*.

A Service Bus egy tranzakciós üzenet közvetítő, és biztosítja, hogy a belső művelet ellen annak üzenettárak tranzakciós integritását. Például az üzenetek áthelyezése a Service Bus, belül üzenetek áthelyezése egy [kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md) vagy [automatikus továbbítását](service-bus-auto-forwarding.md) , az entitások közötti üzenetek, a tranzakciós. Mint ilyen Ha a Service Bus fogad egy üzenetet, azt már tárolt és címkézte meg sorozatszámot. Ettől kezdve az minden üzenet Service Bus belül lévő átvitel megszakad koordinált műveleti entitások között, és sem eredményezi a veszteség (forrás sikeres és sikertelen lesz a cél), vagy duplikáció (sikertelen és a cél sikeres) üzenet.

A Service Bus támogatja az egyetlen üzenetküldő entitásra (üzenetsor, témakör, előfizetés) irányuló csoportosítási műveleteket egy tranzakció hatáskörén belül. Például több üzeneteket küldhet egy-egy tranzakció hatókörén belül a sort, és az üzenetek csak lesz fontos, hogy a várólista napló sikeres befejeződése után a tranzakciót.

## <a name="operations-within-a-transaction-scope"></a>Műveletek egy tranzakció hatókörén belül

A tranzakció hatókörén belül végrehajtható műveletek a következők:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Teljes körű, CompleteAsync, Szolgáltatásműveletnek, AbandonAsync, kézbesítetlen, DeadletterAsync, késleltetése, DeferAsync, RenewLock, RenewLockAsync 

Kap műveletek nem szerepelnek, mivel feltételezzük, hogy az alkalmazás beszerzi az üzenetek a [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) mód valamely kap hurok vagy egy [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) visszahívást, és csak ekkor megnyílik az adott tranzakció-hatókörben, az üzenet feldolgozására.

Rendezése (teljes, szolgáltatásműveletnek, a kézbesítetlen levelek, késleltetése) üzenet, majd, és a függő az általános eredményét a tranzakció hatókörén belül történik.

## <a name="transfers-and-send-via"></a>Adatátvitel és a "Küldés"

Ahhoz, hogy az adatok egy üzenetsorból egy processzor-, és majd egy másik várólista tranzakciós készenléti feladat átadása, Service Bus támogatja *adatátvitel*. Átviteli műveletben, a küldő először egy üzenetet küld az egy *átviteli sorban*, és az átviteli sorban azonnal áthelyezi az üzenetet a szándékolt cél várólista azonos robusztus átadás végrehajtására használatával, amely az automatikus továbbítása képesség támaszkodik. Az üzenet számára soha nem fontos, hogy az átviteli sorban log oly módon, hogy ez láthatóvá válik az átviteli sorban fogyasztók számára.

A funkció hatékonyságának szemléltetése tranzakciós nyilvánvalóvá válik, ha az átviteli sorban magát a küldő bemeneti üzenetek forrását. Más szóval a Service Bus is átvitele az üzenet célvárólistába "keresztül" az átviteli sorban teljes végrehajtása során (vagy késleltetése, vagy a kézbesíthetetlen levelek) a bemeneti üzenet egy atomi műveletnek az összes műveletet. 

### <a name="see-it-in-code"></a>Látja a code-ban

Ilyen adatátvitel beállításához hozzon létre egy üzenet küldője, amely a célvárólista keresztül az átviteli sorban célozza. Akkor is egy fogadót, amely lekéri az üzeneteket, hogy ugyanabból az üzenetsorból. Példa:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Egy egyszerű tranzakció használja ezeket az elemeket, az alábbi példában látható módon. Tekintse meg a teljes példa, tekintse meg a [forráskód a Githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

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

További információ a Service Bus-üzenetsorok a következő cikkekben talál:

* [How to use Service Bus Queues](service-bus-dotnet-get-started-with-queues.md) (A Service Bus-üzenetsorok használata)
* [Láncolás – a Service Bus-entitások automatikus továbbítással](service-bus-auto-forwarding.md)
* [Automatikus továbbítása minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Elemi tranzakciókat és Service Bus-minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Az Azure üzenetsorok és Service Bus üzenetsorok összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


