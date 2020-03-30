---
title: Service Bus aszinkron üzenetküldés | Microsoft dokumentumok
description: Ismerje meg, hogy az Azure Service Bus hogyan támogatja az aszinkronizációt egy áruházon keresztül, és továbbítsa a várólistákkal, témakörökkel és előfizetésekkel rendelkező továbbítási mechanizmust.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761032"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Aszinkron üzenetküldési minták és magas rendelkezésre állás

Az aszinkron üzenetküldés számos módon valósítható meg. Várólistákkal, témakörökkel és előfizetésekkel az Azure Service Bus támogatja az aszinkronizációt egy áruházon és továbbítási mechanizmuson keresztül. Normál (szinkron) művelet esetén üzeneteket küldhet várólistákba és témakörökbe, és üzeneteket fogadhat a várólistákból és az előfizetésekből. Az írott alkalmazások attól függnek, hogy ezek az entitások mindig elérhetők-e. Ha az entitás állapota megváltozik, a különböző körülmények miatt, szüksége van egy módja annak, hogy egy csökkentett képesség entitás, amely képes kielégíteni a legtöbb igényt.

Az alkalmazások általában aszinkron üzenetkezelési mintákat használnak számos kommunikációs forgatókönyv engedélyezéséhez. Alkalmazásokat hozhat létre, amelyekben az ügyfelek üzeneteket küldhetnek a szolgáltatásoknak, még akkor is, ha a szolgáltatás nem fut. A kommunikáció adatlöketeit tapasztaló alkalmazások esetében a várólista segíthet a terhelés kiegyenlítésében azáltal, hogy helyet biztosít a kommunikáció puffereléséhez. Végül egy egyszerű, de hatékony terheléselosztót kaphat az üzenetek több gépen történő elosztásához.

Ezen entitások elérhetőségének fenntartása érdekében fontolja meg számos különböző módon, amelyben ezek az entitások úgy tűnhet, nem érhető el a tartós üzenetküldő rendszer. Általánosságban elmondható, hogy azt látjuk, hogy az entitás elérhetetlenné válik az általunk írt alkalmazások számára a következő különböző módokon:

* Nem lehet üzeneteket küldeni.
* Nem lehet üzeneteket fogadni.
* Nem lehet kezelni az entitásokat (létrehozás, beolvasás, frissítés vagy törlés).
* Nem lehet kapcsolatba lépni a szolgáltatással.

Minden ilyen hibák, különböző hibamódok léteznek, amelyek lehetővé teszik az alkalmazás továbbra is a munkát bizonyos szinten a csökkentett képesség. Egy olyan rendszer például, amely képes üzeneteket küldeni, de nem fogadja őket, továbbra is fogadhat rendeléseket a vevőktől, de nem tudja feldolgozni ezeket a rendeléseket. Ez a témakör ismerteti a lehetséges problémákat, és hogyan ezek a problémák enyhítésére. A Service Bus számos olyan megoldást vezetett be, amelyeket engedélyeznie kell, és ez a témakör az opt-in-tlement megoldások használatára vonatkozó szabályokat is ismerteti.

## <a name="reliability-in-service-bus"></a>Megbízhatóság a szervizbuszban
Az üzenetekkel és az entitásokkal kapcsolatos problémák kezelésére többféle módon is kezelhetők, és a kockázatcsökkentés megfelelő használatára vonatkozó irányelvek is vonatkoznak. Az irányelvek megértéséhez először meg kell értenie, hogy mi sikertelen lehet a Service Bus ban. Az Azure-rendszerek kialakítása miatt ezek a problémák általában rövid életűek. Magas szinten az elérhetetlenség különböző okai a következők:

* Szabályozás egy külső rendszerből, amelytől a Service Bus függ. A szabályozás a tárolási és számítási erőforrásokkal való interakciókból ered.
* Probléma egy olyan rendszerhez, amelytől a Service Bus függ. Például a tároló egy adott része problémákba ütközhet.
* A Service Bus hibája egyetlen alrendszeren. Ebben a helyzetben a számítási csomópont inkonzisztens állapotba kerülhet, és újra kell indítania magát, így az összes entitás, amely et szolgál a terhelési elosztás más csomópontokra. Ez viszont okozhat egy rövid ideig tartó lassú üzenetfeldolgozás.
* A Service Bus hibája egy Azure-adatközponton belül. Ez egy "katasztrofális hiba", amelynek során a rendszer több percig vagy néhány óráig nem érhető el.

