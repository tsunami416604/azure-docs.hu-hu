---
title: Az Azure Service Bus előzetes betöltési üzenetei | Microsoft dokumentumok
description: Az Azure Service Bus-üzenetek előzetes betöltésével javíthatja a teljesítményt. Az üzenetek könnyen elérhetők a helyi lekéréshez, mielőtt az alkalmazás kéri őket.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 80717ab940d27e9bf108b3740309bcd7d71668fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760657"
---
# <a name="prefetch-azure-service-bus-messages"></a>Az Azure Service Bus-üzenetek előzetes betöltése

Ha *az előzetes betöltés* engedélyezve van a hivatalos Service Bus-ügyfelek bármelyikében, a fogadó csendesen több üzenetet szerez be, a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) korlátig, az on túl, amit az alkalmazás eredetileg kért.

Egyetlen kezdeti [fogadási](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) vagy [receiveasync-hívás](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) tehát azonnali felhasználásra vonatkozó üzenetet szerez be, amelyet a rendszer a mint elérhetővé ad vissza. Az ügyfél ezután további üzeneteket szerez be a háttérben, hogy kitöltse az előzetes betöltési puffert.

## <a name="enable-prefetch"></a>Előzetes letöltés engedélyezése

A .NET segítségével az Előzetes betöltés i szolgáltatás engedélyezéséhez a **MessageReceiver**, **QueueClient**vagy SubscriptionClient [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) tulajdonságát nullánál nagyobb számra **állítja.** Ha az értéket nullára állítja, az előzetes beolvasást kapcsolja ki.

Ezt a beállítást egyszerűen hozzáadhatja a [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) vagy a [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) minták beállításainak fogadási oldalára, hogy lássa a hatást ezekben a környezetekben.

Amíg az üzenetek elérhetők az előzetes betöltési pufferben, a későbbi/**receiveasync-hívások** azonnal teljesülnek a pufferből, és a puffer a háttérben töltődik fel, amint rendelkezésre áll a hely. **Receive** Ha nincs elérhető kézbesítési üzenet, a fogadási művelet kiüríti a puffert, majd a várt vagy blokk, ahogy az várható volt.

Az előzetes betöltés ugyanúgy működik az [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) és [onMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API-kkal is.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Ha ez gyorsabb, miért prefetch nem az alapértelmezett beállítás?

Prefetch felgyorsítja az üzenet áramlását azáltal, hogy egy üzenet könnyen elérhető a helyi lekérés, amikor és mielőtt az alkalmazás kéri az egyik. Ez az átviteli nyereség egy olyan kompromisszum eredménye, amelyet az alkalmazás szerzőjének kifejezetten meg kell tennie:

A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) fogadási móddal az előzetes betöltési pufferbe beszerzett összes üzenet már nem érhető el a várólistában, és csak a memórián belüli előzetes betöltési pufferben található, amíg a **Receive**/**ReceiveAsync** vagy az **OnMessage**/**OnMessageAsync** API-kon keresztül be nem érkeznek az alkalmazásba. Ha az alkalmazás leáll, mielőtt az üzenetek beérkeznek az alkalmazásba, ezek az üzenetek helyrehozhatatlanul elvesznek.

PeekLock [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) fogadási módban az előzetes betöltési pufferbe beolvasott üzenetek zárolt állapotban kerülnek be a pufferbe, és a zárolás idikálási órája van. Ha az előzetes betöltési puffer nagy, és a feldolgozás olyan sokáig tart, hogy az üzenetzárolások lejárnak, miközben az előzetes betöltési pufferben tartózkodik, vagy akár az üzenet feldolgozása közben is, előfordulhat, hogy az alkalmazás nak zavaró eseményeket kell kezelnie.

Előfordulhat, hogy az alkalmazás lejárt vagy hamarosan lejáró zárolással rendelkező üzenetet szerez be. Ha igen, az alkalmazás feldolgozhatja az üzenetet, de előfordulhat, hogy nem tudja befejezni a zárolás lejárata miatt. Az alkalmazás ellenőrizheti a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) tulajdonságot (amely a bróker és a helyi gép órája közötti óradöntésnek van alávetve). Ha az üzenetzárolás lejárt, az alkalmazásnak figyelmen kívül kell hagynia az üzenetet; nem api-hívás, vagy az üzenetet kell tenni. Ha az üzenet nem jár le, de a lejárat küszöbön áll, a zárolás üzenet hívásával megújítható és meghosszabbítható egy másik alapértelmezett zárolási [időszakkal. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Ha a zárolás csendben lejár az előbetöltési pufferben, az üzenet félbeszakadtként lesz kezelve, és ismét elérhetővé válik a várólistából való lekéréshez. Ez azt eredményezheti, hogy a beolvasás beolvasása az előzetes betöltési pufferbe kerül; a végén. Ha az előbetöltési puffert általában nem lehet átdolgozni az üzenet lejárata során, akkor az üzenetek ismételten újra betöltése, de soha nem lesz hatékonyan használható (érvényesen zárolt) állapotban, és végül a kézbesítetlen levelek várólistájába kerülnek, miután a maximális szállítási szám túllépése.

Ha nagyfokú megbízhatóságra van szüksége az üzenetek feldolgozásához, és a feldolgozás jelentős munkát és időt vesz igénybe, ajánlott az előzetes betöltési funkciót konzervatív módon vagy egyáltalán nem használni.

Ha nagy átviteli sebességű és üzenetfeldolgozási általában olcsó, előzetes letöltés jelentős átviteli előnyöket eredményez.

A várólista vagy előfizetésen beállított maximális előfetch-számot és zárolási időtartamot úgy kell kiegyenlíteni, hogy a zárolási időtúllépés legalább meghaladja az előre betöltési puffer maximális méretére vonatkozó összesített várható üzenetfeldolgozási időt, plusz egy üzenetet. Ugyanakkor, a zár időtúllépés kellene nem kell olyan hosszú, hogy az üzenetek meghaladhatják a maximális [TimeToLive,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) ha véletlenül leesett, így megkövetelve a zár lejár, mielőtt redelivered.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
