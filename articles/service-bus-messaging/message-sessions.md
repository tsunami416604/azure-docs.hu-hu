---
title: Azure Service Bus üzenet-munkamenetek | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a munkamenetek a kapcsolódó üzenetek nem kötött sorrendjének közös és rendezett kezelésének engedélyezéséhez.
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
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660665"
---
# <a name="message-sessions"></a>Üzenet-munkamenetek
Microsoft Azure Service Bus-munkamenetek lehetővé teszik a kapcsolódó üzenetek nem kötött sorrendjének együttes és rendezett kezelését. A munkamenetek a-ben, az elsőben (FIFO) és a kérés-válasz mintákban is használhatók. Ez a cikk bemutatja, hogyan használhatja a munkameneteket ezen minták megvalósításához Service Bus használatakor. 

## <a name="first-in-first-out-fifo-pattern"></a>Első – első kimenő (FIFO) minta
Ha Service Bus-ben a FIFO-garanciát szeretné megvalósítani, használja a munkameneteket. Service Bus nem rendelkezik az üzenetek közötti kapcsolat természetétől, és nem határoz meg egy adott modellt, amely meghatározza, hogy a rendszer hol induljon el vagy végződik.

> [!NOTE]
> A Service Bus alapszintű csomagja nem támogatja a munkamenetek használatát. A standard és a prémium szintű csomag támogatja a munkameneteket. A szintek közötti különbségekért tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).

Bármely feladó létrehozhat egy munkamenetet, amikor üzeneteket küld egy témakörbe vagy várólistába úgy, hogy [a munkamenet-tulajdonságot](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) egy olyan alkalmazás által definiált azonosítóra állítja be, amely egyedi a munkamenetben. Az AMQP 1,0 protokoll szintjén ez az érték a *Group-ID* tulajdonságra van leképezve.

A munkamenet-kompatibilis várólistákon vagy előfizetéseken a munkamenetek akkor jönnek létre, amikor legalább egy üzenet szerepel a munkamenet [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)-azonosítójában. Ha a munkamenet már létezik, nincs definiálva idő vagy API a munkamenet lejáratának vagy eltűnésének idejére. Elméletileg egy üzenet érkezik egy adott munkamenethez, a következő üzenet egy év múlva, és ha a munkamenet-azonosító megegyezik, a **munkamenet megegyezik a** Service Bus perspektívával.

Az alkalmazások jellemzően azonban egyértelmű fogalmat mutatnak arról, hogy hol kezdődnek és végződik a kapcsolódó üzenetek halmaza. Service Bus nem állít be konkrét szabályokat.

Egy példa arra, hogy miként lehet egy fájl átadására szolgáló sorozatot megszabni az első üzenet **felirat** tulajdonságának megadásához, a köztes üzenetekhez, valamint az utolsó üzenet **befejezéséhez**. A tartalmi üzenetek relatív helyzete a **Start** Message *sorszám*származó aktuális *sorszám* -különbözetként számítható ki.

A Service Bus munkamenet-funkciója lehetővé teszi egy adott fogadási művelet használatát a C# és a Java API-k [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) formájában. A szolgáltatás engedélyezéséhez állítsa be az [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonságot a várólistán vagy az előfizetésen a Azure Resource Manageron keresztül, vagy állítsa be a jelölőt a portálon. A kapcsolódó API-műveletek használatának megkísérlése előtt szükség van rá.

A portálon állítsa be a jelzőt a következő jelölőnégyzet bejelölésével:

![][2]

> [!NOTE]
> Ha a munkamenetek engedélyezve vannak egy várólistán vagy előfizetésen, az ügyfélalkalmazások ***többé nem*** küldhetnek és fogadhatnak rendszeres üzeneteket. Az összes üzenetet el kell juttatni egy munkamenet részeként (a munkamenet-azonosító beállításával), és fogadni kell a munkamenetet.

A munkamenetek API-jai léteznek a várólista-és előfizetési ügyfeleken. Van egy kötelező modell, amely meghatározza a munkamenetek és üzenetek fogadását, valamint a *OnMessage*hasonló, a fogadási hurok kezelésének összetettségét.

