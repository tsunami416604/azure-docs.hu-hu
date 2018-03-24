---
title: Az Azure Service Bus message munkamenetek |} Microsoft Docs
description: Azure Service Bus-üzenetek munkamenetek sorozatát kezelni.
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
ms.date: 01/02/2018
ms.author: sethm
ms.openlocfilehash: 551432cd13c16fdd5423c46ed9c6f740353808f8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Üzenet-munkamenetek: az első, először ki (FIFO) 

A Microsoft Azure Service Bus-munkamenetek kapcsolódó üzenetek unbounded sorozatát közös és rendezett kezelésének engedélyezése. Vegye figyelembe a Service Bus FIFO növekvő, használja a munkameneteket. A Service Bus nincs előírásoknak megfelelő, az üzenetek közötti kapcsolat jellegének, és szintén nem definiál egy adott modell a meghatározására, ahol egy üzenetsort kezdődik vagy végződik.

A küldő hozhat létre egy munkamenetet, ha elküldése üzenetek a témakörben vagy várólista úgy, hogy a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) néhány alkalmazás által meghatározott egyedi azonosítót a munkamenethez tulajdonságot. Az AMQP 1.0 protokoll szintjén ezt az értéket rendeli az *csoportazonosító* tulajdonság.

A munkamenet-kompatibilis várólisták vagy olyan előfizetéseket, munkamenetek jönnek legalább egy üzenetet a munkamenet esetén [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Amennyiben az a munkamenet létezik, nincs meghatározott idő vagy API amikor a munkamenet lejárt, vagy eltűnik. Elméletileg, egy üzenet is fogadhatja munkamenet ma, a következő üzenet egy év időben, és ha a **munkamenet-azonosító** megegyezik, a munkamenet megegyezik a Service Bus szempontjából.

Általában azonban az alkalmazás rendelkezik egyértelmű fogalmát ahol kapcsolódó üzenetek készleteit elindul, és ezzel véget ér. A Service Bus nem állítja be a meghatározott szabályokat.

Arról, hogyan egy feladatütemezési ábrázolni a fájl átvitele is, hogy a **címke** tulajdonságot az első üzenet **start**, a köztes üzenetek **tartalom**, és az utolsó üzenet **end**. A relatív helyzetben levő tartalom üzenetek számítható ki, az aktuális üzenet *SequenceNumber* a különbözeti a **start** üzenet *SequenceNumber*.

A Service Bus lehetővé teszi, hogy egy adott fogadási művelethez, formájában munkamenet szolgáltatása [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) a C# és Java API-k. Engedélyezi a szolgáltatást úgy, hogy a [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság a várólista vagy előfizetés Azure Resource Manager használatával, illetve a jelző a portálon. Erre azért szükség, mielőtt megkísérli a kapcsolódó API-műveletek használata.

A portálon állítsa be a jelzőt mellékel a következő jelölőnégyzetet:

![][2]

Az API-kat a munkamenet létezik várólista és előfizetés-ügyfeleken. Van olyan imperatív modell, amely a munkamenetek és üzenetek fogadásakor, és egy kezelő-alapú modell hasonló *OnMessage*, Ez elrejti a fogadás kezelése összetettsége hurok.

## <a name="session-features"></a>Munkamenet-funkciók

Munkamenetek biztosítanak egyidejű deszerializálni multiplexáló kihagyásos üzenet-adatfolyamok, miközben megőrzi, és biztosítják a rendezett kézbesítést.

![][1]

A [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) fogadó hozta létre az ügyfél elfogadja a munkamenet. Az ügyfél hívások [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) vagy [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) C# nyelven íródtak. A reaktív visszahívási modell regisztrálja a munkamenet-kezelő.

Ha a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektum el van fogadva és egy ügyfél birtokában van, amíg, hogy az ügyfél rendelkezik-e az összes üzenetet, hogy munkamenet kizárólagos zárolást [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) , amely létezik a várólista vagy előfizetést, és is, amely az összes üzenetet a **SessionId** , amelyek továbbra is érkeznek, míg tartott.

A zárolás amikor **bezárása** vagy **CloseAsync** nevezzük, vagy a zárolás lejáratának azokban az esetekben, ahol az alkalmazás a Bezárás művelet nem végezhető el. A munkamenet zárolási kizárólagos zárolást a fájl, ami azt jelenti, hogy az alkalmazás zárja be a a munkamenet, amint azt már nem kell azt és/vagy a nem várt további üzenetek hasonlóan kell kezelni.

Ha több egyidejű fogadóval lekéréses az üzenetsorból, az üzenetek egy adott munkamenethez tartozó szállítják a megadott fogadó, amely jelenleg megszerezte a zárolást a munkamenethez. Ennek a műveletnek az egy kihagyásos üzenet-adatfolyam egy várólista vagy előfizetés szereplő szabályszerűen deszerializálni multiplexed különböző fogadó számára, és ezeket a fogadók élő különböző gépeken is is, a zárolási felügyeleti belül történik, kiszolgálóoldali, mert A Service Bus.

Az előző ábrán három egyidejű munkamenet fogadók. Egy munkamenetet `SessionId` = 4, ami azt jelenti, hogy nincs üzenetek jelenjenek meg az adott munkamenet nem aktív, a tulajdonos ügyfél rendelkezik. A munkamenet úgy működik, például az sokféleképpen egy sub várólistát.

A munkamenet fogadó munkamenet zárolás egy összevonó a által használt üzenet zárolásokat a *betekintés-lock* elszámolási mód. A fogadó nem lehet két üzenet egyidejűleg "útban", de az üzenetek sorrendben kell végrehajtani. Egy új üzenetet csak érhető el, amikor befejeződött vagy a kézbesítetlen lettered az előzetes üzenet. Egy üzenet okok azonos üzenet kézbesítendő kivonásának újra a Tovább gombra a fogadási művelethez.

## <a name="message-session-state"></a>Üzenet munkamenet-állapot

Amikor nagy méretű, magas rendelkezésre állású a felhőalapú rendszerek, a munkafolyamat-kezelő egy adott munkamenethez tartozó munkafolyamatok feldolgozása váratlan meghibásodások esetén helyreállíthatók és is képesek egy másik részlegesen elkészült munka folytatásához képesnek kell lennie folyamat vagy ahol a munkahelyi megkezdte a számítógép.

A munkamenet-állapot konstrukció lehetővé teszi, hogy a üzenet munkameneten belül a broker alkalmazás által meghatározott megjegyzés, hogy viszonyítva munkamenetben feljegyzett feldolgozási állapotát azonnal elérhetővé válik, ha a munkamenet egy új processzor szerzi.

A Service Bus szempontjából üzenet munkamenet állapota nem átlátszó bináris objektum, amely egy üzenet, amely a Service Bus szabványos 256 KB, és a Service Bus prémium 1 MB méretű adat tárolására képes. A munkamenet viszonyítva feldolgozási állapotát lehessen vonni a munkamenet-állapot belül, vagy bizonyos tárolási helye vagy az adatbázis-rekord, amely tárolja ezeket az információkat a munkamenet-állapot mutathat.

A munkamenet-állapot kezelésére szolgáló API-k [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), található meg a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) a C# és Java API-k objektum. A munkamenet, amelyekről korábban nem munkamenet-állapot beállítása értéket ad vissza egy **null** hivatkozást **GetState**. A korábban beállított munkamenet-állapot törlése történik a [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Vegye figyelembe, hogy a munkamenet-állapot maradnak, amíg nem törlődik (visszaadó **null**), akkor is, ha a munkamenet üzeneteit minden felhasználni.

Az összes meglévő távoli üzenetsor vagy előfizetés számba vehetők a **SessionBrowser** Java API-ban és a metódus [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) a a [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) és [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) a .NET-ügyfél.

A munkamenet-állapot tartani a sorhoz vagy az előfizetés módszert felé számolnak érintett entitásként tárolási kvótát. Ha az alkalmazás egy munkamenet-befejeződött, ezért ajánlott az alkalmazás memóriából való eltávolítása a megőrzött állapot külső felügyeleti költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

- [Teljes példa](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) küldése és munkamenet-alapú üzenetek fogadása a Service Bus várólisták a .NET-szabvány szalagtárat használ.
- [Egy minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) , amely a .NET-keretrendszer ügyfél használ a munkamenet-kompatibilis üzenetek kezeléséhez. 

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png