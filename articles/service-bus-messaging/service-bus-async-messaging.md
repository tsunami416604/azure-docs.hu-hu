---
title: Aszinkron üzenetkezelés a Service Bus |} A Microsoft Docs
description: Azure Service Bus aszinkron üzenetkezelés ismertetése.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301570"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Aszinkron üzenetkezelési minták és magas rendelkezésre állás

Aszinkron üzenetkezelés számos különféle módon implementálható. Az üzenetsorok, témakörök és előfizetések Azure Service Bus támogatja asynchronism egy tároló és a továbbítás mechanizmus segítségével. Normál (szinkron) műveletet az üzenetsorok és témakörök üzenetküldés, és üzenetsorok és -előfizetések fogad üzeneteket. Írt alkalmazások mindig legyenek elérhetők ezek az entitások függenek. Ha az entitások állapotát, mert különböző körülmények között, szüksége van egy megoldást nyújtanak a csökkentett képesség entitás, amely a legtöbb szükségleteit képes kielégíteni.

Alkalmazások általában aszinkron üzenetkezelési minták használható számos forgatókönyv kommunikáció engedélyezéséhez. Hozhat létre, amelyben az ügyfelek üzeneteket küldhetnek szolgáltatásokat, akkor is, ha a szolgáltatás nem fut alkalmazás. Alkalmazások számára a kommunikációt, a queue szolgáltatás segítségével tapasztalat terhelés kiegyenlítése helyet azáltal, hogy a puffer kommunikáció. Végül kérheti le egy egyszerű de hatékony, hogy a terheléselosztó üzenetek továbbítása több gép között.

Annak érdekében, hogy ezek az entitások bármelyikének rendelkezésre állás fenntartása, fontolja meg több különböző módon, amelyben ezeket az entitásokat is megjelenhetnek egy tartós üzenetküldő rendszer nem érhető el. Általánosan fogalmazva láthatjuk, az entitás már nem érhető el a következő különböző módokon írunk alkalmazásokhoz:

* Nem sikerült elküldeni az üzeneteket.
* Nem lehet az üzenetek fogadásához.
* Nem lehet entitások kezelése (létrehozása, beolvasása, frissítése vagy törlése entitások).
* Nem lehet kapcsolódni a szolgáltatáshoz.

Minden ilyen hibához tartozó különböző hibaállapot léteznek, amelyek lehetővé teszik az alkalmazások továbbra is csökkentett képesség szint munka elvégzéséhez. Például a rendszer, amelyet üzeneteket küldeni, de nem fogadhatók rendelések is fogadhat az ügyfelektől származó, de nem tudja feldolgozni a rendeléseket. Ez a témakör ismerteti a potenciális problémákat, amely akkor fordulhat elő, és hogyan vannak problémák elhárításáról ezeket a hibákat. A Service Bus megoldások, amelyek kell beleegyezik számos vezetett be, és ez a témakör emellett ismerteti ezek választható megoldások használatára vonatkozó szabályok.

## <a name="reliability-in-service-bus"></a>A Service Bus megbízhatóság
Többféleképpen is üzenet és jogi problémák kezeléséhez, és ezek a megoldások megfelelő használatára vonatkozó irányelveket. Tudni, hogy az irányelveket, először ismernie kell Mi a Service Bus meghiúsulhat. Azure rendszerek kialakítását, mert az összes ezeket a problémákat általában rövid ideig tartó. Magas szinten a különböző elérhetetlensége okait a következőképpen jelenik meg:

* Szabályozás a külső rendszerek, amelyeken a Service Bus függ. Szabályozás és a tárolási és számítási erőforrások közötti kapcsolatok között jelentkezik.
* A rendszer, amelyen a Service Bus függ a probléma. Ha például egy adott részét tárolási problémák merülhetnek fel.
* Hiba történt a Service Bus egyetlen alrendszeren. Ebben a helyzetben egy számítási csomóponton képes inkonzisztens állapotba kerülnek és újra kell indítani, okozó összes entitást más csomópontokhoz terheléselosztás szolgál. Viszont emiatt rövid idő alatt lassú üzenetfeldolgozást.
* Hiba történt a Service Bus egy Azure-adatközpont. Ez a, mely során a rendszer nem érhető el, hány percig vagy néhány óráig "Végzetes hiba".

