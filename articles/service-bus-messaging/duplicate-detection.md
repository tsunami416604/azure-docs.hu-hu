---
title: "Az Azure Service Bus duplikált üzenetek észlelésének |} Microsoft Docs"
description: "Ismétlődő Service Bus-üzenetek"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: efc5608d4812edbb3f477dffbc2b495b331bd787
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="duplicate-detection"></a>Kettős észlelés

Ha egy alkalmazás tapasztal súlyos hiba után azonnal az üzenetet, és az újraindított alkalmazáspéldány tévesen magukat, hogy az előzetes üzenet kézbesítése nem voltak végrehajthatók, egy későbbi küldje hatására jelennek meg a rendszer kétszer ugyanazt az üzenetet.

Akkor is megtörténik egy rövid ideig korábbi ügyfél vagy a hálózati szintű hibát, és a várólistán, a nyugtázási és nem sikerült véglegesíteni az elküldött üzenet küld vissza az ügyfélnek. Ebben a forgatókönyvben az ügyfél a küldési művelet eredményét bizonytalan hagyja.

Kettős észlelés kívül ezekben a helyzetekben bizonytalan engedélyezésével, a küldő újra elküldeni ugyanazt az üzenetet, valamint a az üzenetsor vagy témakör egyetlen duplikált elveti.

Duplikált elemek észlelése segít nyomon követjük, hogy az alkalmazás által szabályozott *MessageId* egy megadott időszak alatt az üzenetsor vagy témakör küldött összes üzenet. Ha bármely új üzenettel végrehajtsa egy *MessageId* , amely már bejelentkezett az az időszak alatt, az üzenet készként való elfogadva (a küldési művelet sikeres), azonban az újonnan elküldött üzenet azonnal figyelmen kívül hagyva, és dobva. Nincs más részei az üzenet nem a *MessageId* minősülnek.

Az azonosító Alkalmazásvezérlés elengedhetetlen, mert csak az, hogy lehetővé teszi, hogy az alkalmazás összekötését a *MessageId* egy üzleti folyamat környezetbe, amelyről az előre rekonstruálható meghibásodása esetén.

Az üzleti folyamatokat, amelyben több üzenetet küldött néhány alkalmazási környezetet kezelése a *MessageId* lehet, hogy az alkalmazás szintű környezeti azonosító egy megrendelőlap számát, például egy összetett és a az tárgyát; például **12345.2017/fizetési**.

A *MessageId* mindig lehet néhány GUID, de az üzleti folyamat azonosítója rögzítő eredményez előre jelezhető ismételhetőség, amely a kettős észlelés funkcióját kihasználva hatékonyan van szükség.

## <a name="enable-duplicate-detection"></a>Ismétlődések észlelésének engedélyezése

A portálon, a szolgáltatás bekapcsol entitás létrehozása során a **ismétlődő észleléséhez** jelölőnégyzetet, amely alapértelmezés szerint ki van kapcsolva. Új témakörök létrehozásához az érték azonos.

![][1]

Programozott módon, állítsa be a jelzőt mellékel a [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) tulajdonság a .NET API-teljes keretrendszerre. Az Azure Resource Manager API-t, a változó értéke az a [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság.

A kettős észlelés előzmények alapértelmezés szerint az üzenetsorok és témakörök 7 nap maximális értéke 30 másodperc. Ez a beállítás a várólista és ez a témakör tulajdonságai ablakban az Azure-portálon módosíthatja.

![][2]

Programozott módon, konfigurálhatja a időintervalluma üzenet-azonosítók megmaradnak, kettős észlelés ablak méretének használatával a [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) tulajdonság a .NET-keretrendszer teljes API-val . Az Azure Resource Manager API-t, a változó értéke az a [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság.

Vegye figyelembe, hogy engedélyezi a kettős észlelés és a az ablak méretének közvetlenül hatással lehet a várólista (és a témakör) átviteli sebesség, mivel az összes rögzített üzenet-azonosítót kell egyeztetendő az újonnan elküldött üzenet azonosítója.

Az ablak kis azt jelenti, hogy kevesebb üzenet-azonosítók kell maradnak, és megfelel, és átviteli tartása érintett kisebb. Magas teljesítmény entitások, kettős észlelés igénylő az ablak lehető legkisebb legyen.

## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
