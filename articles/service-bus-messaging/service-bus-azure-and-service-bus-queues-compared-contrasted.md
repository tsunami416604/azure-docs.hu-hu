---
title: Az Azure Storage-várólisták és a Service Bus-várólisták összehasonlítása
description: Az Azure által kínált két típusú várólisták közötti különbségeket és hasonlóságokat elemzi.
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
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ffa98e511053edc75fd0e6f25f7b0e21ee9ddda0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414535"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Tárolási várólisták és szolgáltatásbusz-várólisták – összehasonlítás és kontrasztos
Ez a cikk a Microsoft Azure által ma kínált két típusú várólisták közötti különbségeket és hasonlóságokat elemzi: a tárolási várólisták at és a Service Bus-várólistákat. Ezen információk használatával összehasonlíthatja és összehasonlíthatja a megfelelő technológiákat, és megalapozottabb döntést hozhat arról, hogy melyik megoldás felel meg leginkább az Ön igényeinek.

## <a name="introduction"></a>Introduction (Bevezetés)
Az Azure kétféle várólista-mechanizmust támogat: **a tárolási várólistákat** és a **Service Bus-várólistákat.**

**A tárolási várólisták**, amelyek az [Azure tárolási](https://azure.microsoft.com/services/storage/) infrastruktúrájának részét képezik, egy egyszerű REST-alapú GET/PUT/PEEK felülettel rendelkeznek, amely megbízható, állandó üzenetküldést biztosít a szolgáltatásokon belül és azok között.

**A Service Bus-várólisták** egy szélesebb [Körű Azure-üzenetkezelési](https://azure.microsoft.com/services/service-bus/) infrastruktúra részét képezik, amely támogatja a sorban tartást, valamint a közzétételt/előfizetést, valamint a fejlettebb integrációs mintákat. A Service Bus várólistáiról/témaköreiről/előfizetéseiről a Service Bus áttekintése című témakörben olvashat [bővebben.](service-bus-messaging-overview.md)

Bár mindkét sorban állási technológia egyidejűleg létezik, a tárolási várólistákat először az Azure Storage-szolgáltatásokra épülő dedikált várólista-tárolási mechanizmusként vezették be. A Service Bus-várólisták a szélesebb körű üzenetkezelési infrastruktúrára épülnek, amely több kommunikációs protokollt, adategyezményt, megbízhatósági tartományt és/vagy hálózati környezetet kiterjedő alkalmazások vagy alkalmazás-összetevők integrálására szolgál.

## <a name="technology-selection-considerations"></a>A technológia kiválasztásával kapcsolatos szempontok
Mind a storage-várólisták, mind a Service Bus-várólisták a Microsoft Azure által jelenleg kínált üzenetsor-küldési szolgáltatás implementációi. Mindegyiknek van egy kicsit más funkciókészlete, ami azt jelenti, hogy kiválaszthatja az egyiket vagy a másikat, vagy mindkettőt használhatja, az adott megoldás vagy az ön által megoldott üzleti / technikai probléma igényeitől függően.

Annak meghatározásakor, hogy egy adott megoldás melyik sorbanállási technológiája felel meg a célnak, a megoldástervezőknek és -fejlesztőknek figyelembe kell venniük ezeket az ajánlásokat. További részletek a következő szakaszban találhatóak.

Megoldástervezőként/-fejlesztőként **érdemes megfontolnia a tárolási várólisták használatát,** ha:

* Az alkalmazásnak több mint 80 GB üzenetet kell tárolnia egy várólistában.
* Az alkalmazás nyomon szeretné követni a várólistán belüli üzenet feldolgozásának folyamatát. Ez akkor hasznos, ha az üzenetet feldolgozó dolgozó összeomlik. Egy későbbi dolgozó ezután használhatja ezt az információt, hogy folytassa onnan, ahol az előző dolgozó abbahagyta.
* A várólistákon végrehajtott összes tranzakció kiszolgálóoldali naplóira van szükség.

Megoldástervezőként/-fejlesztőként **érdemes megfontolnia a Service Bus-várólisták használatát,** ha:

* A megoldásnak képesnek kell lennie az üzenetek fogadására anélkül, hogy le kellene hallgatnia a várólistát. A Service Bus segítségével ez a Service Bus által támogatott TCP-alapú protokollok használatával érhető el a hosszú lekérdezési fogadási művelet használatával.
* A megoldás megköveteli, hogy a várólista garantált first-in-first-out (FIFO) megrendelt kézbesítést biztosítson.
* A megoldásnak támogatnia kell az automatikus duplikáltelem-észlelést.
* Azt szeretné, hogy az alkalmazás párhuzamos, hosszú ideig futó adatfolyamként dolgozza fel az üzeneteket (az üzenetek az üzenet [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) tulajdonságát használó adatfolyamhoz vannak társítva). Ebben a modellben a fogyasztó alkalmazás minden csomópontja verseng az adatfolyamok, szemben az üzenetek. Amikor egy adatfolyamot egy fogyasztó csomóponthoz ad, a csomópont megvizsgálja az alkalmazásfolyam állapotának állapotát tranzakciók használatával.
* A megoldás tranzakciós viselkedést és atomi ságot igényel, ha több üzenetet küld vagy fogad egy várólistából.
* Az alkalmazás kezeli a 64 KB-ot meghaladó üzeneteket, de valószínűleg nem közelíti meg a 256 KB-os korlátot.
* A várólistákszerepkör-alapú hozzáférési modell biztosításának követelményével, valamint a feladók és a fogadók különböző jogosultságaival/engedélyeivel foglalkozik. További információkért tekintse át a következő cikkeket:
    - [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
    - [Hitelesítés egy alkalmazásból](authenticate-application.md)
* Az üzenetsor mérete nem nő 80 GB-nál nagyobbra.
* Az AMQP 1.0 szabványalapú üzenetkezelési protokollt szeretné használni. Az AMQP szolgáltatásról további információt a [Service Bus AMQP – áttekintés című témakörben talál.](service-bus-amqp-overview.md)
* Elképzelheti a várólista-alapú pont-pont kommunikációból egy üzenetcsere-mintába való esetleges áttelepítést, amely lehetővé teszi a további fogadók (előfizetők) zökkenőmentes integrálását, amelyek mindegyike a várólistába küldött üzenetek egy részének vagy mindegyikének független másolatait kapja. Ez utóbbi a Service Bus által biztosított közzétételi/előfizetési képességre vonatkozik.
* Az üzenetkezelési megoldásnak képesnek kell lennie az "At-Most-Once" kézbesítési garancia támogatására anélkül, hogy szükség lenne a további infrastruktúra-összetevők létrehozásához.
* Közzé szeretne tenni és fel szeretne használni üzenettömböket.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Tárolóvárólisták és szolgáltatásbusz-várólisták összehasonlítása
A következő szakaszokban található táblázatok a várólista-szolgáltatások logikai csoportosítását biztosítják, és egy pillantással összehasonlíthatják az Azure Storage-várólistákban és a Service Bus-várólistákban elérhető képességeket.

## <a name="foundational-capabilities"></a>Alapvető képességek
Ez a szakasz a Storage-várólisták és a Service Bus-várólisták által biztosított alapvető sorbanállási lehetőségeket hasonlítja össze.

| Összehasonlítási feltételek | Tárolási várólisták | Service Bus-üzenetsorok |
| --- | --- | --- |
| Rendelési garancia |**Nem** <br/><br>További információt a "További információk" című szakasz első megjegyzésében talál.</br> |**Igen - First-In-First-Out (FIFO)**<br/><br>(üzenetküldési munkamenetek használatával) |
| Szállítási garancia |**Legalább egyszer** |**At-Least-Once** (peeklock fogadási mód használatával - ez az alapértelmezett) <br/><br/>**At-Most-Once** (a ReceiveAndDelete fogadási mód használatával) <br/> <br/> További információ a különböző [fogadási módokról](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Atomműködési támogatás |**Nem** |**Igen**<br/><br/> |
| Fogadási viselkedés |**Nem blokkoló**<br/><br/>(azonnal befejeződik, ha nem talál új üzenetet) |**Blokkolás időhanélküli/időnélküli**<br/><br/>(hosszú szavazást kínál, vagy a ["Comet technika"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Nem blokkoló**<br/><br/>(csak a .NET által felügyelt API használatával) |
| Push-stílusú API |**Nem** |**Igen**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) és [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) munkamenetek .NET API. |
| Fogadási mód |**Peek & lízing** |**Betekintés & zárolás**<br/><br/>**Törlés & fogadása** |
| Exkluzív hozzáférési mód |**Lízing-alapú** |**Zárolás-alapú** |
| Bérleti/zárolási időtartam |**30 másodperc (alapértelmezett)**<br/><br/>**7 nap (maximum)** (Az [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API használatával megújíthat vagy feladhat egy üzenetbérletet.) |**60 másodperc (alapértelmezett)**<br/><br/>Az üzenetzárolást a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API-val újíthatja meg. |
| Bérleti/zárolási pontosság |**Üzenet szintje**<br/><br/>(minden üzenetnek más időtúlértéke lehet, amelyet az [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API használatával szükség szerint frissíthet az üzenet feldolgozása közben) |**Várólista szintje**<br/><br/>(minden várólista zárolási pontossággal rendelkezik az összes üzenetére alkalmazva, de a zárolást a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API-val megújíthatja.) |
| Kötegelt fogadás |**Igen**<br/><br/>(az üzenetek beolvasásakor az üzenetek száma explicit megadása, legfeljebb 32 üzenet) |**Igen**<br/><br/>(implicit módon lehetővé teszi a pre-fetch tulajdonság, vagy kifejezetten a tranzakciók használatával) |
| Kötegelt küldés |**Nem** |**Igen**<br/><br/>(tranzakciók vagy ügyféloldali kötegelés útján) |

### <a name="additional-information"></a>További információ
* A tárolóvárólistákban lévő üzenetek általában elsőként kerülnek ki, de néha nem megfelelőek; például, ha egy üzenet láthatósági időtúltöltési időtartama lejár (például egy ügyfélalkalmazás összeomlik a feldolgozás során). Amikor a láthatósági időtúldátum lejár, az üzenet ismét láthatóvá válik a várólistán, hogy egy másik dolgozó visszavethesse a várólistát. Ezen a ponton előfordulhat, hogy az újonnan látható üzenet a várólistába kerül (amelyet újra meg kell mondani), miután egy eredetileg várólistára helyezett üzenet után.
* A Service Bus-várólisták garantált FIFO-mintája üzenetkezelési munkamenetek használatát igényli. Abban az esetben, ha az alkalmazás összeomlik a **Betekintés & zárolása** módban fogadott üzenet feldolgozása közben, a következő alkalommal, amikor egy várólista-fogadó elfogadja az üzenetküldési munkamenetet, a sikertelen üzenettel kezdődik az élő (TTL) időszak lejárta után.
* A tárolási várólisták szabványos sorban állási forgatókönyvek támogatására szolgálnak, például az alkalmazás-összetevők leválasztására a méretezhetőség és a hibák tűrésének növelése érdekében, a terheléskiegyenlítés és a folyamatmunkafolyamatok létrehozása.
* A Service Bus-munkamenetek környezetében az üzenetek kezelésével kapcsolatos inkonzisztenciák elkerülhetők, ha a munkamenet-állapot ot használja az alkalmazás állapotának tárolására a munkamenet üzenetsorozatának kezelésének folyamatához viszonyítva, valamint a fogadott üzenetek rendezésével és a munkamenet-állapot frissítésével kapcsolatos tranzakciók használatával. Ez a fajta konzisztencia funkció néha címkével *pontosan egyszer feldolgozás* más szállító i termékek, de a tranzakciós hibák nyilvánvalóan okoz üzeneteket kell szállítani, és ezért a kifejezés nem pontosan megfelelő.
* A tárolási várólisták egységes és egységes programozási modellt biztosítanak a várólisták, táblák és BLOB-k között – mind a fejlesztők, mind a műveleti csapatok számára.
* A Service Bus-várólisták egyetlen várólista környezetében támogatják a helyi tranzakciókat.
* A Service Bus által támogatott **fogadási és törlési** mód lehetővé teszi az üzenetküldési műveletek számának (és a kapcsolódó költségeknek) csökkentését a csökkentett kézbesítési biztosításért cserébe.
* A tárolási várólisták lehetővé teszik az üzenetek bérletének kiterjesztését. Ez lehetővé teszi a dolgozók számára, hogy rövid címbérleteket tartsanak fenn az üzeneteken. Így ha egy dolgozó összeomlik, az üzenetet gyorsan fel dolgozhatja újra egy másik dolgozó. Emellett a dolgozó meghosszabbíthatja a bérlet egy üzenet, ha kell feldolgozni, hogy hosszabb, mint az aktuális bérleti idő.
* A tárolási várólisták láthatósági időkérést biztosítanak, amelybe beállíthat egy üzenet sorba állítását vagy dequeuingját. Ezenkívül frissítheti a különböző címbérleti értékekkel rendelkező üzeneteket futásidőben, és frissítheti a különböző értékeket az ugyanabban a várólistában lévő üzenetek között. A Service Bus zárolási időmegtarátjai a várólista metaadataiban vannak definiálva; a zárolást azonban megújíthatja a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metódus hívásával.
* A Service Bus-várólistákban a blokkolási fogadási műveletek maximális időtel töltött ideje 24 nap. A REST-alapú időtúltások maximális értéke azonban 55 másodperc.
* A Service Bus által biztosított ügyféloldali kötegelés lehetővé teszi, hogy egy várólista-ügyfél több üzenetet kötegeljen egyetlen küldési műveletbe. Kötegelés csak aszinkron küldési műveletekhez érhető el.
* Az olyan funkciók, mint a storage-várólisták 200 TB-os felső határa (több, ha virtualizálja a fiókokat) és a korlátlan várólisták ideális platformot jelentenek az SaaS-szolgáltatók számára.
* A tárolási várólisták rugalmas és nagy teljesítményű delegált hozzáférés-vezérlési mechanizmust biztosítanak.

## <a name="advanced-capabilities"></a>Speciális képességek
Ez a szakasz a Storage-várólisták és a Service Bus-várólisták által biztosított speciális képességeket hasonlítja össze.

| Összehasonlítási feltételek | Tárolási várólisták | Service Bus-üzenetsorok |
| --- | --- | --- |
| Ütemezett kézbesítés |**Igen** |**Igen** |
| Automatikus kézbesítési betűk |**Nem** |**Igen** |
| A várakozási idő növelése az élő értékhez |**Igen**<br/><br/>(a láthatósági időelés helybeni frissítése révén) |**Igen**<br/><br/>(külön API-funkción keresztül) |
| Poison üzenet támogatása |**Igen** |**Igen** |
| Helybeni frissítés |**Igen** |**Igen** |
| Kiszolgálóoldali tranzakciós napló |**Igen** |**Nem** |
| Tárolási mutatók |**Igen**<br/><br/>**Perc metrikák:** valós idejű metrikákat biztosít a rendelkezésre álláshoz, a TPS-hez, az API-hívásszámokhoz, a hibák számhoz és egyebekhez, mindezt valós időben (percenként összesítve, és néhány percen belül jelentik az éles környezetben történttől számított néhány percen belül. További információ: [About Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Igen**<br/><br/>(tömeges lekérdezések a [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)hívásával) |
| Állapotkezelés |**Nem** |**Igen**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Üzenet automatikus továbbítása |**Nem** |**Igen** |
| Várólista-függvény kiürítése |**Igen** |**Nem** |
| Üzenetcsoportok |**Nem** |**Igen**<br/><br/>(üzenetküldési munkamenetek használatával) |
| Alkalmazás állapota üzenetcsoportonként |**Nem** |**Igen** |
| Duplikálás észlelése |**Nem** |**Igen**<br/><br/>(a feladó oldalán konfigurálható) |
| Üzenetcsoportok böngészése |**Nem** |**Igen** |
| Üzenetmunkamenetek beolvasása azonosító szerint |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Mindkét sorban állási technológia lehetővé teszi, hogy egy üzenet későbbi kézbesítésre legyen ütemezve.
* A várólista automatikus továbbítása lehetővé teszi, hogy több ezer várólista automatikusan továbbítsa üzeneteit egyetlen várólistába, ahonnan a fogadó alkalmazás felhasználja az üzenetet. Ezzel a mechanizmussal biztonság, vezérlési folyamat és az egyes üzenetközzétevők közötti tárolás elkülönítése érhető el.
* A tárolási várólisták támogatják az üzenetek tartalmának frissítését. Ezt a funkciót az állapotinformációk megőrzésére és az üzenetnövekményes folyamatfrissítésekre használhatja, hogy az utolsó ismert ellenőrzőpontról dolgozhassa fel, és ne kezdje elölről. A Service Bus-várólisták használatával ugyanazt a forgatókönyvet engedélyezheti az üzenetmunkamenetek használatával. A munkamenetek lehetővé teszik az alkalmazásfeldolgozási állapot mentését és beolvasását (a [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) és a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)használatával).
* [A kézbesítetlen levelek ,](service-bus-dead-letter-queues.md)amelyeket csak a Service Bus-várólisták támogatnak, hasznos lehet olyan üzenetek elkülönítéséhez, amelyeket a fogadó alkalmazás nem tud sikeresen feldolgozni, vagy ha az üzenetek nem érik el a céljukat egy lejárt élettartamú (TTL) tulajdonság miatt. A TTL érték határozza meg, hogy mennyi ideig marad egy üzenet a várólistában. A Service Bus segítségével az üzenet egy speciális várólistába kerül, amelynek neve $DeadLetterQueue, amikor a TTL-időszak lejár.
* A "méreg" üzenetek keresése a tárolóvárólistákban, egy üzenet dequeuing, az alkalmazás megvizsgálja az üzenet [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) tulajdonságát. Ha **a DequeueCount** nagyobb, mint egy adott küszöbérték, az alkalmazás áthelyezi az üzenetet egy alkalmazás által definiált "kézbesítetlen levél" várólistába.
* A tárolási várólisták lehetővé teszik a várólistán végrehajtott összes tranzakció, valamint az összesített metrikák részletes naplójának beszerzését. Mindkét beállítás hasznos a hibakereséshez és annak megértéséhez, hogy az alkalmazás hogyan használja a Tárolási várólistákat. Ezek az alkalmazás teljesítményének hangolásához és a várólisták használatának költségeinek csökkentéséhez is hasznosak.
* A Service Bus által támogatott "üzenetmunkamenetek" fogalma lehetővé teszi, hogy egy adott logikai csoporthoz tartozó üzenetek egy adott fogadóhoz legyenek társítva, ami viszont munkamenet-szerű affinitást hoz létre az üzenetek és a megfelelő fogadók között. Ezt a speciális funkciót a Service Bus szolgáltatásban úgy engedélyezheti, hogy a [SessionID tulajdonságot](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) egy üzenetre állítja be. A fogadók ezután figyelhetik egy adott munkamenet-azonosítót, és fogadhatnak olyan üzeneteket, amelyek megosztják a megadott munkamenet-azonosítót.
* A Service Bus várólistái által támogatott duplikáltelem-észlelési funkció automatikusan eltávolítja a [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) tulajdonság értéke alapján a várólistába vagy témakörbe küldött ismétlődő üzeneteket.

## <a name="capacity-and-quotas"></a>Kapacitás és kvóták
Ez a szakasz a tárolási várólistákat és a Service Bus-várólistákat a kapacitás és az esetleg alkalmazandó [kvóták](service-bus-quotas.md) szempontjából hasonlítja össze.

| Összehasonlítási feltételek | Tárolási várólisták | Service Bus-üzenetsorok |
| --- | --- | --- |
| Várólista maximális mérete |**500 TB**<br/><br/>(egyetlen [tárfiók kapacitására](../storage/common/storage-introduction.md#queue-storage)korlátozva) |**1 GB –80 GB**<br/><br/>(várólisták létrehozásakor és [particionálás engedélyezésekor definiálva](service-bus-partitioning.md) – lásd a "További információk" című részt) |
| Az üzenetek maximális mérete |**64 KB**<br/><br/>(48 KB **base64** kódolás esetén)<br/><br/>Az Azure támogatja a nagy méretű üzenetek et a várólisták és a blobok kombinálásával – ekkor akár 200 GB-ot is várólistára lehet állni egyetlen elemhez. |**256 KB** vagy **1 MB**<br/><br/>(beleértve a fejlécet és a törzset is, a maximális fejlécméret: 64 KB).<br/><br/>A [szolgáltatási szinttől](service-bus-premium-messaging.md)függ. |
| Maximális üzenet TTL |**Infinite** (api-version 2017-07-27) |**TimeSpan.Max** |
| Várólisták maximális száma |**Korlátlan** |**10,000**<br/><br/>(szolgáltatásnévtérenként) |
| Egyidejű ügyfelek maximális száma |**Korlátlan** |**Korlátlan**<br/><br/>(A 100 egyidejű kapcsolati korlát csak a TCP protokollalapú kommunikációra vonatkozik) |

### <a name="additional-information"></a>További információ
* A Service Bus kényszeríti a várólista méretkorlátait. A várólista létrehozásakor a várólista maximális várólistamérete 1 és 80 GB közötti lehet. Ha eléri a várólista létrehozásához beállított várólistaméret-értéket, a rendszer további bejövő üzeneteket utasít el, és a hívókód kivételt fogad. A Service Bus kvótáiról a [Service Bus-kvóták című témakörben talál](service-bus-quotas.md)további információt.
* A particionálás nem támogatott a [prémium szinten.](service-bus-premium-messaging.md) A standard szinten 1, 2, 3, 4 vagy 5 GB-os méretben hozhat létre Service Bus-várólistákat (az alapértelmezett érték 1 GB). Standard rétegben, ha a particionálás engedélyezve van (ami az alapértelmezett), a Service Bus 16 partíciót hoz létre minden megadott GB-hoz. Ebben az esetben, ha 5 GB méretű várólistát hoz létre, 16 partícióval a maximális várólistaméret (5 * 16) = 80 GB lesz. A particionált várólista vagy témakör maximális méretét az [Azure Portalon][Azure portal]való bejegyzésmegtekintésével láthatja.
* A tárolási várólisták, ha az üzenet tartalma nem XML-biztonságos, akkor kell **Base64** kódolt. Ha **Base64**-kódolja az üzenetet, a felhasználó hasznos adata 64 KB helyett akár 48 KB is lehet.
* A Service Bus-várólisták ban a várólistában tárolt üzenetek két részből állnak: egy fejlécből és egy törzsből. Az üzenet teljes mérete nem haladhatja meg a szolgáltatási szint által támogatott maximális üzenetméretet.
* Amikor az ügyfelek a TCP protokollon keresztül kommunikálnak a Service Bus várólistáival, az egyetlen Service Bus-várólistához való egyidejű kapcsolatok maximális száma 100-ra korlátozódik. Ez a szám meg van osztva a feladók és a fogadók között. Ha eléri ezt a kvótát, a rendszer elutasítja a további kapcsolatokra vonatkozó további kérelmeket, és a hívókód kivételt fogad. Ez a korlát nem róható ki a REST-alapú API-val a várólistákhoz csatlakozó ügyfelekre.
* Ha egy Service Bus-névtérben 10 000-nél több várólistára van szüksége, kapcsolatba léphet az Azure támogatási csapatával, és növekedést kérhet. Ha a Service Bus segítségével 10 000 várólistán túlra szeretne skálázni, az [Azure Portal][Azure portal]használatával további névtereket is létrehozhat.

## <a name="management-and-operations"></a>Irányítás és üzemeltetés
Ez a szakasz összehasonlítja a storage-várólisták és a Service Bus-várólisták által biztosított felügyeleti funkciókat.

| Összehasonlítási feltételek | Tárolási várólisták | Service Bus-üzenetsorok |
| --- | --- | --- |
| Kezelési protokoll |**PIHENÉS HTTP/HTTPS PROTOKOLLON KERESZTÜL** |**REST HTTPS-en keresztül** |
| Futásidejű protokoll |**PIHENÉS HTTP/HTTPS PROTOKOLLON KERESZTÜL** |**REST HTTPS-en keresztül**<br/><br/>**AMQP 1.0 standard (TCP TLS-sel)** |
| .NET API |**Igen**<br/><br/>(.NET tárolóügyfél API) |**Igen**<br/><br/>(.NET Service Bus API) |
| Natív C++ |**Igen** |**Igen** |
| Java API |**Igen** |**Igen** |
| PHP API |**Igen** |**Igen** |
| Node.js API |**Igen** |**Igen** |
| Tetszőleges metaadat-támogatás |**Igen** |**Nem** |
| Várólista-elnevezési szabályok |**Legfeljebb 63 karakter hosszú**<br/><br/>(A várólista nevében lévő betűknek kisbetűseknek kell lenniük.) |**Legfeljebb 260 karakter hosszú**<br/><br/>(A várólista elérési útjai és nevei nem különböznek a kis- és nagybetűktől.) |
| Várólistahossz-függvény bekerülése |**Igen**<br/><br/>(Hozzávetőleges érték, ha az üzenetek törlés nélkül lejárnak a TTL-en túl.) |**Igen**<br/><br/>(Pontos, időponthoz való érték.) |
| Betekintés funkció |**Igen** |**Igen** |

### <a name="additional-information"></a>További információ
* A tárolási várólisták név/érték párok formájában tetszőleges attribútumokat támogatnak, amelyek alkalmazhatók a várólista leírására.
* Mindkét várólista-technológia lehetővé teszi, hogy betekintsen egy üzenetbe anélkül, hogy zárolna kellene, ami hasznos lehet a várólista-kezelő/-böngésző eszköz megvalósításakor.
* A Service Bus .NET felügyelt üzenetkezelési API-k a teljes kétirányú TCP-kapcsolatokat használják a http-n keresztüli REST-hez képest a jobb teljesítmény érdekében, és támogatják az AMQP 1.0 szabványos protokollt.
* A tárolóvárólisták nevei 3–63 karakter hosszúak lehetnek, kisbetűket, számokat és kötőjeleket tartalmazhatnak. További információt az [Elnevezési várólisták és metaadatok](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)című témakörben talál.
* A Service Bus-várólistanevek legfeljebb 260 karakter hosszúak lehetnek, és kevésbé korlátozó elnevezési szabályokkal rendelkeznek. A Service Bus várólistanevei tartalmazhatnak betűket, számokat, időszakokat, kötőjeleket és aláhúzásjeleket.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Ez a szakasz a Storage-várólisták és a Service Bus-várólisták által támogatott hitelesítési és engedélyezési funkciókat ismerteti.

| Összehasonlítási feltételek | Tárolási várólisták | Service Bus-üzenetsorok |
| --- | --- | --- |
| Hitelesítés |**Szimmetrikus kulcs** |**Szimmetrikus kulcs** |
| Biztonsági modell |Delegált hozzáférés SAS-jogkivonatokon keresztül. |Sas |
| Identitásszolgáltató összevonása |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* A sorbanállási technológiák bármelyikére vonatkozó minden kérést hitelesíteni kell. A névtelen hozzáféréssel rendelkező nyilvános várólisták nem támogatottak. A [SAS](service-bus-sas.md)használatával ezt a forgatókönyvet csak írási SAS, csak olvasható SAS vagy akár egy teljes hozzáférésű SAS közzétételével címezheti.
* A storage várólisták által biztosított hitelesítési séma magában foglalja egy szimmetrikus kulcs használatát, amely egy kivonatalapú üzenethitelesítési kód (HMAC), amelyet az SHA-256 algoritmussal kell kiszámítani, és **Base64** karakterláncként kódolva. A megfelelő protokollról további információt [az Azure Storage-szolgáltatások hitelesítése című témakörben talál.](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services) A Service Bus-várólisták hasonló modellt támogatnak szimmetrikus kulcsok használatával. További információt a [Megosztott hozzáférésű aláírás-hitelesítés a Service Bus segítségével című témakörben talál.](service-bus-sas.md)

## <a name="conclusion"></a>Összegzés
A két technológia mélyebb megértésével megalapozottabb döntést hozhat arról, hogy melyik várakozási sortechnológiát használja, és mikor. A storage-várólisták vagy a Service Bus-várólisták használatának eldöntése egyértelműen számos tényezőtől függ. Ezek a tényezők nagymértékben függhetnek az alkalmazás egyéni igényeitől és architektúrájától. Ha az alkalmazás már használja a Microsoft Azure alapvető képességeit, célszerűbb lehet választani tárolási várólisták, különösen akkor, ha a szolgáltatások közötti alapvető kommunikációra és üzenetküldésre van szüksége, vagy 80 GB-nál nagyobb várólistákra van szüksége.

Mivel a Service Bus-várólisták számos speciális szolgáltatást biztosítanak, például munkameneteket, tranzakciókat, duplikáltelemek észlelését, automatikus kézbesítési levelet és tartós közzétételi/előfizetési képességeket, előnyben részesíthetik őket, ha hibrid alkalmazást hoz fel, vagy ha az alkalmazás egyébként igényli ezeket a funkciókat.

## <a name="next-steps"></a>További lépések
Az alábbi cikkek további útmutatást és információkat nyújtanak a storage-várólisták vagy a Service Bus-várólisták használatával kapcsolatban.

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A várólista-tároló szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Gyakorlati tanácsok a Service Bus által közvetített üzenetküldés továbbfejlesztett teljesítményjavításához](service-bus-performance-improvements.md)
* [Várólisták és témakörök bemutatása az Azure Service Busban (blogbejegyzés)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [A fejlesztői útmutató a szervizbuszhoz](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [A queuing szolgáltatás használata az Azure-ban](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

