---
title: "A Service Bus aszinkron üzenetkezelési |} Microsoft Docs"
description: "Azure Service Bus aszinkron üzenetkezelési leírása."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: d36360f3fb46adf96f53976584987590791b07d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Aszinkron üzenetkezelési minták és magas rendelkezésre állás

Számos különböző módon aszinkron üzenetkezelési megvalósítható. Az üzenetsorok, témakörök és előfizetések Azure Service Bus asynchronism a tároló és a továbbítási mechanizmus segítségével támogatja. Normál (szinkron) műveletet üzenetek küldése az üzenetsorok és témakörök, és üzenetek fogadása az üzenetsorok és előfizetések. Írt alkalmazások ezeket az entitásokat mindig alatt a rendelkezésre álló függ. Ha entitás állapotát, mert különböző körülmények között, szüksége van egy módszerre, amellyel olyan csökkentett funkció entitás, aki többségéhez felel meg.

Alkalmazások általában aszinkron üzenetkezelési mintát engedélyezéséhez használja a kommunikációs helyzetek száma. Alkalmazások, ahol az ügyfelek üzeneteket küldhetnek szolgáltatások, akkor is, ha a szolgáltatás nem fut hozhat létre. Az alkalmazások adott élmény kommunikáció, egy várólista felszakadásáig segíthet a terhelés szinten, egy helyen adja meg a puffer kommunikáció. Végül egy egyszerű, de hatékony terheléselosztó üzenetek szét több számítógép is ki.

Ahhoz, hogy ezeket az entitásokat bármelyikét rendelkezésre állását fenntartásához, fontolja meg a számos különböző módon, amelyben ezeket az entitásokat is megjelennek a tartós üzenetkezelő rendszer nem érhető el. Általánosságban véve az entitás már nem érhető el, azt írni a következő különböző módon alkalmazások látható:

* Nem lehet üzeneteket küldeni.
* Nem lehet üzeneteket fogadni.
* Tudja felügyelni az entitások (létrehozása, beolvasása, frissítése vagy törlése entitások).
* Nem lehet csatlakozni a szolgáltatáshoz.

Minden ilyen hibához a másik meghibásodás vannak, amelyek lehetővé teszik az alkalmazások továbbra is csökkentett funkció szint feladatok végrehajtására. Például egy rendszer, amely képes üzeneteket küldeni, de nem kapott rendelések továbbra is kaphat az ügyfelektől, de nem tudja feldolgozni a rendeléseket. Ez a témakör ismerteti a lehetséges problémák kezeléséhez, amik akkor léphetnek fel, és hogyan ismertetünk problémák elhárításáról. A Service Bus vezetett egy megoldást, amely bírálhatja felül a számát, és ez a témakör emellett ismerteti ezek részt azok mérséklési használatára vonatkozó szabályok.

## <a name="reliability-in-service-bus"></a>A Service Bus megbízhatóság
Számos módon üzenet és egyéb entitások problémák kezeléséhez, és vannak-e azok mérséklési megfelelő használatára vonatkozó irányelveket. Szeretné megtudni, az irányelveket, először ismernie kell Mi a Service Bus sikertelen lehet. Azure rendszerek Tervező, mert ezek a problémák általában rövid élettartamú. Magas szinten a különböző oka elérhetetlensége a következőképpen jelenik meg:

* Sávszélesség-szabályozás a Service Bus függ külső rendszerekből. Sávszélesség-szabályozás a tárolási és számítási erőforrásokat interakció következik be.
* A probléma a rendszert, amelyen a Service Bus függ. Például egy adott részét tárolási is problémák merülhetnek fel.
* Egyetlen alrendszeren Service Bus sikertelen. Ebben a helyzetben a számítási csomópont inkonzisztens állapotba kerülnek kérheti le és újra kell indítani, más csomópontok terheléselosztásához szolgál összes entitás, amely. Rövid idő alatt lassú üzenet feldolgozása pedig okozhatnak.
* Sikertelen a Service Bus egy Azure-adatközpontban belül. Ez azért, amely során a rendszer nem érhető el a rendszer hány perc vagy néhány óra "Végzetes hiba".

> [!NOTE]
> A kifejezés **tárolási** Azure Storage mind az SQL Azure azt.
> 
> 

A Service Bus azok mérséklési lehetőségeit a problémát számos tartalmaz. Az alábbi szakaszok ismertetik az egyes a problémákra és a megfelelő megoldást.

