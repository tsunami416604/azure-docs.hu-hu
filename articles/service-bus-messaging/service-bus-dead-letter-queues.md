---
title: Service Bus kézbesítetlen levelek várólistái | Microsoft dokumentumok
description: A kézbesítetlen levelek várólistáinak ismertetése az Azure Service Busban. A Service Bus-várólisták és a témakör-előfizetések egy másodlagos alvárólistát, úgynevezett kézbesítetlen levelek várólistát biztosítanak.
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
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158901"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A Service Bus kézbesítetlen levelek várólistáinak áttekintése

Az Azure Service Bus-várólisták és a témakör-előfizetések egy másodlagos alvárólistát, úgynevezett *kézbesítetlen levelek várólistáját* (DLQ) biztosítják. A kézbesítetlen levelek várólistáját nem kell explicit módon létrehozni, és nem törölhető vagy más módon nem kezelhető a fő entitástól függetlenül.

Ez a cikk a Service Bus kézbesítetlen levelek várólistáit ismerteti. A vita nagy részét a [Kézbesítetlen levelek várólisták minta a](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) GitHubon illusztrálja.
 
## <a name="the-dead-letter-queue"></a>A kézbesítetlen levelek várólistája

A kézbesítetlen levelek várólistájának célja olyan üzenetek, amelyek nem kézbesíthetők egyetlen címzettnek sem, vagy olyan üzenetek, amelyeket nem lehetett feldolgozni. Az üzenetek ezután eltávolíthatók a DLQ-ból, és megvizsgálhatók. Előfordulhat, hogy egy alkalmazás egy operátor segítségével kijavítja a problémákat, és újra elküldi az üzenetet, naplózza a hibát, és korrekciós intézkedéseket tehet. 

Api- és protokollszempontjából a DLQ többnyire hasonló bármely más várólistához, azzal a különbséggel, hogy az üzenetek csak a szülő entitás kézbesítetlen levelek műveletén keresztül küldhetők el. Ezenkívül az élethez való időt nem figyeli meg a rendszer, és nem lehet kézbesíteni egy DLQ-tól érkező üzenetet. A kézbesítetlen levelek várólistája teljes mértékben támogatja a kukucskálás kézbesítést és a tranzakciós műveleteket.

Nincs automatikus karbantartás a DLQ-n. Az üzenetek mindaddig a DLQ-ban maradnak, amíg kifejezetten le nem olvassa őket a DLQ-ból, és meg nem hívja a [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) hívást a kézbesítési üzenetben.

## <a name="dlq-message-count"></a>DLQ-üzenetek száma
A kézbesítetlen levelek várólistájában lévő üzenetek száma nem szerezhető meg a témakör szintjén. Ennek az az oka, hogy az üzenetek csak akkor jelennek meg a témakör szintjén, ha a Service Bus belső hibát okoz. Ehelyett, amikor a feladó üzenetet küld egy témakörnek, az üzenet ezredmásodpercen belül továbbítja a témakör előfizetéseit, és így már nem a témakör szintjén található. Így láthatja az üzeneteket a témakör előfizetéséhez társított DLQ-ban. A következő példában a **Service Bus Explorer** azt mutatja, hogy jelenleg 62 üzenet van a DLQ-ban a "test1" előfizetéshez. 

![DLQ-üzenetek száma](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

A DLQ-üzenetek számát az Azure CLI paranccsal is lekaphatja: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show). 

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ-ra

A Service Bus számos olyan tevékenységet tartalmaz, amelyek miatt az üzenetek az üzenetkezelő motoron belülről kerülnek leküldéses üzenetekre. Egy alkalmazás explicit módon áthelyezheti az üzeneteket a DLQ-ba. 

