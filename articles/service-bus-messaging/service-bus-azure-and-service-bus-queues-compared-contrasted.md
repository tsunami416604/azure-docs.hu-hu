---
title: "Az Azure tárolási sorok és a Service Bus-üzenetsorok képest és ellentétben |} Microsoft Docs"
description: "Elemzi az Azure által kínált várólisták két típusú közötti Hasonlóságok és különbségeket."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/08/2017
ms.author: sethm
ms.openlocfilehash: d564f3974b2bc6355bb5dc5320a5193fe3c196af
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Tárolási sorok és a Service Bus-üzenetsorok - képest és ellentétben
Ez a cikk elemzi a különbségek és a Microsoft Azure által kínált ma várólisták kétféle Hasonlóságok: tárolási sorok és a Service Bus-üzenetsorok. Ezekre az információkra alapozva összehasonlíthatók az egyes technológiák, és megfontoltabb döntéseket lehet hozni arról, hogy melyik megoldás felel meg leginkább az igényeknek.

## <a name="introduction"></a>Bevezetés
Azure várólista mechanizmusok két típusokat támogatja: **tárüzenetsort** és **Service Bus-üzenetsorok**.

**Tárolási sorok**, amelyek részei a [az Azure storage](https://azure.microsoft.com/services/storage/) infrastruktúra, a szolgáltatás egy egyszerű, REST-alapú GET/PUT/betekintés felületet biztosít a megbízható, állandó üzenetkezelési belül, és a szolgáltatások közötti.

**Service Bus-üzenetsorok** részei egy szélesebb körű [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastruktúra, amely támogatja továbbá az közzétételi/előfizetési queuing, és további speciális integrációs kombinációját. A Service Bus üzenetsorok/témakörök/előfizetések kapcsolatos további információkért tekintse meg a [Service Bus áttekintése](service-bus-messaging-overview.md).

Léteznek két üzenetsor-kezelési technológia egyszerre, tárüzenetsort először egy dedikált várólista tárolási mechanizmus épülő Azure Storage szolgáltatás lett bevezetve. Service Bus-üzenetsorok úgy tervezték, hogy az alkalmazások vagy az alkalmazás-összetevők, amelyek több kommunikációs protokollokat, adategyezményeinek, megbízhatósági tartományok, illetve hálózati környezetben előfordulhat, hogy több integrálja a szélesebb körű üzenetkezelési infrastruktúra épülnek.

## <a name="technology-selection-considerations"></a>Technológia kiválasztása kapcsolatos szempontok
Tárolási sorok és a Service Bus-üzenetsorok a message queuing szolgáltatás jelenleg a Microsoft Azure által kínált implementációja. Mindegyiknek némileg eltérő szolgáltatáskészlet, ami azt jelenti, válassza ki az egyiket, vagy egyaránt, az adott megoldás vagy üzleti vagy műszaki problémamegoldó igényeitől függően.

Annak meghatározása, hogy mely üzenetsor-kezelési technológia célja egy adott megoldás megfelel, megoldás mérnökök és a fejlesztők figyelembe kell vennie ezek a javaslatok. További részletekért tekintse meg a következő szakaszban.

Megoldás felelős mérnök vagy fejlesztők **érdemes használni a tárolási sorok** során:

* Az alkalmazás több mint 80 GB üzenetet kell tárolnia a sorhoz, ahol az üzenetek élettartama rövidebb, mint 7 nap.
* Az alkalmazás szeretné nyomon követni a dolgoz fel üzenetet a várólistában belül. Ez akkor hasznos, ha a munkavégző, egy üzenet feldolgozása összeomlik. A későbbi feldolgozók származó, ahol abbahagyta az előzetes worker folytatásához használhatja ezt az információt.
* Az összes, szemben a várólisták végrehajtott tranzakciók ügyféloldali kiszolgálónapló van szüksége.

Megoldás felelős mérnök vagy fejlesztők **érdemes használni a Service Bus-üzenetsorok** során:

* A megoldás fogadhat üzeneteket, és kérdezze le a várólista nélkül kell lennie. A Service busszal, ez elérhető révén a hosszú lekérdezési fogadási művelethez, az TCP-alapú protokoll, amely támogatja a Service Bus használatával.
* A megoldáshoz szükségesek a várólistában, adja meg a garantált első-a-first-(FIFO) rendelt kézbesítését.
* Azt szeretné, hogy egy szimmetrikus tapasztalattal az Azure-ban és a Windows Server (magánfelhő). További információkért lásd: [Service Bus Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).
* A megoldás ismétlődő automatikus felderítésének támogatásához képesnek kell lennie.
* Az alkalmazás folyamat üzenetek párhuzamos hosszan futó adatfolyamokként (üzenetek társítva egy adatfolyam használata a [munkamenet-azonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) az üzenetben tulajdonság). Ebben a modellben minden csomópontja a fogyasztó alkalmazás sávszélességen adatfolyamok üzenetek szemben. Amikor egy felhasználó csomópont rendelkezésére áll az adatfolyam, a csomópont ellenőrizheti az alkalmazás adatfolyam állapotot tranzakciók állapotának.
* A megoldás szükséges tranzakciós viselkedését és atomicity küldésekor vagy fogadásakor több üzenetet az üzenetsorból.
* Az idő-live (TTL) jellemző az alkalmazás-specifikus munkaterhelés lépheti túl a 7 napon belül.
* Az alkalmazás-kezelési üzenetek azért lépheti túl a 64 KB-os kezeli, de valószínűleg nem megközelítés a 256 KB-os korlátozza.
* Adja meg a küldő és a szerepköralapú hozzáférés-modell, a várólisták és a különböző jogok vagy engedélyek követelmény foglalkozik.
* A várólista-méret nem fog 80 GB-nál nagyobb.
* Az AMQP 1.0 alapuló üzenetküldési protokoll használni kívánt. Az AMQP kapcsolatos további információkért lásd: [Service Bus AMQP áttekintése](service-bus-amqp-overview.md).
* Akkor is envision egy esetleges áttelepítés egy üzenet exchange mintát, amely lehetővé teszi a részét vagy egészét független példányt kap, amelyek zökkenőmentes integráció további fogadók (előfizető) várólista-alapú pont-pont kommunikáció a várólistára küldött üzenetek. A közzététel/előfizetés funkció alapértelmezés szerint a Service Bus által biztosított az utóbbi hivatkozik.
* A kezelési megoldás támogatja a "Legtöbb-visszaküldést" szállítási garancia nélkül hozhat létre a további összetevőket kell képesnek kell lennie.
* Szeretné tudni közzétételére és felhasználására kötegek üzenetek.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Tárolási sorok és a Service Bus-üzenetsorok összehasonlítása
Az alábbi szakaszokban található táblázatokat adja meg a várólista szolgáltatások logikai csoportját, és lehetővé teszik, hogy egy pillanat alatt hasonlítsa össze az Azure Storage várólisták és a Service Bus-üzenetsorok képességeit.

## <a name="foundational-capabilities"></a>Eligazodást képességek
Ez a szakasz néhány olyan tárolási sorok és a Service Bus-üzenetsorok által biztosított alapvető üzenetsor-kezelési funkciója hasonlítja össze.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus által kezelt üzenetsorok |
| --- | --- | --- |
| Rendezés növekvő |**Nem** <br/><br>További információkért lásd: első Megjegyzés a "További információk" című részét.</br> |**Igen – első-First Out (FIFO)**<br/><br>(révén üzenetkezelési munkamenetek) |
| Garantált kézbesítés |**A legalább egyszeri** |**A legalább egyszeri**<br/><br/>**A legtöbb-visszaküldést** |
| Támogatási atomi művelet |**Nem** |**Igen**<br/><br/> |
| Viselkedés fogadása |**A nem blokkoló**<br/><br/>(befejezése azonnal, ha nincs új üzenet megtalálható) |**Időtúllépés vagy anélkül blokkolása**<br/><br/>(kínál, hosszú lekérdezési, vagy a ["Üstökös technika"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**A nem blokkoló**<br/><br/>(.NET használatával felügyelt API csak) |
| Leküldéses stílusú API |**Nem** |**Igen**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) és **OnMessage** munkamenetek .NET API-t. |
| Fogadás módban |**Betekintés & bérleti** |**Betekintés & zárolása**<br/><br/>**Kap & törlése** |
| Kizárólagos hozzáférési mód |**Címbérlet-alapú** |**Zárolási-alapú** |
| Bérleti/zárolást időtartama |**30 másodperc (alapértelmezett)**<br/><br/>**7 nap (maximum)** (újítsa meg, vagy egy üzenet bérleti használatával kiadási a [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API.) |**60 másodperc (alapértelmezett)**<br/><br/>Egy üzenet zárolási használatával megújíthatják a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| Bérleti/zárolást pontosság |**Üzenet szint**<br/><br/>(minden üzenetet is rendelkezik különböző időtúllépési érték, amely frissítheti szükség szerint használatával az üzenet feldolgozása közben a [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API) |**Várólista szintjét**<br/><br/>(Minden várólistának vonatkozik. az üzenetek zárolási pontossága, de megújíthatják a zárolás használatával a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Kötegelni fogadása |**Igen**<br/><br/>(explicit módon megadni üzenetek száma történő beolvasásra legfeljebb 32 üzenetek) |**Igen**<br/><br/>(implicit módon engedélyezése az előzetes betöltési tulajdonság vagy explicit módon tranzakciók) |
| A kötegelt küldése |**Nem** |**Igen**<br/><br/>(révén tranzakciók vagy ügyféloldali kötegelés) |

### <a name="additional-information"></a>További információ
* Tárolási sorok üzenetek általában első-az első-kimenő, de időnként el nem megfelelő sorrendben; például amikor egy állapotüzenet látható időkorlát tartama érvényessége lejár (például miatt a feldolgozás során összeomló ügyfélalkalmazás). A láthatósági időkorlátot lejártakor az üzenet lesz látható a várakozási sorban a Created azt egy másik feldolgozónak meg újra. Ezen a ponton az újonnan megjelenő üzenet előfordulhat, hogy kell helyezni a (kell várólistából kivéve újra) sor után egy üzenetet, amely után az eredetileg a várólistán lévő.
* A Service Bus-üzenetsorok garantált FIFO mintát üzenetkezelési munkamenetek használatát igényli. Abban az esetben, ha az alkalmazás összeomlik érkezett egy üzenet feldolgozása közben a **Belepillantás & zárolása** módban, a várólista címzett fogad el egy üzenetkezelési munkamenet a következő alkalommal elindul, a hibás üzenettel után az idő-live (TTL) időszakának lejártáig.
* Tárolási sorok szabványos üzenetsor-kezelési helyzetek feltételeinek megteremtésére készültek, például a méretezhetőséget és a hibák, tolerancia növeléséhez megszakító alkalmazás-összetevők simítás, és a feldolgozási munkafolyamatok kialakítását betöltése.
* A Service Bus-üzenetsorok a *: legalább egyszeri* garantált kézbesítés. Emellett a *legtöbb-visszaküldést* szemantikai munkamenet-állapot tárolásához az alkalmazásállapot használatával, és a tranzakciók i üzeneteket fogadni, és a munkamenet-állapot frissítése támogatja.
* Tárolási sorok biztosít egy egységes és egységes programozási modell várólisták, a táblák és a Blobok – a fejlesztők számára, és műveletek csoportjai.
* Service Bus-üzenetsorok támogatást nyújt a helyi tranzakciók az adott környezetben, egy adott sorba.
* A **fogadásához és törléséhez** Service Bus által támogatott mód lehetővé teszi a csökkentse az üzenetkezelési műveletek száma (és a kapcsolódó költségeket) süllyesztett kézbesítési megbízhatósági cserébe.
* Tárolási sorok címbérleteket biztosíthat a kiterjesztheti a bérletek üzenetek. Ez lehetővé teszi, hogy a dolgozók üzenetek rövid címbérlet karbantartása. Így ha egy munkavégző összeomlik, az üzenet gyorsan feldolgozható újra egy másik feldolgozónak. Emellett egy munkavégző kiterjesztheti a címbérlet megújuljon az üzenet, ha a feldolgozási hosszabb, mint az aktuális bérleti ideje.
* Tárolás az üzenetsorok elsőnek a láthatósági időkorlátot használható beállítása után a enqueuing vagy üzenet üzenetmozgatót. Emellett egy üzenet frissítés futásidőben különböző bérleti értékekkel, és különböző értékeket frissítés ugyanazon a várólistán lévő üzenetek között. A Service Bus zárolási időtúllépések határozzák meg a várólista metaadatok; azonban megújíthatják a zárolás meghívásával a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metódust.
* A maximális időkorlátot egy blokkolja a fogadási művelethez a Service Bus-üzenetsorok nem 24 nap. REST-alapú időtúllépések van azonban, 55 másodpercben maximális értéket.
* Ügyféloldali kötegelés Service Bus által biztosított lehetővé teszi a várólista ügyfél több üzenetet kötegelt be egy küldési művelet. Kötegelés érhető el csak aszinkron műveletek.
* Szolgáltatások, mint a tároló (ha virtualizálhatja a fiókok további) és korlátlan várólisták a 200 TB-os felső határ teszik ideális platform a Szolgáltatottszoftver-szolgáltatók.
* Tárolási sorok adjon meg egy rugalmas és performant delegált hozzáférés-vezérlési mechanizmus.

## <a name="advanced-capabilities"></a>Speciális képességek
Ez a szakasz a tárolási sorok és a Service Bus-üzenetsorok által biztosított speciális képességek hasonlítja össze.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus által kezelt üzenetsorok |
| --- | --- | --- |
| Ütemezett kézbesítését |**Igen** |**Igen** |
| Automatikus halott levelek kezelése |**Nem** |**Igen** |
| Várólista élő idő érték növelése |**Igen**<br/><br/>(keresztül láthatósági időkorlátot helybeni frissítése) |**Igen**<br/><br/>(egy dedikált API-függvénye keresztül megadott) |
| Elhalt üzenet támogatása |**Igen** |**Igen** |
| Frissítés helyben |**Igen** |**Igen** |
| Kiszolgálóoldali tranzakció napló |**Igen** |**Nem** |
| Storage mérőszámainak |**Igen**<br/><br/>**Metrikák MINUTE**: biztosít a valós idejű metrikák rendelkezésre állás érdekében TP-k, az API-hoz a számát, a hiba száma és további, az összes valós idejű (percenként összesítve, és az éles környezetben csak történtekről néhány percen belül jelentett a hívás. További információkért lásd: [kapcsolatos Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Igen**<br/><br/>(tömeges lekérdezések meghívásával [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Felügyeleti állapot |**Nem** |**Igen**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatikus-üzenettovábbítással |**Nem** |**Igen** |
| Várólista függvény kiürítése |**Igen** |**Nem** |
| Üzenet csoportok |**Nem** |**Igen**<br/><br/>(révén üzenetkezelési munkamenetek) |
| Alkalmazásállapot üzenet csoportonként |**Nem** |**Igen** |
| Kettős észlelés |**Nem** |**Igen**<br/><br/>(a küldő oldalon konfigurálható) |
| Üzenet csoportok tallózása |**Nem** |**Igen** |
| Üzenet munkamenet-azonosító szerint beolvasása |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Mindkét üzenetsor-kezelési technológiák segítségével egy üzenetet, amely lehet ütemezve egy későbbi időpontban.
* Várólista automatikus-továbbítási lehetővé teszi, hogy a több ezer automatikus-előre az üzenetek egy adott sorba, ahol a fogadó alkalmazás használ fel az üzenet-várólistákból. A mechanizmus segítségével biztonsági elérése, a folyamatot szabályozhatja, és mindegyik üzenet közzétevő közötti tárolási elkülönítése.
* Tárolási sorok támogatásához frissítése message tartalmat. Is használhatja ezt a funkciót tárolásakor állapotadatokat és a növekményes folyamatban lévő frissítések az üzenetet, hogy a legutóbbi ellenőrzőponttól, nem nulláról dolgozható. A Service Bus-üzenetsorok az azonos forgatókönyv üzenet munkamenetek használatával engedélyezheti. Munkamenetek lehetővé teszik a Mentés és a feldolgozási állapotának beolvasása (használatával [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Dead megnevezéséhez](service-bus-dead-letter-queues.md), amely csak a Service Bus-üzenetsorok, által támogatott hasznos lehet üzeneteket, amelyek nem dolgozható fel sikeresen a fogadó alkalmazás, vagy ha üzenetek nem érhető el a rendeltetési miatt lejárt idő a működés közbeni (elkülönítése TTL) tulajdonsága. Az élettartam értéke határozza meg, mennyi ideig üzenet marad a várólistán. A Service busszal az üzenet átkerül egy különleges várólista $DeadLetterQueue meghívva, amikor a TTL időszak lejár.
* "Poison" üzenetek megkeresése a tárüzenetsort, amikor egy üzenet üzenetmozgatót az alkalmazás keressen-e a [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) az üzenet tulajdonságát. Ha **DequeueCount** nagyobb, mint a megadott küszöbértéket, az alkalmazás az üzenet áthelyezése egy alkalmazás által meghatározott "" halottlevél.
* Tárolási üzenetsorok lehetővé teszik az beszerzése az összes, szemben a várólista, valamint összesített metrikák végrehajtott tranzakciók részletes naplója. Mindkét lehetőség Hibakeresés és az ismertetése, hogy az alkalmazás által tárüzenetsort hasznosak. Hasznos, ha az alkalmazás teljesítményének hangolása és -üzenetsorok használatával járó költségek csökkentése szerepelnek.
* "Üzenet munkamenetek" a Service Bus által támogatott fogalma lehetővé teszi, hogy a megadott fogadó, ilyenkor pedig létrejön az üzenetek és a megfelelő fogadók között egy munkamenet-szerű affinitás társítani kell egy adott logikai csoportjához tartozó üzenetek. A funkció a Service Bus speciális beállításával engedélyezheti a [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) üzenetben tulajdonság. Fogadók figyelni az egy adott munkamenet-Azonosítót, majd, hogy a megadott munkamenet-azonosítót.
* A Service Bus-üzenetsorok által támogatott automatikusan ismétlődést észlelési funkció eltávolítja az ismétlődő üzenetsor vagy témakör értéke alapján küldött üzenetek a [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) tulajdonság.

## <a name="capacity-and-quotas"></a>Kapacitás és a kvóták
Ez a szakasz összehasonlítja tárolási sorok és a Service Bus-üzenetsorok szempontjából [kapacitás és a kvóták](service-bus-quotas.md) , amely előfordulhat, hogy érvényes.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus által kezelt üzenetsorok |
| --- | --- | --- |
| A várólista maximális hossza |**500 TB**<br/><br/>(csak egy [tárfiókok kapacitásával egyetlen](../storage/common/storage-introduction.md#queue-storage)) |**1 GB-os 80 GB**<br/><br/>(a várólista létrehozása után definiált és [particionálás engedélyezése](service-bus-partitioning.md) – a "További információk" című rész) |
| Maximális üzenetméret |**64 KB**<br/><br/>(48 KB használatakor **Base64** kódolás)<br/><br/>Azure nagy üzeneteket is támogatja üzenetsorokat és blobokat – ekkor is sorba helyezni a kombinálásával legfeljebb 200 GB-ot csak egy elemet. |**256 KB-os** vagy **1 MB**<br/><br/>(beleértve a fejléc és a szövegtörzset, maximális fejléc mérete: 64 KB).<br/><br/>Függ a [szolgáltatásréteg](service-bus-premium-messaging.md). |
| Maximális üzenet TTL tulajdonsága |**7 nap** |**TimeSpan.Max** |
| Sorok maximális száma |**Korlátlan** |**10,000**<br/><br/>(egyes szolgáltatásnévtér) |
| Egyidejű ügyfelek maximális száma |**Korlátlan** |**Korlátlan**<br/><br/>(100 egyidejű kapcsolat korlátai csak vonatkozik a TCP protokoll-alapú kommunikációt) |

### <a name="additional-information"></a>További információ
* A Service Bus várólista méretkorlátait érvénybe lépteti. A várólista maximális hossza a várólista létrehozásakor van megadva, és értéke 1 és 80 GB közötti lehet. A várólista létrehozása a várólista mérete értéke elérésekor, további bejövő üzenetek vissza kell utasítani, és a kivételt a hívó kód fogadja. A Service Bus kvóták kapcsolatos további információkért lásd: [Service Bus kvóták](service-bus-quotas.md).
* Az a [Standard csomagra](service-bus-premium-messaging.md), 1, 2, 3, 4 vagy 5 GB méretű (az alapértelmezett érték 1 GB-os) a Service Bus-üzenetsorok is létrehozhat. A prémium szinten lévő várólistákat is létrehozhat és 80 GB-nál. A Standard szint, a particionálás engedélyezve (utóbbi az alapértelmezett érték), a Service Bus 16 partíciók hoz létre minden egyes megadott GB. Így, ha létrehoz egy sort, amely 5 GB-nál, 16 partíciókat a várólista maximális hossza válik (5 * 16) = 80 GB. Megjelenik a particionált üzenetsor vagy témakör maximális mérete alapján a bejegyzés a [Azure-portálon][Azure portal]. A prémium csomagban csak 2 partíció / várólista jönnek létre.
* Tárolási sorok, ha az üzenet tartalma nem XML-biztonságos, majd azt kell **Base64** kódolású. Ha Ön **Base64**-kódolni az üzenetet, a felhasználó hasznos lehet akár 48 KB 64 KB-os helyett.
* A Service Bus-üzenetsorok, az egyes üzeneteket tárolja a sorhoz két részből áll: egy fejléc és a szervezet. Az üzenet teljes mérete nem haladhatja meg a szolgáltatási réteg által támogatott maximális méretét.
* Ha az ügyfelek a TCP protokollon keresztül kommunikálnak a Service Bus-üzenetsorok, egy Service Bus-üzenetsorral létesített egyidejű kapcsolatok maximális számának korlátozódik 100. Ez a szám megosztott küldők és a fogadók között. Ez a kvóta elérésekor, további kapcsolatokat későbbi kérelmek vissza kell utasítani, és egy kivételt a hívó kód fogadja. Ezt a határt nem írják elő a REST-alapú API-jával várólistákhoz csatlakozó ügyfelek.
* Ha több mint 10 000 várólistákból egy Service Bus-névtér van szüksége, lépjen kapcsolatba az Azure támogatási csapata, és korlátozás megnövelésére. A Service busszal 10 000 várólisták felüli, is létrehozhat további névteret használja a [Azure-portálon][Azure portal].

## <a name="management-and-operations"></a>Felügyelete és műveletei
Ez a szakasz a tárolási sorok és a Service Bus-üzenetsorok által nyújtott szolgáltatások hasonlítja össze.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus üzenetsorok |
| --- | --- | --- |
| Felügyeleti protokoll |**REST-HTTP/HTTPS-KAPCSOLATON keresztül** |**REST-HTTPS-KAPCSOLATON keresztül** |
| Futásidejű protokoll |**REST-HTTP/HTTPS-KAPCSOLATON keresztül** |**REST-HTTPS-KAPCSOLATON keresztül**<br/><br/>**Az AMQP 1.0-s szabvány (TCP with TLS)** |
| .NET API |**Igen**<br/><br/>(.NET tárolási ügyfél API-ja) |**Igen**<br/><br/>(.NET a Service Bus API) |
| Natív C++ |**Igen** |**Igen** |
| Java API |**Igen** |**Igen** |
| A PHP API |**Igen** |**Igen** |
| NODE.js API |**Igen** |**Igen** |
| Tetszőleges metaadatok támogatása |**Igen** |**Nem** |
| Várólista elnevezési szabályok |**Legfeljebb 63 karakter**<br/><br/>(A várólistacímke betűjét kisbetűnek kell lennie.) |**Legfeljebb 260 karakter**<br/><br/>(Várólista elérési útja és neve nem különböztetik meg.) |
| Várólista hossza függvény beolvasása |**Igen**<br/><br/>(Becsült érték üzenetek nélkül törlődnek az Élettartamon túl lejár. Ha.) |**Igen**<br/><br/>(Pontos, időpontban érték.) |
| Betekintés a függvényt |**Igen** |**Igen** |

### <a name="additional-information"></a>További információ
* Tárolási sorok támogatást nyújt a várólista leírás név/érték párok formájában alkalmazható tetszőleges attribútumokkal.
* Mindkét várólista technológiák lehetővé teszik a üzenet Belepillantás anélkül, hogy zárolja, ami hasznos lehet egy várólista Explorerre/eszköz végrehajtása során.
* A Service Bus .NET közvetítőalapú üzenettovábbítás API-k használja ki az kétirányú TCP-kapcsolatok a HTTP Protokollon keresztül REST képest nagyobb teljesítményt, és az AMQP 1.0 szabványos protokollt támogatják.
* Tárolási sorok nevének 3 – 63 karakter hosszú lehet, kisbetűket, számokat és kötőjeleket tartalmazhat. További információkért lásd: [elnevezési üzenetsorok és metaadatok](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* A Service Bus Várólistanevek legfeljebb 260 karakter hosszú lehet, és kevésbé korlátozó elnevezési szabályokat. A Service Bus Várólistanevek betűket, számokat, pontokat, kötőjeleket és aláhúzásjeleket tartalmazhat.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Ez a szakasz ismerteti tárolási sorok és a Service Bus-üzenetsorok által támogatott hitelesítési és engedélyezési szolgáltatásokat.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus által kezelt üzenetsorok |
| --- | --- | --- |
| Hitelesítés |**Szimmetrikus kulcs** |**Szimmetrikus kulcs** |
| Biztonsági modell |Delegált hozzáférést SAS-tokenje keresztül. |SAS |
| Identitás-összevonási szolgáltató |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Minden kérelemnél bármelyik, az üzenetsor-kezelési technológiák hitelesíteni kell. A névtelen hozzáférés a nyilvános várólisták nem támogatottak. Használatával [SAS](service-bus-sas.md), azzal, hogy közzétesz egy csak írható SAS, csak olvasható SAS vagy akár egy teljes hozzáférési SAS ebben a forgatókönyvben meg lehet oldani.
* A hitelesítési sémát megadott tároló várólisták magában foglalja a szimmetrikus kulcs, amely a kivonat-alapú üzenethitelesítési kód (HMAC), használja az SHA-256 algoritmus számított és kódolása egy **Base64** karakterlánc. A megfelelő protokollal kapcsolatos további információkért lásd: [az Azure Storage szolgáltatásainak hitelesítése](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-üzenetsorok egy szimmetrikus kulcsok használata hasonló modellt támogatja. További információkért lásd: [megosztott hozzáférési aláírást hitelesítést a Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Összegzés
Való bemutatják, a két technológia, amelyet lesz több tájékozott döntést a várólista technológiákat használ, és mikor. A döntést a tárüzenetsort vagy a Service Bus-üzenetsorok használatával egyértelműen számos tényezőtől függ. Ezek a tényezők függ, hogy fokozottan az alkalmazás és az architektúra egyéni igényekhez. Ha az alkalmazás már a Microsoft Azure legfontosabb funkcióit, célszerű lehet válassza ki a tárolási sorok, különösen akkor, ha az alapvető kommunikáció és az üzenetkezelési szolgáltatások vagy 80 GB-nál nagyobb lehet szükség várólisták között van szüksége.

Service Bus-üzenetsorok, adjon meg egy számot a speciális szolgáltatások, például munkamenetek, tranzakciók, mert ismétlődő automatikus észlelési kézbesítetlen levelek kezelése és a tartós közzétételi/előfizetési képességeket, akkor lehet, hogy egy előnyben részesített választott hibrid készítésekor alkalmazás, vagy ha az alkalmazás egyéb szükséges ezeket a szolgáltatásokat.

## <a name="next-steps"></a>További lépések
A következő cikkekben további útmutatás és tárüzenetsort vagy a Service Bus-üzenetsorok használatával kapcsolatos információ.

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Queue Storage szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Gyakorlati tanácsok a teljesítménnyel kapcsolatos fejlesztések használatával a Service Bus közvetítőalapú üzenettovábbítás](service-bus-performance-improvements.md)
* [Introducing üzenetsorok és témakörök az Azure Service Bus (blogbejegyzés)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Service Bus a fejlesztői útmutató](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Az Üzenetsor-kezelés szolgáltatás használatával az Azure-ban](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

