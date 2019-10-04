---
title: Kézbesítetlen levelek várólistáinak Service Bus | Microsoft Docs
description: A kézbesítetlen levelek várólistájának Azure Service Bus áttekintése
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
ms.date: 05/21/2019
ms.author: aschhab
ms.openlocfilehash: afa2e6e46579d9ce2906e2686cf40adf4b65ab2b
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516596"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A kézbesítetlen levelek várólistájának Service Bus áttekintése

Azure Service Bus a várólisták és a témakör-előfizetések egy másodlagos alvárólistát biztosítanak, amelyet *kézbesítetlen levelek várólistájának* (DLQ) nevezünk. A kézbesítetlen levelek várólistáját nem kell explicit módon létrehozni, és nem törölhető vagy más módon felügyelhető a fő entitástól függetlenül.

Ez a cikk a Service Bus kézbesítetlen levelek várólistáit ismerteti. A megbeszélések nagy részét a GitHubon a [kézbesítetlen levelek várólistájára vonatkozó példa](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) szemlélteti.
 
## <a name="the-dead-letter-queue"></a>A kézbesítetlen levelek várólistája

A kézbesítetlen levelek várólistája olyan üzenetek tárolására szolgál, amelyeket nem lehet kézbesíteni bármely fogadónak vagy nem feldolgozható üzenetnek. Az üzenetek ezután eltávolíthatók a DLQ, és megtekinthetők a vizsgálatuk. Előfordulhat, hogy egy alkalmazás az operátorral, a probléma megoldásával és az üzenet újbóli elküldésével, a hiba elhárítása és a javítási művelet elvégzésével kapcsolatos hibát jelez. 

Az API-k és a protokollok szempontjából a DLQ többnyire a többi várólistához hasonlít, kivéve, hogy az üzeneteket csak a szülő entitás kézbesítetlen levelek művelete küldheti el. Emellett a rendszer nem figyeli az élettartamot, és nem kézbesítheti az üzenetet egy DLQ. A kézbesítetlen levelek várólistája teljes mértékben támogatja a betekintés-zárolási kézbesítést és a tranzakciós műveleteket.

Vegye figyelembe, hogy a DLQ automatikus törlése nem történik meg. Az üzenetek addig maradnak a DLQ, amíg explicit módon le nem kéri őket a DLQ, és meghívja a [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) függvényt a kézbesítetlen levelek üzenetében.

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ

Számos olyan tevékenység van Service Bus, amely az üzenetek DLQ való leküldését eredményezi az üzenetküldési motoron belül. Az alkalmazások explicit módon is helyezhetnek üzeneteket a DLQ. 

