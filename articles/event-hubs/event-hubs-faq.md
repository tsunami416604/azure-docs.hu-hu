---
title: Gyakori kérdések – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs gyakori kérdések (GYIK) listáját és azok válaszait tartalmazza.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 7f6e1896c97c96cd484d15fb9e6a3056e5c5d6b2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086368"
---
# <a name="event-hubs-frequently-asked-questions"></a>Az Event Hubs gyakran tesz fel kérdéseket

## <a name="general"></a>Általános kérdések

### <a name="what-is-an-event-hubs-namespace"></a>Mi az Event Hubs névtér?
A névtér az Event Hub/Kafka-témakörök hatókör-tárolója. Ez ad egy egyedülálló [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). A névtér alkalmazástárolóként szolgál, amely több Event Hub/Kafka-témakört is tárolhat. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Mikor hozhatok létre új névteret, és mikor használhatok meglévő névteret?
A kapacitásallokációkat[( átviteli egységeket (ÁTVITELI EGYSÉGEK)](#throughput-units)a névtér szintjén számlázunk. Egy névtér is társítva van egy régióhoz.

Előfordulhat, hogy új névteret szeretne létrehozni ahelyett, hogy az alábbi esetek egyikében egy meglévőt használna: 

- Egy új régióhoz társított Eseményközpontra van szükség.
- Egy másik előfizetéshez társított Event Hubra van szükség.
- Külön kapacitásallokációval rendelkező Eseményközpontra van szükség (azaz a hozzáadott eseményközponttal rendelkező névtér kapacitásigénye meghaladja a 40 TU-küszöbértéket, és nem szeretne a dedikált fürtre menni)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Mi a különbség az Event Hubs Basic és a Standard szintek között?

Az Azure Event Hubs standard szintű szolgáltatása az alapszintű szinten elérhető funkciókon túl is biztosít funkciókat. A Standard a következő funkciókat tartalmazza:

* Hosszabb eseménymegőrzés
* További közvetített kapcsolatok, a túlterhelési díj több, mint a szám tartalmazza
* Több, mint egy [fogyasztói csoport](event-hubs-features.md#consumer-groups)
* [Elfog](event-hubs-capture-overview.md)
* [Kafka integráció](event-hubs-for-kafka-ecosystem-overview.md)

A tarifacsomagokról, köztük a dedikált eseményközpontokról az [Event Hubs díjszabási részleteiben](https://azure.microsoft.com/pricing/details/event-hubs/)talál további információt.

### <a name="where-is-azure-event-hubs-available"></a>Hol érhető el az Azure Event Hubs?

Az Azure Event Hubs minden támogatott Azure-régióban elérhető. Egy listát, látogasson el az [Azure-régiók](https://azure.microsoft.com/regions/) oldalon.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Használhatok egyetlen AMQP-kapcsolatot több eseményközpontból történő küldéshez és fogadáshoz?

Igen, mindaddig, amíg az összes eseményközpontok ugyanabban a névtérben.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Mi az események maximális megőrzési ideje?

Event Hubs Standard szint jelenleg támogatja a maximális megőrzési időszak hét nap. Az eseményközpontok nem állandó adattárként szolgálnak. A 24 óránál hosszabb megőrzési időszakok olyan forgatókönyvekhez szolgálnak, amelyekben célszerű egy eseményfolyamot ugyanabba a rendszerbe visszajátszani; például egy új gépi tanulási modell betanítása vagy ellenőrzése a meglévő adatokon. Ha hét napon túl üzenetmegőrzésre van szüksége, az [Event Hubs Capture](event-hubs-capture-overview.md) engedélyezése az eseményközpontban lekéri az adatokat az eseményközpontból a Storage-fiókba vagy az Ön által választott Azure Data Lake Service-fiókba. A rögzítés engedélyezése a megvásárolt átviteli egységek alapján díjat számít fel.

Beállíthatja a tárfiókban rögzített adatok megőrzési időszakát. Az Azure Storage **életciklus-kezelési** szolgáltatása gazdag, szabályalapú szabályzatot kínál általános célú v2- és blobstorage-fiókokhoz. A szabályzat segítségével átválthatja az adatokat a megfelelő hozzáférési szintekre, vagy lejár az adatok életciklusának végén. További információ: [Az Azure Blob tárolási életciklusának kezelése.](../storage/blobs/storage-lifecycle-management-concepts.md) 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hogyan figyelhetem az Eseményközpontokat?
Az Event Hubs kimerítő metrikákat bocsát ki, amelyek biztosítják az erőforrások állapotát az [Azure Monitor](../azure-monitor/overview.md)számára. Azt is lehetővé teszik, hogy az Event Hubs szolgáltatás általános állapotát nem csak a névtér szintjén, hanem az entitás szintjén is felmérheti. Megtudhatja, hogy milyen figyelést kínál az [Azure Event Hubs.](event-hubs-metrics-azure-monitor.md)

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Milyen portokat kell megnyitni a tűzfalon? 
Az alábbi protokollok segítségével küldhet és fogadhat üzeneteket az Azure Service Bus segítségével:

- Advanced Message Queueing Protocol (AMQP)
- HTTP
- Apache Kafka

Tekintse meg az alábbi táblázatot a kimenő portok meg kell nyitnia, hogy használja ezeket a protokollokat az Azure Event Hubs kommunikálni. 

| Protocol (Protokoll) | Portok | Részletek | 
| -------- | ----- | ------- | 
| AMQP | 5671 és 5672 | Lásd: [AMQP protokollútmutató](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Lásd: [Eseményközpontok használata a Kafka-alkalmazásokból](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Milyen IP-címekre van szükségem az engedélyezési listához?
A kapcsolatokhoz megfelelő IP-címek és fehér listák megkereséséhez kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot a parancssorból: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Jegyezze fel a `Non-authoritative answer`visszaadott IP-címet a ban. Csak akkor változik meg, ha visszaállítja a névteret egy másik fürtre.

Ha a zónaredundanciát használja a névtérhez, néhány további lépést kell tennie: 

1. Először futtassa az nslookup-ot a névtéren.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Jegyezze fel a nevet a **nem mérvadó válasz** szakaszban, amely az alábbi formátumok egyikében található: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Futtassa az nslookup-ot az s1, s2 és s3 utótagokkal rendelkező mindegyikhez, hogy lekérhesd mindhárom példány IP-címét, amely három rendelkezésre állási zónában fut, 

## <a name="apache-kafka-integration"></a>Apache Kafka integráció

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hogyan integrálhatom a meglévő Kafka-alkalmazásomat az Event Hubs-szal?
Az Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Apache Kafka alapú alkalmazások használhatnak. A PaaS Kafka-élményhez csak konfigurációs módosításra van szükség. Alternatívát kínál a saját Kafka-fürt futtatásához. Az Event Hubs támogatja az Apache Kafka 1.0-s és újabb ügyfélverziókat, és együttműködik a meglévő Kafka alkalmazásokkal, eszközökkel és keretrendszerekkel. További információ: [Event Hubs for Kafka repo.](https://github.com/Azure/azure-event-hubs-for-kafka)

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Milyen konfigurációs módosításokat kell végrehajtani a meglévő alkalmazás beszélni Event Hubs?
Egy eseményközponthoz való csatlakozáshoz frissítenie kell a Kafka ügyfél konfigurációját. Ez úgy történik, hogy létrehoz egy Event Hubs névteret, és beszerzi a [kapcsolati karakterláncot.](event-hubs-get-connection-string.md) Módosítsa a bootstrap.servers-t úgy, hogy az Event Hubs fqdn-t és a portot 9093-ra mutassa. Frissítse a sasl.jaas.config-ot, hogy a Kafka ügyfelet az Event Hubs végpontjára irányítsa (amely a kapott kapcsolati karakterlánc), az alábbi megfelelő hitelesítéssel:

bootstrap.servers={YOUR. RENDEZVÉNY. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule szükséges username="$ConnectionString" password="{YOUR. RENDEZVÉNY. Kapcsolat. KARAKTERLÁNC}";

Példa:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.plain.PlainLoginModule szükséges felhasználónév="$ConnectionString" jelszó="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Megjegyzés: Ha a sasl.jaas.config nem támogatott konfiguráció a keretrendszerben, keresse meg a SASL felhasználónév és jelszó beállításához használt konfigurációkat, és használja azokat. Állítsa be a felhasználónevet $ConnectionString és a jelszót az Event Hubs kapcsolati karakterláncához.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Mekkora az Eseményközpontok üzenet-/eseménymérete?
Az Eseményközpontok számára engedélyezett üzenetméret 1 MB.

## <a name="throughput-units"></a>Átviteli egységek

### <a name="what-are-event-hubs-throughput-units"></a>Mik azok az Event Hubs átviteli egységek?
Az Eseményközpontokban az átviteli forgalom határozza megabájtban lévő adatok mennyiségét, illetve az 1 KB-os események számát (ezerben), amelyek be- és kifelé haladnak az Eseményközpontokon keresztül. Ezt az átviteli értéket átviteli egységekben (Átviteli egységek) mérik. Vásárlás iszt.hu mielőtt az Event Hubs szolgáltatás használatát. Az Event Hubs IN-ek explicit módon kiválaszthatók portál- vagy Eseményközpontok erőforrás-kezelősablonjainak használatával. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Az átviteli egységek a névtér összes eseményközpontjára vonatkoznak?
Igen, az átviteli egységek (Átviteli egységek) az Event Hubs névtér összes eseményközpontjára vonatkoznak. Ez azt jelenti, hogy a névtér szintjén vásárol többes készletet, és az adott névtér alatt meg vannak osztva az eseményközpontok között. Minden TU a következő képességekre jogosítja fel a névteret:

- Legfeljebb 1 MB/s a beérkező események (eseményközpontba küldött események), de legfeljebb 1000 be- ésres események, felügyeleti műveletek vagy api-hívások vezérlése API-hívások másodpercenként.
- Legfeljebb 2 MB/s kimenő események (eseményközpontból felhasznált események), de legfeljebb 4096 kimenő események.
- Akár 84 GB eseménytároló (elég az alapértelmezett 24 órás megőrzési időszakra).

### <a name="how-are-throughput-units-billed"></a>Hogyan történik a számlázás az átviteli egységek számlázása?
Az átviteli egységek (Átviteli egységek) számlázása óránként történik. A számlázás az adott órában kiválasztott egységek maximális számán alapul. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hogyan optimalizálhatom az átviteli egységek használatát?
Akár egy átviteli egységet (TU) is indíthat, és bekapcsolhatja az [automatikus felfújást.](event-hubs-auto-inflate.md) Az automatikus felfújás funkció lehetővé teszi, hogy növekszik a toszka, mint a forgalom / hasznos teher növekszik. A felső készlet számát is beállíthatja.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hogyan működik az Event Hubs automatikus felfújási funkciója?
Az automatikus felfújás funkció lehetővé teszi az átviteli egységek (Átviteli egységek) skálázását. Ez azt jelenti, hogy elkezdheti az alacsony tus megvásárlásával, és automatikusan felfújja a tosz-t, ahogy a be- ésalágyulás növekszik. Ez ad ön egy költséghatékony lehetőség, és teljes mértékben ellenőrzése a kezelt számos kezelt. Ez a funkció csak **egy skálázási funkció,** és teljes mértékben szabályozhatja a készletkészlet számának leskálázását frissítésével. 

Előfordulhat, hogy alacsony átviteli egységek (Átviteli egységek), például 2 átviteli egységek. Ha azt jósolja, hogy a forgalom 15 teljes munkaidős egyedre nőhet, kapcsolja be az automatikus felfújási funkciót a névtérben, és állítsa a maximális korlátot 15 teljes értékre. Mostantól a forgalom növekedésével automatikusan növelheti a vezérigazgatóit.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Költség jár az automatikus felfújás funkció bekapcsolásakor?
Ehhez a funkcióhoz **nincs költség** társítva. 

### <a name="how-are-throughput-limits-enforced"></a>Hogyan történik az átviteli korlátok kényszerítése?
Ha a teljes bejövő átviteli sebesség vagy a teljes bejövő események aránya a névtér összes eseményközpontjaiban meghaladja az összesített átviteli egység kibocsátási egységeket, a küldők szabályozása és a bejövő kvóta túllépésének jelző hibaüzenetei jelennek meg.

Ha a teljes kimenő forgalom vagy a névtér összes eseményhubjának teljes kimenő kimenő sebessége meghaladja az összesített átviteli egységkibocsátási egységekszámát, a rendszer szabályozza a fogadókat, és hibaüzeneteket kap, amelyek azt jelzik, hogy a kimenő forgalom kvótáját túllépték. A be- és kilépési kvóták külön vannak kényszerítve, így egyetlen feladó sem lassíthatja az eseményfelhasználást, és a fogadó sem akadályozhatja meg, hogy eseményeket küldjenek egy eseményközpontba.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Korlátozva van a lefoglalható/kiválasztható átviteli egységek száma?
Egy több-bérlős ajánlat, átviteli egységek nőhet akár 40 átviteli egységek (legfeljebb 20 átviteli egységek a portálon, és egy támogatási jegyet, hogy emelje fel 40 átviteli egységek ugyanazon a névtérben). A 40 int-en túl az Event Hubs az Erőforrás-/kapacitásalapú modellt, az **Event Hubs Dedikált fürtöket**kínálja. A dedikált fürtök kapacitásegységekben (CUs) kerülnek értékesítésre.

## <a name="dedicated-clusters"></a>Dedikált fürtök

### <a name="what-are-event-hubs-dedicated-clusters"></a>Mik azok a dedikált Event Hubs-fürtök?
Az Event Hubs dedikált fürtjei egybérlős telepítéseket kínálnak a legigényesebb követelményekkel rendelkező ügyfelek számára. Ez az ajánlat olyan kapacitásalapú fürtöt hoz létre, amelyet nem kötnek átviteli egységek. Ez azt jelenti, hogy a fürt segítségével betöltése és streamelése az adatokat a processzor és a memória használata a fürt. További információ: [Event Hubs Dedicated clusters](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Mire elegendő egyetlen kapacitásegység?
Egy dedikált fürt, mennyit lehet betöltése és stream különböző tényezőktől függ, mint például a gyártók, a fogyasztók, a sebesség, amellyel a betöltés és feldolgozás, és még sok más. 

Az alábbi táblázat a tesztelés során elért teljesítményeredményeket mutatja be:

| Hasznos teher alakja | Vevők | Be- és be- és visszasiklósávszélesség| Be- és be- és visszaküldött üzenetek | Kimenő sávszélesség | Kimenő üzenetek | Teljes Tosz | Tous per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1 KB-os kötegek | 2 | 400 MB/mp | 400 ezer üzenet/mp | 800 MB/mp | 800 ezer üzenet/mp | 400 tosz | 100 tosz | 
| 10x10 KB-os kötegek | 2 | 666 MB/mp | 66,6 ezer üzenet/mp | 1,33 GB/mp | 133 ezer üzenet/mp | 666 Tosz | 166 Tosz |
| 6x32 KB-os kötegek | 1 | 1,05 GB/mp | 34 ezer üzenet / mp | 1,05 GB/mp | 34 ezer üzenet/mp | 1000 tosz | 250 Tosz |

A vizsgálat során a következő kritériumokat alkalmazták:

- Egy dedikált Event Hubs-fürt öt kapacitásegységgel (CUs) használatos. 
- A betöltéshez használt eseményközpont 200 partícióval volt. 
- A betöltött adatokat két fogadóalkalmazás fogadta az összes partícióról.

Az eredmények képet adnak arról, hogy mit lehet elérni egy dedikált Event Hubs-fürttel. Emellett a felszentelési fürt jön az Event Hubs Capture engedélyezve van a mikro-köteg és a hosszú távú megőrzési forgatókönyvek.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hogyan hozhatok létre egy dedikált eseményközpontokat?
Hozzon létre egy eseményközpontok dedikált fürt küld el egy [kvóta növelése támogatási kérelmet,](https://portal.azure.com/#create/Microsoft.Support) vagy kapcsolatba lép az [Event Hubs csapat.](mailto:askeventhubs@microsoft.com) Általában körülbelül két hetet vesz igénybe, hogy a fürt telepítve, és átadta az Ön által használt. Ez a folyamat ideiglenes, amíg egy teljes önkiszolgálás érhető el az Azure Portalon keresztül.

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="how-many-partitions-do-i-need"></a>Hány partícióra van szükségem?
A partíciók száma az eseményközpont létrehozásakor határozható meg, és 2 és 32 közé eshet. A partíciók száma nem módosítható, ezért érdemes a partíciók számának beállításakor a hosszú távú skálázást. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. A partíciókról további információt a Partíciók című [témakörben talál.](event-hubs-features.md#partitions)

Előfordulhat, hogy a létrehozás időpontjában a lehető legmagasabb értéket, azaz 32 értéket szeretné beállítani. Ne feledje, hogy egynél több partícióval eredményez több partícióra küldött eseményeket anélkül, hogy megtartaná a sorrendet, kivéve, ha úgy konfigurálja a küldőket, hogy csak a 32-ből csak egy partícióra küldjenek, így a fennmaradó 31 partíció felesleges. Az előbbi esetben mind a 32 partíción végig kell olvasnia az eseményeket. Az utóbbi esetben nincs nyilvánvaló többletköltség, eltekintve az eseményprocesszor-állomáson az extra konfigurációtól.

Az Event Hubs úgy van kialakítva, hogy fogyasztói csoportonként egyetlen partícióolvasót engedélyezze. A legtöbb esetben a négy partíció alapértelmezett beállítása elegendő. Ha az esemény feldolgozása méretezése, érdemes megfontolni további partíciók hozzáadása. Nincs adott átviteli korlát egy partíción, azonban az összesített átviteli a névtérben korlátozza az átviteli egységek száma. A névtérben lévő átviteli egységek számának növelésével további partíciókat szeretne, amelyek lehetővé teszik az egyidejű olvasók számára a saját maximális átviteli teljesítmény elérését.

Azonban ha van egy modell, amelyben az alkalmazás affinitása egy adott partícióhoz, a partíciók számának növelése nem lehet semmilyen hasznot az Ön számára. További információt a [rendelkezésre állás és a konzisztencia című témakörben talál.](event-hubs-availability-and-consistency.md)

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-more-pricing-information"></a>Hol találhatok további árképzési információkat?

Az Event Hubs díjszabásáról az [Event Hubs díjszabási részleteiben talál](https://azure.microsoft.com/pricing/details/event-hubs/)teljes körű tájékoztatást.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Az Event Hubs-események 24 óránál tovább történő megtartásáért díjat számítunk fel?

Az Event Hubs standard szint lehetővé teszi, hogy az üzenetek megőrzési időszaka 24 óránál hosszabb, legfeljebb hét napig. Ha a tárolt események teljes száma meghaladja a kiválasztott átviteli egységek (84 GB/átviteli egység) tárolási keretét, a keretet meghaladó méretet a közzétett Azure Blob-tárolási díjban számítjuk fel. Az egyes átviteli egységek tárolási kerete fedezi a 24 órás megőrzési időszakok (alapértelmezett) tárolási költségeit, még akkor is, ha az átviteli egységet a maximális bejövő kibocsátási egységig használják.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hogyan történik az Event Hubs tárolási mérete, és hogyan számítják fel?

Az összes tárolt esemény teljes méretét, beleértve az eseményfejlécek vagy a lemeztároló struktúrák belső terhelését az összes eseményközpontban, a nap folyamán mérik. A nap végén kiszámítja a maximális tárolási méretet. A napitárolási keret kiszámítása a nap folyamán kiválasztott átviteli egységek minimális száma alapján történik (minden átviteli egység 84 GB-os engedményt biztosít). Ha a teljes méret meghaladja a számított napi tárolási keretet, a felesleges tárhely számlázása az Azure Blob tárolási díjai alapján történik **(helyileg redundáns tárolási** díjmellett).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hogyan történik az Event Hubs inress események kiszámítása?

Az eseményközpontba küldött minden esemény számlázható üzenetnek számít. A *be- éselődési esemény* olyan adategységként van definiálva, amely legfeljebb 64 KB. Minden olyan esemény, amely legfeljebb 64 KB méretű, egy számlázható eseménynek minősül. Ha az esemény nagyobb, mint 64 KB, a számlázható események száma az esemény mérete alapján, a 64 KB többszörösei szerint történik. Például egy 8 KB-os esemény, amelyet az eseményközpontnak küldenek, egy eseményként kerül számlázásra, de az eseményközpontba küldött 96 KB-os üzenet számlázása két eseményként történik.

Az eseményközpontból felhasznált események, valamint a felügyeleti műveletek és a vezérlőhívások, például az ellenőrzőpontok, nem számítanak számlázható be- ésbevétel-eseményeknek, hanem az átviteli egységkeretig gyűlnek össze.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>A felügyelt kapcsolati díjak az Event Hubs-ra vonatkoznak?

A csatlakozási díjak csak akkor érvényesek, ha az AMQP protokollt használja. A HTTP-n keresztüli események küldéséért nem számítunk fel csatlakozási díjat, függetlenül a küldő rendszerek vagy eszközök számától. Ha az AMQP használatát tervezi (például hatékonyabb eseménystreamelésvagy kétirányú kommunikáció engedélyezése az IoT parancs- és vezérlőforgatókönyvekben), tekintse meg az [Event Hubs díjszabási információkat](https://azure.microsoft.com/pricing/details/event-hubs/) tartalmazó lapon, hogy az egyes szolgáltatási szintekhány kapcsolat szerepelnek-e.

### <a name="how-is-event-hubs-capture-billed"></a>Hogyan történik az Eseményközpontok rögzítése?

A rögzítés akkor engedélyezett, ha a névtér bármely eseményközpontjában engedélyezve van a Rögzítés beállítás. Az Event Hubs Capture számlázása megvásárolt átviteli egységenként óránként történik. Az átviteli egység számának növelésével vagy csökkentésével az Event Hubs Capture számlázási tükrözi ezeket a változásokat a teljes órás lépésekben. Az Event Hubs Capture számlázásról az [Event Hubs díjszabási információi című témakörben talál](https://azure.microsoft.com/pricing/details/event-hubs/)további információt.

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Kiszámláznak az Event Hubs-rögzítéshez kiválasztott tárfiókért?

A Rögzítés egy eseményközpontban engedélyezett tárfiókot használ. Mivel ez a tárfiók, a konfiguráció módosításait az Azure-előfizetés számlázása.

## <a name="quotas"></a>Kvóták

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Vannak-e kvóták társítva Event Hubs?

Az összes Event Hubs-kvóta listáját a [kvóták](event-hubs-quotas.md)ban láthatja.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem tudok névteret létrehozni, miután egy másik előfizetésből letettem? 
Ha töröl egy névteret egy előfizetésből, várjon 4 órát, mielőtt újra létrehozza azt ugyanazzal a névvel egy másik előfizetésben. Ellenkező esetben a következő hibaüzenet `Namespace already exists`jelenhet meg: . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Melyek az Event Hubs által létrehozott kivételek és a javasolt műveletek?

A lehetséges Eseményközpontok alóli kivételek listáját a [Kivételek áttekintése című témakörben](event-hubs-messaging-exceptions.md)találja.

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Event Hubs kétféle [diagnosztikai naplót](event-hubs-diagnostic-logs.md) támogat – hibanaplók és működési naplók rögzítése – mindkettő a jsonban jelenik meg, és az Azure Portalon keresztül kapcsolható be.

### <a name="support-and-sla"></a>Támogatás és SLA

Az Event Hubs technikai támogatása a [közösségi fórumokon](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)érhető el. A számlázási és előfizetés-kezelési támogatás díjmentesen biztosított.

Ha többet szeretne megtudni az SLA-ról, tekintse meg a [Szolgáltatásiszint-szerződések](https://azure.microsoft.com/support/legal/sla/) oldalt.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Eseményközpontok – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Eseményközpontok automatikus felfújása](event-hubs-auto-inflate.md)
