---
title: Az Azure Service Bus-üzenetek halasztása |} A Microsoft Docs
description: Kézbesítés Service Bus-üzenetek
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 18e668249c50ffbc1020aa12455fdfb87dcb8a24
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282549"
---
# <a name="message-deferral"></a>Üzenetek halasztása

Ha egy üzenetsor vagy előfizetés ügyfél kap egy üzenet, hogy nem hajlandó feldolgozni, de esetében a feldolgozás nem pillanatnyilag lehetségesnél miatt különleges körülmények belül az alkalmazás, "elhalasztása" lekérés később üzenet lehetősége van. Az üzenet az üzenetsorban vagy előfizetésben marad, de a rendszer félreteszi azt.

Késleltetési kifejezetten létrehozott munkafolyamat-forgatókönyvek feldolgozás funkciója. A munkafolyamat-keretrendszerek szükség lehet a meghatározott sorrendben feldolgozandó bizonyos műveletek, és előfordulhat, hogy van néhány fogadott üzenetek feldolgozásához elhalasztani, amíg befejeződik, előírt előzetes munka, amely a többi üzenet arról tájékoztatja.

Egy egyszerű szemléltető példa egy feladatütemezési, amelyben fizetési megjelenik egy értesítés egy külső fizetési szolgáltatói rendszerekben mielőtt végbement volna a megfelelő beszerzési rendelés lett az áruházban a teljesítése rendszer Rendelésfeldolgozó. Ebben az esetben a teljesítése rendszer a fizetési értesítés feldolgozása, csak egy megrendelés társítandó, akkor előfordulhat, hogy késleltetése. Szinkronizálási forgatókönyvekben, ahol különböző forrásokból származó üzenetek meghajtó, a munkafolyamat előre, a valós idejű végrehajtásának sorrendje valóban megfelelő lehet, de az üzeneteket az eredményekkel tükröző sorrendben előfordulhat, hogy érkeznek.

Végső soron a késleltetési az üzenetek érkezési sorrendben átrendezése, amelyben azok feldolgozható, eközben üzeneteket biztonságosan el kell halasztani feldolgozási melyet üzenet áruházzal rendeléshez célszerű.

## <a name="message-deferral-apis"></a>Üzenetek halasztása API-k

Az API [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) vagy [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) a .NET-keretrendszer ügyfél [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) a .NET Standard ügyfél és a **mesageReceiver.defer** vagy **messageReceiver.deferSync** a Java-ügyfél. 

Késleltetett üzenet az összes aktív üzenetek (ellentétben a kézbesítetlen levelek alüzenetsor az élő) együtt a fő üzenetsor maradnak, de azok már nem fogadhatók a rendszeres Receive/ReceiveAsync funkciókkal. Késleltetett üzenet keresztül könnyen megtalálhatók legyenek [az üzenetek közötti böngészés](message-browsing.md) Ha egy alkalmazás elveszti őket nyomon.

Egy késleltetett üzenetet lekéréséhez tulajdonosától feladata megjegyzésénél a [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , azt késleltet azt. Bármely, hogy ismeri a sorszáma egy késleltetett üzenetet fogadó is később az üzenet explicit módon a `Receive(sequenceNumber)`.

Ha nem lehet feldolgozni egy üzenetet, mert az üzenet egy adott erőforrás átmenetileg nem érhető el, de üzenet feldolgozása nem summarily felfüggesztésére vonatkozó, egy üzenetet elhelyezése az ügyféloldali néhány percet módja ne felejtse el a  **SequenceNumber** a egy [ütemezett üzenet](message-sequencing.md) bizonylatok néhány perc múlva, és újra a késleltetett üzenet beolvasása, az ütemezett üzenet érkezésekor. Ha üzenetkezelőként függ, hogy a művelet egy adatbázis, és átmenetileg nem érhető el, hogy az adatbázis, azt kell nem késleltetési, hanem inkább az üzenetek fogadása felfüggesztése funkciót azonban teljesen, amíg az adatbázis újból elérhető lesz.


## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)