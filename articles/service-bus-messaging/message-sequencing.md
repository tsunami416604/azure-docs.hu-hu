---
title: "Az Azure Service Bus message alkalmazás-előkészítés és időbélyegeket |} Microsoft Docs"
description: "A Service Bus-üzenetsort és Timestamp rendelés megőrzése"
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
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="message-sequencing-and-timestamps"></a>Üzenet sorba rendezés és időbélyegeket

Alkalmazás-előkészítés és timestamping két szolgáltatásokat mindig engedélyezve vannak a Service Bus-entitások keresztül surface a [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) és [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) kapott vagy megtekintett tulajdonságai üzenetek.

Azokban az esetekben, ahol az üzenetek abszolút sorrend nem meghatározó, illetve amelyben ügyféllel kell megbízható egyedi azonosítóját üzenetek, a broker bélyegzők üzenetek résnek mentes, az üzenetsor vagy témakör viszonyítva sorszám növelése. Particionált entitások a sorszám jelenik meg a partíció viszonyítva.

A **SequenceNumber** értéke egyedi 64 bites egész számnak rendelt üzenet elfogadja és tárolja a funkciók és a saját belső azonosítóként. A particionált entitások a legfelső 16 bit tükrözik, a partíció azonosítója. Sorozatszámok váltása nulla Ha elfogy a 48/64 bites tartományon.

A sorszám megbízható egyedi azonosítóként lehet, mert a központi és semleges hatóság és az ügyfelek által nem van hozzárendelve. Azt is érkezési igaz sorrendet jelöli, és pontosabb, mint egy időbélyegző rendezési feltételként, mert időbélyegeket felbontása nem rendelkezhet szélsőséges üzenet ütemben és (azonban minimális) óra eltérésére helyzetekben licencfeltétel tárgyát képezheti. Ha a munkamenet-átvitelszervező csomópontok közötti áttérések tulajdonjogát.

A abszolút érkezési sorrendben számít, például az üzleti forgatókönyvek, amelyben korlátozott számú kínált terméket szolgáltatott közben rendelkezik származnak-first-érkezési alapon utolsó; energiaoptimalizálást egyszerre jegy értékesítési például.

A időbélyeggel funkció úgy működik, mint egy semleges és megbízható hatóság, amely megtalálható üzenet érkezésének időpontja UTC idő szerint pontosan rögzíti a **EnqueuedTimeUtc** tulajdonság. Az érték akkor hasznos, ha egy üzleti forgatókönyv attól függ, például, hogy egy munkaelem egy adott napon éjfél, előtt lett küldve a határidők a feldolgozásuk azonban sokkal mögött a várólistát.

## <a name="scheduled-messages"></a>Ütemezett üzenetek

Elküldheti a üzeneteket az üzenetsor vagy témakör késleltetett feldolgozásra; például az elérhető legyen, a feldolgozás a rendszer egy bizonyos időpontban-feladat ütemezése a. Ez a funkció egy megbízható elosztott időalapú Feladatütemező valósít meg.

Ütemezett üzenetek nem materializálni a várólista csak meghatározott sorba helyezni. A megadott idő előtti ütemezett üzenetek lehet megszakítani. Megszakítási törli az üzenetet.

Ütemezheti az üzeneteket, vagy úgy, hogy a [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) tulajdonság rendszeres küldési útvonalon, vagy explicit módon az üzenet küldésekor a [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. Az utóbbi azonnal visszaadja az ütemezett üzenet **SequenceNumber**, amelyet később felhasználhat az ütemezett üzenet törlése, ha szükséges. Ütemezett üzenetek és a sorozatszámok is felderíthető használatával [üzenet böngészés](message-browsing.md).

A **SequenceNumber** a ütemezett üzenetet csak érvényes közben a következő: Ebben az állapotban. Az üzenet átmenetek az aktív állapotra, mint az üzenetet a rendszer hozzáfűzi a várólista, mintha a várólistában levő volna az aktuális azonnali, beleértve a hozzárendelése egy új, **SequenceNumber**.

Mivel a szolgáltatás adott üzenetet van horgonyozva és üzenetek csak a várólistában levő egyszer, a Service Bus nem támogatja ismétlődő ütemezésekre üzenetek.

## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)