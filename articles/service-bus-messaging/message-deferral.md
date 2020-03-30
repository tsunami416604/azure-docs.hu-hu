---
title: Azure Service Bus - üzenethalasztás
description: Ez a cikk bemutatja, hogyan halasztja el az Azure Service Bus-üzenetek kézbesítését. Az üzenet az üzenetsorban vagy előfizetésben marad, de a rendszer félreteszi azt.
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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538396"
---
# <a name="message-deferral"></a>Üzenetek halasztása

Ha egy várólista- vagy előfizetés-ügyfél olyan üzenetet kap, amelyet hajlandó feldolgozni, de amelyhez az alkalmazáson belüli különleges körülmények miatt jelenleg nem lehetséges a feldolgozás, lehetősége van arra, hogy az üzenet lekérését egy későbbi pontra halassza el. Az üzenet az üzenetsorban vagy előfizetésben marad, de a rendszer félreteszi azt.

A halasztás egy kifejezetten munkafolyamat-feldolgozási forgatókönyvekhez létrehozott szolgáltatás. A munkafolyamat-keretrendszerek bizonyos műveletek feldolgozását egy adott sorrendben követelhetik meg, és előfordulhat, hogy el kell halasztaniuk egyes fogadott üzenetek feldolgozását mindaddig, amíg más üzenetek által tájékoztatott előzetes munkát el nem végeznek.

Egy egyszerű szemléltető példa egy olyan rendelésfeldolgozási folyamat, amelyben egy külső fizetési szolgáltató tól érkező fizetési értesítés jelenik meg a rendszerben, mielőtt az egyeztetési beszerzési rendelést propagálásra továbbítanák az üzlet elejéről a teljesítési rendszerre. Ebben az esetben a teljesítési rendszer elhalaszthatja a fizetési értesítés feldolgozását, amíg nincs olyan megrendelés, amelyhez hozzá lehet rendelni. A találkozási helyzetekben, ahol a különböző forrásokból származó üzenetek előreviszik a munkafolyamatot, a valós idejű végrehajtási sorrend valóban helyes lehet, de az eredményeket tükröző üzenetek sorrenden kívül eshetnek.

Végső soron a halasztás segíti az üzenetek átrendezését az érkezési rendelésből olyan rendbe, amelyben fel dolgozhatók, miközben ezeket az üzeneteket biztonságosan hagyják az üzenettárolóban, amelynek feldolgozását el kell halasztani.

## <a name="message-deferral-apis"></a>Üzenethalasztási API-k

Az API [brokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) vagy [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) a .NET Framework ügyfélben, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) a .NET Standard ügyfélben és [az IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) vagy az [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) a Java ügyfélben. 

A késleltetett üzenetek a fő várólistában maradnak az összes többi aktív üzenettel együtt (ellentétben az alvárólistában élő kézbesítetlen levelek üzeneteivel), de a továbbiakban nem fogadhatók a normál Fogadási/Fogadási függvényekkel. A késleltetett üzenetek üzenetböngészés útján fedezhetők [fel,](message-browsing.md) ha egy alkalmazás elveszíti azokat.

A késleltetett üzenet beolvasásához a tulajdonos felelős a [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) megjegyzésért, amíg az elhalasztja azt. A halasztott üzenetek sorszámát ismerő fogadók később explicit módon `Receive(sequenceNumber)`fogadhatják az üzenetet a segítségével.

Ha egy üzenet nem dolgozható fel, mert egy adott erőforrás az üzenet kezeléséhez átmenetileg nem érhető el, de az üzenetfeldolgozást nem szabad röviden felfüggeszteni, az üzenet néhány percre történő félretétele a **SequenceNumber** egy néhány perc múlva feladott [ütemezett üzenetben](message-sequencing.md) való megjegyzése, és a halasztott üzenet újbóli beolvasása az ütemezett üzenet érkezésekor. Ha egy üzenetkezelő az összes művelet adatbázisátor függ, és az adatbázis átmenetileg nem érhető el, akkor ne használjon halasztást, hanem függessze fel a fogadási üzeneteket, amíg az adatbázis újra elérhetővé nem válik.


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