> [!NOTE]
> A **tárolás** kifejezés az Azure Storage-t és az SQL Azure-t is jelentheti.
> 
> 

A Service Bus számos megoldást tartalmaz ezekre a problémákra. A következő szakaszok ismertetik az egyes kérdéseket és azok megfelelő mérséklését.

### <a name="throttling"></a>Throttling
A Service Bus segítségével a szabályozás lehetővé teszi a kooperatív üzenetdíj-kezelést. Minden egyes Service Bus-csomópont számos entitásnak ad otthont. Ezek az entitások a processzor, a memória, a tárolás és más aspektusak tekintetében támasztja alatt a rendszer igényeit. Ha ezek közül bármelyik a megadott küszöbértékeket meghaladó használatot észlel, a Service Bus megtagadhat egy adott kérelmet. A hívó kap egy [ServerBusyException és][ServerBusyException] újrapróbálkozik 10 másodperc után.

Ennek csökkentéseként a kódnak be kell olvasnia a hibát, és legalább 10 másodpercre le kell állítania az üzenet újrapróbálkozásait. Mivel a hiba az ügyfélalkalmazás különböző részeiközött is előfordulhat, várható, hogy minden egyes darab egymástól függetlenül végrehajtja az újrapróbálkozási logikát. A kód csökkentheti annak valószínűségét, hogy egy várólistán vagy témakörben engedélyezi a particionálást.

### <a name="issue-for-an-azure-dependency"></a>Azure-függőség kiadása
Az Azure-on belüli egyéb összetevők esetenként szolgáltatási problémákkal is rendelkezhetnek. Ha például a Service Bus által használt rendszer frissítése folyamatban van, a rendszer ideiglenesen csökkentett képességeket tapasztalhat. Az ilyen típusú problémák megoldásához a Service Bus rendszeresen vizsgálja és alkalmazza a kockázatcsökkentést. Ezek a mérséklések mellékhatásai nem jelennek meg. Például a tárolással kapcsolatos átmeneti problémák kezeléséhez a Service Bus olyan rendszert valósít meg, amely lehetővé teszi az üzenetküldési műveletek következetes működését. A megoldás jellege miatt az elküldött üzenet akár 15 percet is igénybe vehet, hogy megjelenjen az érintett várólistában vagy előfizetésben, és készen álljon egy fogadási műveletre. Általánosságban elmondható, hogy a legtöbb entitás nem fogja tapasztalni ezt a problémát. Azonban az Azure-on belüli Service Bus-ban lévő entitások száma miatt ez a megoldás néha szükséges a Service Bus-ügyfelek egy kis részhalmaza esetén.

### <a name="service-bus-failure-on-a-single-subsystem"></a>A Service Bus hibája egyetlen alrendszeren
Minden alkalmazás esetén a körülmények a Service Bus belső összetevőjének inkonzisztenssé válását okozhatják. Amikor a Service Bus észleli ezt, adatokat gyűjt az alkalmazásból, hogy segítse a történtek diagnosztizálásában. Az adatok gyűjtése után az alkalmazás újraindul, hogy megpróbálja visszaadni azt egy konzisztens állapotba. Ez a folyamat meglehetősen gyorsan történik, és azt eredményezi, hogy egy entitás úgy tűnik, hogy néhány percig nem érhető el, bár a tipikus leállási idő sokkal rövidebb.

Ezekben az esetekben az ügyfélalkalmazás létrehoz egy [System.TimeoutException][System.TimeoutException] vagy [MessagingException][MessagingException] kivételt. A Service Bus automatikus ügyfél-újrapróbálkozási logika formájában tartalmaz egy kockázatcsökkentést. Miután az újrapróbálkozási időszak kimerült, és az üzenet nem kézbesítve, akkor fedezze fel más említett a cikkben a [leállások és katasztrófák kezeléséről][handling outages and disasters].

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta az aszinkron üzenetküldés alapjait a Service Busban, további részleteket olvashat a [kimaradások és katasztrófák kezeléséről.][handling outages and disasters]

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
