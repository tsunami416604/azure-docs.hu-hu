---
title: Az Azure Service Bus üzenet-munkamenetek |} A Microsoft Docs
description: Azure Service Bus üzenet-munkamenetek feladatütemezések kezeléséhez.
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
ms.date: 09/02/2018
ms.author: spelluru
ms.openlocfilehash: f1f796f7dc0a5ca4e1143f07303c218117e745e7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314319"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Üzenet-munkamenetek: az első, először ki (FIFO) 

A Microsoft Azure Service Bus-munkamenetek engedélyezze a közös és rendezett kezelését a kapcsolódó üzenetek korlátlan streameken működő sorrendje. Vegye figyelembe a Service Bus FIFO garancia,-munkameneteket kell használnia. A Service Bus nem előíró kapcsolatos üzenetek közötti kapcsolat jellegét, és szintén nem határoz meg egy adott modell meghatározásához, ahol egy üzenetsort kezdődik vagy végződik.

> [!NOTE]
> A Service Bus alapszintű csomagja nem támogatja a munkamenetek. A standard és prémium szintű csomag olyan munkameneteket támogat. További információkért lásd: [Service Bus díjszabásáról](service-bus-pricing-billing.md).

Bármely küldő hozhat létre egy munkamenetet, mikor küldjön üzeneteket üzenetsor vagy témakör beállításával a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) tulajdonságot néhány alkalmazás által meghatározott, a munkamenet egyedi azonosítója. Az AMQP 1.0 protokoll szintjén ezt az értéket képez a *csoportazonosító* tulajdonság.