Ahogy a közvetítő áthelyezi az üzenetet, két tulajdonságot adnak hozzá az üzenethez, mivel a közvetítő meghívja a [kézbesítetlen levelek](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metódus belső verzióját az üzenetre `DeadLetterReason` : `DeadLetterErrorDescription`és.

Az alkalmazások meghatározhatják a `DeadLetterReason` tulajdonsághoz tartozó saját kódokat, de a rendszer a következő értékeket állítja be.

| Állapot | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Mindig |HeaderSizeExceeded |A stream méretkvótája túl lett lépve. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing és SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Az üzenet lejárt, és a kézbesítetlenek üzenetek közé került. |
| SubscriptionDescription.RequiresSession |A munkamenet-azonosító null értékű. |A munkamenet engedélyezett entitása nem engedélyezi az olyan üzeneteket, amelyek munkamenet-azonosítója null értékű. |
| ! kézbesítetlen levelek várólistája | MaxTransferHopCountExceeded | A várólisták közötti továbbítás esetén engedélyezett ugrások maximális száma. Az érték értéke 4. |
| Alkalmazás explicit kézbesítetlen betűjele |Alkalmazás által megadott |Alkalmazás által megadott |

## <a name="exceeding-maxdeliverycount"></a>MaxDeliveryCount túllépése

A várólisták és előfizetések mindegyike [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) és [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) tulajdonsággal rendelkezik; az alapértelmezett érték 10. Ha a zárolás ([ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)) alatt egy üzenet érkezik, de explicit módon elhagyták, vagy a zárolás lejárt, akkor a [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) üzenet növekszik. Ha a [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) túllépi a [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), az üzenet átkerül a DLQ, és `MaxDeliveryCountExceeded` megadja az okkódot.

Ez a viselkedés nem tiltható le, de a [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) nagyon nagy számra lehet beállítani.

## <a name="exceeding-timetolive"></a>Exceeding TimeToLive

Ha a [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy a [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság értéke **true** (az alapértelmezett érték a **false**), az összes lejáró üzenet áthelyezve a DLQ, az `TTLExpiredException` okkód megadásával.

Vegye figyelembe, hogy a lejárt üzenetek csak akkor törlődnek, és átkerülnek a DLQ, ha van legalább egy aktív fogadó a fő sorból vagy előfizetésből. Ez a viselkedés a tervezés szerint történik.

## <a name="errors-while-processing-subscription-rules"></a>Hibák az előfizetési szabályok feldolgozásakor

Ha a [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság engedélyezve van egy előfizetéshez, az előfizetés SQL-szűrési szabályának végrehajtása során felmerülő hibák rögzítése a DLQ és a jogsértő szolgáltatással együtt történik. üzenetet.

## <a name="application-level-dead-lettering"></a>Alkalmazás szintű kézbesítetlen levelek

A rendszerszintű kézbesítetlen levelek szolgáltatáson kívül az alkalmazások a DLQ is használhatják, hogy explicit módon visszautasítsák az elfogadhatatlan üzeneteket. Ebbe beletartozhatnak azok az üzenetek, amelyek nem dolgozhatók fel megfelelően a rendszerhibák, a helytelenül formázott adattartalom tárolására szolgáló üzenetek, illetve az egyes üzenet szintű biztonsági sémák használata esetén sikertelen hitelesítéssel rendelkező üzenetek.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Továbbítás-vagy küldés-forgatókönyvekben kézbesítetlen levelek

Az üzeneteket a rendszer a következő feltételekkel küldi el a kézbesítetlen levelek várólistára történő átviteléhez:

- Egy üzenet több mint 4 várólistán vagy egymással összeláncolt [](service-bus-auto-forwarding.md)témakörön halad át.
- A célvárólista vagy a témakör le van tiltva vagy törölve van.
- A célvárólista vagy a témakör mérete meghaladja az entitások maximális méretét.

A kézbesítetlen üzenetek lekéréséhez létrehozhat egy fogadót a [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) segédprogram használatával.

## <a name="example"></a>Példa

A következő kódrészlet létrehoz egy üzenetet fogadót. A fő üzenetsor fogadási ciklusában a kód lekéri az üzenetet a [Receive (TimeSpan. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)üzenettel, amely arra kéri a közvetítőt, hogy azonnal visszaadja az összes azonnal elérhető üzenetet, vagy ha eredmény nélkül szeretne visszatérni. Ha a kód üzenetet kap, azonnal elhagyja azt, ami növeli a `DeliveryCount`következőt:. Ha a rendszer áthelyezi az üzenetet a DLQ, a fő várólista üres, és a hurok kilép, mivel [](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) a ReceiveAsync **Null**értéket ad vissza.

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

Ha a .NET SDK-t használja, a kézbesítetlen levelek várólistájának elérési útját a SubscriptionClient. FormatDeadLetterPath () metódus használatával érheti el. Ez a metódus a következő témakört veszi fel: név/előfizetés neve és utótagja a **/$DeadLetterQueue**.


## <a name="next-steps"></a>További lépések

Service Bus várólistákkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Azure Queues és a Service Bus-várólisták összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

