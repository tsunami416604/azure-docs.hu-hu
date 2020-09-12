---
title: Gyakori kérdések – Azure Event Hubs | Microsoft Docs
description: Ez a cikk a gyakori kérdések (GYIK) listáját tartalmazza az Azure Event Hubs és azok válaszait illetően.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9995588e618679ae38a11aff26485d1ba0b60688
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89288967"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs gyakori kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-an-event-hubs-namespace"></a>Mi az Event Hubs névtér?
A névtér az Event hub/Kafka témakörök hatóköri tárolója. Egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)-t biztosít. A névtér olyan alkalmazás-tárolóként szolgál, amely több Event hub-vagy Kafka-témakört is képes elhelyezni. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Mikor hozzon létre egy új névteret, vagy használjon egy meglévő névteret?
A kapacitás kiosztása ([átviteli egység (TUs)](#throughput-units)) a névtér szintjén kerül kiszámlázásra. A névtér egy régióhoz is társítva van.

A következő esetekben érdemes lehet új névteret létrehozni a meglévő használata helyett: 

- Szüksége lesz egy új régióhoz társított Event hub-ra.
- Egy másik előfizetéshez társított Event hub szükséges.
- Az Event hub-nak külön kapacitás-kiosztással kell rendelkeznie (azaz a hozzáadott Event hub-hoz tartozó névtér kapacitásának nagyobbnak kell lennie a 40-as számú TU-küszöbértéknél, és nem kívánja a dedikált fürthöz jutni)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Mi a különbség az alapszintű és a standard csomag Event Hubs között?

Az Azure Event Hubs standard szintje az alapszintű szinten elérhető funkciókat is biztosítja. A standard a következő funkciókat tartalmazza:

* Hosszú esemény megőrzése
* További felügyelt kapcsolatok, amelyek több mint a megadott számon felüli díjat tartalmaznak
* Több, mint egyetlen [fogyasztói csoport](event-hubs-features.md#consumer-groups)
* [Rögzítés](event-hubs-capture-overview.md)
* [Kafka-integráció](event-hubs-for-kafka-ecosystem-overview.md)

A díjszabással kapcsolatos további információkért, beleértve a dedikált Event Hubs is, tekintse meg a [Event Hubs díjszabási részleteit](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Hol érhető el az Azure Event Hubs?

Az Azure Event Hubs az összes támogatott Azure-régióban elérhető. A listában keresse fel az [Azure-régiók](https://azure.microsoft.com/regions/) lapot.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Használhatok egyetlen AMQP-kapcsolattal több Event hub-ból való küldésre és fogadásra?

Igen, ha az összes esemény-hubok ugyanabban a névtérben vannak.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Mi a maximális megőrzési időszak az eseményekhez?

A standard szintű Event Hubs jelenleg legfeljebb hét napos megőrzési időtartamot támogat. Az Event hubok nem végleges adattárként szolgálnak. A 24 óránál hosszabb megőrzési időtartamok olyan forgatókönyvekhez készültek, amelyekben az esemény-adatfolyamok ugyanabba a rendszerbe való visszajátszására alkalmasak. például egy új gépi tanulási modell betanításához vagy ellenőrzéséhez a meglévő adattípusokon. Ha az üzenetek megőrzése hét nap elteltével is szükséges, a [Event Hubs rögzítésének](event-hubs-capture-overview.md) engedélyezése az Event hub-on az Event hub adatait az Ön által választott Storage-fiókba vagy Azure Data Lake-szolgáltatási fiókba kéri le. Ha engedélyezi a rögzítést, a megvásárolt átviteli egységek alapján kell fizetnie.

A rögzített adatok megőrzési időtartamát beállíthatja a Storage-fiókban. Az Azure Storage **életciklus-kezelési** funkciója az általános célú v2-és blob Storage-fiókok gazdag, szabályon alapuló szabályzatát kínálja. A szabályzat segítségével átválthatja az adatait a megfelelő hozzáférési rétegekbe, vagy lejárhat az adatéletciklus végén. További információ: [Az Azure Blob Storage életciklusának kezelése](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hogyan figyeli a Event Hubs?
Event Hubs teljes mérőszámokat bocsát ki, amelyek a [Azure monitor](../azure-monitor/overview.md)erőforrásainak állapotát biztosítják. Emellett lehetővé teszik a Event Hubs szolgáltatás általános állapotának felmérését, nem csak a névtér szintjén, hanem az entitás szintjén is. Ismerje meg, hogy az [Azure Event Hubs](event-hubs-metrics-azure-monitor.md)milyen monitorozási lehetőségeket kínál.

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Milyen portokat kell megnyitni a tűzfalon? 
Az üzenetek küldéséhez és fogadásához a következő protokollokat használhatja Azure Service Bus:

- Advanced Message Queueing Protocol (AMQP)
- HTTP
- Apache Kafka

Az alábbi táblázat tartalmazza azokat a kimenő portokat, amelyeket meg kell nyitni a protokollok Azure Event Hubs-vel való kommunikációhoz való használatához. 

| Protokoll | Portok | Részletek | 
| -------- | ----- | ------- | 
| AMQP | 5671 és 5672 | Lásd: [AMQP protokoll – útmutató](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Lásd: [Event Hubs használata a Kafka-alkalmazásokból](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Milyen IP-címeket kell engedélyezni?
Az alábbi lépéseket követve megkeresheti a kapcsolatok engedélyezett listájához hozzáadandó megfelelő IP-címeket:

1. Futtassa a következő parancsot egy parancssorból: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Jegyezze fel a visszaadott IP-címet `Non-authoritative answer` . Ha egy másik fürtre állítja vissza a névteret, csak akkor változna meg a változás.

Ha a zóna redundanciát használja a névtérhez, néhány további lépést is végre kell hajtania: 

1. Először futtassa az nslookupt a névtéren.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Jegyezze fel a nevet a **nem mérvadó válasz** szakaszban, amely az alábbi formátumok egyike: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Futtassa az nslookupt mindegyikhez az S1, az S2 és az S3 utótaggal a három rendelkezésre állási zónában futó mindhárom példány IP-címeinek lekéréséhez. 

### <a name="where-can-i-find-client-ip-sending-or-receiving-msgs-to-my-namespace"></a>Hol találom az ügyfél IP-küldési és-fogadási üzeneteit a névtérbe?
Először engedélyezze az [IP-szűrést](event-hubs-ip-filtering.md) a névtérben. 

Ezután engedélyezze a diagnosztikai naplókat [Event Hubs virtuális hálózati kapcsolatok eseményeihez](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) a [diagnosztikai naplók engedélyezése](event-hubs-diagnostic-logs.md#enable-diagnostic-logs)című témakör utasításait követve. Ekkor megtekintheti az IP-címet, amelyhez a rendszer megtagadja a kapcsolódást.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="apache-kafka-integration"></a>Apache Kafka integráció

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>A meglévő Kafka-alkalmazást a Event Hubs Hogyan integrálni?
A Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Apache Kafka-alapú alkalmazásai használhatnak. A konfigurációs változás minden, ami szükséges a Péteri Kafka-élményhez. Alternatívát biztosít a saját Kafka-fürt futtatásához. Event Hubs támogatja a Apache Kafka 1,0-es és újabb verziójú ügyfélprogramokat, és együttműködik a meglévő Kafka-alkalmazásokkal, eszközökkel és keretrendszerekkel. További információ: [Event Hubs for Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)-tárház.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Milyen konfigurációs módosításokat kell végrehajtani a meglévő alkalmazáshoz, hogy Event Hubs beszéljen?
Az Event hub-hoz való kapcsolódáshoz frissítenie kell a Kafka-ügyfél konfigurációit. Event Hubs névtér létrehozásával és a [kapcsolatok karakterláncának](event-hubs-get-connection-string.md)beszerzésével végezhető el. Módosítsa a bootstrap. Servert úgy, hogy a Event Hubs teljes tartománynevet és a portot 9093-re mutassa. Frissítse a sasl.jaas.configt, hogy a Kafka-ügyfelet a Event Hubs végpontra irányítsa (amely az Ön által beszerzett kapcsolódási karakterlánc), a megfelelő hitelesítéssel az alábbi ábrán látható módon:

bootstrap. Servers = {a. EVENTHUBS. FQDN}: 9093 Request. timeout. MS = 60000 Security. Protocol = SASL_SSL SASL. mechanizmus = PLAIN sasl.jaas.config= org. Apache. Kafka. Common. Security. Plain. PlainLoginModule kötelező Felhasználónév = "$ConnectionString" password = "{a. EVENTHUBS. Kapcsolat. KARAKTERLÁNC} ";

Példa:

bootstrap. Servers = dummynamespace. servicebus. Windows. net: 9093 Request. timeout. MS = 60000 Security. Protocol = SASL_SSL SASL. mechanizmus = PLAIN sasl.jaas.config= org. Apache. Kafka. Common. Security. Plain. PlainLoginModule szükséges username = "$ConnectionString" password = "Endpoint = SB://dummynamespace.servicebus.Windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Megjegyzés: Ha sasl.jaas.config nem támogatott konfiguráció a keretrendszerben, keresse meg a SASL felhasználónevének és jelszavának beállításához használt konfigurációkat, és használja ezeket. Adja meg a felhasználónevet $ConnectionString és a jelszót a Event Hubs-kapcsolódási karakterlánchoz.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Mi a Event Hubs üzenet/esemény mérete?
Az Event Hubs számára engedélyezett maximális üzenet mérete 1 MB.

## <a name="throughput-units"></a>Átviteli egységek

### <a name="what-are-event-hubs-throughput-units"></a>Mik azok a Event Hubs átviteli egységek?
A Event Hubs átviteli sebessége a Mega bájtokban lévő adatok mennyiségét, illetve az olyan 1 KB-os események számát határozza meg, amelyek beáramlnak és leküldenek Event Hubs keresztül. Ezt az átviteli sebességet az átviteli egységek (TUs) mérik. A Event Hubs szolgáltatás használatának megkezdése előtt vásárolja meg az TUs-t. Explicit módon kiválaszthatja Event Hubs TUs-t a portálon vagy Event Hubs Resource Manager-sablonok használatával. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Az átviteli egységek a névtér összes esemény-hubhoz érvényesek?
Igen, az átviteli egységek (TUs) a Event Hubs névtér összes esemény-hubhoz érvényesek. Ez azt jelenti, hogy a rendszer a névtér szintjén vásárolja meg az TUs-t, és az adott névtér alá tartozó Event hubok között meg van osztva. Mindegyik TU a következő képességekhez jogosítja fel a névteret:

- Akár 1 MB/s a bejövő események másodpercenkénti száma (esemény központba elküldve), de legfeljebb 1000 bejövő esemény, felügyeleti művelet vagy vezérlési API-hívás másodpercenként.
- Akár 2 MB/s a kimenő események másodpercenkénti száma (az Event hub által felhasznált események), de legfeljebb 4096 kimenő esemény.
- Akár 84 GB-nyi esemény-tárterület (elegendő az alapértelmezett 24 órás megőrzési időszakhoz).

### <a name="how-are-throughput-units-billed"></a>Hogyan történik az átviteli egységek számlázása?
Az átviteli egységek (TUs) számlázása óránként történik. A számlázás az adott órában kiválasztott egységek maximális száma alapján történik. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hogyan optimalizálható a használat az átviteli egységeken?
Akár alacsony átviteli egység (TU) is elindítható, és bekapcsolhatja az [automatikus](event-hubs-auto-inflate.md)kikapcsolást. Az automatikus kibővítés funkció lehetővé teszi, hogy növelje a forgalom/hasznos adattartalom növekedését. A felső korlátot is megadhatja az TUs számának.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hogyan működik a Event Hubs a működésének automatikus kivonása?
Az automatikus kiemelés funkció lehetővé teszi az átviteli egységek (TUs) vertikális felskálázását. Ez azt jelenti, hogy az alacsony állapotú és az automatikus kiemelések megvásárlásával megvásárolhatja az alapszintű állapotot, mivel a bejövő forgalom növekszik. Költséghatékony megoldást kínál, és teljes körűen szabályozhatja a felügyelni kívánt TUs mennyiségét. Ez **a funkció egy** vertikálisan felhasználható funkció, és a frissítéssel teljes mértékben szabályozható az TUs-szám skálázása. 

Előfordulhat, hogy alacsony átviteli egységekkel (TUs) szeretne kezdeni, például 2 TUs. Ha azt jósolja, hogy a forgalom 15 TUs-ra nőhet, kapcsolja be az automatikus feltöltés funkciót a névtérben, és állítsa be a maximális korlátot 15 TUs-re. A forgalom növekedésével mostantól automatikusan növelheti az TUs-t.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Van-e díj társítva az automatikus feltöltés funkció bekapcsolásakor?
Ehhez a szolgáltatáshoz **nem tartozik díj** . 

### <a name="how-are-throughput-limits-enforced"></a>Hogyan kényszeríti az átviteli korlátokat?
Ha a névtérben lévő összes esemény-hubhoz kiterjedő teljes beáramlási sebesség vagy **a teljes** belépési arány meghaladja az összesített átviteli egységre vonatkozó kedvezményeket, a küldők szabályozva lesznek, és a bejövő forgalomra vonatkozó kvóta túllépése miatti hibákat jeleznek.

Ha a teljes **kimenő** forgalom vagy az esemény teljes kilépési aránya a névtérben lévő összes esemény hubok esetében meghaladja az összesített átviteli egységre vonatkozó kedvezményeket, a rendszer szabályozza a fogadókat, de nem generál szabályozási hibát. 

A bejövő és a kimenő forgalomra vonatkozó kvóták külön vannak kikényszerítve, így egyetlen feladó sem csökkentheti az események felhasználását, és nem akadályozhatja meg, hogy a fogadó eseményt küldjön az Event hub-ba.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Korlátozva van a foglalható/kiválasztható átviteli egységek (TUs) száma?
Több-bérlős ajánlat esetében az átviteli egységek akár 40-ig is növekednek (a portálon akár 20 darabot is kiválaszthat, és egy támogatási jegyet is megadhat, hogy 40 TUs-re ugyanazon a névtéren). Az 40-as túllépés Event Hubs a **dedikált Event Hubs-fürtök**nevű erőforrás/kapacitás alapú modellt kínálja. A dedikált fürtöket kapacitási egységekben (ke) értékesítjük.

## <a name="dedicated-clusters"></a>Dedikált fürtök

### <a name="what-are-event-hubs-dedicated-clusters"></a>Mik azok a dedikált Event Hubs-fürtök?
Dedikált Event Hubs-fürtök egybérlős üzemelő példányokat biztosítanak a legigényesebb követelményeknek megfelelő ügyfelek számára. Ez az ajánlat olyan kapacitás-alapú fürtöt hoz létre, amely nem az átviteli egységekhez van kötve. Ez azt jelenti, hogy a fürtöt használhatja a fürt CPU-és memóriahasználat által diktált adatok betöltésére és továbbítására. További információ: dedikált Event Hubs- [fürtök](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Mire elegendő egyetlen kapacitásegység?
Egy dedikált fürt esetében a betöltés és az adatfolyam mennyisége a különböző tényezőktől, például a termelőktől, a fogyasztóktól, a betöltés és a feldolgozástól, valamint sok más tényezőtől függ. 

A következő táblázat a tesztelés során elért teljesítménytesztek eredményeit mutatja be:

| Hasznos adat alakzat | Fogadók | Bejövő sávszélesség| Bejövő üzenetek | Kimenő sávszélesség | Kimenő üzenetek | Teljes TUs | TUs/CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB kötegek | 2 | 400 MB/s | 400k üzenetek/mp | 800 MB/s | 800k üzenetek/mp | 400 TUs | 100 TUs | 
| 10x10KB kötegek | 2 | 666 MB/s | 66.6 üzenet/mp | 1,33 GB/s | 133k üzenetek/mp | 666 TUs | 166 TUs |
| 6x32KB kötegek | 1 | 1,05 GB/s | 34k üzenetek/mp | 1,05 GB/s | 34k üzenetek/mp | 1000 TUs | 250 TUs |

A tesztelés során a rendszer a következő feltételeket használta:

- Egy dedikált Event Hubs fürt négy kapacitású egységgel (ke) volt használva. 
- A betöltéshez használt Event hub 200 partíciót tartalmazott. 
- A betöltött adatok az összes partíciótól kapott két fogadó alkalmazástól érkeztek.

Az eredményekből megtalálhatja, hogy mit lehet megvalósítani egy dedikált Event Hubs-fürttel. Emellett a dedikált fürt a mikro-és hosszú távú adatmegőrzési forgatókönyvekhez engedélyezett Event Hubs rögzítéssel is rendelkezik.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hogyan hozzon létre egy dedikált Event Hubs-fürtöt?
Egy Event Hubs dedikált fürtöt úgy hozhat létre, hogy egy [kvótával bővíti a támogatási kérést](https://portal.azure.com/#create/Microsoft.Support) , vagy kapcsolatba lép a [Event Hubs csapatával](mailto:askeventhubs@microsoft.com). Általában két hetet vesz igénybe, hogy a fürt üzembe helyezése és átadása az Ön általi használatra megtörténjen. Ez a folyamat ideiglenes, amíg a Azure Portalon keresztül elérhetővé nem válik a teljes körű kiszolgálás.

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="how-many-partitions-do-i-need"></a>Hány partícióra van szükségem?
A partíciók száma az eseményközpont létrehozásakor határozható meg, és 2 és 32 közé eshet. A partíciók száma nem módosítható, ezért érdemes megfontolni a hosszú távú méretezést a partíciók számának beállításakor. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. További információ a partíciókon: [partíciók](event-hubs-features.md#partitions).

A létrehozás időpontjában érdemes lehet beállítani a lehető legmagasabb értéket, amely a 32. Ne feledje, hogy több partíció használata esetén a rendszer több partícióba küldi az eseményeket anélkül, hogy megtartja a sorrendet, kivéve, ha a küldőket úgy konfigurálja, hogy csak egyetlen, a 32-es partíción kívülről küldje el a fennmaradó 31 partíciót. Az előző esetben az összes 32-partíción el kell olvasnia az eseményeket. Az utóbbi esetben az Event Processor Host-on kívül az extra konfigurációtól eltekintve nincs nyilvánvaló további díj.

Event Hubs úgy lett kialakítva, hogy a felhasználói csoportok számára egyetlen partíciós olvasót engedélyezzen. A legtöbb felhasználási esetben a négy partíció alapértelmezett beállítása elegendő. Ha az esemény feldolgozását szeretné méretezni, érdemes lehet további partíciókat hozzáadnia. Nincs konkrét átviteli korlát a partíción, azonban a névtérben lévő összesített átviteli sebességet az átviteli egységek száma korlátozza. A névtérben lévő átviteli egységek számának növelésével további partíciók is lehetnek, amelyek lehetővé teszik az egyidejű olvasók számára a maximális átviteli sebesség elérését.

Ha azonban van olyan modellje, amelyben az alkalmazás affinitással rendelkezik egy adott partícióhoz, akkor a partíciók számának növelésével nem élvezheti Önt. További információ: [rendelkezésre állás és konzisztencia](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-more-pricing-information"></a>Hol találhatok további díjszabási információkat?

A Event Hubs díjszabásával kapcsolatos részletes információkért tekintse meg a [Event Hubs díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Van olyan díj, amely több mint 24 óránál Event Hubs eseményt tart fenn?

A standard szintű Event Hubs 24 óránál hosszabb üzenetek megőrzési időtartamát teszi lehetővé legfeljebb hét napig. Ha a tárolt események teljes száma meghaladja a tárolási kapacitást a kiválasztott átviteli egységek (84 GB/s átviteli egység) számára, akkor a juttatást meghaladó méretet a közzétett Azure Blob Storage-díjszabás alapján számítjuk fel. Az egyes átviteli egységekben a tárolási támogatás kiterjed a 24 órás megőrzési időtartamra (az alapértelmezett értékre), még akkor is, ha az átviteli egység a maximális bejövő forgalomhoz képest fel van használva.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hogyan számítja ki és számítja ki a Event Hubs tárolási méretet?

Az összes tárolt esemény teljes mérete, beleértve az esemény-fejlécek belső terhelését vagy az összes Event hub lemezes tárolási struktúráját, a nap folyamán méri. A nap végén a rendszer kiszámítja a maximális tárolási méretet. A napi tárterületet a nap folyamán kiválasztott átviteli egységek minimális száma alapján számítjuk ki (minden átviteli egység 84 GB-os kedvezményt biztosít). Ha a teljes méret meghaladja a napi kiszámított tárterületet, a felesleges tárterületet az Azure Blob Storage díjszabása (a **helyileg redundáns tárolási** arány) alapján számlázjuk.

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hogyan számítják ki Event Hubs beáramlási események kiszámításának módját?

Az Event hub-nak küldött összes esemény számlázandó üzenetnek számít. A *bejövő események* olyan adategységként vannak definiálva, amely kisebb vagy egyenlő, mint 64 kb. A 64 KB-nál kisebb vagy azzal egyenlő események csak egy számlázandó eseménynek tekintendők. Ha az esemény meghaladja a 64 KB-ot, a számlázható események száma az esemény méretétől függően számítja ki a 64 KB-os többszörösét. Az Event hub-nak küldött 8 KB-os esemény például egy eseménynek számít, az Event hub-nak küldött 96 KB-os üzenet pedig két eseménynek számít.

Az Event hub által felhasznált események, valamint a felügyeleti műveletek és a vezérlési hívások, például az ellenőrzőpontok nem számítanak számlázandó beáramlási eseménynek, hanem az átviteli egységre is érvényesek.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>A felügyelt kapcsolatok díjai a Event Hubsra vonatkoznak?

A kapcsolati díjak csak akkor érvényesek, ha az AMQP protokollt használják. Az események HTTP-n keresztüli küldéséhez nem tartoznak kapcsolódási díjak, a küldő rendszerek és eszközök számától függetlenül. Ha azt tervezi, hogy AMQP használ (például a hatékonyabb esemény-adatfolyam eléréséhez vagy a kétirányú kommunikáció engedélyezéséhez a IoT-parancsok és-vezérlési forgatókönyvek esetében), tekintse meg [Event Hubs a díjszabási információkkal](https://azure.microsoft.com/pricing/details/event-hubs/) foglalkozó oldalt, amelyből megtudhatja, hogy hány kapcsolat szerepel az egyes szolgáltatási rétegekben.

### <a name="how-is-event-hubs-capture-billed"></a>Hogyan történik a Event Hubs rögzítésének számlázása?

A rögzítés akkor engedélyezett, ha a névtérben található bármely Event hub engedélyezve van a rögzítési lehetőséggel. Event Hubs a rögzítés óradíjat igényel a megvásárolt átviteli egységenként. Az átviteli egységek számának növelése vagy csökkenése esetén Event Hubs rögzítési számlázás a teljes óránkénti növekmények alapján mutatja be ezeket a módosításokat. További információ a Event Hubs rögzítésének számlázásáról: [Event Hubs díjszabási információk](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Fizetnem kell a Event Hubs rögzítéshez kiválasztott Storage-fiókhoz?

A rögzítés a megadott Storage-fiókot használja, ha az engedélyezve van az Event hub-ban. Ahogy a Storage-fiókja, a konfiguráció minden változása az Azure-előfizetésre lesz kiszámlázva.

## <a name="quotas"></a>Kvóták

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Van-e kvóta társítva a Event Hubshoz?

Az összes Event Hubs kvóta listáját itt tekintheti meg: [kvóták](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem lehet névteret létrehozni egy másik előfizetésből való törlés után? 
Ha töröl egy névteret egy előfizetésből, várjon 4 órát, mielőtt újra létrehozza azt ugyanazzal a névvel egy másik előfizetésben. Ellenkező esetben a következő hibaüzenet jelenhet meg: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Melyek a Event Hubs által generált kivételek és a javasolt műveletek?

A lehetséges Event Hubs kivételek listáját lásd: [kivételek áttekintése](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Event Hubs a [diagnosztikai naplók](event-hubs-diagnostic-logs.md) két típusát támogatja – a rögzítési hibák naplóit és az operatív naplókat – mindkettő a JSON-ban szerepel, és a Azure Portaln keresztül kapcsolható be.

### <a name="support-and-sla"></a>Támogatás és SLA

A Event Hubs technikai támogatása a [Microsoft Q&](/answers/topics/azure-service-bus.html)keresztül érhető el Azure Service Bus. A számlázási és előfizetés-kezelési támogatást díjmentesen biztosítjuk.

Ha többet szeretne megtudni az SLA-ról, tekintse meg a [szolgáltatói szerződéseket](https://azure.microsoft.com/support/legal/sla/) ismertető oldalt.

## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](./event-hubs-about.md)
* [Event hub létrehozása](event-hubs-create.md)
* [Event Hubs automatikus kiemelés](event-hubs-auto-inflate.md)