A munkamenet-kompatibilis üzenetsoroknak vagy előfizetéseknek, munkamenetek keletkeznek, ha legalább egy üzenetet a munkamenetével [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Létezik egy munkamenetben, ha nincs megadott vagy API amikor a munkamenet lejár vagy eltűnik. Egy üzenetet egy munkamenethez, egy éves időben, a következő üzenet fogadhatók elméletileg, ha a **munkamenet-azonosító** megegyezik-e a munkamenet megegyezik a Service Bus szempontjából.

Általában azonban egy alkalmazás rendelkezik egy egyértelmű fogalmát, ahol kapcsolódó üzenetek kezdődik és ér véget. A Service Bus nem állítja be az adott szabályokat.

Példa bemutatja, hogyan egy feladatütemezési ábrázolni a fájl áttelepítése közben, hogy állítsa be a **címke** tulajdonságot az első üzenet **start**, köztes üzeneteket **tartalom**, és az utolsó üzenet **záró**. A relatív helyzetben levő tartalom üzenetek ki kell számítani az aktuális üzenetnek számít *SequenceNumber* a különbözeti a **start** üzenet *SequenceNumber*.

A Service Bus lehetővé teszi, hogy egy adott művelet kap formájában munkamenet szolgáltatása [lehívása](/dotnet/api/microsoft.servicebus.messaging.messagesession) a C# és Java API-kat. Beállításával engedélyezheti a szolgáltatást a [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság az üzenetsor vagy az előfizetés Azure Resource Manageren keresztül, vagy a portálon a jelző. Ez azért szükséges, mielőtt elkezdené használni a kapcsolódó API-műveleteket.

A portálon állítsa be a jelzőt mellékel a következő jelölőnégyzetet:

![][2]

Munkamenet során az API-k léteznek az üzenetsor és előfizetés-ügyfeleken. Az imperatív modell, amely szabályozza a munkamenetek és az üzenetek érkezésekor, és a egy kezelő-alapú modell hasonló *OnMessage*, hogy elrejti az összetett kezelését a fogadás ikonjához.

## <a name="session-features"></a>Munkamenet-funkciók

Munkamenetek biztosítanak egyidejű megszüntetéséhez multiplexálási kihagyásos üzenet-adatfolyamok, miközben fenntartja és rendezett kézbesítési garantáló.

![][1]

A [lehívása](/dotnet/api/microsoft.servicebus.messaging.messagesession) fogadó által az ügyfél elfogadja a munkamenet jön létre. Az ügyfél hívásai [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) vagy [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) C#-ban. A munkamenet-kezelő regisztrálja a reaktív jellegű visszahívási modellben.

Ha a [lehívása](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektum elfogadása és a egy ügyfél birtokában van, amíg, hogy az ügyfél rendelkezik-e az adott munkamenet összes üzenetet a kizárólagos zárolást [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) , amely létezik, az üzenetsorban vagy előfizetésben, és Emellett az összes üzenet az adott **SessionId** , amely továbbra is érkeznek, míg tartott.

Akkor szabadul fel, a zárolás amikor **bezárásához** vagy **CloseAsync** nevezzük, vagy a zárolás lejáratának azokban az esetekben, ahol az alkalmazás nem tudja, zárja be a művelet végrehajtásához. A munkamenet zárolást például kizárólagos zárolást egy fájlon, ami azt jelenti, hogy az alkalmazás zárja be a a munkamenet, amint azt már nem kell azt és/vagy nem vár semmilyen további üzenetek kell kezelni.

Ha több egyidejű fogadóval kérje le az üzenetsorból, az adott munkamenethez tartozó üzenetek elküldése a megadott fogadó, amelyhez jelenleg a zárolást az adott munkamenethez. A művelet egy kihagyásos üzenet-adatfolyam egy üzenetsorban vagy előfizetésben levő szabályszerűen megszüntetéséhez multiplexed különböző fogadónak is, és ezeket a fogadók élő különböző gépeken is is, mivel a zárolás felügyeleti Szolgáltatásoldali, belül történik. A Service Bus.

Az előző ábrán három egyidejű munkamenet fogadók. Egy munkamenetet `SessionId` = 4 már nem aktív, a tulajdonos ügyfél, ami azt jelenti, hogy nincsenek üzenetek érhetők el ebben a munkamenetben. A munkamenet sub az üzenetsorokhoz hasonlóan számos módon működik.

A munkamenet címzett munkamenet zárolás számára a által használt üzenet zárolása egy rendszerére a *betekintési zárolással való* elszámolás mód. A fogadó nem lehet két üzenet egyidejűleg "útban", de az üzenetek sorrendben kell feldolgozni. Egy új üzenetet csak szerezhető be, amikor az előző üzenetet befejeződött vagy kézbesítetlen lettered. Egy üzenet oka azonos üzenet kiszolgálása megszakítása újra a Tovább gombra a fogadási művelet.

## <a name="message-session-state"></a>Üzenet munkamenet-állapot

A munkafolyamatok nagy léptékben méretezhető, magas rendelkezésre állású felhőbeli rendszerek, a munkafolyamat-kezelő egy adott munkamenethez tartozó feldolgozásakor helyreálljon a nem várt hibák után, és is képesek egy másik a részlegesen elkészült munka folytatása képesnek kell lennie folyamat vagy gép, ahol a munka kezdete.

A munkamenet-állapot konstrukció lehetővé teszi, hogy egy üzenet munkamenet belül a közvetítő alkalmazás által meghatározott jegyzet úgy, hogy a munkamenet viszonyított rögzített feldolgozási állapotát azonnal elérhetővé válik, ha a munkamenet egy új processzor által igényelve.

A Service Bus szempontjából üzenet munkamenet állapota átlátszatlan bináris objektum, amelyet egy üzenetet, amely a standard szintű Service Bus 256 KB, és a Service Bus prémium szintű 1 MB méretű adatokat tartalmazhat. A feldolgozási állapot viszonyított munkamenet tárolhatja belüli munkamenet-állapot, vagy a munkamenet-állapot mutathat bizonyos tárolási hely vagy az ilyen adatokat tartalmazó adatbázis-rekord.

A munkamenet-állapot kezelésére szolgáló API-k [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), találhatók a [lehívása](/dotnet/api/microsoft.servicebus.messaging.messagesession) egyaránt a C# és Java API-objektumában. Egy munkamenetet, amely korábban nem munkamenet-állapot volt beállítva értéket ad vissza egy **null** hivatkozást **GetState**. Törölje a korábban beállított munkamenet-állapot végzett [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Vegye figyelembe, hogy a munkamenet-állapot marad, amíg nem törlődik (visszaadó **null**), akkor is, ha egy munkamenetben lévő üzenetek felhasznált.

Az összes meglévő munkameneteket egy üzenetsorban vagy előfizetésben számba vehetők az **SessionBrowser** módszer a Java API-ban, és a [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) a a [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) és [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) a .NET-ügyfél.

A munkamenet-állapot tartott egy üzenetsorban vagy az előfizetés felé számolnak adott entitás tárolási kvótát. Az alkalmazás-munkamenet befejezésekor ezért ajánlott az alkalmazás karbantartása a megőrzött állapot külső felügyeleti költségek elkerülése érdekében.

## <a name="next-steps"></a>További lépések

- [Egy teljes példát](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) üzenetek küldése és fogadása munkamenet-alapú a Service Bus-üzenetsorok, a .NET Standard kódtár használatával.
- [A minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) , amely a .NET-keretrendszer ügyfél használ a munkamenet-felismerésre képes üzeneteket kezel. 

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png