Ahogy az üzenetet a bróker mozgatja, két tulajdonság kerül az üzenetbe, mivel a közvetítő `DeadLetterReason` `DeadLetterErrorDescription`meghívja a [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metódus belső verzióját az üzenetben: és .

Az alkalmazások saját kódokat `DeadLetterReason` határozhatnak meg a tulajdonsághoz, de a rendszer a következő értékeket állítja be.

| Állapot | DeadLetterReason (Kézbesítési levélok) | Halottlevélhiba-leírás |
| --- | --- | --- |
| Mindig |HeaderSizeExceeded |A stream méretkvótája túl lett lépve. |
| ! Témakörleírása.<br />EnableFilteringMessagesBeforePublishing és SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |Kivétel. GetType(). név |Kivétel. Üzenetet |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Az üzenet lejárt, és a kézbesítetlenek üzenetek közé került. |
| SubscriptionDescription.RequiresSession |A munkamenet-azonosító null értékű. |A munkamenet engedélyezett entitása nem engedélyezi az olyan üzeneteket, amelyek munkamenet-azonosítója null értékű. |
| !kézbesítetlen levelek várólistája | MaxTransferHopCountExceedtúllép | Az engedélyezett ugrások maximális száma a várólisták közötti továbbításkor. Az érték 4-re van állítva. |
| Alkalmazás explicit kézbesítési levél |Alkalmazás által megadott |Alkalmazás által megadott |

## <a name="exceeding-maxdeliverycount"></a>A maximális kézbesítési szám túllépése

A várólisták és az előfizetések mindegyike [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) és [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) tulajdonsággal rendelkezik; az alapértelmezett érték 10. Amikor egy üzenetet zárolás alatt kézbesítettek[(ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), de vagy kifejezetten elhagyták, vagy a zárolás lejárt, a [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) üzenet növekszik. Ha [a DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) meghaladja a [MaxDeliveryCount értéket,](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)az üzenet `MaxDeliveryCountExceeded` a DLQ-ba kerül, megadva az okkódot.

Ez a viselkedés nem tiltható le, de beállíthatja [A MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) nagy számra.

## <a name="exceeding-timetolive"></a>Idő túllépése

Ha a [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [a SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság **értéke igaz** (az alapértelmezett **értéke hamis),** az összes `TTLExpiredException` lejáró üzenet a DLQ-ba kerül, megadva az okkódot.

A lejárt üzenetek csak akkor törlődnek és kerülnek át a DLQ-ba, ha legalább egy aktív fogadó lehúz a fő várólistából vagy előfizetésből; hogy a viselkedés a tervezés.

## <a name="errors-while-processing-subscription-rules"></a>Hibák az előfizetési szabályok feldolgozása közben

Ha a [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság engedélyezve van egy előfizetéshez, az előfizetés SQL-szűrőszabályának végrehajtása közben előforduló hibák a DLQ-ban lesznek rögzítve a jogsértő üzenettel együtt.

## <a name="application-level-dead-lettering"></a>Alkalmazásszintű holtfelirat

A rendszer által biztosított kézbesítési jelszavak mellett az alkalmazások a DLQ segítségével kifejezetten elutasíthatják az elfogadhatatlan üzeneteket. Tartalmazhatnak olyan üzeneteket, amelyeket nem lehet megfelelően feldolgozni bármilyen rendszerprobléma miatt, hibásan formázott hasznos terhet tartalmazó üzenetek, vagy olyan üzeneteket, amelyek valamilyen üzenetszintű biztonsági séma használata esetén sikertelenek a hitelesítésben.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Kézbesítési lebetű a ForwardTo vagy a SendVia forgatókönyvekben

Az üzenetek a következő feltételek mellett kerülnek az átadási kézbesítetlen levelek várólistájába:

- Az üzenetek több mint négy várólistán vagy témakörön haladnak át, amelyek össze vannak [láncolva.](service-bus-auto-forwarding.md)
- A célvárólista vagy a témakör le van tiltva vagy törlődik.
- A célvárólista vagy -témakör meghaladja az entitás maximális méretét.

Ezek nek a kézbesítésben lévő üzeneteknek a lekéréséhez hozzon létre egy vevőt a [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) segédprogram-módszerrel.

## <a name="example"></a>Példa

A következő kódrészlet létrehoz egy üzenetfogadót. A fő várólista fogadási hurokjában a kód lekéri az üzenetet a [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)segítségével, amely arra kéri a brókert, hogy azonnal adja vissza a könnyen elérhető üzeneteket, vagy eredmény nélkül térjen vissza. Ha a kód üzenetet kap, azonnal elhagyja azt, ami a `DeliveryCount`. Miután a rendszer áthelyezi az üzenetet a DLQ-ba, a fő várólista üres, és a hurok kilép, mivel a [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) **függvény null értéket**ad vissza.

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

## <a name="path-to-the-dead-letter-queue"></a>A kézbesítetlen levelek várólistájának elérési útja
A kézbesítetlen levelek várólistáját a következő szintaxissal érheti el:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Ha a .NET SDK-t használja, a SubscriptionClient.FormatDeadLetterPath() metódus segítségével lejuthat a kézbesítetlen levelek várólistájának elérési útját. Ez a módszer a témakör nevét/előfizetésének nevét és a /$DeadLetterQueue kapcsolóval rendelkező utótagokat **veszi fel.**


## <a name="next-steps"></a>További lépések

A Service Bus-várólistákról az alábbi cikkekben talál további információt:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Azure-várólisták és a Service Bus-várólisták összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

