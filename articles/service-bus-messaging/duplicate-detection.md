---
title: Az Azure Service Bus duplikált üzenetészlelése | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan észlelheti az ismétlődéseket az Azure Service Bus-üzenetekben. Az ismétlődő üzenet figyelmen kívül hagyható és eldobható.
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
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760368"
---
# <a name="duplicate-detection"></a>Duplikálás észlelése

Ha egy alkalmazás sikertelen lesz egy végzetes hiba miatt közvetlenül az üzenet küldése után, és az újraindított alkalmazáspéldány tévesen úgy véli, hogy az előző üzenetkézbesítés nem történt meg, egy későbbi küldés hatására ugyanaz az üzenet kétszer jelenik meg a rendszerben.

Az is lehetséges, hogy egy hiba az ügyfél vagy a hálózat szintjén egy pillanattal korábban, és egy elküldött üzenetet kell lekötni a várólistába, a nyugtázás nem sikerült visszaadni az ügyfélnek. Ebben a forgatókönyvben az ügyfél kétségei vannak a küldési művelet kimenetelét illetően.

A duplikáltelem-észlelés kivonja a kételyt ezekből a helyzetekből azáltal, hogy engedélyezi a feladónak ugyanazt az üzenetet, és a várólista vagy a témakör elveti a duplikált másolatokat.

A duplikáltelem-észlelés engedélyezése segít nyomon követni a várólistába vagy témakörbe küldött összes üzenet alkalmazás által vezérelt *MessageId* azonosítóját egy adott időablakban. Ha az időablakban naplózott *MessageId-del* bármilyen új üzenetet küld, az üzenet elfogadottként jelenik meg (a küldési művelet sikeres), de az újonnan elküldött üzenetet a rendszer azonnal figyelmen kívül hagyja, és elejti. Az üzenet más részei nem számítanak figyelembe, mint a *MessageId.*

Az azonosító alkalmazásvezérlése alapvető fontosságú, mert csak az az alkalmazás teszi lehetővé, hogy a *MessageId* egy olyan üzleti folyamatkörnyezethez kapcsolódjon, amelyből hiba esetén kiszámíthatóan rekonstruálható.

Egy olyan üzleti folyamat esetén, amelyben egy alkalmazáskörnyezet kezelése során több üzenetet küldenek, a *MessageId* lehet az alkalmazásszintű környezeti azonosító összetett azonosítója, például egy beszerzési rendelés száma, és az üzenet tárgya, például **12345.2017/fizetés**.

A *MessageId* mindig lehet valamilyen GUID, de az azonosító rögzítése az üzleti folyamathoz kiszámítható ismételhetőséget eredményez, ami a duplikáltelem-észlelési funkció hatékony kihasználásához szükséges.

> [!NOTE]
> Ha a duplikáltelem-észlelés engedélyezve van, és a munkamenet-azonosító vagy a partíciókulcs nincs beállítva, az üzenetazonosító lesz a partíciókulcs. Ha az üzenetazonosító sincs beállítva, a .NET és az AMQP-tárak automatikusan létrehoznak egy üzenetazonosítót az üzenethez. További információt a Partíciókulcsok használata című [témakörben talál.](service-bus-partitioning.md#use-of-partition-keys)

## <a name="enable-duplicate-detection"></a>Duplikáltelem-észlelés engedélyezése

A portálon a szolgáltatás be van kapcsolva az entitás létrehozása során a **Duplikáltelem-észlelés engedélyezése** jelölőnégyzet, amely alapértelmezés szerint ki van kapcsolva. Az új témakörök létrehozásának beállítása egyenértékű.

![][1]

> [!IMPORTANT]
> A várólista létrehozása után nem engedélyezhető/nem tilthatja le a duplikáltelemek észlelését. Ezt csak a várólista létrehozásakor teheti meg. 

Programozott módon a [queuedescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) tulajdonsággal beállította a jelzőt a teljes keretrendszer .NET API-n. Az Azure Resource Manager API-val az érték a [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonsággal van beállítva.

A duplikáltelem-észlelési idő előzményei alapértelmezés szerint 30 másodperc a várólisták és a témakörök esetében, legfeljebb hét napig. Ezt a beállítást módosíthatja a várólista és a témakör tulajdonságai ablakban az Azure Portalon.

![][2]

Programozott módon konfigurálhatja a duplikáltelem-észlelési ablak méretét, amely alatt az üzenetazonosítók megőrződnek a [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) tulajdonsággal a teljes . Az Azure Resource Manager API-val az érték a [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonsággal van beállítva.

A duplikáltelem-észlelés engedélyezése és az ablak mérete közvetlenül befolyásolja a várólista (és a témakör) átviteli felismerését, mivel az összes rögzített üzenetazonosítót össze kell egyeztetni az újonnan elküldött üzenetazonosítóval.

Az ablak kis méretű megtartása azt jelenti, hogy kevesebb üzenetazonosítót kell megtartani és egyeztetni, és az átviteli hang kevésbé érintett. A nagy átviteli sebességű entitások, amelyek duplikáltelem-észlelést igénylő, meg kell tartani az ablak a lehető legkisebb.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Azokban az esetekben, amikor az ügyfélkód nem tudja újraelküldeni az üzenetet ugyanazzal a *MessageId azonosítóval,* mint korábban, fontos, hogy olyan üzeneteket tervezzen, amelyek biztonságosan újrafeldolgozhatók. Ez [a blogbejegyzést a idempotencia](https://particular.net/blog/what-does-idempotent-mean) leírja a különböző technikákat, hogyan kell csinálni.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
