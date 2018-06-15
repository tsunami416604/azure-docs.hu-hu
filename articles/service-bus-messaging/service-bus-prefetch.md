---
title: Az Azure Service Bus előzetes betöltési üzenetek |} Microsoft Docs
description: Azure Service Bus üzenetek prefetching jobb teljesítmény érdekében.
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
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 0a61918108a48f4a9fa3d1c07cc8d41525f1f2a0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28928161"
---
# <a name="prefetch-azure-service-bus-messages"></a>Előzetesen lehívott lapok Azure Service Bus-üzenetek

Ha *előzetes betöltési* engedélyezve van a Service Bus hivatalos ügyfelek bármelyike, a fogadó csendben szerez be további üzeneteket, akár a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) határértéket, mi az alkalmazás kezdetben kell megadnia.

Egyetlen kezdeti [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) vagy [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) hívás ezért szerez be a közvetlen fogyasztás visszaadott leghamarabb elérhetőként üzenet. Az ügyfél további üzenetek a háttérben, töltse ki az előzetes betöltési puffer majd megszerzi.

## <a name="enable-prefetch"></a>Előzetes betöltés engedélyezése

A .NET, engedélyezi az előzetes betöltési szolgáltatást úgy, hogy a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) tulajdonsága egy **MessageReceiver**, **QueueClient**, vagy **SubscriptionClient**  csak nullánál nagyobb szám. Az érték nulla kikapcsolja az előzetes betöltési.

Egyszerűen hozzáadhatja ezt a beállítást, a fogadóoldali a [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) vagy [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) ezekben a környezetekben hatásának minták beállításait.

Amíg üzenetek érhetők el az előzetes betöltési puffer, minden további **Receive**/**ReceiveAsync** hívások azonnal teljesítik a pufferből és a pufferben lévő feltöltik a háttér, szabad terület lesz elérhető. Ha nincsenek üzenetek kézbesítésre, a fogadási művelet kiüríti a a puffer majd vár vagy blokkok várt módon.

Előzetes betöltés is működik együtt a [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) és [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API-k.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Ha gyorsabb, miért nem előzetes betöltési az alapértelmezett lehetőség?

Előzetes betöltési felgyorsítja azzal, hogy azonnal elérhetők legyenek a helyi beolvasásához üzenet, amikor előtt legalább egy kéri az alkalmazás és az üzenet-adatfolyam. Az átviteli sebesség nyereség oka egy kompromisszum, amely az alkalmazás szerzőjéhez explicit módon kell végrehajtania:

Az a [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete) fogadás módban, az előzetes betöltési puffer beszerzett összes üzenet már nem érhetők el a várólistában, és csak a memórián belüli előzetes betöltési pufferben találhatók, amíg az alkalmazás kaptak keresztül a **kap**/**ReceiveAsync** vagy **OnMessage**/**OnMessageAsync** API-k. Ha az alkalmazás leállása előtt az üzenetek fogadása az alkalmazásba, az üzenetek is végleg elvesznek.

Az a [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) fogadás módban, a lehívott az előzetes betöltési puffer az üzenetek a puffer zárolt állapotban való beszerzett, és a zárolás önkéntes időtúllépés órája rendelkezik. Ha az előzetes betöltési puffer mérete nagy, és a feldolgozásra kerül túl sokáig zárolások lejár közben az előzetes betöltési puffer, vagy az alkalmazás az üzenet feldolgozása közben is található az üzenet, előfordulhat, hogy egyes zavaró események kezeléséhez a lehet.

Az alkalmazás előfordulhat, hogy egy üzenetet, amelyben egy lejárt vagy imminently lejáró zárolást szerezni. Ha igen, az alkalmazás esetleg feldolgozni az üzenetet, de majd keresse meg, hogy azt nem befejeződését, a zárolás lejárati ideje miatt. Az alkalmazás ellenőrizheti a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) tulajdonság (amely az óra eltérésére a és a helyi számítógép óra között). Ha az üzenet zárolása lejárt, az alkalmazás kell figyelmen kívül hagyja az üzenetet; Nincs API-hívás a vagy üzenettel kell tenni. Ha az üzenet nem járt le, de lejárati közvetlen, a zárolás megújítani-e, és hogy egy másik alapértelmezett zárolási ideig meghívásával kiterjesztett [üzenet. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Ha a zárolást az előzetes betöltési pufferben csendes lejár, az üzenet elhagyott rendszer kezeli, és újra szeretné elérhetővé tenni a lekérdezés az üzenetsorból. Előfordulhat, hogy okozó úgy, hogy lehet lekérni az előzetes betöltési puffer; a végén helyezi. Ha az előzetes betöltési puffer nem általában kell keresztül az üzenet lejárati során, ez használható (érvényesen zárolt) állapotban lennie ismételten prefetched, de soha nem hatékony kézbesített üzenetek okozza, és végül áthelyezik a kézbesítetlen levelek várólistájára egyszer a maximális száma túllépve.

Ha magas szintű megbízhatóság üzenet feldolgozása van szüksége, és feldolgozási időt vesz igénybe, jelentős mennyiségű munkával jár, és az idő, előzetes betöltési szolgáltatás használata konzervatív módon, vagy egyáltalán nem ajánlott.

Ha magas teljes kell, és általában olcsó üzenet feldolgozása, a lehívott jelentős átviteli sebesség előnyeit adja eredményül.

A lehívott maximális számát és a várólista vagy az előfizetés a zárolás konfigurált kell úgy, hogy a zárolás időtúllépését legalább meghaladja a feldolgozási ideje az előzetes betöltési puffer maximális méretét az összegző várható üzenet, valamint egy üzenet lehet átgondolni. Egy időben, a zárolás időtúllépését kell kerülnie nem lehet, hogy üzenetek lépheti túl a maximális hosszú [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) Ha véletlenül megszakadnak, így igénylő a zárolás lejárati dátuma előtt éppen újra.

## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
