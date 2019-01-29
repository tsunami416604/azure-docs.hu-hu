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
ms.openlocfilehash: 58ce1e4bd7ffee642c062fde47fd5aff7fa9da0b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168822"
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

Ebben az esetben az ügyfélalkalmazást hoz létre egy [System.TimeoutException] [ System.TimeoutException] vagy [Istransient] [ MessagingException] kivétel. A Service Bus egy megoldás erre a problémára formájában, automatizált ügyfél újrapróbálkozási logikát tartalmaz. Miután az újrapróbálkozási időszak kimerül, és az üzenet nem lesz kézbesítve, használjon más funkciókat, például áttekintheti [párosított névterek][paired namespaces]. Párosított névterek ebben a cikkben tárgyalt egyéb korlátozásokkal rendelkezik.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Hiba a Service Bus, az Azure-adatközpont
Az Azure-adatközpontban hiba legvalószínűbb oka sikertelen frissítés üzembe helyezése a Service Bus és a függő rendszert. Mivel a platform érett rendelkezik, az adott hibatípust valószínűségét mennyiséget. Egy adatközpontok kiesésével szemben is fordulhat elő, amelyek tartalmazzák a következő okból:

* Elektromos szolgáltatáskimaradás (tápegység és eltűnnek létrehozni power).
* Kapcsolat (internet/csere típusú ügyfelek és az Azure között).

Mindkét esetben természetes és mesterséges katasztrófa okozza a problémát. Megkerülő megoldásként, és győződjön meg arról, hogy továbbra is küldhet üzeneteket, akkor használhatja, [párosított névterek] [ paired namespaces] ahhoz, hogy üzeneteket kell küldeni egy másik helyen, míg az elsődleges hely ismét kifogástalan állapotú legyen. További információkért lásd: [ajánlott eljárásai az alkalmazások a Service Bus leállásainak és katasztrófákkal szembeni szigetelő][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Párosított névterek
A [párosított névterek] [ paired namespaces] funkció támogatja a forgatókönyvek, amelyben egy Service Bus-entitás vagy adatközponton belüli központi telepítési elérhetetlenné válik. Ez az esemény ritkán jelentkezik, míg az elosztott rendszerek továbbra is fel kell készíteni legrosszabb használatieset-forgatókönyveinek kezelésére. Általában ez az esemény történik, mert néhány elem, amelyektől függ a Service Bus egy rövid távú kapcsolatos problémák tapasztalhatók. A leállások rendelkezésre állásának fenntartása a Service Bus felhasználók használhatják két külön névtereket, lehetőleg külön adatközpontok, a saját üzenetküldési entitások üzemeltetéséhez. Ez a szakasz további része a következő kifejezésekkel használja:

* Elsődleges névtér: A névtér, amellyel az alkalmazás használja, a küldési és fogadási műveletek.
* Másodlagos névtér: A névtér, amely egy biztonsági mentést az elsődleges névtér funkcionál. Logikai alkalmazás nem működik együtt a névtér.
* Feladatátvételi időköz: Fogadja el a szokásos hibákat előtt az alkalmazás az idő az elsődleges névtér átvált a másodlagos névtérre.

Párosított névterek támogatási *küldése a rendelkezésre állási*. Küldjön a rendelkezésre állási megőrzi az üzenetek küldéséhez. Küldési rendelkezésre állási használatához az alkalmazás az alábbi követelményeknek kell megfelelnie:

1. Csak fogadása az elsődleges névtérből.
2. Üzeneteket küldeni egy adott üzenetsor vagy témakör sorrendben érkezik.
3. Üzenetek a munkameneten belül érkezik, sorrendben. Ez a normál működéshez a munkamenetek szünetet. Ez azt jelenti, hogy az alkalmazás a munkamenetek logikailag üzenetek használja.
4. A munkamenet-állapot csak az elsődleges névtér változatlan marad.
5. Az elsődleges üzenetsornak is online állapotba kerül, és indítsa el az üzenetek fogadására, mielőtt a másodlagos üzenetsor összes üzenet kézbesíti az elsődleges üzenetsornak.

A következő részekben bemutatjuk az API-k, az API-k hogyan vannak megvalósítva, és mintakód bemutatja, hogy a funkciót használja. Vegye figyelembe, hogy nincsenek-e ez a funkció társított számlázását.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>The MessagingFactory.PairNamespaceAsync API
A párosított névterek szolgáltatást is tartalmaz a [PairNamespaceAsync] [ PairNamespaceAsync] metódust a [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] osztály:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

A feladat befejeztével a névtér párosítási egyben befejeződött, és készen áll a kapcsolatos [MessageReceiver][MessageReceiver], [QueueClient] [ QueueClient] , vagy [TopicClient] [ TopicClient] hoztak létre a [MessagingFactory] [ MessagingFactory] példány. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] párosítás, amely a különböző típusú érhetők el az alaposztály alaposztályát van egy [MessagingFactory] [ MessagingFactory] objektum. Jelenleg az egyetlen származtatott osztály ilyen nevű [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], amely megvalósítja a küldési rendelkezésre állási követelmények vonatkoznak. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] egymásra épülnek, konstruktorral rendelkezik. A konstruktor megnézzük a legtöbb paraméterekkel, a többi konstruktorok viselkedését képes megérteni.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ezek a paraméterek jelentése a következő:

