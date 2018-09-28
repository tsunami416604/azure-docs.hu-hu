---
title: Az Azure Service Bus-üzenetek előkészítése és időbélyegek |} A Microsoft Docs
description: A Service Bus-üzenetsort és időbélyeggel rendelkező rendelés megőrzése
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
ms.date: 09/25/2018
ms.author: spelluru
ms.openlocfilehash: a15e726e237bcdbd9d380aaf9232e5d16a2e648f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409135"
---
# <a name="message-sequencing-and-timestamps"></a>Üzenetek előkészítése és időbélyegek

Alkalmazás-előkészítés és a timestamping is két olyan szolgáltatás, amely mindig engedélyezve vannak az összes Service Bus-entitások, és felület révén a [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) és [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) fogadott vagy a megtekintett tulajdonságai üzenetek.

Az azokban az esetekben, ahol az üzenetek abszolút sorrend és/vagy, amelyben egy fogyasztó szüksége van a megbízható egyedi azonosító üzenetek, és a gap ingyenes, a közvetítő stampek üzenetek sorszáma képest az üzenetsor vagy témakör növelése. A particionált entitások esetében a sorszám képest a partíció jelenik meg.

A **SequenceNumber** értéke, egy üzenet fogadja, és a belső azonosítóként a közvetítő és a függvény által tárolt rendelt egyedi 64 bites egész. A particionált entitások a legfelső 16 bit tükrözik, a partíció azonosítója. Sorozatszámok vihetők át a nulla, ha elfogy a 48, 64 bites tartomány.

A sorszám is lehet megbízható egyedi azonosítóként, mivel a központi és semleges hatóság és az ügyfelek által nem van hozzárendelve. Azt is érkezési igaz sorrendet jelöli, és pontosabb, mint egy rendelés feltételként időbélyeg mert előfordulhat, hogy rendelkezik egy elég nagy felbontású szélsőséges üzenet díjakon időbélyegzőket és a (azonban minimális) óra torzulása helyzetekben áldozatai lehetnek, a közvetítő csomópontok közötti átmeneteket tulajdonjogát.

Az abszolút érkezési sorrend, például az üzleti forgatókönyvek, ahol a korlátozott számú is elérhető termékek fájlnévkiterjesztései erejéig származnak-first-érkezési alapon utolsó; concert jegyeladásokkal csak például szolgál.

Az időbélyegző funkció működik-e egy semleges és megbízható hatóság, amely pontosan rögzíti az Egyezményes világidő érkezési egy üzenet, megjelennek a **EnqueuedTimeUtc** tulajdonság. Az érték akkor hasznos, ha egy üzleti forgatókönyvre határidőket, például hogy egy munkaelem egy adott dátumon éjfél előtt küldött függ, de messze a várólistát mögött helyezkedik el a feldolgozást.

## <a name="scheduled-messages"></a>Ütemezett üzenetek

Küldhet üzenetet egy üzenetsorba vagy témakörbe késleltetett feldolgozási; Ha például egy feladat elérhető legyen, feldolgozás céljából egy rendszer által egy adott időpontra ütemezése. Ez a funkció egy megbízható elosztott időalapú scheduler valósít meg.

Ütemezett üzenetek nem tényleges táblává alakíthatóak a várólistában a meghatározott sorba időpontig. A megadott idő előtti ütemezett üzenetek szakadhatnak meg. Megszakítás törli az üzenetet.

Ütemezheti üzenetek beállításával vagy a [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) tulajdonság rendszeres küldési útvonalon keresztül, vagy explicit módon az üzenetek küldésekor a [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API-t. Az utóbbi azonnal visszaadja az ütemezett üzenet **SequenceNumber**, amellyel később megszakítja az ütemezett üzenet, ha szükséges. Ütemezett üzenetek és a sorszámok is könnyen megtalálhatók legyenek használatával [az üzenetek közötti böngészés](message-browsing.md).

A **SequenceNumber** az ütemezett üzenet csak akkor érvényes, miközben az üzenet ebben az állapotban van. Az üzenet átmeneteket aktív állapotba kerül, mint az üzenetet a rendszer hozzáfűzi az üzenetsorban, mintha volt-e a várólistán lévő jelenlegi azonnali, amely tartalmazza a hozzárendelése egy új, **SequenceNumber**.

Mivel a funkció az egyes üzeneteket van rögzítve és üzenetek csak akkor lehet várólistán lévő egyszer, a Service Bus nepodporuje ismétlődő ütemezések szerint üzenetek.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)