> [!NOTE]
> Az előfizetési időszak **tárolási** Azure Storage és az SQL Azure jelenti.
> 
> 

A Service Bus ezeket a problémákat a megoldások az számát tartalmazza. A következő részekben bemutatjuk az összes hiba és azok megfelelő.

### <a name="throttling"></a>Throttling
A Service busszal szabályozás együttműködési üzenet arány kezelését teszi lehetővé. Minden egyes Service Bus-csomópont Kezelőkód számos entitás. A Processzor, memória, tárolási és más aspektusokat tekintetében a rendszer minden ilyen entitásnál teszi a növekvő igények szerint. Ha bármelyik ezek értékkorlátozással észlel, használati, amely meghaladja a meghatározott küszöbértékeket, Service Bus is megtagadása egy adott kérelem. A hívó kap egy [ServerBusyException] [ ServerBusyException] és újrapróbálkozás 10 másodperc múlva.

A megoldás a kódot kell olvassa el a hibát, és az üzenet bármely újrapróbálkozások halt legalább 10 másodpercig. Mivel a hiba akkor fordulhat elő, megtalálhatja az ügyfél-alkalmazás között, várható, hogy minden darab egymástól függetlenül végrehajtja az újrapróbálkozási logika. A kód csökkentheti a valószínűsége annak, üzenetsor vagy témakör particionálása engedélyezésével szabályozás alatt áll.

### <a name="issue-for-an-azure-dependency"></a>Az Azure függ a probléma
Azure-on belüli más összetevők alkalmanként problémákba ütközhet szolgáltatás. Például egy Service Bus használó rendszer frissítésekor a rendszer ideiglenesen tapasztalhatnak csökkentett képességeket. Az ilyen típusú problémák megoldása, a Service Bus rendszeresen folytat, és megoldások valósítja meg. Ezek a megoldások a hatásai jelennek meg. Például a storage átmeneti hibák kezelésére, a Service Bus valósít meg egy rendszer, amely lehetővé teszi üzenet küldése műveleteket, következetesen működjenek. A megoldás jellege miatt elküldött üzenet legfeljebb 15 percbe is telhet jelennek meg az érintett üzenetsorban vagy előfizetésben, és készen áll a fogadás művelet. A legtöbb entitásnak általánosan fogalmazva, nem fog tapasztalni a probléma. Azonban megadott entitások száma a Service Bus Azure-ban, ez a megoldás néha szükség van egy Service Bus-ügyfelek kisebb részhalmazát.

### <a name="service-bus-failure-on-a-single-subsystem"></a>A Service Bus hiba egyetlen alrendszerek
Bármilyen alkalmazással esetekben okozhat egy Service Bus inkonzisztenciáját belső összetevője. A Service Bus észleli ezt, ha az alkalmazás diagnosztizálásakor, mi történt a támogatási adatokat gyűjti. Az adatok gyűjtése történik, ha az alkalmazás újraindítása a kísérlet azt vissza egy konzisztens állapotba. Ez a folyamat meglehetősen gyorsan történik, és egy tipikus alkalommal le, ha nem érhető el akár néhány perc tűnő entitásban eredmények sokkal rövidebb.

Ebben az esetben az ügyfélalkalmazást hoz létre egy [System.TimeoutException] [ System.TimeoutException] vagy [Istransient] [ MessagingException] kivétel. A Service Bus egy megoldás erre a problémára formájában, automatizált ügyfél újrapróbálkozási logikát tartalmaz. Az újrapróbálkozási időszak kimerül, és az üzenet nem lesz kézbesítve, a cikkben említett egyéb használatával megvizsgálhatja [leállások és katasztrófák kezelése][handling outages and disasters].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az aszinkron üzenetkezelés a Service Bus alapjait, olvassa el további részletek [leállások és katasztrófák kezelése][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
