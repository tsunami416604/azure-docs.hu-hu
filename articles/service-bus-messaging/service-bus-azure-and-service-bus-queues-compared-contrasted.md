---
title: Az Azure Storage-várólisták és a Service Bus-várólisták összevetése és kontrasztos összehasonlítása | Microsoft Docs
description: Elemzi az Azure által kínált két típusú várólista közötti különbségeket és hasonlóságokat.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: bf2b83725f8ce8e712974c182c9a11e8ed0d04f0
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013225"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Tárolási várólisták és Service Bus várólisták – összehasonlítás és kontrasztos
Ez a cikk a Microsoft Azure ma által kínált két típusú várólista közötti különbségeket és hasonlóságokat elemzi: Tárolási várólisták és Service Bus várólisták. Ezekre az információkra alapozva összehasonlíthatók az egyes technológiák, és megfontoltabb döntéseket lehet hozni arról, hogy melyik megoldás felel meg leginkább az igényeknek.

## <a name="introduction"></a>Bevezetés
Az Azure a várólista-mechanizmusok két típusát támogatja: **Tárolási várólisták** és **Service Bus várólisták**.

Az [Azure Storage](https://azure.microsoft.com/services/storage/) -infrastruktúra részét képező **tárolási várólisták**egy egyszerű REST-alapú Get/Put/Peek felületet biztosítanak, amely megbízható, állandó üzenetküldést tesz lehetővé a szolgáltatások között és azok között.

A **Service Bus Queues** egy szélesebb körű [Azure](https://azure.microsoft.com/services/service-bus/) -üzenetkezelési infrastruktúra része, amely támogatja a várakozási sorba helyezést, valamint a közzétételt és előfizetést, valamint a fejlettebb integrációs mintákat. Service Bus várólistákkal/témakörökkel/előfizetésekkel kapcsolatos további információkért tekintse meg a [Service Bus áttekintését](service-bus-messaging-overview.md).

Noha mindkét üzenetsor-kezelési technológia párhuzamosan létezik, a tárolási várólistákat először vezették be, az Azure Storage szolgáltatásokra épülő dedikált üzenetsor-tárolási mechanizmusként. Service Bus várólisták az alkalmazások vagy alkalmazás-összetevők integrálására szolgáló szélesebb körű üzenetkezelési infrastruktúrára épülnek, amely több kommunikációs protokollt, adategyezményt, megbízhatósági tartományt és/vagy hálózati környezetet is kiterjedhet.

## <a name="technology-selection-considerations"></a>A technológia kiválasztási szempontjai
A tárolási várólisták és a Service Bus várólisták a Microsoft Azure által jelenleg kínált üzenetsor-kezelő szolgáltatás implementációi. Mindegyikhez némileg eltérő szolgáltatáskészlet tartozik, ami azt jelenti, hogy kiválaszthatja az egyiket vagy a másikat, vagy használhatja mindkettőt az adott megoldás vagy az Ön által feloldott üzleti vagy technikai probléma igényeitől függően.

Ha meghatározza, hogy melyik üzenetsor-kezelő technológia felel meg egy adott megoldás célnak, a megoldás-építészeknek és a fejlesztőknek figyelembe kell venniük ezeket a javaslatokat. További részletekért tekintse meg a következő szakaszt.

Megoldás-építészként/fejlesztőként érdemes megfontolni a **tárolási várólisták használatát** a következők esetén:

* Az alkalmazásnak több mint 80 GB üzenetet kell tárolnia egy várólistában.
* Az alkalmazás nyomon szeretné követni az üzenetek várólistán belüli feldolgozásának folyamatát. Ez akkor hasznos, ha az üzenetet feldolgozó dolgozó összeomlik. Egy későbbi feldolgozó ezt az információt használhatja annak folytatására, hogy az előző feldolgozó abbahagyta a munkát.
* A várólistákon végrehajtott összes tranzakció kiszolgálóoldali naplófájljaira van szükség.

Megoldás-építészként/fejlesztőként érdemes **megfontolni Service Bus várólisták használatát** , ha:

* A megoldásnak képesnek kell lennie az üzenetek fogadására anélkül, hogy le kellene kérdezni a várólistát. A Service Bus ez a hosszú lekérdezési fogadási műveletnek a Service Bus által támogatott TCP-alapú protokollok használatával történő használatával érhető el.
* A megoldáshoz szükséges, hogy a várólista biztosítson egy garantált, első alkalommal kifelé irányuló (FIFO) rendezett kézbesítést.
* A megoldásnak képesnek kell lennie az automatikus ismétlődő észlelés támogatására.
* Azt szeretné, hogy az alkalmazás párhuzamos, hosszan futó adatfolyamként dolgozza fel az üzeneteket (az üzenetek az üzenetben található [munkamenet](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) -tulajdonsággal vannak társítva). Ebben a modellben a felhasználó alkalmazás minden csomópontja verseng a streamek számára, az üzenetek helyett. Ha egy adatfolyamot egy felhasználó csomóponthoz adnak, a csomópont a tranzakciók használatával megvizsgálhatja az alkalmazás-adatfolyam állapotát.
* A megoldás tranzakciós viselkedést és atomi kezelést igényel, ha több üzenetet küld vagy fogad egy várólistából.
* Az alkalmazás kezeli az 64 KB-nál nagyobb méretű üzeneteket, de a 256 KB-os korlátot valószínűleg nem fogja megközelíteni.
* A szerepkör-alapú hozzáférési modellnek a várólistákhoz való biztosításához, valamint a küldők és a fogadók számára a különböző jogokkal/engedélyekkel kell foglalkoznia. További információkért tekintse át a következő cikkeket:
    - [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
    - [Hitelesítés alkalmazásból](authenticate-application.md)
* A várólista mérete nem növekszik 80 GB-nál nagyobb mértékben.
* A AMQP 1,0 szabványokon alapuló üzenetküldési protokollt kívánja használni. További információ a AMQP: [Service Bus AMQP áttekintése](service-bus-amqp-overview.md).
* A várólista-alapú pont-pont típusú kommunikációból egy olyan üzenetváltási mintára vonatkozó végleges áttelepítést készíthet, amely lehetővé teszi a további fogadók (előfizetők) zökkenőmentes integrálását, amelyek mindegyike független másolatokat fogad a várólistába küldött üzenetek. Az utóbbi a Service Bus által natív módon nyújtott közzétételi/előfizetési képességre hivatkozik.
* Az üzenetkezelési megoldásnak képesnek kell lennie támogatni a "legfeljebb egyszeri" kézbesítési garanciát anélkül, hogy létre kellene hoznia a további infrastruktúra-összetevőket.
* Szeretné közzétenni és felhasználni az üzenetek kötegeit.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>A tárolási várólisták és a Service Bus várólisták összehasonlítása
A következő szakaszokban szereplő táblázatok a várólista-funkciók logikai csoportosítását teszik lehetővé, és összehasonlíthatja az Azure Storage-várólistákban és a Service Bus-várólistákban elérhető képességeket.

## <a name="foundational-capabilities"></a>Alapvető képességek
Ez a szakasz összehasonlítja a tárolási várólisták és a Service Bus várólisták által biztosított alapvető üzenetsor-kezelő képességeket.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Megrendelés jótállása |**Nem** <br/><br>További információkért tekintse meg a "További információ" című szakasz első megjegyzését.</br> |**Igen – elsőként elsőként ki (FIFO)**<br/><br>(üzenetküldési munkamenetek használatával) |
| Kézbesítési garancia |**At-Least-Once** |**Legalább egyszer** (PeekLock fogadási mód használata – ez az alapértelmezett beállítás) <br/><br/>Legfeljebb **egyszer** (ReceiveAndDelete fogadási mód használata) <br/> <br/> További információ a különböző [fogadási módokról](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Atomi működés támogatása |**Nem** |**Igen**<br/><br/> |
| Fogadások viselkedése |**Nem blokkoló**<br/><br/>(azonnal befejeződik, ha nem található új üzenet) |**Blokkolás/időkorlát nélkül**<br/><br/>(hosszú lekérdezéseket, vagy az ["Comet Technique"](https://go.microsoft.com/fwlink/?LinkId=613759)-t kínál)<br/><br/>**Nem blokkoló**<br/><br/>(csak .NET felügyelt API-k használatával) |
| Leküldéses stílusú API |**Nem** |**Igen**<br/><br/>A [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) és a **OnMessage** munkamenetek .NET API-ját. |
| Fogadási mód |**Betekintés & bérletbe** |**Betekintés & zárolás**<br/><br/>**Fogadás & Törlés** |
| Kizárólagos hozzáférési mód |**Bérlet-alapú** |**Zárolási alapú** |
| Bérlet/zárolás időtartama |**30 másodperc (alapértelmezett)**<br/><br/>**7 nap (maximum)** (A [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API használatával megújíthatja vagy felszabadíthatja az üzenetek bérletét.) |**60 másodperc (alapértelmezett)**<br/><br/>Az [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API használatával megújíthat egy üzenet zárolását. |
| Bérlet/zárolás pontossága |**Üzenet szintje**<br/><br/>(minden üzenet rendelkezhet eltérő időtúllépési értékkel, amelyet az üzenet feldolgozásakor szükség szerint frissíthet a [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API használatával) |**Várólista szintje**<br/><br/>(minden várólistához tartozik egy zárolási pontosság az összes üzenetre, de a zárolást a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API használatával újíthatja meg.) |
| Kötegelt fogadás |**Igen**<br/><br/>(explicit módon megadhatja az üzenetek számát az üzenetek beolvasása során, legfeljebb 32 üzenet) |**Igen**<br/><br/>(a beolvasás előtti tulajdonság implicit engedélyezése vagy explicit módon a tranzakciók használatával) |
| Kötegelt küldés |**Nem** |**Igen**<br/><br/>(tranzakciók vagy ügyféloldali kötegek használatával) |

### <a name="additional-information"></a>További információ
* A tárolási várólisták üzenetei általában elsők, de előfordulhat, hogy nem sorrendben vannak. például ha egy üzenet láthatóságának időtúllépési időtartama lejár (például egy ügyfélalkalmazás összeomlása miatt a feldolgozás során). Ha a láthatósági időkorlát lejár, az üzenet ismét láthatóvá válik a várólistán egy másik feldolgozó számára, hogy a várólistára kerüljön. Ebben az esetben az újonnan látható üzenet a várólistába kerül (a-t újra el kell helyezni) egy olyan üzenet után, amely eredetileg várólistán lévő.
* A Service Bus Queues-ben garantált FIFO-minta az üzenetkezelési munkamenetek használatát igényli. Abban az esetben, ha az alkalmazás összeomlik a betekintési **& zárolási** módjában fogadott üzenet feldolgozásakor, a várólista következő fogadása egy üzenetküldési munkamenetet fogad el, és az élettartam (TTL) időszak lejárta után a sikertelen üzenettel kezdődik.
* A tárolási várólisták úgy lettek kialakítva, hogy támogassák a szabványos üzenetsor-kezelő forgatókönyveket, például az alkalmazás-összetevők leválasztását, hogy növelje a meghibásodások méretezhetőségét és hibatűrését, a terheléselosztást és a folyamat munkafolyamatait.
* Service Bus várólisták támogatják a *legalább egyszeri* kézbesítési garanciát. 
* A Service Bus munkamenetek kontextusában az üzenetek kezelésével kapcsolatos következetlenségek elkerülhetők, ha a munkamenet-állapottal szeretné tárolni az alkalmazás állapotát a munkamenet üzenet-sorrendjének kezeléséhez képest, valamint tranzakciók használatával a fogadott üzenetek rendezése és a munkamenet állapotának frissítése. Ez a fajta konzisztencia-szolgáltatás néha pontosan egyszeri *feldolgozást* végez a többi gyártó termékeiben, de a tranzakciós hibák nyilvánvalóan visszaküldik az üzeneteket, így a kifejezés nem pontosan megfelelő.
* A Storage-várólisták egységes és konzisztens programozási modellt biztosítanak a várólisták, táblák és Blobok között, a fejlesztők és az operatív csapatok számára egyaránt.
* Service Bus a várólisták a helyi tranzakciók támogatását biztosítják egyetlen üzenetsor kontextusában.
* A Service Bus által támogatott **fogadási és törlési** mód lehetővé teszi az üzenetkezelési műveletek számának (és a kapcsolódó költségek) csökkentését az Exchange-ben a lecsökkentett kézbesítési garancia érdekében.
* A tárolási várólisták bérleteket biztosítanak az üzenetek bérletének meghosszabbítására. Ez lehetővé teszi, hogy a dolgozók rövid bérleteket tartsanak fenn az üzenetekben. Így ha egy feldolgozó összeomlik, az üzenetet gyorsan fel lehet dolgozni egy másik feldolgozó. Emellett a feldolgozó kiterjesztheti a bérletet egy üzenetben, ha az az aktuális bérleti időpontnál hosszabb ideig fel kell dolgoznia.
* A tárolási várólisták olyan láthatósági időtúllépést biztosítanak, amelyet egy üzenet enqueuing vagy dequeuing szolgáltatásával lehet beállítani. Emellett frissítheti a különböző címbérleti értékekkel rendelkező üzeneteket futásidőben, és az ugyanazon a várólistán lévő üzenetek különböző értékeit is frissítheti. Service Bus zárolási időtúllépések definiálva vannak a várólista metaadataiban; a zárolást azonban a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metódus meghívásával is megújíthatja.
* Service Bus várólistán a blokkoló fogadási művelet maximális időtúllépése 24 nap. Azonban a REST-alapú időtúllépések maximális értéke 55 másodperc.
* A Service Bus által biztosított ügyféloldali kötegek lehetővé teszik, hogy egy üzenetsor-ügyfél több üzenetet is egy küldési műveletbe írjon. A kötegelt feldolgozás csak aszinkron küldési műveletekhez érhető el.
* Az olyan funkciók, mint például a 200 TB-os felső korlátja (többek között a fiókok virtualizálása) és a korlátlan számú várólista, ideális platformot biztosítanak az SaaS-szolgáltatók számára.
* A tárolási várólisták rugalmas és teljesítményű delegált hozzáférés-vezérlési mechanizmust biztosítanak.

## <a name="advanced-capabilities"></a>Speciális képességek
Ez a szakasz a tárolási várólisták és a Service Bus várólisták speciális képességeit hasonlítja össze.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Ütemezett kézbesítés |**Igen** |**Igen** |
| Automatikus kézbesítetlen levelek |**Nem** |**Igen** |
| A várólista-élettartam értékének növelése |**Igen**<br/><br/>(a láthatósági időkorlát helyben történő frissítése révén) |**Igen**<br/><br/>(dedikált API-függvénnyel biztosítva) |
| Megmérgezhető üzenetek támogatása |**Igen** |**Igen** |
| Helyben történő frissítés |**Igen** |**Igen** |
| Kiszolgálóoldali tranzakciónapló |**Igen** |**Nem** |
| Tárolási metrikák |**Igen**<br/><br/>**Perc mérőszámok**: valós idejű mérőszámokat biztosít a rendelkezésre álláshoz, a TPS, az API-hívások számait, a hibák számát és egyebeket, mindezt valós időben (percenként összesítve), és néhány percen belül jelentést készített az éles környezetben történtek közül. További információ: [a Storage Analytics mérőszámok ismertetése](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Igen**<br/><br/>(tömeges lekérdezések a [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)meghívásával) |
| Állapotkezelés |**Nem** |**Igen**<br/><br/>[Microsoft. ServiceBus. Messaging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus.](/dotnet/api/microsoft.servicebus.messaging.entitystatus)letiltva, [Microsoft. ServiceBus. Messaging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [ Microsoft. ServiceBus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Üzenet automatikus továbbítása |**Nem** |**Igen** |
| Várólista-törlési függvény |**Igen** |**Nem** |
| Üzenetek csoportjai |**Nem** |**Igen**<br/><br/>(üzenetküldési munkamenetek használatával) |
| Alkalmazás állapota csoportonként |**Nem** |**Igen** |
| Duplikálás észlelése |**Nem** |**Igen**<br/><br/>(a küldő oldalon konfigurálható) |
| Tallózási üzenetek csoportjai |**Nem** |**Igen** |
| Üzenet-munkamenetek beolvasása azonosító alapján |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Mindkét üzenetsor-kezelő technológia lehetővé teszi, hogy egy későbbi időpontban ütemezhető legyen az üzenet kézbesítése.
* Az üzenetsor automatikus továbbítása lehetővé teszi, hogy több ezer várólista automatikusan továbbítsa az üzeneteket egyetlen várólistába, amelyből a fogadó alkalmazás felhasználja az üzenetet. Ezt a mechanizmust használhatja a biztonság, a vezérlési folyamat és a tárolók elkülönítésére az egyes üzenetek közzétevői között.
* A tárolási várólisták támogatást nyújtanak az üzenetek tartalmának frissítéséhez. Ezzel a funkcióval megtarthatja az állapotadatokat és a növekményes előrehaladási frissítéseket az üzenetbe, hogy az utolsó ismert ellenőrzőpontról feldolgozható legyen, és ne a semmiből kellene kezdenie. Service Bus várólistákkal ugyanezt a forgatókönyvet is engedélyezheti az üzenet-munkamenetek használatával. A munkamenetek lehetővé teszik az alkalmazások feldolgozási állapotának mentését és beolvasását (a [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)használatával).
* [](service-bus-dead-letter-queues.md)A csak Service Bus várólisták által támogatott kézbesítetlen levelek esetében hasznos lehet a fogadó alkalmazás által nem feldolgozható üzenetek elkülönítése, vagy ha egy lejárt élettartam (TTL) miatt nem lehet elérni a célhelyet. tulajdonság. A TTL érték azt határozza meg, hogy mennyi ideig marad az üzenet a várólistán. Service Bus esetén az üzenet egy $DeadLetterQueue nevű speciális várólistába kerül, amikor a TTL-időszak lejár.
* Ha meg szeretné keresni a "méreg" üzeneteket a tárolási várólistákban, amikor egy üzenet dequeuing üzenetet küld, az alkalmazás megvizsgálja az üzenet [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) tulajdonságát. Ha a **DequeueCount** nagyobb, mint egy megadott küszöbérték, az alkalmazás áthelyezi az üzenetet egy alkalmazás által definiált "kézbesítetlen levél" várólistára.
* A tárolási várólisták lehetővé teszik a várólistán végrehajtott összes tranzakció részletes naplójának, valamint az összesített mérőszámok beszerzését. Mindkét lehetőség hasznos lehet a hibakereséshez és annak megismeréséhez, hogy az alkalmazás hogyan használja a tárolási várólistákat. Hasznosak továbbá az alkalmazások teljesítményének finomhangolásához és a várólisták használatának költségeinek csökkentéséhez.
* A Service Bus által támogatott "üzenet-munkamenetek" fogalma lehetővé teszi, hogy egy adott logikai csoporthoz tartozó üzenetek egy adott fogadóhoz legyenek társítva, ami viszont az üzenetek és a hozzájuk tartozó fogadók közötti kapcsolati affinitást hoz létre. Ezt a speciális funkciót a Service Busban engedélyezheti egy üzenetben a [munkamenet](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) -azonosító tulajdonság beállításával. A fogadók ezt követően egy adott munkamenet-azonosítót és a megadott munkamenet-azonosítót megosztó üzeneteket fogadhatnak.
* Service Bus várólisták által támogatott duplikált észlelési funkció automatikusan eltávolítja a várólistára vagy témakörre küldött duplikált üzeneteket a [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) tulajdonság értéke alapján.

## <a name="capacity-and-quotas"></a>Kapacitás és kvóták
Ez a szakasz a tárolási várólistákat és a Service Bus várólistákat a lehetséges [kapacitás és kvóták](service-bus-quotas.md) szempontjából hasonlítja össze.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Várólista maximális mérete |**500 TB**<br/><br/>( [egyetlen Storage-fiók kapacitására](../storage/common/storage-introduction.md#queue-storage)korlátozódik) |**1 GB – 80 GB**<br/><br/>(az üzenetsor létrehozásával és a [particionálás engedélyezésével](service-bus-partitioning.md) van meghatározva – lásd a "További információk" szakaszt) |
| Maximális üzenetméret |**64 KB**<br/><br/>(48 KB **Base64** kódolás használatakor)<br/><br/>Az Azure a várólisták és a Blobok kombinálásával is támogatja a nagyméretű üzeneteket, ekkor akár 200 GB-ot is sorba helyezni egyetlen elemhez. |**256 kb** vagy **1 MB**<br/><br/>(beleértve a fejlécet és a törzset is, a fejléc maximális mérete: 64 KB).<br/><br/>A [szolgáltatási szinttől](service-bus-premium-messaging.md)függ. |
| Üzenetek maximális élettartama (TTL) |**Végtelen** (az API-Version 2017-07-27-es verziótól kezdve) |**TimeSpan.Max** |
| Várólisták maximális száma |**Korlátlan** |**10,000**<br/><br/>(szolgáltatási névtér esetében) |
| Egyidejű ügyfelek maximális száma |**Korlátlan** |**Korlátlan**<br/><br/>(100 az egyidejű kapcsolati korlát csak a TCP protokoll alapú kommunikációra vonatkozik) |

### <a name="additional-information"></a>További információ
* Service Bus kényszeríti a várólista méretének korlátozásait. A várólista maximális mérete a várólista létrehozásakor van megadva, és 1 és 80 GB közötti érték adható meg. Ha a várólista méretének beállítása az üzenetsor létrehozásakor értékre van állítva, a rendszer elutasítja a további bejövő üzeneteket, és kivételt kap a hívási kód. További információ a Service Bus kvótákkal kapcsolatban: [Service Bus kvóták](service-bus-quotas.md).
* A particionálás nem támogatott a [prémium](service-bus-premium-messaging.md)szinten. A standard szinten Service Bus várólistákat az 1, 2, 3, 4 vagy 5 GB méretekben hozhatja létre (az alapértelmezett érték 1 GB). A standard szinten, a particionálás engedélyezve (amely az alapértelmezett), Service Bus 16 partíciót hoz létre minden egyes megadott GB-hoz. Így ha egy 5 GB méretű várólistát hoz létre, 16 partícióval a maximális várólista mérete (5 * 16) = 80 GB lesz. A particionált üzenetsor vagy témakör maximális mérete megtekinthető a [Azure Portal][Azure portal]bejegyzésének megkeresésével.
* A Storage Queues használata esetén, ha az üzenet tartalma nem XML-Safe, akkor **Base64** kódolású kell lennie. Ha **Base64**kódolással kódolja az üzenetet, a felhasználói adattartalom legfeljebb 48 kb lehet, 64 kb helyett.
* A Service Bus várólisták esetében az üzenetsor-sorokban tárolt összes üzenet két részből áll: egy fejlécből és egy törzsből. Az üzenet teljes mérete nem haladhatja meg a szolgáltatási szintet támogató üzenetek maximális méretét.
* Ha az ügyfelek a TCP protokollon keresztül kommunikálnak Service Bus várólistákkal, az egyService Busos üzenetsor egyidejű kapcsolatainak maximális száma a 100-ra korlátozódik. Ez a szám a küldők és a fogadók között van megosztva. Ha eléri ezt a kvótát, a további kapcsolatokra vonatkozó további kérések el lesznek utasítva, és a hívási kód kivételt kap. Ez a korlát nincs bevezetve a várólistákhoz REST-alapú API használatával csatlakozó ügyfeleken.
* Ha több mint 10 000 várólistára van szüksége egyetlen Service Bus névtérben, vegye fel a kapcsolatot az Azure támogatási csoportjával, és növelje a növekményt. Ha az 10 000-es várólistán kívülre szeretné méretezni a Service Bus, további névtereket is létrehozhat a [Azure Portal][Azure portal]használatával.

## <a name="management-and-operations"></a>Felügyelet és műveletek
Ez a szakasz összehasonlítja a tárolási várólisták és Service Bus várólisták által biztosított felügyeleti szolgáltatásokat.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Felügyeleti protokoll |**REST HTTP/HTTPS-n keresztül** |**REST HTTPS-kapcsolaton keresztül** |
| Futásidejű protokoll |**REST HTTP/HTTPS-n keresztül** |**REST HTTPS-kapcsolaton keresztül**<br/><br/>**AMQP 1,0 standard (TCP TLS-sel)** |
| .NET API |**Igen**<br/><br/>(.NET Storage ügyféloldali API) |**Igen**<br/><br/>(.NET Service Bus API) |
| NatívC++ |**Igen** |**Igen** |
| Java API |**Igen** |**Igen** |
| PHP API |**Igen** |**Igen** |
| Node. js API |**Igen** |**Igen** |
| Tetszőleges metaadat-támogatás |**Igen** |**Nem** |
| Várólista elnevezési szabályai |**Legfeljebb 63 karakter hosszú lehet**<br/><br/>(A várólista nevének kisbetűnek kell lennie.) |**Legfeljebb 260 karakter hosszú lehet**<br/><br/>(A várólista elérési útja és neve nem megkülönbözteti a kis-és nagybetűket.) |
| Várólista hosszának beolvasása függvény |**Igen**<br/><br/>(Megközelítő érték, ha az üzenetek törlés nélkül lejárnak az ÉLETTARTAMon kívül.) |**Igen**<br/><br/>(Pontos, időponthoz megadott érték.) |
| Betekintés függvény |**Igen** |**Igen** |

### <a name="additional-information"></a>További információ
* A tárolási várólisták olyan tetszőleges attribútumok támogatását biztosítják, amelyek a várólista leírására alkalmazhatók név/érték párok formájában.
* A várólista-technológiák mindkét esetben lehetővé teszik az üzenetek betekintését a zárolás nélkül, ami hasznos lehet a üzenetsor-kezelő/böngésző eszköz megvalósítása során.
* A Service Bus .NET-alapú üzenetkezelési API-k teljes kétirányú TCP-kapcsolatot használnak a nagyobb teljesítmény érdekében a HTTP-n keresztüli REST-kapcsolathoz képest, és támogatják a AMQP 1,0 standard protokollt.
* A tárolási várólisták nevei 3-63 karakter hosszúak lehetnek, kisbetűket, számokat és kötőjeleket tartalmazhatnak. További információ: a [várólisták és a metaadatok elnevezése](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus üzenetsor neve legfeljebb 260 karakter hosszúságú lehet, és kevésbé korlátozó elnevezési szabályokkal rendelkezhet. Service Bus üzenetsor neve betűket, számokat, pontokat, kötőjeleket és aláhúzást tartalmazhat.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Ez a szakasz a tárolási várólisták és Service Bus várólisták által támogatott hitelesítési és engedélyezési funkciókat ismerteti.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Authentication |**Szimmetrikus kulcs** |**Szimmetrikus kulcs** |
| Biztonsági modell |Delegált hozzáférés SAS-tokeneken keresztül. |SAS |
| Identitás-szolgáltató összevonása |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* A várólista-technológiák bármelyikére vonatkozó kérelmet hitelesíteni kell. A névtelen hozzáféréssel rendelkező nyilvános várólisták nem támogatottak. Az [sas](service-bus-sas.md)használatával ezt a forgatókönyvet csak írható sas, írásvédett sas vagy akár teljes hozzáférésű sas közzétételével kezelheti.
* A tárolási várólisták által biztosított hitelesítési séma magában foglalja egy szimmetrikus kulcs használatát, amely egy kivonatoló alapú üzenethitelesítő kód (HMAC), amely az SHA-256 algoritmussal lett kiszámítva, és **Base64** -karakterláncként van kódolva. További információ a megfelelő protokollról: [Az Azure Storage-szolgáltatások hitelesítése](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). A Service Bus-várólisták a szimmetrikus kulcsokat használó hasonló modellt támogatják. További információ: [közös hozzáférésű aláírások hitelesítése Service Bussal](service-bus-sas.md).

## <a name="conclusion"></a>Összegzés
A két technológia mélyebb megismerése révén jobban tájékozott döntést hozhat, hogy melyik üzenetsor-technológiát használja, és mikor. A tárolási várólisták vagy Service Bus-várólisták használatára vonatkozó döntés egyértelműen több tényezőtől függ. Ezek a tényezők nagy mértékben befolyásolhatják az alkalmazás és az architektúrájuk egyedi igényeit. Ha az alkalmazás már a Microsoft Azure alapvető képességeit használja, érdemes lehet a tárolási várólistákat választania, különösen akkor, ha alapszintű kommunikációt és üzenetküldést igényel a szolgáltatások között, vagy olyan várólistákra van szüksége, amelyek mérete nagyobb, mint 80 GB.

Mivel a Service Bus Queues számos speciális funkciót biztosít, például a munkameneteket, a tranzakciókat, a duplikált észlelést, az automatikus kézbesítetlen levelek használatát és a tartós közzétételi/előfizetési képességeket, érdemes választani, ha hibrid alkalmazásra, vagy ha az alkalmazás egyébként megköveteli ezeket a funkciókat.

## <a name="next-steps"></a>További lépések
A következő cikkek további útmutatást és információkat nyújtanak a tárolási várólisták vagy Service Bus várólisták használatával kapcsolatban.

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Queue Storage szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Ajánlott eljárások a teljesítmény növeléséhez Service Bus felügyelt üzenetkezelés használatával](service-bus-performance-improvements.md)
* [A Azure Service Bus várólistáinak és témaköreinek bemutatása (blogbejegyzés)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [A Service Bus fejlesztői útmutatója](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Az Azure-beli üzenetsor-kezelő szolgáltatás használata](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