### <a name="throttling"></a>Szabályozás
A Service busszal sávszélesség-szabályozás lehetővé teszi az együttműködési üzenet sebessége. Minden egyes Service Bus-csomópont Kezelőkód számos entitás. Processzor, memória, tárolási és egyéb facets tekintetében a rendszer minden entitásainak teszi iránti igények kielégítése érdekében. Ha ezek értékkorlátozás bármelyikét használati észlel, amely meghaladja a meghatározott küszöbértékeket, Service Bus megtagadhatja egy adott kérelem. A hívó kap egy [ServerBusyException] [ ServerBusyException] és újrapróbálkozások 10 másodperc után.

A csökkentése a kódot kell olvassa el a hibát, és az üzenet minden újrapróbálkozások halt legalább 10 másodpercet. A hiba akkor fordulhat elő, a felhasználói kérelem darab között, mivel várható, hogy minden egyes adatra egymástól függetlenül végrehajtja az újrapróbálkozási logika. A kód csökkentheti az üzenetsor vagy témakör particionálás engedélyezésével szabályozva valószínűségét.

### <a name="issue-for-an-azure-dependency"></a>Egy Azure-függőségének probléma
Az Azure egyéb összetevők alkalmanként rendelkezhet szolgáltatásokkal kapcsolatos problémákról. Például egy Service Bus használó rendszer frissítésekor a rendszer is ideiglenesen csökkentett képességeket tapasztal. Ilyen jellegű problémák megoldása érdekében a Service Bus rendszeresen megvizsgálja, és azok mérséklési megvalósítja. Ezek azok mérséklési a hatásai szerepelhet. Például tárolóval átmeneti problémák kezeléséhez, a Service Bus valósítja meg a rendszer, amely lehetővé teszi az üzenet küldési művelet következetesen működjenek. A megoldás jellege miatt elküldött üzenet legfeljebb 15 percbe is telhet az érintett várólista vagy előfizetés megjelenjen, és készen áll a fogadási művelet. A legtöbb entitások általánosságban véve nem fog tapasztalni a probléma. Azonban az Azure Service Bus megadott entitások száma, a megoldás néha szükség van a Service Bus-ügyfelek csak kis részét.

### <a name="service-bus-failure-on-a-single-subsystem"></a>A Service Bus hiba egyetlen alrendszeren
Minden más alkalmazáshoz körülmények között a Service Bus inkonzisztenciáját belső összetevőként okozhat. A Service Bus észleli ezt, ha az alkalmazás támogatási megállapításában, mi történt adatait gyűjti. Miután az adatgyűjtés a következők, az alkalmazás újraindítása hogy Alapértelmezések konzisztens állapotú legyen. Ez a folyamat meglehetősen gyorsan végbemegy, és egy entitás legfeljebb néhány percet, nem lehet, bár a tipikus üzemen kívül töltött idejét látszólag eredményei sokkal rövidebb.

Ezekben az esetekben az ügyfélalkalmazás hoz létre egy [System.TimeoutException] [ System.TimeoutException] vagy [MessagingException] [ MessagingException] kivétel. A Service Bus tartalmaz a megoldás a probléma automatikus ügyfél újrapróbálkozási logika formájában. Miután az újrapróbálkozási időszak kimerül, és az üzenet nem érkezik, ismerje meg a más funkciókat használ, például a [névterek párosítva][paired namespaces]. Párhuzamos névterek ebben a cikkben említett egyéb korlátozásokkal rendelkezik.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Sikertelen volt-e a Service Bus egy Azure-adatközpontban belül
Egy Azure-adatközpontban található hiba legvalószínűbb oka a sikertelen frissítés telepítése a Service Bus vagy egy függő rendszer. Mivel a platform van jelen, a hiba az ilyen típusú valószínűségét mennyiséget. Datacenter hiba is megtörténhet, többek között a következők lehetnek az okai:

* Elektromos kimaradás (tápegység és eltűnnek előállítása power).
* A kapcsolat (internet break az ügyfelek és az Azure között).

