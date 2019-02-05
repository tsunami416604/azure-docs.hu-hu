---
title: A Service Bus kézbesíthetetlen levelek sorai |} A Microsoft Docs
description: Azure Service Bus kézbesíthetetlen levelek sorai áttekintése
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 0364304a203e03faf69868174a45cb41850ce112
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733314"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A Service Bus kézbesíthetetlen levelek sorai áttekintése

Az Azure Service Bus-üzenetsorok és üzenettémakör-előfizetéseket adjon meg egy másodlagos alárendelt üzenetsort, nevű egy *kézbesítetlen levelek várólistájára* (DLQ). A kézbesítetlen levelek várólistájára nem kell explicit módon hozhatók létre és nem törölhető és nem felügyelt, ellenkező esetben a fő entitás független.

Ez a cikk ismerteti a Service Bus kézbesíthetetlen levelek sorai. A hozzászólás számos ezt a [kézbesíthetetlen levelek üzenetsorok minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) a Githubon.
 
## <a name="the-dead-letter-queue"></a>A kézbesítetlen levelek várólistájára vonatkozik

A kézbesítetlen levelek várólistájára célja az üzeneteket, amelyek nem lehet kézbesíteni címzettnek sem, vagy üzeneteket, amelyek nem dolgozható fel. Üzenetek majd eltávolítja a DLQ és megvizsgálni. Egy alkalmazás előfordulhat, az operátornak segítségével problémák orvoslása, és küldje el újra a message, jelentkezzen a tény, hogy hiba történt, és korrekciós műveletek. 

API-t és a protokoll szempontból a DLQ csak a bármely más várólista hasonló azzal a különbséggel, hogy az üzenetek csak a kézbesíthetetlen levelek műveletet a fölérendelt entitás keresztül kell benyújtani. Time-to-live emellett nem teljesülnek, és nem lehet a kézbesíthetetlen levelek egy DLQ üzenetét. A kézbesítetlen levelek várólistájára teljes mértékben támogatja a betekintési zárolással való kézbesítést és a tranzakciós műveletek.

Vegye figyelembe, hogy nincs-e a DLQ nincs automatikus tisztítására. Üzenetek a DLQ maradnak, amíg explicit módon kérheti le azokat a DLQ és hívás [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) a kézbesíthetetlen levelek üzenethez.

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ

Nincsenek üzenetek beolvasása a DLQ belül az üzenetkezelési motor magát a leküldött okozó a Service Bus több tevékenységet. Egy alkalmazás a DLQ üzeneteket közvetlenül is helyezheti. 

Az üzenet a közvetítő által lekérdezi áthelyezték, két tulajdonságokkal is bővül, az üzenet, a közvetítő meghívja a belső verzióját a [kézbesítetlen](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metódus az üzenet: `DeadLetterReason` és `DeadLetterErrorDescription`.

Alkalmazások a saját kódokat adhat meg a `DeadLetterReason` tulajdonság, de a rendszer beállítja a következő értékeket.

| Állapot | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Mindig |HeaderSizeExceeded |Ez az adatfolyam méretének kvótája túl lett lépve. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing és SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Az üzenet lejárt, és a kapcsolat megszakadásának lettered volt. |
| SubscriptionDescription.RequiresSession |A munkamenet-azonosító null értékű. |A munkamenet engedélyezve entitás nem teszi lehetővé egy üzenet, amelynek a munkamenet-azonosító null értékű. |
| ! kézbesítetlen levelek várólistája |MaxTransferHopCountExceeded |Null |
| Explicit kapcsolat megszakadásának megnevezéséhez alkalmazás |Alkalmazás által megadott |Alkalmazás által megadott |

## <a name="exceeding-maxdeliverycount"></a>MaxDeliveryCount túllépése

Üzenetsorok és -előfizetések mindegyik rendelkezik egy [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) és [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) tulajdonság alapértelmezett értéke 10. Minden alkalommal, amikor egy üzenet kézbesítése megtörtént egy zárolás alatt ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), de lett vagy explicit módon elhagyott vagy lejárt a zárolás, az üzenet [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) van növekszik. Amikor [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) meghaladja [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), az üzenet átkerül a DLQ megadása a `MaxDeliveryCountExceeded` okkód.

Ez a viselkedés nem tiltható le, de beállíthatja [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) nagyon nagy számú.

## <a name="exceeding-timetolive"></a>Exceeding TimeToLive

Ha a [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonsága **Igaz** (az alapértelmezett érték **hamis**), minden lejáró üzenetek kerülnek a DLQ megadása a `TTLExpiredException` okkód.

Vegye figyelembe, hogy lejárt üzenetek csak üríti ki és átkerül a DLQ, ha legalább egy aktív fogadót, a fő üzenetsor vagy előfizetés; lehetőség van Ezt a viselkedést a rendszer kialakításából fakad.

## <a name="errors-while-processing-subscription-rules"></a>Hiba történt az előfizetési szabályok feldolgozása

Ha a [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság engedélyezve van egy előfizetést, az előfizetéshez tartozó SQL-szűrési szabály végrehajtása közben jelentkező hibák a DLQ mentén rögzíti a rendszer a hibás üzenettel.

## <a name="application-level-dead-lettering"></a>Alkalmazásszintű kézbesítetlen levelek kezelése

Kívül, a rendszer által biztosított kézbesítetlen levelek kezelése alkalmazások is használhatnak a DLQ kifejezetten elutasítás elfogadhatatlan üzeneteket. Ez magában foglalhatja üzeneteket, amelyek nem lehet megfelelően feldolgozni tetszőleges rendszerprobléma miatt, üzeneteket, amelyek rendelkeznek a helytelen formátumú hasznos adat található, vagy üzeneteket, amelyek bizonyos üzenet-szintű biztonsági rendszer használata esetén a hitelesítés sikertelen.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering in ForwardTo or SendVia scenarios

Üzeneteket küld az átvitel kézbesítetlen levelek várólistájára vonatkozik az alábbi feltételek:

- Több mint 4 várólisták és témakörök, amelyek áthalad egy üzenet [összeláncolt](service-bus-auto-forwarding.md).
- A cél-üzenetsor vagy témakör le van tiltva vagy törölve.
- A cél-üzenetsor vagy témakör meghaladja a maximális entitás.

Ezek az üzenetek kézbesíthetetlen levelek lettered lekéréséhez a fogadó használatával hozhat létre a [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) segédprogram metódust.

## <a name="example"></a>Példa

Az alábbi kódrészlet létrehoz egy üzenetet fogadó. A fő üzenetsor receive hurokba került a kód lekéri az üzenet [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), amely rákérdez, hogy a közvetítő azonnal vissza üzeneteié könnyen hozzáférhető, vagy nincs eredményt adja vissza. A kódot egy üzenetet kap, ha, közvetlenül felad azt, milyen lépésekben a `DeliveryCount`. Miután a rendszer áthelyezi az üzenetet a DLQ, a fő üzenetsor üres, és a hurok kilépjen-e, mint [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) adja vissza **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetsorok a következő cikkekben talál:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Azure üzenetsorok és Service Bus üzenetsorok összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