* *secondaryNamespaceManager*: Egy inicializált [NamespaceManager] [ NamespaceManager] a másodlagos névtér-példányt, amely a [PairNamespaceAsync] [ PairNamespaceAsync] metódus segítségével beállíthatja a a másodlagos névtér. A névtér-kezelő üzenetsorok névtér listájának beszerzése érdekében, és győződjön meg arról, hogy létezik-e a szükséges várakozó fájlok számát a várólisták szolgál. Ha még nem léteznek a kérdéses üzenetsorok, a rendszer létrehozza őket. [NamespaceManager] [ NamespaceManager] képes létrehozni a jogkivonatot az szükséges a **kezelés** jogcím.
* *messagingFactory*: A [MessagingFactory] [ MessagingFactory] példány a másodlagos névtér. A [MessagingFactory] [ MessagingFactory] objektumra küldéséhez használt és, ha a [EnableSyphon] [ EnableSyphon] tulajdonsága **Igaz**, üzenet fogadása a várakozó fájlok számát a várólistákat.
* *backlogQueueCount*: A várakozó fájlok számát a várólisták létrehozásához számát. Ez az érték legalább 1 kell lennie. Üzeneteket küld a várakozó fájlok számát, ha ezek a várólisták egyik véletlenszerűen kiválasztott. Ha az érték 1-re, akkor csak egy üzenetsor minden eddiginél használhatók. Ez akkor fordul elő, és a egy várakozó üzenetsor hibákat okoz, ha az ügyfél nem tud próbálkozzon egy másik várakozó üzenetsor és az üzenet küldése sikertelen lehet. Azt javasoljuk, hogy az érték néhány nagyobb értéket, és az alapértelmezett 10 értéket. Ez az alkalmazás által naponta adatok mennyiségétől függően magasabb vagy alacsonyabb értékre módosíthatja. Minden egyes várakozó üzenetsor képes tárolni legfeljebb 5 GB-os üzeneteket.
* *failoverInterval*: Mennyi ideig óraszáma mielőtt bármilyen egyetlen entitás átvált a másodlagos névtérre hibák az elsődleges névtér fogja elfogadni. A feladatátvétel egy entitás az entitás által történik. Egyetlen névtér entitások gyakran élő a Service Bus belül különböző csomópontokon. Hiba történt egy entitás nem jelenti azt, a másik hiba. Ez az érték állíthatja [System.TimeSpan.Zero] [ System.TimeSpan.Zero] történő feladatátvételt a másodlagos azonnal az első, nem átmeneti hiba után. A feladatátvételi időzítő kiváltó hibák lehet bármely [Istransient] [ MessagingException] , amelyben a [IsTransient] [ IsTransient] tulajdonsága hamis, vagy egy [ System.TimeoutException][System.TimeoutException]. Kivételek, mint például [UnauthorizedAccessException] [ UnauthorizedAccessException] nem okozhat a feladatátvétel, mert azt jelzik, hogy az ügyfél nem megfelelően van konfigurálva. A [ServerBusyException] [ ServerBusyException] nem nem ok feladatátvételi, mert a helyes mintát, hogy Várjon 10 másodpercet, majd újból elküldeni az üzenetet.
* *enableSyphon*: Azt jelzi, hogy az adott párosítást kell is syphon térjen vissza az elsődleges névtér a másodlagos névtérre üzeneteit. Általánosságban véve az alkalmazásokat, amelyek üzenetküldés kell ezt az értéket **hamis**; alkalmazásokat, amelyek az üzenetek fogadásához kell beállítania ezt az értéket **igaz**. Ennek oka az, hogy milyen gyakran, nincsenek üzenetküldők-nál kevesebb üzenet fogadók. A fogadók számától függően választhat egy egyetlen alkalmazás példánya Szifonos feladatok kezelésére. Több fogadóval használatával minden egyes várakozó üzenetsor számlázási hatással van.

A kód használatához hozzon létre egy elsődleges [MessagingFactory] [ MessagingFactory] példány, egy másodlagos [MessagingFactory] [ MessagingFactory] példány, egy másodlagos [ NamespaceManager] [ NamespaceManager] példány, és a egy [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] példány. A hívás egyszerű: a következő lehet:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Amikor a tevékenység által visszaadott a [PairNamespaceAsync] [ PairNamespaceAsync] metódus befejeződik, minden rendben van állítva, és készen áll a használatra. Mielőtt a feladat ad vissza, előfordulhat, hogy nem befejeződött az összes megfelelő működéséhez szükséges a párosítás háttérműveletek. Ennek eredményeképpen nem először érdemes üzenetküldésre, amíg a feladat adja vissza. Ha az összes hiba történt, például a helytelen hitelesítő adatait, vagy nem sikerült létrehozni a várakozó fájlok számát a várólisták, a kivételek, a rendszer hibajelzést a feladat befejezése után. Miután a feladat adja vissza, ellenőrizze, hogy a az üzenetsorok található lettek-e vagy megvizsgálásával létrehozott a [BacklogQueueCount] [ BacklogQueueCount] tulajdonsága a [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] példány. A fenti kóddal, a művelet a következőképpen jelenik meg:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az aszinkron üzenetkezelés a Service Bus alapjait, olvassa el további részletek [párosított névterek][paired namespaces].

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
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