Mindkét esetben természetes és mesterséges katasztrófa okozta a problémát. Megkerülő, és győződjön meg arról, hogy továbbra is küldhet üzeneteket, használhatja [névterek párosítva] [ paired namespaces] engedélyezése üzenetek egy másik helyen kell küldeni, amíg az elsődleges hely újra kifogástalan állapotú legyen. További információkért lásd: [ajánlott eljárások az alkalmazások a Service Bus kimaradások és vészhelyzetek szigetelő][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Párosított névterek
A [névterek párosítva] [ paired namespaces] szolgáltatás forgatókönyveket támogat, amelyben egy Szolgáltatásbusz-entitás vagy az adatközponton belüli központi telepítési nem érhető el. Ez az esemény ritkán kerül sor, elosztott rendszerek továbbra is kell készíteni legrosszabb esetben helyzetek kezelésére. Általában ez az esemény történik, mert néhány elem, amelytől függ a Service Bus egy rövid távú problémát tapasztal. Rendelkezésre állását fenntartásához kimaradás során, a Service Bus felhasználók segítségével két különálló névterek, lehetőleg külön adatközpontok, a gazdagép az üzenetküldési entitások. Ez a szakasz további része a következő kifejezésekkel használja:

* Elsődleges névtér: A névteret, amelyhez az alkalmazás interakciót folytat a küldési és fogadási műveletek.
* Másodlagos névtér: A névtér, amely úgy működik, mint a biztonsági másolat az elsődleges névtérhez. Úgy az alkalmazáslogikát nem működik együtt a névtérhez.
* Feladatátvételi időköz: idő előtt az elsődleges névtér vált az alkalmazást a másodlagos névtér normál hibák fogadásához.

Névterek támogatási párosítva *rendelkezésre állási küldése*. Küldjön a rendelkezésre állási megőrzi az üzenetek küldése. Küldési rendelkezésre állási használatához az alkalmazás a következő követelményeknek kell megfelelniük:

1. Az elsődleges névtér csak érkező üzenetek.
2. Egy adott várólistára küldött üzenetek vagy témakör előfordulhat, hogy nem megfelelő sorrendben érkeznek.
3. Üzenet-munkameneten belül előfordulhat, hogy érkezésekor sorrendje. Ez a normál működésének munkamenetek szünetet. Ez azt jelenti, hogy az alkalmazás a munkamenetek logikailag üzenetek használja.
4. A munkamenet-állapot csak az elsődleges névtéren lesz tartva.
5. Elsődleges queue ismét online elérhető, és indítsa el az üzenetek fogadását, mielőtt a másodlagos várólista összes üzenet kézbesíti az elsődleges queue.

Az alábbi szakaszok ismertetik az API-k, az API-k hogyan valósíthatók meg, és látható mintakód, melyet a következő funkciót használja. Vegye figyelembe, hogy vannak-e ez a szolgáltatás társított számlázási megvalósítását.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>A MessagingFactory.PairNamespaceAsync API
A párhuzamos névterek funkció tartalmazza a [PairNamespaceAsync] [ PairNamespaceAsync] metódust a [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] osztály:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

A feladat befejezését követően a névtér párosítás egyben elkészültét és bármely által kezelhető [MessageReceiver][MessageReceiver], [QueueClient][QueueClient], vagy [TopicClient] [ TopicClient] létrehozni a [MessagingFactory] [ MessagingFactory] példány. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] párosítása, amely különböző típusú érhetők az alaposztály van egy [MessagingFactory] [ MessagingFactory] objektum. Jelenleg az egyetlen származtatott osztály ilyen nevű [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], amely megvalósítja a küldési rendelkezésre állási követelményeinek. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] tartozik egy konstruktorokat, amelyek létrehozása másik kiszolgálón. A konstruktor megnézi a legtöbb paraméterekkel rendelkező, a más konstruktorok viselkedését tudja értelmezni.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ezek a paraméterek jelentése a következő:

