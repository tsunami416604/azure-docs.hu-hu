---
title: Azure Service Bus üzenetmunkamenetei | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan használhatók munkamenetek a kapcsolódó üzenetek nem kötött sorozatainak közös és rendezett kezeléséhez.
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
ms.openlocfilehash: 4df6396d156c3fe1b75e3cac3d3f4aad7f23553a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660665"
---
# <a name="message-sessions"></a>Üzenet-munkamenetek
A Microsoft Azure Service Bus-munkamenetek lehetővé teszik a kapcsolódó üzenetek nem kötött sorozatainak közös és rendezett kezelését. A munkamenetek első be, első ként (FIFO) és kérés-válasz mintákban használhatók. Ez a cikk bemutatja, hogyan használhatja a munkamenetek ezeket a mintákat a Service Bus használatakor. 

## <a name="first-in-first-out-fifo-pattern"></a>Első be, első ki (FIFO) minta
A SERVICE Bus FIFO-garanciájának megvalósításához használjon munkameneteket. A Service Bus nem előíró jellegű az üzenetek közötti kapcsolat jellegét, és nem határoz meg egy adott modellt annak meghatározására, ahol egy üzenetsorozat elindul vagy végződik.

> [!NOTE]
> A Service Bus alapszintű szintje nem támogatja a munkameneteket. A standard és a prémium szintű csomagok támogatják a munkameneteket. A szintek közötti különbségek, lásd: [Service Bus díjszabása.](https://azure.microsoft.com/pricing/details/service-bus/)

Bármely feladó létrehozhat munkamenetet, amikor üzeneteket küld egy témakörbe vagy várólistába, ha a [SessionId tulajdonságot](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) a munkamenet egyedi alkalmazásáltal definiált azonosítójára állítja be. Az AMQP 1.0 protokoll szintjén ez az érték a *csoportazonosító* tulajdonságra van leképezve.

Munkamenet-barát várólistákon vagy előfizetéseken munkamenetek akkor jönnek létre, ha legalább egy üzenet jelenik meg a munkamenet [Munkamenet-azonosítójával.](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) Ha egy munkamenet létezik, nincs meghatározott idő vagy API, amikor a munkamenet lejár, vagy eltűnik. Elméletileg egy üzenet ma, a következő üzenet egy év múlva, és ha a **SessionId** megegyezik, a munkamenet ugyanaz a Service Bus szempontból.

Általában azonban egy alkalmazás egyértelmű fogalma, ahol egy sor kapcsolódó üzenetek elindul és végződik. A Service Bus nem állít be konkrét szabályokat.

A fájlok átvitelének sorrendjét például úgy lehet meghatározni , hogy beállítja a **Címke** tulajdonságot az első **üzenet indításához**, a közbenső **üzenetekhez a tartalomba**, valamint az utolsó üzenet **befejezéséhez**. A tartalomüzenetek relatív helyzete kiszámítható a *SequenceNumber (SequenceNumber)* üzenet aktuális üzeneteként a *SequenceNumber* **kezdőüzenetből.**

A Service Bus munkamenet-szolgáltatása lehetővé tesz egy adott fogadási műveletet [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) formájában a C# és a Java API-kban. A szolgáltatás engedélyezéséhez a [requireson a requires session](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság ot a várólistán vagy az előfizetésen keresztül az Azure Resource Manager, vagy a jelző a portálon beállításával. A kapcsolódó API-műveletek használata előtt szükség van rá.

A portálon állítsa be a jelzőt a következő jelölőnégyzettel:

![][2]

> [!NOTE]
> Ha a munkamenetek engedélyezve vannak egy várólistán vagy előfizetésben, az ügyfélalkalmazások ***már nem*** tudnak normál üzeneteket küldeni/fogadni. Minden üzenetet egy munkamenet részeként kell elküldeni (a munkamenet-azonosító beállításával), és a munkamenet fogadásával kell fogadni.

A munkamenetek API-k léteznek a várólista- és előfizetés-ügyfeleken. Van egy elengedhetetlen modell, amely szabályozza, hogy mikor munkamenetek és üzenetek érkeznek, és egy kezelő-alapú modell, hasonló *OnMessage*, amely elrejti a fogadási hurok kezelésének összetettségét.

### <a name="session-features"></a>Munkamenet-funkciók

A munkamenetek az áthatárolt üzenetfolyamok egyidejű de-multiplexelését biztosítják, miközben megőrzik és garantálják a megrendelt kézbesítést.

![][1]

A [MessageSession-fogadót](/dotnet/api/microsoft.servicebus.messaging.messagesession) a munkamenetet fogadó ügyfél hoz létre. Az ügyfél [a QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) vagy [a QueueClient.AcceptMessageSessionAsync metódust](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) hívja meg C#-ban. A reaktív visszahívási modellben regisztrálja a munkamenet-kezelőt.

Ha a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektumot elfogadják, és amíg egy ügyfél birtokában van, az ügyfél kizárólagos zárolást tart a munkamenet [munkamenet-munkamenet-azonosítójával](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) rendelkező összes üzenetre, amely a várólistában vagy az előfizetésben található, valamint az adott **Munkamenet-azonosítóval** rendelkező összes üzeneten, amely a munkamenet megtartása közben is megérkezik.

A zárolás a **Close** vagy **closeAsync megírásakor,** vagy ha a zárolás lejár olyan esetekben, amikor az alkalmazás nem tudja a bezárási műveletet, felszabadul. A munkamenet-zárolást úgy kell kezelni, mint egy fájl kizárólagos zárolását, ami azt jelenti, hogy az alkalmazásnak be kell zárnia a munkamenetet, amint már nincs rá szüksége, és/vagy nem vár további üzeneteket.

Ha több egyidejű fogadó kér ki a várólistából, az adott munkamenethez tartozó üzeneteket a rendszer az adott munkamenet zárolását jelenleg tartó fogadóhoz küldi. Ezzel a művelettel egy átrendeződő üzenetfolyam egy várólistában vagy előfizetésben tisztán de-multiplexed a különböző fogadók és ezek a fogadók is élhetnek a különböző ügyfélgépeken, mivel a zárolási felügyelet történik szolgáltatás-oldali, a Service Bus belül.

Az előző ábrán három egyidejű munkamenet-fogadó látható. Egy munkamenet `SessionId` = 4 nem rendelkezik aktív, birtokló ügyfél, ami azt jelenti, hogy nem üzeneteket kézbesíteni az adott munkamenet. A munkamenet sokféleképpen működik, mint egy alvárólista.

A munkamenet-fogadó által tartott *munkamenet-zárolás a betekintési zárolási* mód által használt üzenetzárolások esernyője. A fogadó nem lehet két üzenet egyidejűleg "repülés közben", de az üzeneteket fel kell dolgozni sorrendben. Új üzenet csak akkor szerezhető be, ha a korábbi üzenet befejeződött vagy a holtüzenet. Az üzenet elhagyása miatt ugyanaz az üzenet ismét megjelenik a következő fogadási művelettel.

### <a name="message-session-state"></a>Üzenetmunkamenet állapota

Ha a munkafolyamatokat nagy méretű, magas rendelkezésre állású felhőrendszerekben dolgozzák fel, az adott munkamenethez társított munkafolyamat-kezelőnek képesnek kell lennie a váratlan hibák utáni helyreállításra, és egy másik folyamaton vagy gépen folytathatja a részlegesen befejezett munkát ahol a munka elkezdődött.

A munkamenet-állapot eszköz lehetővé teszi egy alkalmazás által definiált jegyzetelés egy üzenet munkamenet a brókeren belül, hogy a rögzített feldolgozási állapot képest, hogy a munkamenet azonnal elérhetővé válik, ha a munkamenet beszerzése egy új processzor.

A Service Bus szempontjából az üzenet munkamenet-állapot egy átlátszatlan bináris objektum, amely képes tárolni az adatokat egy üzenet mérete, amely 256 KB Service Bus Standard és 1 MB Service Bus Premium. A munkamenethez viszonyított feldolgozási állapot a munkamenet-állapoton belül tartható, vagy a munkamenet-állapot mutathat valamilyen tárolási helyre vagy adatbázisrekordra, amely ilyen információkat tárol.

A session state ( [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és [GetState)](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)kezeléséhez szükséges API-k a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektumon találhatók a C# és a Java API-kban is. Az a munkamenet,amelyiknek korábban nem volt munkamenet-állapotkészlete, **null** hivatkozást ad vissza a **GetState**számára. Az előzőleg beállított munkamenet-állapot törlése [setstate(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)értékkel történik.

A munkamenet-állapot mindaddig megmarad, amíg nincs törölve **(null értéket**ad vissza), még akkor is, ha a munkamenet összes üzenete felhasználásra kerül.

A várólistában vagy előfizetésben lévő összes munkamenet a Java API **SessionBrowser** metódusával, a .NET-ügyfél [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) és [SubscriptionClient ügyfélen](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) lévő [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) metódusával is számbavehet.

A várólistában vagy előfizetésben tárolt munkamenet-állapot beleszámít az entitás tárolási kvótájába. Ha az alkalmazás befejeződött egy munkamenettel, ezért ajánlott, hogy az alkalmazás törölje a megőrzött állapotát a külső felügyeleti költségek elkerülése érdekében.

### <a name="impact-of-delivery-count"></a>A szállítások számának hatása

A kézbesítési szám meghatározása üzenetenként a munkamenetek kontextusában kissé eltér a munkamenetek hiányában a definíciótól. Itt van egy táblázat, amely összefoglalja, ha a szállítási szám növekszik.

| Forgatókönyv | Az üzenet kézbesítési száma növekszik |
|----------|---------------------------------------------|
| A munkamenet elfogadásra kerül, de a munkamenet zárolása lejár (időout miatt) | Igen |
| A munkamenet elfogadásra kerül, a munkameneten belüli üzenetek nem fejeződnek be (még akkor sem, ha zárolva vannak), és a munkamenet le van zárva | Nem |
| A munkamenet elfogadásra kerül, az üzenetek befejeződnek, majd a munkamenet explicit módon lezárul | N / A (Ez a standard áramlás. Itt az üzenetek törlődnek a munkamenetből) |

## <a name="request-response-pattern"></a>Kérelem-válasz minta
A [kérelem-válasz minta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) egy jól megalapozott integrációs minta, amely lehetővé teszi a küldő alkalmazás számára, hogy küldjön egy kérelmet, és lehetővé teszi a fogadó számára, hogy helyesen küldjön választ a küldő alkalmazásnak. Ez a minta általában szüksége van egy rövid életű várólista vagy témakör az alkalmazás válaszokküldéséhez. Ebben a forgatókönyvben a munkamenetek egy egyszerű alternatív megoldást nyújtanak hasonló szemantikával. 

Több alkalmazás is elküldheti a kérelmeket egyetlen kérelem várólistába, egy adott fejlécparaméter rel egyedileg azonosítja a feladó alkalmazást. A fogadó alkalmazás feltudja dolgozni a várólistába érkező kérelmeket, és válaszokat küldhet egy munkamenet-kompatibilis várólistára, és beállíthatja a munkamenet-azonosítót a feladó által a kérésüzenetben küldött egyedi azonosítóra. A kérelmet küldő alkalmazás ezután üzeneteket fogadhat egy adott munkamenet-azonosítón, és helyesen feldolgozhatja a válaszokat.

> [!NOTE]
> Az alkalmazás, amely elküldi a kezdeti kérelmeket `SessionClient.AcceptMessageSession(SessionID)` tudnia kell a munkamenet-azonosítót, és használja a munkamenet zárolását, amelyre a választ vár. Célszerű olyan GUID-ot használni, amely egyedileg azonosítja az alkalmazás példányát munkamenet-azonosítóként. Nem lehet munkamenet-kezelő `AcceptMessageSession(timeout)` vagy a várólistán annak biztosítása érdekében, hogy a válaszok rendelkezésre állnak, hogy zárolhatók és feldolgozzák az adott fogadók.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [Microsoft.Azure.ServiceBus minták vagy](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) [a Microsoft.ServiceBus.Messaging minták](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) at egy példát, amely a . 

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
