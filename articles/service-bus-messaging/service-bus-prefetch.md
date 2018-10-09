---
title: Az Azure Service Bus előzetes betöltési üzenetek |} A Microsoft Docs
description: Azure Service Bus-üzenetek prefetching növelheti a teljesítményt.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: spelluru
ms.openlocfilehash: 9c88ea7433232b62c006c908cd2768d318d36d43
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854048"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus-üzenetek előzetes betöltése

Amikor *előzetes betöltési* engedélyezett bármely hivatalos Service Bus-ügyfél, a fogadó csendben szerez be további üzeneteket, akár a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) határértéket, amit az alkalmazás kezdetben kéri.

Egyetlen kezdeti [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) vagy [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) hívás ezért szerez be a visszaadott leghamarabb elérhetőként közvetlen fogyasztás üzenetet. Az ügyfél ezután szerez be további üzenetek a háttérben, töltse ki az előzetes betöltési puffer.

## <a name="enable-prefetch"></a>Előzetes lehívás engedélyezése

.NET-tel, beállításával engedélyezheti a előzetes betöltési szolgáltatást a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) tulajdonságát egy **MessageReceiver**, **QueueClient**, vagy **SubscriptionClient**  csak nullánál nagyobb szám. Az érték nulla kikapcsolja az előzetes betöltési.

Egyszerűen hozzáadhatja ezt a beállítást, a fogadóoldali a [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) vagy [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) minták beállításokat, hogy a hatás ezekben a környezetekben.

Miközben üzenetek érhetők el az előzetes betöltési puffer, minden további **Receive**/**ReceiveAsync** hívások azonnal teljesítik a buffer szolgáltatásból származó és a pufferben lévő feltöltik a háttér-, a szabad terület lesz elérhető. Ha nincsenek üzenetek a kézbesítési, a receive műveletet üríti a a puffer majd vár vagy blokkok elvárt módon.

Előzetes betöltési is ugyanúgy működik a [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) és [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API-k.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Ha gyorsabb, miért nincs előzetes betöltési az alapértelmezett beállítást?

Előzetes betöltési felgyorsítja az üzenet a folyamat mikor, és mielőtt az alkalmazás kéri egy megadásával könnyen elérhető helyi lekérésre üzenetet. Az átviteli sebesség nyereség oka egy kompromisszum, amely az alkalmazás szerzője explicit módon kell tennie:

Az a [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód kapni, összes üzenetet, a lehívott puffer beszerzett már nem érhetők el az üzenetsorban, és csak a memórián belüli előzetes betöltési puffer található mindaddig, amíg az alkalmazásba kaptak keresztül a **kap**/**ReceiveAsync** vagy **OnMessage**/**OnMessageAsync** API-k. Ha az alkalmazás leállása előtt a fogadása az alkalmazások üzeneteket elvesznek visszavonhatatlanul.

Az a [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) mód kap, üzeneteket beolvasni a lehívott puffer, a puffer zárolt állapotban való beszerzett, és rendelkezik a timeout órája a zárolás óramű pontossággal működik. A lehívott puffer mérete nagy, és a feldolgozásra kerül rövidre az üzenet zárolása közben a lehívott puffer, vagy az alkalmazás az üzenet feldolgozása közben is levő lejár, az alkalmazás kezelni bizonyos zavaró eseményeket lehet.

Az alkalmazás egy üzenetet egy lejárt vagy imminently lejáró zárolási előfordulhat, hogy beszerezni. Ha igen, az alkalmazás lehet feldolgozni az üzenetet, de keresse meg, hogy azt nem tudja végrehajtani, a zárolás lejárati miatt. Az alkalmazás ellenőrizheti a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) tulajdonság (amelynek óra torzulása a közvetítő és a helyi gép óra között). Ha az üzenet zárolási lejárt, az alkalmazás kell figyelmen kívül hagyhatja az üzenetet; egyetlen API-hívás vagy annak segítségével az üzenet kell tenni. Ha az üzenet nem járt le, de lejárati közvetlen, a zárolás megújítani-e, és hogy meghívásával egy másik alapértelmezett zárolási időszak szerint kiterjesztett [üzenet. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Ha a zárolást a lehívott pufferben csendes lejár, az üzenet félbeszakadtként való megjelölése kezeli, és ismét felhasználhatóvá válik beolvasása az üzenetsorból. Amely azt kéri le, a lehívott puffer; vezethet a végén helyezi. Ha az előzetes betöltési puffer nem általában kell keresztül során az üzenetek lejáratkor, ez használható (érvényesen zárolt) állapotban lehet ismételten prefetched, de soha nem hatékony kézbesített üzenetek, és végül helyezik át a kézbesítetlen levelek várólistájára egyszer a kézbesítések maximális száma túl van.

Üzenetfeldolgozás szüksége lesz egy magas szintű megbízhatóság, és jelentős mennyiségű munkával jár, és az idő a feldolgozásra kerül, javasoljuk, hogy konzervatív módon vegyen figyelembe vagy egyáltalán nem használja a előzetes betöltési szolgáltatását.

Ha magas egész kell, és gyakran olcsó üzenetfeldolgozást, előzetes betöltési poskytne jelentős átviteli sebesség előnyeit.

A lehívott maximális száma és a Zárolás időtartama a várólista vagy az előfizetés konfigurált kell lehet átgondolni, hogy a zárolási időtúllépést legalább meghaladja az összegző várható üzenet feldolgozási ideje az előzetes betöltési puffer maximális méretét, valamint egy üzenetet. Egy időben, a lezárás időkorlátja túl hosszú, hogy üzeneteket is haladja meg a maximális, hogy nem kell kerülnie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) véletlenül eldobásakor, amikor az így igénylő a zárolás folyamatban újbóli kézbesítése után.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