* *secondaryNamespaceManager*: egy inicializált [NamespaceManager] [ NamespaceManager] a másodlagos névtér-példányt, amely a [PairNamespaceAsync] [ PairNamespaceAsync] módszer segítségével állítsa be a másodlagos névtér. A névtér Managert használja a következő névtérbeli várólisták listájának, és ellenőrizze, hogy létezik-e a szükséges várakozó várólisták. Ha ezek a várólisták nem léteznek, a rendszer hozza létre őket. [NamespaceManager] [ NamespaceManager] képes létrehozni a jogkivonatot az szükséges a **kezelése** jogcímek.
* *messagingFactory*: A [MessagingFactory] [ MessagingFactory] a másodlagos névtér-példányt. A [MessagingFactory] [ MessagingFactory] objektum küldéséhez használt és, ha a [EnableSyphon] [ EnableSyphon] tulajdonsága **igaz**, a várakozó várólisták érkező üzenetek fogadására.
* *backlogQueueCount*: létrehozásához várakozó várólisták száma. Ennek az értéknek legalább 1 kell lennie. Üzeneteket küld a várakozó fájlok számát, ha ezek a várólisták egyik véletlenszerűen választott. Ha az érték 1, majd csak egy sor valaha is használható. Ez akkor fordul elő, és az egyik várakozó várólista hibák állít elő, amikor az ügyfél nem próbálja meg egy másik várakozó várólista, és előfordulhat, hogy nem sikerült elküldeni az üzenetet. Azt javasoljuk, hogy az érték néhány nagyobb értéket, és az alapértelmezett érték 10. Attól függően, hogy mennyi adatot naponta elküldi az alkalmazás magasabb vagy alacsonyabb értékre módosíthatja. Minden tartalék várólista legfeljebb 5 GB üzenetek tárolására képes.
* *failoverInterval*: időintervalluma fogad hibák az elsődleges névtéren minden egyetlen entitás átállítaná a másodlagos névtér előtt időt. A feladatátvétel entitás által entitás alapon. Egyetlen névtér az entitás gyakran megmarad a Service Bus egy másik csomópontja. Egy entitás hibája miatt nem feltétlenül jelenti azt egy másik hibát. Ez az érték állíthatja [System.TimeSpan.Zero] [ System.TimeSpan.Zero] így a másodlagos közvetlenül az első, nem átmeneti hiba után. A feladatátvételi időzítő kiváltó hibák a [MessagingException] [ MessagingException] , amelyben a [IsTransient] [ IsTransient] tulajdonság értéke HAMIS, vagy egy [System.TimeoutException][System.TimeoutException]. Más kivételek, például a [UnauthorizedAccessException] [ UnauthorizedAccessException] , nem váltják ki feladatátvételi, mert azt jelzik, hogy az ügyfél helytelenül van konfigurálva. A [ServerBusyException] [ ServerBusyException] nem nem ok feladatátvétel miatt a megfelelő mintát Várjon 10 másodpercet, majd újból elküldeni az üzenetet.
* *enableSyphon*: azt jelzi, hogy az adott párosítás kell is syphon üzenetek a másodlagos névtér vissza az elsődleges névtér. Általánosságban elmondható, alkalmazásokat, amelyek üzeneteket küldeni kell beállítania ezt az értéket **hamis**; alkalmazásokat, amelyek az üzenetek fogadásához kell beállítania ezt az értéket **igaz**. Ennek oka, hogy gyakran, nincsenek üzenetküldők-nál kevesebb üzenetet fogadó. Fogadók számától függően beállíthatja úgy a egy egyetlen alkalmazáspéldány Szifonos feladatok kezelésére. Segítségével számos fogadók számlázási hatással van a minden várakozó várólista.

A kód használatához hozzon létre egy elsődleges [MessagingFactory] [ MessagingFactory] példány, egy másodlagos [MessagingFactory] [ MessagingFactory] példány, egy másodlagos [NamespaceManager] [ NamespaceManager] példányt, és egy [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] példány. A hívás más dolga, mint a következő lehet:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Amikor a tevékenység által visszaadott a [PairNamespaceAsync] [ PairNamespaceAsync] metódus befejeződött, minden rendben van állítva, és készen áll a használatra. Előtt a feladat lett visszaadva, akkor előfordulhat, hogy nem befejeződött az összes háttérben szükséges munka a párosítás működnek megfelelően. Emiatt nem webalkalmazásokba üzenetküldésre, amíg a feladat adja vissza. Hiba történt, például rossz hitelesítő adatait, vagy nem sikerült létrehozni a várakozó várólisták, ha a kivételek fog jelezni, a feladat befejeződése után. Ha ismét a feladat, győződjön meg arról, hogy a várólisták található, vagy létrehozott megvizsgálásával a [BacklogQueueCount] [ BacklogQueueCount] tulajdonságát a [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] példány. Az előzőekben látható kód a művelet a következőképpen jelenik meg:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus üzenetkezelés aszinkron alapjait, olvassa el, további részleteket [névterek párosítva][paired namespaces].

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
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
