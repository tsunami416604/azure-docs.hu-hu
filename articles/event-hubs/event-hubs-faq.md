---
title: Gyakori kérdések – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk az Azure Event Hubs és a válaszok a gyakori kérdések (GYIK) listája.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d1ed16465efb6c70b4426f22e8b9983112142c79
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162645"
---
# <a name="event-hubs-frequently-asked-questions"></a>Az Event Hubs – gyakori kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-an-event-hubs-namespace"></a>Mi az Event Hubs-névtér?
A névtér egy hatókörkezelési tároló az Event Hub és Kafka-témakörökhöz a. Biztosít egy egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Egy névtér szolgál egy alkalmazástárolót, amelynek több Event Hub és Kafka-témaköröket is tartalmazhat. 

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Mi a különbség az alapszintű Event Hubs és a Standard szint között?

Az Azure Event Hubs Standard szintű csomagban érhető el az alapszintű csomag biztosít. A Standard jelennek meg a következő funkciókat:

* Esemény hosszabb megőrzés
* További felügyelt kapcsolatok a kerettúllépési díjat számítunk fel több, mint a-szám
* Több [fogyasztói csoportot](event-hubs-features.md#consumer-groups)
* [rögzítése](event-hubs-capture-overview.md)
* [A Kafka-integráció](event-hubs-for-kafka-ecosystem-overview.md)

Csomagok – köztük az Event Hubs dedikált, a díjszabással kapcsolatban további információt talál a [az Event Hubs szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Hol érhető el az Azure Event Hubs?

Azure Event hubs szolgáltatás az összes támogatott Azure-régióban érhető el. Nyissa meg a listáját, a [Azure-régiók](https://azure.microsoft.com/regions/) lapot.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Használhatom-e egyetlen AMQP-kapcsolaton küldése és fogadása az event hubs-eseményközpontokból több?

Igen, mindaddig, amíg az event hubs a ugyanazon névterében van.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Mi az a maximális adatmegőrzési időtartam eseményeket?

Event Hubs Standard csomag jelenleg támogatja a maximális adatmegőrzési időtartam hét nap. Az Event hubs nem állandó adattárként tartozhat. 24 óránál hosszabb megőrzési időszakok forgatókönyvek, amelyben célszerű visszajátszani egy eseménysorozatot az eredeti rendszerekben; lettek tervezve. Ha például használatával betanítani vagy ellenőrizni egy új gépi tanulási modellt a meglévő adatok. Hét napon túli adatmegőrzés kell üzenetet, ha engedélyezése [Event Hubs Capture](event-hubs-capture-overview.md) az esemény-hub kér le az adatokat az eseményközpontból érkező az a Storage-fiók vagy a tetszőleges Azure Data Lake szolgáltatás fiók. Rögzítés engedélyezése kötelezettséggel jár a megvásárolt átviteli egységek alapján.

### <a name="how-do-i-monitor-my-event-hubs"></a>Hogyan követhetem figyelemmel az Event Hubs?
Az Event Hubs bocsát ki, amelyek az erőforrásokat az állapotát az alábbi mérőszámok [Azure Monitor](../azure-monitor/overview.md). Emellett segítségével felmérheti az általános állapotát, az Event Hubs szolgáltatás nem csak a névterek szintjén, hanem az entitások szintjén. Ismerje meg, milyen figyelésével kapcsolatos kínáljuk [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

## <a name="apache-kafka-integration"></a>Az Apache Kafka-integráció

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hogyan integrálhatja meglévő Kafka az alkalmazásom az Event Hubs?
Az Event Hubs biztosít egy Kafka-végpontot, amely a meglévő Apache Kafka-alapú alkalmazások által használható. Egy konfigurációmódosítás Ez minden, a PaaS Kafka tapasztalatra van szükség. Biztosít a saját Kafka-fürt futtatása helyett használhatók. Az Event Hubs az Apache Kafka 1.0-s és újabb ügyfél verzióit támogatja, és együttműködik a meglévő Kafka alkalmazásokat, eszközöket és keretrendszereket. További információkért lásd: [az Event Hubs, Kafka adattárban](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Milyen konfigurációs módosításokat kell megtenni a meglévő alkalmazás kommunikáljon az Event Hubs?
Szeretne csatlakozni egy Kafka-kompatibilis Event Hub, a Kafka ügyfél configs frissítéséhez szüksége. Event Hubs-névtér létrehozása és beszerzése ehhez a [kapcsolati karakterlánc](event-hubs-get-connection-string.md). Az Event Hubs teljes Tartománynevét és a portot átirányítása 9093 bootstrap.servers módosítása Frissítse a sasl.jaas.config irányítani a Kafka-ügyfél, az Event Hubs Kafka-kompatibilis végponthoz (amely az Ön beszerzett kapcsolati karakterlánc), a megfelelő hitelesítési alább látható módon:

bootstrap.Servers={Your. EVENTHUBS. FQDN}: 9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule szükséges felhasználónév = "$ConnectionString" jelszó = "{YOUR. EVENTHUBS. A KAPCSOLAT. KARAKTERLÁNC} ";

Példa:

bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule szükséges felhasználónév = "$ ConnectionString"password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName DummyAccessKeyName; = SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Megjegyzés: Ha sasl.jaas.config nem támogatott konfiguráció a keretrendszer, keresse meg a konfigurációk, amely segítségével a SASL-felhasználónevet és jelszót, és azokat használja helyette. Állítsa be a username $ConnectionString és a jelszót az Event Hubs kapcsolati karakterlánc.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Mi az Event hubs Kafka-kompatibilis üzenet/esemény méretét?
A Kafka-kompatibilis az Event Hubs számára engedélyezett maximális mérete 1MB.

## <a name="throughput-units"></a>Átviteli egységek

### <a name="what-are-event-hubs-throughput-units"></a>Mik azok az Event Hubs átviteli egység?
Az Event Hubs átviteli adatok mennyisége megabájt vagy 1 KB-os események száma (a több ezer) határozza meg, hogy bejövő és kimenő forgalom az Event Hubs segítségével. Az átviteli sebesség mérése, az átviteli egységek (átviteli egységek). Átviteli egységek beszerzési, az Event Hubs szolgáltatás használata előtt. Explicit módon kiválaszthatja Event Hubs átviteli egységek kell portálon vagy az Event Hubs Resource Manager-sablonok használatával. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>A kapacitásegységek egy névtér összes event hubs vonatkoznak?
Igen, az Event Hubs-névtér összes event hubs átviteli egységek (átviteli egységek) vonatkoznak. Az azt jelenti, hogy a névterek szintjén átviteli egységek megvásárlása és a névtéren az event hubs között. Minden egyes átviteli egység a névtér feljogosítja a a következő funkciókat:

- Akár 1 MB / s belépő események (egy eseményközpontba küldött események), de nem több mint 1000 beáramlási események, műveletek vagy vezérlőelem API-hívások / másodperc.
- Akár 2 MB / s kapacitás a kilépő események (egy adott eseményközpontból beolvasott események), de legfeljebb csak 4096 kapacitás a kilépő események.
- Akár 84 GB-nyi eseménytár (az alapértelmezett 24 órányi adatmegőrzéshez elegendő).

### <a name="how-are-throughput-units-billed"></a>Hogyan történik a átviteli egységek számlázása?
Átviteli egységek (átviteli egységek) számlázása óránként történik. A számlázás az egységek maximális száma az adott órában kiválasztott alapul. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hogyan optimalizálhatom a használati a saját átviteli egység?
Akár egy átviteli egységgel rendelkezhet (pl) is elindíthatja, és kapcsolja be a [automatikus feltöltésről](event-hubs-auto-inflate.md). Az automatikus feltöltési funkció lehetővé teszi a forgalom/hasznos növeli az átviteli egységek növekszik. Átviteli egységek számát is beállíthat egy felső korlátot.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hogyan működik az automatikus feltöltési funkció az Event hubs?
Az automatikus feltöltési funkció lehetővé teszi horizontális particionálás az átviteli egységek (átviteli egységek). Az azt jelenti, hogy alacsony átviteli egységek vásárlásával elindításához, és az automatikus feltöltési skálázását követve rugalmasan méretezhető az átviteli egységek fel, a bejövő forgalom növeli. Biztosít egy szolgáltatás költséghatékony lehetőséget és szabályozhatja az átviteli egységek száma kezeléséhez. Ez a funkció egy **vertikális felskálázás csak** funkció, és teljes mértékben vezérelheti a leskálázást a átviteli egységek számát frissítésével. 

Előfordulhat, hogy szeretné elindítani az alacsony átviteli egységek (átviteli egységek), például 2 átviteli egységek. Ha meg előre jelezni, hogy a forgalom 15 átviteli egységek, előfordulhat, hogy növekedjen turn-on az automatikus feltöltésről a névtér-funkció, és állítsa be a maximális korlátját, 15 átviteli egységek. Akkor is most már az átviteli egységek Automatikus növelés pedig a forgalomhoz igazítható.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Díjköteles kapcsolódó, ha be van kapcsolva az automatikus feltöltési funkció?
Nincs **ingyenesen** társított ezt a szolgáltatást. 

### <a name="how-are-throughput-limits-enforced"></a>Hogyan tartatja be átviteli korlátai?
Ha a belépő kapacitás vagy a belépő üzenetek gyakorisága összességében egy névtér összes event hubs meghaladja az összesített átviteli egység a kedvezmények, feladók szabályozott, és hibajelzésekben, hogy a belépési kapacitáskvóta túllépését.

Ha a kilépő kapacitás vagy a kilépő üzenetek gyakorisága egy névtér összes event hubs között meghaladja az összesített átviteli egység a kedvezmények, fogadók szabályozott, és hibajelzésekben, hogy a kilépési kapacitáskvóta túllépését. Bejövő és kimenő kvóták külön-külön érvényben vannak, így nincsenek sem tudja lassítani eseményfeldolgozás, és egyetlen külső eseményfogyasztó megakadályozhatja az eseményeket egy eseményközpontba küldött.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Átviteli egységek (átviteli egységek), amely lehet fenntartott vagy kiválasztott száma korlátozva van?
Egy több-bérlős kínáló, az átviteli egységek növelhető akár 40 átviteli egységek (legfeljebb 20 átviteli egységek kiválasztása a portálon, és létre egy támogatási jegyet, azt emelni ugyanazt a névteret a 40 átviteli egységek). 40 átviteli egységek, túl az Event Hubs kínál a kapacitás, erőforrás-alapú modell nevű a **Event Hubs dedikált fürtök**. Dedikált fürtök értékesítik kapacitásegységek (CUs).

## <a name="dedicated-clusters"></a>Dedikált fürtök

### <a name="what-are-event-hubs-dedicated-clusters"></a>Mik azok az Event Hubs dedikált fürtök?
Event Hubs dedikált fürtök egybérlős központi telepítések legszigorúbb követelményeknek rendelkező ügyfelek számára kínálnak. Ezt az ajánlatot egy kapacitás-alapú fürtöt, amely nincs kötve a kapacitásegységek által épít fel. Ez azt jelenti, hogy sikerült-e használni a fürt képes feldolgozni, és az adatok streamelése a CPU és memória kihasználtsága a fürt által. További információkért lásd: [Event Hubs dedikált fürtök](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>IP-címek fenntartási nem egy egyetlen kapacitásegységet én választom eléréséhez?
Dedikált fürt mennyit képes fogadni, és adatfolyam-függ, számos tényező befolyásolja, például a gyártó, a fogyasztók, a sebesség, amellyel feldolgozására és feldolgozása és még sok más. 

Következő táblázatban a teljesítményteszt eredménye, hogy mi érhető el a tesztelés során:

| Hasznos adat alakzat | Fogadók | Bejövő sávszélesség| Bejövő üzenetek | Kimenő adatforgalmat | Kimenő üzenetek | Teljes átviteli egységek | Átviteli egységek / Kapacitásegység |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB váró | 2 | 400 MB/mp | 400 k üzenetek/mp | 800 MB/mp | 800 k üzenetek/mp | 400 átviteli egységek | 100 átviteli egységek | 
| 10x10KB váró | 2 | 666 MB/mp | 66.6 k üzenetek/mp | 1,33 GB/mp | 133 k üzenetek/mp | 666 átviteli egységek | 166 átviteli egységek |
| 6x32KB váró | 1 | 1.05 GB/mp | 34 k üzenetek / másodperc | 1.05 GB/mp | 34 k üzenetek/mp | 1000 átviteli egységek | 250 átviteli egységek |

A vizsgálat során, a következő feltételek lett megadva:

- A dedikált Event Hubs fürt négy kapacitásegységek (CUs) lett megadva. 
- Az event hubs streamfeldolgozási használt 200 partíciók rendelkezett. 
- Volt betöltött adatokat fogad az összes partíció két fogadó alkalmazás kapott.

Az eredmények támpontot, mi érhető el a dedikált Event Hubs-fürtökkel. Emellett egy tárterületét fürtöt tartalmaz az Event Hubs rögzítés funkciója engedélyezve van a irányuló mikrokötegelt és hosszú távú adatmegőrzési-forgatókönyvekhez.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hogyan hozhatok létre egy Event Hubs dedikált fürtöt?
Elküldésével egy dedikált Event Hubs-fürt létrehozása egy [kvótájának növelését támogatási kérelem](https://portal.azure.com/#create/Microsoft.Support) vagy kapcsolatba lép a [Event Hubs-csapattal](mailto:askeventhubs@microsoft.com). Általában körülbelül két hetet lekéréséhez szükséges a fürt üzembe helyezését és gyorsítási értéknek, átadná Ön használhatja. Ez a folyamat csak ideiglenes, amíg a teljes önkiszolgáló szeretné elérhetővé tenni az Azure Portalon vagy Azure Resource Manager-sablonok, amely körülbelül két órát vesz igénybe a fürt üzembe helyezéséhez.

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="how-many-partitions-do-i-need"></a>Hogyan sok partíció van szükségem?

A partíciók száma az eseményközpont beállítása után nem módosítható. Az adott szem előtt fontos úgy gondolja, hogy hány partíciók kapcsolatos első lépések előtt szüksége. 

Az Event Hubs egy fogyasztói csoportot egy egyetlen partícióolvasónak nyilván lehetővé teszi. A legtöbb használati esetek négy partíciót, az alapértelmezett beállítás is elegendő. Ha az eseményfeldolgozás horizontális, érdemes érdemes lehet hozzáadni a két további partíció. Nincs konkrét átviteli korlátozva a partíción, azonban az átviteli egységek száma korlátozza az összesített átviteli sebességre, a névtérben. Növeli az átviteli egységek száma a névtérben, előfordulhat, hogy szeretne további partíciókat, hogy az egyidejű olvasók saját maximális átviteli sebesség eléréséhez.

Előfordulhat azonban, ha a modell, amelyben az alkalmazás rendelkezik egy adott partíció való kapcsolatot, a partíciók számának növelésével nem minden javát. További információkért lásd: [rendelkezésre állás és konzisztencia](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-more-pricing-information"></a>Hol találhatok további díjszabási információk?

Event Hubs-díjszabás kapcsolatos részletes információkért lásd: a [az Event Hubs szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Van valamilyen díja az Event Hubs-események megőrzése 24 óránál tovább áll?

Az Event Hubs Standard csomag lehetővé teszi, hogy üzenetmegőrzés legfeljebb hét napja 24 óránál hosszabb ideig. Ha a tárolt események teljes száma meghaladja a tároló (kapacitásegységenként 84 GB) a kiválasztott kapacitásegységek száma, a mérete, amely meghaladja a díja a közzétett Azure Blob storage sebességét. A keretet az egyes kapacitásegységek 24 órányi megőrzéshez költségeit tartalmazza (az alapértelmezett beállítás) még akkor is, ha a Kapacitásegység a legnagyobb bejövő keretet.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hogyan az Event Hubs tárméret kiszámítása és díját?

Minden tárolt események, belső, adminisztratív kapcsolódóan tárakon lemezt beleértve minden event hubs, amelyek összméretén egész nap folyamatosan mérjük. A nap végén megállapítjuk az aznap foglalt tárméret maximumát. A napi tárkeretet az adott napra választott kapacitásegységek számából állapítjuk meg – ha egy adott nap folyamán többször több különböző értéket jelöltek ki, akkor a legkisebbet vesszük figyelembe. Egy kapacitásegység tárkerete 84 GB. Ha a teljes mérete meghaladja a számított napi keretet, a blobtárolói díjszabásán számoljuk használatával az Azure Blob tárolási díjait (címen a **helyileg redundáns tárolás** arány).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hogyan számítják a belépő események Event Hubs?

Az eseményközpontba érkező minden egyes esemény egy üzenetnek számít. Egy *belépő esemény* , amely legfeljebb 64 KB méretű adategység típusúként van definiálva. Bármely esemény, amely legfeljebb 64 KB méretű tekinthető egy esemény számlázható. Ha az esemény 64 KB-nál nagyobb, számlázható események száma értendő az esemény mérete a 64 KB hányszorosa. Például az event hubs küldött 8 KB-os esemény egy eseménynek történik, de egy 96 KB-os üzenetet az event hubs két üzenetként történik.

Ezek az eseményközpontok felé, valamint a felügyeleti műveletek és a vezérlő hívásokat, például az ellenőrzőpontok nem számítanak belépő üzenetnek események, de beleszámítanak a kapacitáskeretbe legfeljebb események.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Az Event Hubs vonatkoznak felügyelt kapcsolati díjat?

Kapcsolat a díjak vonatkoznak, csak ha az AMQP protokollt használja. Az események HTTP-n keresztül történő küldése – a küldő rendszerek és eszközök számától függetlenül – díjmentes. Ha azt tervezi, használhatja az AMQP (például a hatékonyabb eléréséhez, vagy a kétirányú kommunikációt az IoT-parancsban, és szabályozhatja a forgatókönyvek), a [díjszabási információk az Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) hány részleteiért lap kapcsolatok az egyes szolgáltatásszinteken szerepelnek.

### <a name="how-is-event-hubs-capture-billed"></a>Hogyan számítjuk az Event Hubs Rögzítés díját?

Rögzítés engedélyezve van, ha a névtérben található bármely eseményközpont rögzítési lehetősége engedélyezve van. Az Event Hubs Capture számlázása óránként megvásárolt kapacitásegységenként. A kapacitásegységek számának növelése vagy csökkenése, az Event Hubs rögzítés árváltozásaként mutatkozik meg, egész órára kerekítve tartalmazza. Az Event Hubs Capture számlázással kapcsolatos további információkért lásd: [díjszabási információk az Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Fizetnem kell a a az Event Hubs rögzítéshez választott tárfiókot?

Rögzítés, adja meg, ha engedélyezve van az eseményközpontban tárfiókot használ. A storage-fiókot, mert ehhez a konfigurációhoz módosítások számítjuk fel az Azure-előfizetéshez.

## <a name="quotas"></a>Kvóták

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Milyen kvóták társított Event Hubs?

Minden Event Hubs-kvótákról listáját lásd: [kvóták](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem tudok-névtér létrehozása egy másik előfizetésből törlését követően? 
Ha egy névtér töröl egy előfizetést, várjon, amíg 4 óra, mielőtt újra egy másik előfizetésben található ugyanazzal a névvel. Ellenkező esetben a következő hibaüzenet jelenhet: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Mik az Event Hubs és a javasolt lépések által előállított kivételeket?

Lehetséges az Event Hubs-kivételek listáját lásd: [kivételek áttekintése](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Event Hubs két típusát támogatja [diagnosztikai naplók](event-hubs-diagnostic-logs.md) -hibanaplók és műveleti naplók –, amelyek szerepelnek a JSON-ban, és az Azure Portalon lehet bekapcsolni.

### <a name="support-and-sla"></a>Támogatás és szolgáltatásszintek

Az Event Hubs technikai támogatás érhető el a [közösségi fórumokat is talál](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). A számlázás és az előfizetések kezelésének támogatása díjmentesen igénybe vehető.

SZERZŐDÉSÜNK kapcsolatos további információkért tekintse meg a [szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/) lapot.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs az automatikus feltöltési](event-hubs-auto-inflate.md)
