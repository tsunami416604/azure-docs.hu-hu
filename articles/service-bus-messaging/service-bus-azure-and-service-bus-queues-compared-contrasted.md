---
title: Az Azure Storage-üzenetsorok és Service Bus-üzenetsorok összehasonlítása és összehasonlítása |} A Microsoft Docs
description: Kétféle típusú Azure-ban elérhető várólisták közötti különbségeket és hasonlóságokat elemzi.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/05/2018
ms.author: spelluru
ms.openlocfilehash: 49694780f4fe32f8068c1d7c4cc87cfb88f77fe5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700980"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Tároló-üzenetsorok és Service Bus-üzenetsorok összehasonlítása
Ez a cikk elemzi a közötti különbségeket és hasonlóságokat a Microsoft Azure jelenleg elérhető két típusú: tároló-üzenetsorok és Service Bus-üzenetsorok. Ezekre az információkra alapozva összehasonlíthatók az egyes technológiák, és megfontoltabb döntéseket lehet hozni arról, hogy melyik megoldás felel meg leginkább az igényeknek.

## <a name="introduction"></a>Bevezetés
Az Azure két típusú várólista mechanizmusokat támogatja: **tárolási üzenetsorok** és **Service Bus-üzenetsorok**.

**Tárolási üzenetsorok**, amelyek részét képezik a [az Azure storage](https://azure.microsoft.com/services/storage/) infrastruktúra, a szolgáltatás egy egyszerű REST-alapú GET és PUT/BETEKINTÉSI felületet biztosít a megbízható, állandó belül, és a szolgáltatások közötti üzenetküldési.

**Service Bus-üzenetsorok** része egy szélesebb körű [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastruktúra, amely támogatja az üzenetsor-kezelési és közzétételi/előfizetési és speciális integrációs minták. További információ a Service Bus üzenetsorok és üzenettémák, előfizetések: a [a Service Bus áttekintése](service-bus-messaging-overview.md).

Bár mindkét üzenetsor-kezelési technológiák létezhetnek, tároló-üzenetsorok először épülő Azure Storage szolgáltatás dedikált várólista tárolási mechanizmusként lettek bevezetve. Service Bus-üzenetsorok a szélesebb körű üzenetküldési infrastruktúra, amely alkalmazások és alkalmazás-összetevők, magában foglalhat több kommunikációs protokollokat, adatok szerződések, megbízhatósági tartományok, illetve hálózati környezetek épülnek.

## <a name="technology-selection-considerations"></a>Szempontok a technológia kiválasztása
Tároló-üzenetsorok és a Service Bus-üzenetsorok a message queuing szolgáltatás jelenleg a Microsoft Azure által kínált megvalósításai. Mindegyiknek némileg eltérő szolgáltatáskészlet, ami azt jelenti, hogy válassza ki az egyiket, vagy egyaránt, az adott megoldás vagy üzleti és technikai problémamegoldó igényeitől függően.

Annak meghatározása, hogy melyik üzenetsor-kezelési technológia megfelel az adott megoldás célja, megoldástervezők és fejlesztők vegye figyelembe ezeket a javaslatokat. További részletekért tekintse meg a következő szakaszban.

Fejlesztőként solution architect/ **érdemes használni a Storage szolgáltatás üzenetsoraiba** során:

* Az alkalmazás 80 GB-nál nagyobb üzenetek kell tárolnia, egy üzenetsorban.
* Az alkalmazás szeretné nyomon követni a dolgoz fel üzenetet az üzenetsorba belül. Ez akkor hasznos, ha összeomlik a feldolgozó dolgoz fel üzenetet. Egy későbbi feldolgozó ezt az információt, ahol abbahagyta a korábbi feldolgozó folytatja használhatja.
* Az összes, az üzenetsorok ellen végrehajtott tranzakciók ügyféloldali kiszolgálónaplók van szüksége.

Fejlesztőként solution architect/ **érdemes a Service Bus-üzenetsorok használatával** során:

* A megoldás képes üzeneteket fogadni a várólista lekérdezik nélkül kell lennie. A Service busszal, ez érhető el, a hosszú lekérdezéseket ügyféladatoknak fogadási művelet, amely támogatja a Service Bus TCP-alapú protokollok használatával.
* A megoldáshoz szükséges a várólista adja meg a garantált első-az-érkezési idősorrendben történő (FIFO) kézbesítési rendezve.
* A megoldás támogatásához automatikus duplikáltelem-észlelési képesnek kell lennie.
* Azt szeretné, hogy az alkalmazás üzenetek feldolgozásával párhuzamos hosszú ideig futó adatfolyamként (üzenetek társítva egy adatfolyam használata a [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) tulajdonságot az üzenethez). Ebben a modellben a fogyasztó alkalmazás minden egyes csomópontjának ki is szoríthatják adatfolyamok, üzenetek helyett. Adatfolyam egy fogyasztó csomópontra van megadva, a csomópont is vizsgálja meg a stream alkalmazásállapot tranzakciók használatával állapotát.
* A megoldáshoz szükséges tranzakciós viselkedését és atomitást küldésekor vagy fogadásakor több üzenetet egy üzenetsorból.
* Az alkalmazás kezeli a lehet hosszabb 64 KB-os üzenetet, de nem valószínű megközelítés a 256 KB-os korlátozza.
* A követelmény, hogy az üzenetsorok, és különböző jogokkal és engedélyekkel szerepköralapú hozzáférés-modell biztosítása a küldők és fogadók foglalkozik.
* Az üzenetsor mérete nem nő 80 GB-nál nagyobb.
* Az AMQP 1.0 szabványokon alapuló üzenetkezelési protokollt használni szeretne. Az AMQP kapcsolatos további információkért lásd: [Service Bus AMQP áttekintése](service-bus-amqp-overview.md).
* Egy üzenetsor-alapú pont-pont kommunikáció végleges áttelepítés egy üzenet, amely lehetővé teszi a zökkenőmentes integrációt további fogadók (előfizető), néhány vagy összes független példányt kap, amelyek mindegyike exchange mintát is elképzeli az üzenetsorba küldött üzenetek. A Service Bus által natív módon biztosított közzétételi és előfizetési képesség az utóbbi hivatkozik.
* Az üzenetkezelési megoldás támogatásához anélkül, hogy a további infrastruktúra-összetevőket hozhat létre a ", és a legtöbb – egyszer" kézbesítési garanciával képesnek kell lennie.
* Szeretné tudni közzétételére és felhasználására kötegek üzenetek.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Tároló-üzenetsorok és Service Bus-üzenetsorok összehasonlítása
A következő szakaszok táblázatai adja meg az üzenetsor-szolgáltatások logikai csoportosítása, és lehetővé teszik, egyetlen pillantással lehetőségein Azure Storage-üzenetsorokba és a Service Bus-üzenetsorok összehasonlítása.

## <a name="foundational-capabilities"></a>Alapvető képességek
Ez a szakasz az alapvető üzenetsor-kezelési funkciói biztosítják a tároló-üzenetsorok és Service Bus-üzenetsorok hasonlítja össze.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus üzenetsorok |
| --- | --- | --- |
| Garantált rendezése |**Nem** <br/><br>További információkért tekintse meg az első a "További információ" részben.</br> |**Igen – első-First Out (FIFO)**<br/><br>(ügyféladatoknak munkamenetek üzenetküldése) |
| Kézbesítési garanciával |**A legalább egyszeri** |**A legalább egyszeri**<br/><br/>**A legtöbb – egyszeri** |
| Atomi műveletnek támogatása |**Nem** |**Igen**<br/><br/> |
| Viselkedés fogadása |**Nem kizáró**<br/><br/>(befejezése azonnal Ha új üzenet nem található) |**Időtúllépés rendelkező/nem blokkolja-e**<br/><br/>(kínál hosszú lekérdezések vagy az ["Comet technikával"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Nem kizáró**<br/><br/>(használatával .NET API-t csak kezelt) |
| Leküldéses stílusú API |**Nem** |**Igen**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) és **OnMessage** munkamenetek .NET API-t. |
| Fogadás módban |**Betekintés és lízing** |**Belepillantás & zárolása**<br/><br/>**Kap & törlése** |
| Exkluzív hozzáférési mód |**Címbérlet-alapú** |**Lock-alapú** |
| Bérlet/Zárolás időtartama |**30 másodperces (alapértelmezett)**<br/><br/>**7 nap (maximum)** (újítsa meg, vagy egy üzenet bérleti a kiadási a [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API.) |**60 másodperc (alapértelmezett)**<br/><br/>Egy üzenet zárolási használatával megújíthatják a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API-t. |
| Bérleti/zárolást pontosság |**Üzenet-szint**<br/><br/>(minden üzenetet is rendelkezik különböző időtúllépési érték, amely frissíthet szükséges használatával az üzenet feldolgozása közben a [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API-t) |**Üzenetsor-szint**<br/><br/>(minden egyes üzenetsorhoz van alkalmazandó az összes üzenetet a zárolási pontossága, de megújíthatják a zárolás használata a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Kötegelt fogadása |**Igen**<br/><br/>(explicit módon meghatározva üzenetek száma egy legfeljebb 32 üzenetet üzenetet lekérésekor) |**Igen**<br/><br/>(implicit módon engedélyezi az előzetes betöltési tulajdonság, vagy explicit módon tranzakciók használatával) |
| Kötegelt küldése |**Nem** |**Igen**<br/><br/>(használatával vagy az ügyféloldali kötegelés tranzakciók) |

### <a name="additional-information"></a>További információ
* A tároló-üzenetsorok üzenetek általában első-az-érkezési idősorrendben, de néha lehetnek sorrendben; például amikor egy üzenet láthatósági időkorlát időtartama lejár (például eredményeként egy ügyfélalkalmazás összeomlik a feldolgozása közben). Amikor a láthatósági időkorlát lejár, az üzenet válik újra egy másik feldolgozó eltávolítása a sorból, hogy az üzenetsor látható. Ezen a ponton az újonnan látható üzenetet elhelyezhetők a várólistán (kell el távolítva a sorból újra), egy üzenet, amely eredetileg a várólistán lévő után azt követően.
* A garantált FIFO minta a Service Bus-üzenetsorok üzenet-munkamenetek használatát igényli. Abban az esetben, ha az alkalmazás összeomlik, egy üzenet érkezik feldolgozása közben a **Belepillantás & zárolása** mód, a következő alkalommal, amikor a várólista fogadó fogadja el az üzenetkezelési munkamenet, el is indítja a hibás üzenettel után az idő-az-élettartam (TTL) időszakának lejártáig.
* Tárolási üzenetsorok támogatására készültek standard üzenetsor-kezelési forgatókönyvek, például a méretezhetőséget és a hibák, hibatűrésének növelése megszakító alkalmazás-összetevők betöltése terheléskiegyenlítést, és a feldolgozási munkafolyamatok kialakítását.
* Service Bus-üzenetsorok támogatása a *: legalább egyszeri* kézbesítési garanciával. Emellett a *, és a legtöbb – egyszer* szemantikai munkamenet-állapot tárolásához az alkalmazásállapot használatával és a tranzakciók szolgáltatásfrissítést üzeneteket fogadni, és frissítse a munkamenet-állapot használatával támogatható.
* Tárolási üzenetsorok egységes egy egységes és következetes programozási modell – Blobok, táblák és üzenetsorok fejlesztők számára, és műveleti csapatoknak.
* Service Bus-üzenetsorok támogatást nyújt a helyi tranzakció környezetében egyetlen üzenetsorhoz.
* A **fogadása és törlése** Service Bus által támogatott mód lehetővé teszi a csökkentse az üzenetkezelési műveletek száma (és a kapcsolódó költségek) alacsonyabb kézbesítési garanciával rendelkező engedélyezések.
* Tároló-üzenetsorok lehetővé teszi az üzenetek a bérletek kiterjesztése címbérleteket biztosíthat. Ez lehetővé teszi, hogy a munkavállalók számára, hogy rövid címbérlet üzenetek karbantartása. Így ha egy feldolgozó összeomlik, az üzenet gyorsan újra által feldolgozható egy másik feldolgozó. Emellett egy munkavégző kiterjesztheti a bérletet, egy üzenet, ha azt kell őket feldolgoznia hosszabb, mint a jelenlegi bérleti időt.
* Tárolási üzenetsorok elsőnek a láthatósági időkorlátot is beállítása után a sorba, vagy az üzenet üzenetmozgatót. Emellett egy üzenet frissítése futásidőben különböző bérleti értékekkel, és frissítse a különböző értékeket ugyanazon a várólistán lévő üzenetek között. Az üzenetsor metaadatai; határozzák meg a Service Bus-zárolás időtúllépése azonban megújíthatják a zárolás meghívásával a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metódust.
* A maximális időkorlátot egy blokkolja-e kapni a művelet a Service Bus-üzenetsorok 24 nap. Azonban a REST-alapú időtúllépések kell maximális értéke pedig 55 másodperc.
* Ügyféloldali kötegelés a Service Bus által biztosított lehetővé teszi az ügyfelek a batch-több üzenet küldése egyetlen műveletben várólista. Kötegelés csak akkor használható az aszinkron műveletek.
* Szolgáltatások, például a 200 TB-os határértéket (több, ha a fiókok különböző helyekre történő virtualizálása) tároló-üzenetsorok és a korlátlan várólistákat tenni ideális platformot jelentenek az SaaS-szolgáltatók.
* Tároló-üzenetsorokat biztosít egy rugalmas, és a nagy teljesítményű delegált hozzáférés-vezérlési mechanizmus.

## <a name="advanced-capabilities"></a>Speciális képességek
Ez a szakasz a tároló-üzenetsorok és Service Bus-üzenetsorok által biztosított speciális képességek hasonlítja össze.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus üzenetsorok |
| --- | --- | --- |
| Ütemezett kézbesítés |**Igen** |**Igen** |
| Automatikus kézbesíthetetlen levelek közé helyezésének |**Nem** |**Igen** |
| Üzenetsor-time-to-live érték növelése |**Igen**<br/><br/>(a helyben frissítés láthatósági időkorlát) keresztül |**Igen**<br/><br/>(a megadott függvény dedikált API-n keresztül) |
| Ártalmas üzenetek támogatása |**Igen** |**Igen** |
| Frissítés helyben |**Igen** |**Igen** |
| Kiszolgálóoldali tranzakció – log |**Igen** |**Nem** |
| Storage-mérőszámok |**Igen**<br/><br/>**Metrikák MINUTE**: biztosítja a valós idejű metrikák rendelkezésre állás érdekében TPS, API-hívás számát, a hiba számát és más, valós idejű (percenként összesítve, és az éles környezetben csak történtekről néhány percen belül jelentett. További információkért lásd: [kapcsolatos Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Igen**<br/><br/>(tömeges lekérdezések meghívásával [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Állapotkezelés |**Nem** |**Igen**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Üzenet automatikus továbbítással |**Nem** |**Igen** |
| Üzenetsor függvény törlése |**Igen** |**Nem** |
| Üzenet-csoportok |**Nem** |**Igen**<br/><br/>(ügyféladatoknak munkamenetek üzenetküldése) |
| Üzenet csoportonként alkalmazásállapot |**Nem** |**Igen** |
| Duplikálás észlelése |**Nem** |**Igen**<br/><br/>(a küldő oldalon konfigurálható) |
| Üzenet csoportok tallózása |**Nem** |**Igen** |
| Üzenet-munkamenetek beolvasása azonosító alapján |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Mindkét üzenetsor-kezelési technológiák segítségével egy üzenetet egy későbbi időpontban történő továbbítását ütemezni.
* Üzenetsor automatikus továbbítással lehetővé teszi a több ezer üzenetsorok automatikus továbbítása az üzeneteket, amelyről a fogadó alkalmazásnak használ fel az üzenetet egy üzenetsorba. Ez a mechanizmus használatával érhet el a biztonsági, átvitelvezérlés, és mindegyik üzenetet közzétevő közötti tárolási elkülönítése.
* Tároló-üzenetsorok üzenet tartalmának frissítése biztosít támogatást. Használhatja ezt a funkciót megőrzése állapotadatait és növekményes folyamata az üzenet, hogy fel lehessen dolgozni az utolsó ellenőrzőponttól helyett az alapoktól kezdve. A Service Bus-üzenetsorok engedélyezheti az üzenet-munkamenetek használatával ugyanezt a forgatókönyvet. Munkamenetek engedélyezése, hogy mentheti és az alkalmazás feldolgozási állapotát (használatával [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Kapcsolat megszakadásának megnevezéséhez](service-bus-dead-letter-queues.md), amely csak a Service Bus-üzenetsorok által támogatott hasznos lehet, amely nem dolgozható fel sikeresen a fogadó alkalmazás, vagy ha üzenetek nem tudja elérni a rendeltetési helyükre miatt lejárt idő TTL (üzenetek elkülönítése TTL) tulajdonság. Az élettartam értéke Itt adhatja meg, mennyi ideig üzenet marad az üzenetsorban. A Service Bus az üzenet $DeadLetterQueue meghívva, ha az élettartam lejárta speciális várólistára kerül.
* "Ártalmas" üzenetek megkeresése a Storage-üzenetsorokba, amikor egy üzenet üzenetmozgatót az alkalmazás megvizsgálja a [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) tulajdonság az üzenet. Ha **DequeueCount** nagyobb, mint egy megadott küszöbértéket, az alkalmazást áthelyezi az üzenetet az alkalmazás által meghatározott "kézbesítetlen levelek" üzenetsorába.
* Tároló-üzenetsorok lehetővé teszi az összes, a tranzakciók az üzenetsorhoz, valamint összesített mérőszámok elvégezni egy részletes naplóba beszerzése. Mindkét lehetőség hasznosak a hibakereséshez és megértése, hogyan használja a az alkalmazás a tároló-üzenetsorok. Referenciaként is hasznosak az alkalmazás teljesítmény-finomhangolási, és csökkenti a költségeket,-üzenetsorok használatával.
* A "üzenet-munkamenetek" a Service Bus által támogatott fogalmát lehetővé teszi, hogy az üzeneteket, amelyek egy adott fogadót, amely létrehoz egy munkamenet-szerű affinitás üzenetek és a megfelelő fogadók között társítani kell egy bizonyos logikai csoporthoz tartozik. Ez a funkció a Service Bus speciális beállításával engedélyezheti a [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) üzenetben tulajdonság. Ezután egy adott munkamenet-azonosító figyelni és, hogy a megadott munkamenet-azonosítót fogadók.
* A duplikáció észlelési funkciót, Service Bus-üzenetsorok által támogatott automatikusan eltávolítja az ismétlődő üzeneteket küld egy üzenetsorba vagy témakörbe, az érték alapján az [üzenetazonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) tulajdonság.

## <a name="capacity-and-quotas"></a>Kapacitás és a kvóták
Ez a szakasz hasonlítja össze a Storage-üzenetsorok és Service Bus-üzenetsorok szempontjából [kapacitás és a kvóták](service-bus-quotas.md) , előfordulhat, hogy a alkalmazni.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus üzenetsorok |
| --- | --- | --- |
| Várólista maximális mérete |**500 TB**<br/><br/>(legfeljebb egy [egyetlen a tárfiókok kapacitásával](../storage/common/storage-introduction.md#queue-storage)) |**1 GB – 80 GB**<br/><br/>(az üzenetsorok létrehozásakor meghatározott, és [particionálás engedélyezése](service-bus-partitioning.md) – a "További információk" című szakaszában talál) |
| Maximális üzenetméret |**64 KB-OS**<br/><br/>(48 KB-os használatakor **Base64** kódolás)<br/><br/>Az Azure támogatja a nagy méretű üzenetek üzenetsorok és blobok – ekkor is sorba kombinálásával egyetlen cikkre legfeljebb 200 GB. |**256 KB-os** vagy **1 MB**<br/><br/>(beleértve mind a fejléc szövegét, a fejléc maximális mérete: 64 KB-os).<br/><br/>Attól függ, a [szolgáltatásszint](service-bus-premium-messaging.md). |
| Üzenetek maximális Élettartama |**Végtelen** (től 2017-07-27 api-version) |**TimeSpan.Max** |
| Várólista maximális száma |**Korlátlan** |**10,000**<br/><br/>(szolgáltatás névterenként) |
| Egyidejű ügyfelek maximális száma |**Korlátlan** |**Korlátlan**<br/><br/>(100 egyidejű kapcsolathoz megadott korlátot csak vonatkozik a TCP protokoll-alapú kommunikációt) |

### <a name="additional-information"></a>További információ
* A Service Bus queue méretbeli korlátokat érvénybe lépteti. A várólista maximális mérete az üzenetsor létrehozásakor van megadva, és rendelkezhetnek 1 és 80 GB közötti értéket. A queue értékének beállítani a várólista létrehozása elérésekor, további beérkező üzeneteket a rendszer elutasítja, és kivételt fogja fogadni a hívó kód. A Service Bus kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták](service-bus-quotas.md).
* A particionálás nem támogatott a [prémium szintű](service-bus-premium-messaging.md). A Standard szintű Service Bus-üzenetsorok (az alapértelmezett érték 1 GB-os) 1, 2, 3, 4 vagy 5 GB méretű is létrehozhat. A Standard szint, a particionálás engedélyezve van (amely az alapértelmezett érték), Service Bus 16 partíciót hoz létre minden egyes megadott GB. Mint ilyen, ha létrehoz egy sort, amely 5 GB-os méretig, a 16 partíciók várólista maximális mérete válik (5 * 16) = 80 GB. A particionált üzenetsor vagy témakör maximális mérete láthatja hozzá tartozó bejegyzés megnézzük a [az Azure portal][Azure portal].
* A Storage-üzenetsorokba, ha az üzenet tartalma nem XML-biztonságos, majd lehet **Base64** kódolva. Ha Ön **Base64**-kódolni az üzenetet, a felhasználó hasznos adattartalom lehet akár 48 KB, 64 KB-os helyett.
* A Service Bus-üzenetsorok, minden tárolt egy üzenetsorban lévő üzenet két részből áll: a fejléc és a egy szervezet. Az üzenet összesített mérete nem haladhatja meg a szolgáltatási réteg által támogatott maximális mérete.
* Amikor az ügyfelek a TCP protokollon keresztül kommunikálnak a Service Bus-üzenetsorok, egy Service Bus-üzenetsorba való egyidejű kapcsolatok maximális számának korlátozódik 100. Ez a szám küldők és fogadók között van megosztva. Ez a kvóta elérésekor, további kapcsolatok későbbi kérelmeket a rendszer elutasítja, és kivételt fogja fogadni a hívó kód. Ez a korlátozás nem írják elő az üzenetsorok használata REST-alapú API-ügyfelekhez.
* Ha több mint 10 000 üzenetsorok egyetlen a Service Bus-névtér van szüksége, lépjen kapcsolatba az Azure ügyfélszolgálata, és növelni. 10 000, a Service Bus-üzenetsorok túli méretezéséhez is létrehozhat további névtereket használja az [az Azure portal][Azure portal].

## <a name="management-and-operations"></a>Felügyeleti és műveletek
Ez a szakasz a tároló-üzenetsorok és Service Bus-üzenetsorok által biztosított felügyeleti szolgáltatásait hasonlítja össze.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus üzenetsorok |
| --- | --- | --- |
| Felügyeleti protokoll |**REST-HTTP/HTTPS-kapcsolaton keresztül** |**REST-HTTPS-kapcsolaton keresztül** |
| Futásidejű protokoll |**REST-HTTP/HTTPS-kapcsolaton keresztül** |**REST-HTTPS-kapcsolaton keresztül**<br/><br/>**Az AMQP 1.0-s standard szintű (TCP-TLS)** |
| .NET API |**Igen**<br/><br/>(.NET tárolási ügyfél API-t) |**Igen**<br/><br/>(.NET Service Bus API-t) |
| Natív C++ |**Igen** |**Igen** |
| Java API |**Igen** |**Igen** |
| PHP-API |**Igen** |**Igen** |
| NODE.js API |**Igen** |**Igen** |
| Tetszőleges metaadatok támogatása |**Igen** |**Nem** |
| Várólista elnevezési szabályok |**Legfeljebb 63 karakter**<br/><br/>(Az üzenetsor nevének betűjét kisbetűnek kell lennie.) |**Legfeljebb 260 karakter**<br/><br/>(Várólista elérési útját és nevét az identitásokban nem számítanak.) |
| Várólista hossza függvény beolvasása |**Igen**<br/><br/>(Becsült érték Ha üzenetek lejárati idővel rendelkeznek az Élettartamon túli nélkül törlése folyamatban van.) |**Igen**<br/><br/>(Pontos, időponthoz érték.) |
| Belepillantás függvény |**Igen** |**Igen** |

### <a name="additional-information"></a>További információ
* Tároló-üzenetsorokat biztosít támogatást tetszőleges az attribútumokat, amelyek alkalmazhatók az üzenetsor leírását név/érték párok formájában.
* Mindkét várólista technológiái lehetővé teszi üzenet betekintési anélkül zárolja, amely akkor hasznos, ha egy üzenetsor/Explorerre eszköz végrehajtási nyújtanak.
* A Service Bus .NET által felügyelt üzenetkezelési API-kat használja ki teljes TCP-kapcsolatok HTTP REST képest jobb teljesítmény, és támogatják a szabványos AMQP 1.0 protokoll.
* Tárolási üzenetsorok nevei is lehet 3-63 karakter hosszú, kisbetűket, számokat és kötőjeleket tartalmazhat. További információkért lásd: [elnevezési üzenetsorok és metaadatok](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* A Service Bus-üzenetsorok nevei legfeljebb 260 karakter hosszú lehetnek, és kevésbé korlátozó elnevezési szabályoknak. A Service Bus-üzenetsorok nevei betűket, számokat, pontokat, kötőjeleket és aláhúzásjeleket tartalmazhat.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Ez a szakasz bemutatja a tároló-üzenetsorok és Service Bus-üzenetsorok által támogatott hitelesítési és engedélyezési funkciókat.

| Összehasonlítási feltétel | Tárolási üzenetsorok | Service Bus üzenetsorok |
| --- | --- | --- |
| Hitelesítés |**Szimmetrikus kulcs** |**Szimmetrikus kulcs** |
| Biztonsági modell |Delegált hozzáférés SAS-jogkivonatok használatával. |SAS |
| Identitás-összevonási szolgáltató |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Az üzenetsor-kezelési technológiák valamelyikéhez minden kérelmet kell hitelesíteni. A névtelen hozzáférés a nyilvános várólisták nem támogatottak. Használatával [SAS](service-bus-sas.md), meg lehet oldani a forgatókönyv csak írási SAS, írásvédett SAS- vagy akár egy teljes hozzáférési SAS tegye közzé.
* A hitelesítési séma megadott Storage üzenetsorok, a kivonat-alapú üzenethitelesítési kód (HMAC), egy szimmetrikus kulcs használatát magában foglalja az SHA-256 algoritmus számított és kódolásban egy **Base64** karakterlánc. A megfelelő protokoll kapcsolatos további információkért lásd: [az Azure Storage szolgáltatásainak hitelesítése](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-üzenetsorok támogatják egy hasonló modellt használ a szimmetrikus kulcsokat. További információkért lásd: [közös hozzáférésű Jogosultságkódos hitelesítés Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Összegzés
Jobban megértheti a két technológia információk megjelenítésével, akkor fog tudni megalapozottabb döntéseket a várólista technológiákat használni, és mikor. A Storage-üzenetsorokba vagy a Service Bus-üzenetsorok használata a döntést egyértelműen számos tényezőtől függ. Ezek a tényezők is függ az alkalmazás és architektúrájának egyedi igényeinek. Ha az alkalmazás már használja a Microsoft Azure core képességeit, érdemesebb lehet válassza ki a Storage-üzenetsorokba, különösen akkor, ha alapszintű kommunikációs és üzenetküldési szolgáltatások vagy 80 GB-nál nagyobb lehet szükség üzenetsorok között van szüksége.

Mivel a Service Bus-üzenetsorok számos speciális szolgáltatásokra, munkamenetek, tranzakciók, például az automatikus észlelés ismétlődő kézbesítetlen levelek kezelése, és hosszú élettartamú közzétételi/előfizetési képességeket, hogy lehet egy előnyben részesített választás hibrid készítésekor alkalmazás, vagy ha az alkalmazás más módon kell ezeket a funkciókat.

## <a name="next-steps"></a>További lépések
A következő cikkekben talál további útmutatást és tároló-üzenetsorok és Service Bus-üzenetsorok használatával kapcsolatos információkat biztosítanak.

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Queue Storage szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Ajánlott eljárások a teljesítmény használatával a Service Bus közvetítőalapú üzenettovábbítás](service-bus-performance-improvements.md)
* [Bemutatkozik az üzenetsorokat és Üzenettémákat, az Azure Service Busban (blogbejegyzés)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [A Service Bus fejlesztői útmutatója](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Az üzenetsor-kezelési szolgáltatással az Azure-ban](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