### <a name="session-features"></a>Munkamenet-funkciók

A munkamenetek egyidejű, egymással párhuzamosan megjelenő üzeneteket biztosítanak a megrendelt kézbesítés megőrzése és garantálása mellett.

![][1]

A munkamenetet elfogadó ügyfél létrehoz egy [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -fogadót. Az ügyfél meghívja a [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) vagy a [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) metódust a alkalmazásban C#. A reaktív visszahívási modellben regisztrál egy munkamenet-kezelőt.

Ha a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -objektumot elfogadják, és az ügyfél tárolja, az ügyfél kizárólagos zárolást biztosít minden olyan üzenetben, amely az adott munkamenethez [tartozó munkamenet-](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) azonosítóval rendelkezik, **és amely a** munkamenet időpontjának megérkezése után is megérkezik.

A zárolás akkor jelenik meg, ha a **Bezárás** vagy a **CloseAsync** meghívása megtörténik, vagy ha a zárolás olyan esetekben jár le, amikor az alkalmazás nem tudja végrehajtani a bezárási műveletet. A munkamenet-zárolást úgy kell kezelni, mint egy fájl kizárólagos zárolását, ami azt jelenti, hogy az alkalmazásnak le kell zárnia a munkamenetet, amint már nincs rá szüksége, és/vagy nem vár semmilyen további üzenetet.

Ha több párhuzamos fogadó is lekéri a várólistáról, az adott munkamenethez tartozó üzeneteket a rendszer az adott munkamenethez tartozó zárolást betöltő adott fogadónak küldi el. Ezzel a művelettel az egyik várólistában vagy előfizetésben lévő, egymástól elválasztott üzenetek streamje tiszta módon, különböző fogadók számára érhető el, és ezek a fogadók különböző ügyfélszámítógépeken is élhetnek, mivel a zárolási felügyelet Service Buson belül történik.

Az előző ábrán három egyidejű munkamenet-fogadó látható. Egy `SessionId` = 4 azonosítójú munkamenet nem rendelkezik aktív, tulajdonosi ügyféllel, ami azt jelenti, hogy az adott munkamenetből nem érkeznek üzenetek. A munkamenetek számos módon működnek, mint például az alárendelt üzenetsor.

A munkamenet-fogadó által tárolt munkamenet-zárolás egy esernyő a *betekintési zárolási* mód által használt üzenetek zárolásához. A fogadó fél nem rendelkezhet egyszerre két üzenettel "a repülésben", de az üzeneteket sorrendben kell feldolgozni. Egy új üzenet csak akkor szerezhető be, ha az előző üzenet el lett hajtva, vagy a kézbesítve van. Az üzenet elhagyása azt eredményezi, hogy ugyanazt az üzenetet ismét a következő fogadási művelettel kézbesíti a rendszer.

### <a name="message-session-state"></a>Üzenet-munkamenet állapota

Ha a munkafolyamatokat nagy léptékű, magas rendelkezésre állású felhőalapú rendszerekben dolgozza fel, akkor az adott munkamenethez társított munkafolyamat-kezelőnek képesnek kell lennie a váratlan hibák elhárítására, és a részben befejezett munkát egy másik folyamaton vagy gépen a ahol a munka megkezdődött.

A munkamenet-állapot lehetőség lehetővé teszi egy üzenet-munkamenet alkalmazás által meghatározott megjegyzésének megadását a közvetítőn belül, így az adott munkamenethez viszonyított rögzített feldolgozási állapot azonnal elérhetővé válik, amikor egy új processzor szerzi be a munkamenetet.

Az Service Bus perspektívából az üzenet-munkamenet állapota egy átlátszatlan bináris objektum, amely egy üzenet méretének tárolására képes, amely 256 KB Service Bus standard és 1 MB a Service Bus Premium esetében. A munkamenet-állapothoz viszonyított feldolgozási állapot a munkamenet-állapotban tartható, vagy a munkamenet-állapot olyan tárolási helyre vagy adatbázis-rekordra mutathat, amely az ilyen adatokat tárolja.

A munkamenet-állapot, a [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)kezelésére szolgáló API-k a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektumban találhatók mind C# a, mind a Java API-kkal. Egy korábban nem megadott munkamenet-állapotú munkamenet **Null** hivatkozást ad vissza a **GetState**. A korábban beállított munkamenet-állapot törlése a [SetState (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)értékkel történik.

A munkamenet-állapot addig marad mindaddig, amíg nincs törölve ( **Null értékre**tér vissza), még akkor is, ha a munkamenetben lévő összes üzenetet felhasználják.

Az üzenetsor vagy előfizetés összes meglévő munkamenete a Java API **SessionBrowser** metódusával és a .net-ügyfél [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) és SubscriptionClient [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) együtt is enumerálható [](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) .

Egy várólistában vagy előfizetésben tárolt munkamenet-állapot az entitás tárolási kvótája felé mutat. Ha az alkalmazás egy munkamenettel fejeződött be, ezért ajánlott az alkalmazás számára megőrizni a megőrzött állapotot, hogy elkerülje a külső felügyeleti költségeket.

### <a name="impact-of-delivery-count"></a>A kézbesítések számának következményei

Az üzenetek kézbesítési számának a munkamenetek kontextusában való meghatározása a munkamenetek hiányában a definíciótól némileg eltér. Itt látható egy táblázat, amely összegzi a kézbesítések számának növelését.

| Forgatókönyv | Az üzenet kézbesítési száma nő |
|----------|---------------------------------------------|
| A munkamenet el van fogadva, de a munkamenet zárolása lejár (időtúllépés miatt) | Igen |
| A munkamenet elfogadása megtörtént, a munkamenetben lévő üzenetek nem lesznek végrehajtva (még akkor is, ha zárolva vannak), és a munkamenet be van zárva. | Nem |
| A munkamenet elfogadva, az üzenetek befejeződtek, majd a munkamenet explicit módon be van zárva | N/A (ez a standard folyamat. Itt az üzenetek el lesznek távolítva a munkamenetből) |

## <a name="request-response-pattern"></a>Kérelem – válasz minta
A [kérelem-válasz minta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) egy jól bevált integrációs minta, amely lehetővé teszi, hogy a küldő alkalmazás küldjön egy kérést, és módot biztosít arra, hogy a fogadó helyesen küldjön vissza választ a küldő alkalmazásnak. Ez a minta általában egy rövid élettartamú várólistára vagy témakörre van szüksége ahhoz, hogy az alkalmazás válaszokat küldjön. Ebben az esetben a munkamenetek egy egyszerű alternatív megoldást biztosítanak, amely hasonló szemantikai megoldásokkal rendelkezik. 

Több alkalmazás is elküldheti kérelmeit egyetlen kérelem-várólistába, egy adott fejléc-paraméterrel, amely egyedileg azonosítja a küldő alkalmazást. A fogadó alkalmazás feldolgozhatja a várólistára érkező kéréseket, és elküldheti a válaszokat egy munkamenetek számára engedélyezett várólistán, és beállíthatja a munkamenet-azonosítót arra a egyedi azonosítóra, amelyet a feladó küldött a kérelem üzenetében. A kérést elküldő alkalmazás egy adott munkamenet-AZONOSÍTÓban fogadhat üzeneteket, és helyesen dolgozza fel a válaszokat.

> [!NOTE]
> A kezdeti kérelmeket küldő alkalmazásnak ismernie kell a munkamenet-azonosítót, és `SessionClient.AcceptMessageSession(SessionID)` kell használnia annak a munkamenetnek a zárolásához, amelyen a válasz várható. Érdemes olyan GUID-t használni, amely egyedileg azonosítja az alkalmazás példányát munkamenet-azonosítóként. Nem lehet munkamenet-kezelő vagy `AcceptMessageSession(timeout)` a várólistán annak biztosítása érdekében, hogy a válaszok elérhetők legyenek az egyes fogadók számára a zároláshoz és a feldolgozáshoz.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Microsoft. Azure. ServiceBus Samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) vagy a [Microsoft. ServiceBus. Messaging példákat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) egy olyan példához, amely a .NET-keretrendszer ügyfelet használja a munkamenet-kompatibilis üzenetek kezelésére. 

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
