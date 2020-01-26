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
ms.openlocfilehash: 0338663046c21adad358e8fddec12a3cc8151c79
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759329"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Üzenet-munkamenetek: először a, első kimenő (FIFO) 

Microsoft Azure Service Bus-munkamenetek lehetővé teszik a kapcsolódó üzenetek nem kötött sorrendjének együttes és rendezett kezelését. Ha Service Bus-ben a FIFO-garanciát szeretné megvalósítani, használja a munkameneteket. A Service Bus nem az üzenetek közötti kapcsolat természetével kapcsolatos, és nem határoz meg egy adott modellt, amely meghatározza, hogy egy adott üzenet hogyan induljon el vagy végződik.

> [!NOTE]
> A Service Bus alapszintű csomagja nem támogatja a munkameneteket. A standard és a prémium szintű csomag támogatja a munkameneteket. További információ: [Service Bus díjszabása](https://azure.microsoft.com/pricing/details/service-bus/).

Bármely feladó létrehozhat egy munkamenetet, amikor üzeneteket küld egy témakörbe vagy várólistába úgy, hogy [a munkamenet-tulajdonságot](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) egy olyan alkalmazás által definiált azonosítóra állítja be, amely egyedi a munkamenetben. Az AMQP 1,0 protokoll szintjén ez az érték a *Group-ID* tulajdonságra van leképezve.

A munkamenet-kompatibilis várólistákon vagy előfizetéseken a munkamenetek akkor jönnek létre, amikor legalább egy üzenet szerepel a munkamenet [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)-azonosítójában. Ha a munkamenet már létezik, nincs definiálva idő vagy API a munkamenet lejáratának vagy eltűnésének idejére. Elméletileg egy üzenet érkezik egy adott munkamenethez, a következő üzenet egy év múlva, és ha a munkamenet-azonosító megegyezik, a **munkamenet megegyezik a** Service Bus perspektívával.

Az alkalmazások jellemzően azonban egyértelmű fogalmat mutatnak arról, hogy hol kezdődnek és végződik a kapcsolódó üzenetek halmaza. Service Bus nem állít be konkrét szabályokat.

Egy példa arra, hogy miként lehet egy fájl átadására szolgáló sorozatot megszabni az első üzenet **felirat** tulajdonságának megadásához, a köztes üzenetekhez, valamint az utolsó üzenet **befejezéséhez**. A tartalmi üzenetek relatív helyzete a **Start** Message *sorszám*származó aktuális *sorszám* -különbözetként számítható ki.

A Service Bus munkamenet-funkciója lehetővé teszi egy adott fogadási művelet használatát a C# és a Java API-k [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) formájában. A szolgáltatás engedélyezéséhez állítsa be az [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonságot a várólistán vagy az előfizetésen a Azure Resource Manageron keresztül, vagy állítsa be a jelölőt a portálon. Erre azért van szükség, mielőtt megkísérli használni a kapcsolódó API-műveleteket.

A portálon állítsa be a jelzőt a következő jelölőnégyzet bejelölésével:

![][2]

> [!NOTE]
> Ha a munkamenetek engedélyezve vannak egy várólistán vagy előfizetésen, az ügyfélalkalmazások ***többé nem*** küldhetnek és fogadhatnak rendszeres üzeneteket. Az összes üzenetet el kell juttatni egy munkamenet részeként (a munkamenet-azonosító beállításával), és fogadni kell a munkamenetet.

A munkamenetek API-jai léteznek a várólista-és előfizetési ügyfeleken. Elengedhetetlen modell, amely a munkamenetek és az üzenetek fogadását, valamint a *OnMessage*hasonló, a fogadási hurok kezelésének bonyolultságát eltakaró kezelő-alapú modellt vezérli.

## <a name="session-features"></a>Munkamenet-funkciók

A munkamenetek egyidejű, egymással párhuzamosan megjelenő üzeneteket biztosítanak a megrendelt kézbesítés megőrzése és garantálása mellett.

![][1]

A munkamenetet elfogadó ügyfél létrehoz egy [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -fogadót. Az ügyfél meghívja a [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) vagy a [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) metódust a alkalmazásban C#. A reaktív visszahívási modellben regisztrál egy munkamenet-kezelőt.

Ha a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -objektumot elfogadják, és egy ügyfél tárolja, az ügyfél kizárólagos zárolást biztosít az adott munkamenethez [tartozó,](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) a várólistán vagy az előfizetésen belüli összes üzenetre vonatkozóan, valamint az azon munkamenet-azonosítóval rendelkező összes üzeneten is **, amely még** mindig megérkezik a munkamenet megtartása közben.

A zárolás akkor jelenik meg, ha a **Bezárás** vagy a **CloseAsync** meghívása megtörténik, vagy ha a zárolás olyan esetekben jár le, amikor az alkalmazás nem tudja végrehajtani a bezárási műveletet. A munkamenet-zárolást úgy kell kezelni, mint egy fájl kizárólagos zárolását, ami azt jelenti, hogy az alkalmazásnak azonnal le kell zárnia a munkamenetet, ha már nincs rá szüksége, és/vagy nem vár semmilyen további üzenetet.

Ha több párhuzamos fogadó is lekéri a várólistáról, az adott munkamenethez tartozó üzeneteket a rendszer az adott munkamenethez tartozó zárolást betöltő adott fogadónak küldi el. Ezzel a művelettel egy várólistában vagy előfizetésben található, egymással összekapcsolt üzenetküldési adatfolyam tiszta módon, különböző fogadók számára érhető el, és ezek a fogadók különböző ügyfélszámítógépeken is élhetnek, mivel a zárolás kezelése a szolgáltatás mellett történik Service Bus.

Az előző ábrán három egyidejű munkamenet-fogadó látható. Egy `SessionId` = 4 azonosítójú munkamenet nem rendelkezik aktív, tulajdonosi ügyféllel, ami azt jelenti, hogy az adott munkamenetből nem érkeznek üzenetek. A munkamenetek számos módon működnek, mint például az alárendelt üzenetsor.

A munkamenet-fogadó által tárolt munkamenet-zárolás egy esernyő a *betekintési zárolási* mód által használt üzenetek zárolásához. A fogadónak nem lehet egyszerre két üzenete "a repülésben", de az üzeneteket sorrendben kell feldolgozni. Egy új üzenet csak akkor szerezhető be, ha az előző üzenet el lett hajtva, vagy a kézbesítve van. Az üzenet elhagyása azt eredményezi, hogy ugyanazt az üzenetet ismét a következő fogadási művelettel kézbesíti a rendszer.

## <a name="message-session-state"></a>Üzenet-munkamenet állapota

Ha a munkafolyamatokat nagy léptékű, magas rendelkezésre állású felhőalapú rendszerekben dolgozzák fel, akkor az adott munkamenethez társított munkafolyamat-kezelőnek képesnek kell lennie a váratlan hibák elhárítására, és képesnek kell lennie arra, hogy a részben befejezett munkákat más a folyamat vagy a gép, ahol a munka elindult.

A munkamenet-állapot lehetőség lehetővé teszi egy üzenet-munkamenet alkalmazás által meghatározott megjegyzésének megadását a közvetítőn belül, így az adott munkamenethez viszonyított rögzített feldolgozási állapot azonnal elérhetővé válik, amikor egy új processzor szerzi be a munkamenetet.

Az Service Bus perspektívából az üzenet-munkamenet állapota egy átlátszatlan bináris objektum, amely egy üzenet méretének tárolására képes, amely 256 KB Service Bus standard és 1 MB a Service Bus Premium esetében. A munkamenet-állapothoz viszonyított feldolgozási állapot a munkamenet-állapotban tartható, vagy a munkamenet-állapot olyan tárolási helyre vagy adatbázis-rekordra mutathat, amely az ilyen adatokat tárolja.

A munkamenet-állapot, a [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)kezelésére szolgáló API-k a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektumban találhatók mind C# a, mind a Java API-kkal. Egy korábban nem megadott munkamenet-állapotú munkamenet **Null** hivatkozást ad vissza a **GetState**. A korábban beállított munkamenet-állapot törlése a [SetState (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)értékkel történik.

Vegye figyelembe, hogy a munkamenet-állapot addig marad, amíg nem törlődik (a **Null érték**visszaadása), még akkor is, ha a munkamenetben lévő összes üzenet használatban van.

Az üzenetsor vagy előfizetés összes meglévő munkamenete a Java API **SessionBrowser** metódusával és a .net-ügyfél [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) és SubscriptionClient [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) együtt is enumerálható [](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) .

Egy várólistában vagy előfizetésben tárolt munkamenet-állapot az entitás tárolási kvótája felé mutat. Ha az alkalmazás egy munkamenettel fejeződött be, ezért ajánlott az alkalmazás számára megőrizni a megőrzött állapotot, hogy elkerülje a külső felügyeleti költségeket.

## <a name="impact-of-delivery-count"></a>A kézbesítések számának következményei

Az üzenetek kézbesítési számának a munkamenetek kontextusában való meghatározása a munkamenetek hiányában a definíciótól némileg eltér. Itt látható egy táblázat, amely összegzi a kézbesítések számának növelését.

| Alkalmazási helyzet | Az üzenet kézbesítési száma nő |
|----------|---------------------------------------------|
| A munkamenet el van fogadva, de a munkamenet zárolása lejár (időtúllépés miatt) | Igen |
| A munkamenet el van fogadva, a munkameneten belüli üzenetek nem lesznek végrehajtva (még akkor is, ha zárolva vannak), és a munkamenet be van zárva. | Nem |
| A munkamenet elfogadva, az üzenetek befejeződtek, majd a munkamenet explicit módon be van zárva | N/A (ez a standard folyamat. Itt az üzenetek el lesznek távolítva a munkamenetből) |

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Microsoft. Azure. ServiceBus Samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) vagy a [Microsoft. ServiceBus. Messaging példákat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) egy olyan példához, amely a .NET-keretrendszer ügyfelet használja a munkamenet-kompatibilis üzenetek kezelésére. 

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
