---
title: Válasszon árképzési szintet vagy SKU-t
titleSuffix: Azure Cognitive Search
description: 'Az Azure Cognitive Search kiépíthető ezekben az SKU-ban: az ingyenes, az alapszintű és a standard szintű, valamint a standard szint számos erőforrás-konfigurációban és kapacitási szinten érhető el.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 00080322b4fa474e5095d40afb041134e1a85fe7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519733"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Válasszon árképzési szintet az Azure Cognitive Search

Azure Cognitive Search szolgáltatás létrehozásakor a rendszer egy [erőforrást hoz létre](search-create-service-portal.md) a szolgáltatás élettartamára rögzített árképzési szinten (vagy SKU-ban). A rétegek közé tartoznak az ingyenes, az alapszintű, a standard és a Storage optimalizált funkciók. A standard és a Storage optimalizált szolgáltatás számos konfigurációval és kapacitással érhető el.

A legtöbb ügyfél az ingyenes szintjével kezdődik, így kiértékelheti a szolgáltatást. Az értékelés után a rendszer közösen létrehoz egy második szolgáltatást a magasabb szintű fejlesztési és éles környezetekben való üzembe helyezéshez.

## <a name="feature-availability-by-tier"></a>A szolgáltatás rendelkezésre állása rétegek szerint

Az alábbi táblázat a réteggel kapcsolatos szolgáltatások megkötéseit ismerteti.

| Funkció | Korlátozások |
|---------|-------------|
| [indexelők](search-indexer-overview.md) | Az indexelő nem érhető el az S3 HD-ben. |
| [MI-bővítés](search-security-manage-encryption-keys.md) | Az ingyenes szinten fut, de nem ajánlott. |
| [Ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) | Az ingyenes szinten nem érhető el. |
| [IP-tűzfal-hozzáférés](service-configure-firewall.md) | Az ingyenes szinten nem érhető el. |
| [Integráció az Azure Private linkkel](service-create-private-endpoint.md) | Az ingyenes szinten nem érhető el. |

A legtöbb funkció minden szinten elérhető, beleértve az ingyenes, de az erőforrás-igényes funkciók nem működnek megfelelően, kivéve, ha elegendő kapacitást biztosít. Például az [AI-dúsítás](cognitive-search-concept-intro.md) olyan hosszan futó képességekkel rendelkezik, amelyek egy ingyenes szolgáltatás esetében időtúllépést mutatnak, kivéve, ha az adatkészlet kicsi.

## <a name="tiers-skus"></a>Rétegek (SKU)

A rétegek a következők szerint különböztethetők meg:

+ A létrehozható indexek és indexelő mennyisége
+ Partíciók mérete és sebessége (fizikai tárterület)

A kiválasztott rétegek határozzák meg a számlázható sebességet. A következő képernyőkép a Azure Portal megjeleníti az elérhető csomagokat, mínusz a díjszabás (amely a Portálon és a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található. Az **ingyenes**, az **alapszintű**és a **standard** a leggyakoribb szintek.

Az **ingyenes** szolgáltatás korlátozott keresési szolgáltatást hoz létre kisebb projektekhez, beleértve a gyors útmutatót és az oktatóanyagokat. Belsőleg a replikák és partíciók megosztása több előfizető között történik. Az ingyenes szolgáltatás nem méretezhető, és nem futtathat jelentős számítási feladatokat.

Az **alapszintű és a** **standard** a leggyakrabban használt számlázó rétegek, amelyek alapértelmezett értéke a **standard** . A vezérlőhöz tartozó dedikált erőforrásokkal nagyobb projekteket telepíthet, optimalizálhatja a teljesítményt, és beállíthatja a kapacitást.

![Az Azure Cognitive Search díjszabási szintjei](media/search-sku-tier/tiers.png "Az Azure Cognitive Search díjszabási szintjei")

Egyes rétegek bizonyos típusú munkákra vannak optimalizálva. Például a **Standard 3 nagy sűrűségű (S3 HD)** az S3 *üzemeltetési módja* , ahol a mögöttes hardver nagy számú kisebb indexre van optimalizálva, és bérlős forgatókönyvekhez készült. Az S3 HD azonos egységenkénti díjszabással rendelkezik, de a hardver nagy számú kisebb index esetén a gyors fájlokra van optimalizálva.

A **tárolásra optimalizált** csomagok nagyobb tárolókapacitást biztosítanak, mint a standard szinteken a TB-onként alacsonyabb áron. Az elsődleges kompromisszum nagyobb lekérdezési késés, amelyet az adott alkalmazásra vonatkozó követelmények érvényesítéséhez kell érvényesíteni.  Ha többet szeretne megtudni a rétegek teljesítményével kapcsolatos megfontolásokról, tekintse meg a [teljesítmény-és optimalizálási szempontokat](search-performance-optimization.md).

További információt a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/), a [szolgáltatási korlátok az Azure Cognitive Search](search-limits-quotas-capacity.md) cikkben, valamint a portál lapon talál, amikor egy szolgáltatást kiépít.

## <a name="billable-events"></a>Számlázható események

Az Azure Cognitive Search-ra épülő megoldások a következő módokon vehetik igénybe a költségeket:

+ Magának a szolgáltatásnak a díja, nonstop futtatása, minimális konfiguráció esetén (egy partíció és egy replika)
+ Kapacitás (replikák vagy partíciók) hozzáadása
+ Sávszélességgel kapcsolatos díjak (kimenő adatforgalom) 
+ Kiegészítő szolgáltatások, például AI-bővítés (Cognitive Services csatolása az szakértelmével-ben, amely az AI-feldolgozást definiálja vagy az Azure Storage használata a Knowledge Store-hoz) vagy keresési szolgáltatás üzembe helyezése egy magánhálózati virtuális hálózaton

### <a name="service-costs"></a>Szolgáltatási költségek

A virtuális gépekkel vagy más, a díjak elkerülésére szolgáló egyéb erőforrásokkal ellentétben az Azure Cognitive Search szolgáltatás mindig a kizárólagos használatra dedikált hardveren érhető el. Ennek megfelelően a szolgáltatás létrehozása olyan számlázható esemény, amely a szolgáltatás létrehozásakor kezdődik, és a szolgáltatás törlésekor lejár. 

A minimális díj az első keresési egység (az egyik replika x egy partíció) a számlázható sebességgel. Ez a minimális érték a szolgáltatás élettartamára van javítva, mert a szolgáltatás nem futhat ennél a konfigurációnál kisebb mértékben. A minimumon túl egymástól függetlenül is hozzáadhat replikákat és partíciókat. A kapacitás replikák és partíciók révén történő növekményes növekedése a következő képlet alapján növeli a számla mennyiségét: [(replikák x Partitions x Rate)](#search-units), ahol a felszámított díj a kiválasztott árképzési szinttől függ.

Ha megbecsüli egy keresési megoldás költségét, ne feledje, hogy a díjszabás és a kapacitás nem lineáris. (A kapacitás megkettőzése meghaladja a költségeket.) A képlet működéséről a következő témakörben talál példát: [replikák és partíciók lefoglalása](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Sávszélességgel kapcsolatos díjak

Az [Indexelő](search-indexer-overview.md) használata a szolgáltatások helyétől függően a számlázásra is hatással lehet. Ha az Azure Cognitive Search szolgáltatást ugyanabban a régióban hozza létre, mint az adatait, teljes mértékben kiküszöbölheti az adatforgalom díját. Íme néhány információ a sávszélesség- [díjszabási lapról](https://azure.microsoft.com/pricing/details/bandwidth/):

+ A Microsoft nem számít fel semmilyen beérkező adatforgalmat az Azure egyik szolgáltatására sem, sem az Azure-Cognitive Search kimenő adatait.
+ A többszolgáltatásos megoldásokban nem számítunk fel díjat a vezeték nélküli adatforgalomért, ha az összes szolgáltatás ugyanabban a régióban található.

A kimenő adatokra akkor számítunk fel díjat, ha a szolgáltatások különböző régiókban találhatók. Ezek a díjak valójában nem részei az Azure Cognitive Search-számlájának. Itt említik, mert ha adatok vagy mesterséges intelligenciával rendelkező indexelő használatával kéri le az adatok különböző régiókban való lekérését, akkor a teljes számlán látható költségeket fogja látni.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-gazdagítás Cognitive Services

Az [AI](cognitive-search-concept-intro.md)-bővítés esetében érdemes megtervezni, hogy [egy számlázható Azure Cognitive Services-erőforrást](cognitive-search-attach-cognitive-services.md), ugyanabban a régióban, mint az Azure Cognitive Searcht, az utólagos elszámolású feldolgozás S0 díjszabási szintjére kell terveznie. Nincs rögzített díj a Cognitive Services csatolásával kapcsolatban. Csak a szükséges feldolgozásért kell fizetnie.

| Művelet | Számlázási hatás |
|-----------|----------------|
| Dokumentum repedése, szöveg kinyerése | Ingyenes |
| A dokumentumok repedése, a képek kinyerése | A számlázás a dokumentumokból kinyert képek száma alapján történik. Az [Indexelő konfigurációjában](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)a **imageAction** az a paraméter, amely a képek kinyerését váltja ki. Ha a **imageAction** értéke "None" (alapértelmezett), nem számítunk fel díjat a képek kinyeréséhez. A képek kinyerésének díja az Azure Cognitive Search [díjszabási részleteit ismertető](https://azure.microsoft.com/pricing/details/search/) oldalon található.|
| [Beépített kognitív képességek](cognitive-search-predefined-skills.md) | A számlázás ugyanolyan sebességgel történik, mint ha a feladatot a Cognitive Services közvetlen használatával végezte el. |
| Egyéni készségek | Az egyéni képességek az Ön által megadott funkciók. Az egyéni képességek használatának díja teljes mértékben attól függ, hogy az egyéni kód más mért szolgáltatásokat hív-e meg. |

A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) funkció lehetővé teszi egy olyan gyorsítótár megadását, amely lehetővé teszi, hogy az indexelő hatékonyabban fusson, és csak azokat a kognitív képességeket futtassa, amelyekre szükség van, ha később módosítja a készségkészlet, így időt és pénzt takarít meg.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Számlázási képlet (R x P = SU)

Az Azure Cognitive Search műveletekkel kapcsolatos legfontosabb számlázási koncepció a *keresési egység* (su). Mivel az Azure Cognitive Search az indexeléshez és a lekérdezésekhez tartozó replikák és partíciók függ, nem kell csupán egyet vagy a másikat számláznia. Ehelyett a számlázás a kettő összetett példányán alapul.

A SU a szolgáltatás által használt *replikák* és *partíciók* terméke: **(R x P = su)**.

Minden szolgáltatás egy SU-val kezdődik (az egyik replikát egy partícióval szorozva) a minimális értékkel. Bármely szolgáltatás esetében a maximális érték 36 SUs. A maximális érték több módon is elérhető: 6 partíció x 6 replika vagy 3 partíció x 12 replika, például. Gyakori, hogy a teljes kapacitásnál kevesebbet használ (például egy 3 replika, 3 partíciós szolgáltatás, amely 9 SUs-ként lett kiszámlázva). Tekintse meg a [partíciós és replika kombinációk](search-capacity-planning.md#chart) diagramot az érvényes kombinációk esetében.

A számlázási díj óradíja/SU. Az egyes szintek fokozatosan magasabb arányban jelennek meg. A magasabb szintek nagyobb és gyorsabb partíciókkal rendelkeznek, és ez az adott szinten a legmagasabb szintű óradíjat is segíti. Az egyes szintek díjszabását a [díjszabás részletei](https://azure.microsoft.com/pricing/details/search/) lapon tekintheti meg.

A legtöbb ügyfél csak egy részét a teljes kapacitás online állapotba helyezi, a többi pedig tartalékban tartja. A számlázáshoz a SU-képlet alapján kiszámított partíciók és replikák száma határozza meg, hogy mit fizet óránként.

## <a name="how-to-manage-costs"></a>A költségek kezelése

A következő javaslatok segítségével csökkentheti a költségeket, vagy hatékonyabban kezelheti a költségeket:

+ Hozza létre az összes erőforrást ugyanabban a régióban, vagy a lehető legkevesebb régióban, hogy csökkentse vagy eltávolítsa a sávszélességgel kapcsolatos díjakat.

+ Összevonja az összes szolgáltatást egyetlen erőforráscsoport, például az Azure Cognitive Search, Cognitive Services és minden más, a megoldásban használt Azure-szolgáltatás között. A Azure Portal keresse meg az erőforráscsoportot, és használja a **Cost Management** -parancsokat, hogy betekintést kapjon a tényleges és a tervezett költségekbe.

+ Tekintse át az Azure-webalkalmazást az előtér-alkalmazáshoz, hogy a kérések és válaszok az adatközpont határain belül maradjanak.

+ Vertikális felskálázás az erőforrás-igényes műveletekhez, például az indexeléshez, majd a rendszeres lekérdezési feladatokhoz tartozó leállások újraigazítása. Az Azure Cognitive Search minimális konfigurációjának megkezdése (egy partícióból és egy replikából álló egy SU), majd a felhasználói tevékenység figyelése olyan használati minták azonosításához, amelyek nagyobb kapacitást jelezhetnek. Ha kiszámítható minta van, lehetséges, hogy szinkronizálni tudja a méretezést a tevékenységgel (ezt a kódot kell írnia az automatizáláshoz).

Emellett megtekintheti a [számlázással és a költséghatékonysággal](https://docs.microsoft.com/azure/billing/billing-getting-started) kapcsolatos beépített eszközöket és szolgáltatásokat.

A keresési szolgáltatás ideiglenes leállítása nem lehetséges. A dedikált erőforrások mindig működőképesek, és a szolgáltatás élettartama szempontjából kizárólagos használatra vannak kiosztva. A szolgáltatás törlése végleges, és a hozzá tartozó adatai is törlődnek.

A szolgáltatás szempontjából a számla csökkentése egyetlen módja annak, hogy csökkentse a replikákat és a partíciókat olyan szintre, amely továbbra is elfogadható teljesítményt és [SLA-megfelelőséget](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biztosít, vagy egy alacsonyabb szintű szolgáltatást hoz létre (az S1 óradíjak alacsonyabbak, mint az S2 vagy az S3). Feltételezve, hogy kiépíti a szolgáltatást a betöltési kivetítések alsó végére, ha kibővíti a szolgáltatást, létrehozhat egy második nagyobb rétegű szolgáltatást, újraépítheti az indexeket a második szolgáltatásban, majd törölheti az elsőt.

## <a name="how-to-evaluate-capacity-requirements"></a>A kapacitásra vonatkozó követelmények kiértékelése

Az Azure Cognitive Search a kapacitást *replikák* és *partíciók*strukturálják.

+ A replikák a keresési szolgáltatás példányai. Mindegyik replika egyetlen elosztott terhelésű másolatot üzemeltet az indexből. Egy hat replikával rendelkező szolgáltatás például hat másolatot tartalmaz a szolgáltatásba betöltött összes indexről.

+ A partíciók az indexeket és a kereshető adattárolók automatikus felosztását végzik. Az indexet két partíció osztja szét, három partíciót pedig a harmadik felé, és így tovább. A kapacitás szempontjából a *partíció mérete* az elsődleges megkülönböztető funkció a rétegek között.

> [!NOTE]
> Minden standard és Storage optimalizált csomag támogatja a [replikák és partíciók rugalmas kombinációját](search-capacity-planning.md#chart) , így [optimalizálhatja a rendszer sebességét vagy tárhelyét](search-performance-optimization.md) az egyensúly megváltoztatásával. Az alapszintű csomag legfeljebb három replikát biztosít a magas rendelkezésre álláshoz, de csak egy partícióval rendelkezik. Az ingyenes szintek nem biztosítanak dedikált erőforrásokat: a számítási erőforrásokat több előfizető is megosztja.

### <a name="evaluating-capacity"></a>Kapacitás kiértékelése

A szolgáltatás üzemeltetésének kapacitása és költségei. A rétegek két szintre korlátozzák a korlátozásokat: a tárolást és az erőforrásokat. Érdemes meggondolni, mivel a határértékek közül az első a hatályos korlát.

Az üzleti követelmények általában a szükséges indexek számát írják elő. Előfordulhat például, hogy globális indexre van szüksége a dokumentumok nagyméretű tárházához. Vagy előfordulhat, hogy több indexre van szüksége a régió, az alkalmazás vagy az üzleti Niche alapján.

Az indexek méretének meghatározásához [létre kell egyet](search-what-is-an-index.md). A mérete az importált és az index konfiguráción alapul, például az, hogy engedélyezi-e a javaslatokat, a szűrést és a rendezést.

A teljes szöveges kereséshez az elsődleges adatstruktúra egy [fordított index](https://en.wikipedia.org/wiki/Inverted_index) -struktúra, amely különböző tulajdonságokkal rendelkezik, mint a forrásadatok. A fordított indexek esetében a méretet és az összetettséget a tartalom határozza meg, nem feltétlenül a betáplált adatmennyiség. Egy nagy redundanciával rendelkező nagyméretű adatforrás kisebb indexet eredményezhet, mint egy nagy mértékben változó tartalmat tartalmazó kisebb adathalmaz. Így ritkán lehet az index méretét az eredeti adatkészlet méretétől függően kikövetkeztetni.

> [!NOTE] 
> Annak ellenére, hogy az indexek és a tárolás jövőbeli igényeit is megbecsülik, érdemes meggondolni. Ha a rétegek kapacitása túl alacsonyra vált, egy új szolgáltatást kell kiépíteni egy magasabb szintű szinten, majd [újra kell töltenie az indexeket](search-howto-reindex.md). A szolgáltatás egyik SKU-ról egy másikra történő frissítése nem történik meg.
>

### <a name="estimate-with-the-free-tier"></a>Becslés az ingyenes szintjével

A kapacitás becslésének egyik megközelítése az ingyenes szinten való kezdés. Ne feledje, hogy az ingyenes szolgáltatás legfeljebb három indexet kínál, 50 MB tárterületet és 2 perc indexelési időt. A megszorítások alapján megbecsülheti a tervezett indexek méretét, de ezek a lépések a következők:

+ [Hozzon létre egy ingyenes szolgáltatást](search-create-service-portal.md).
+ Készítsen elő egy kis, reprezentatív adatkészletet.
+ [Hozzon létre egy kezdeti indexet a portálon](search-get-started-portal.md) , és jegyezze fel a méretét. A funkciók és attribútumok hatással vannak a tárterületre. Például a javaslatok hozzáadásával (keresési típusú lekérdezések) növelheti a tárolási követelményeket. Ha ugyanazt az adatkészletet használja, előfordulhat, hogy egy index több verzióját is létrehozhatja az egyes mezők különböző attribútumaival, hogy megtekintse, hogyan változnak a tárolási követelmények. További információkért lásd: [alapszintű index létrehozása "tárolási vonzatok"](search-what-is-an-index.md#index-size).

A durva becsléssel megduplázhatja ezt a költségvetést két index (fejlesztés és éles környezet) esetében, majd a szintet ennek megfelelően kiválaszthatja.

### <a name="estimate-with-a-billable-tier"></a>Becslés számlázható szintjével

A dedikált erőforrások nagyobb mintavételezési és feldolgozási időt biztosítanak a fejlesztés során az index mennyiségének, méretének és lekérdezési köteteinek reális becsléséhez. Néhány ügyfél azonnal egy számlázható réteggel ugrik, majd újraértékeli a fejlesztési projekt futamideje alatt.

1. [Tekintse át a szolgáltatási korlátokat az egyes szinteknél](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) annak megállapításához, hogy az alsóbb szintű csomagok támogatni tudják-e a szükséges indexek számát. Az alapszintű, S1 és S2 szinten az index korlátai 15, 50 és 200. A tárolási optimalizált csomagok korlátja 10 index, mert a rendszer úgy van kialakítva, hogy támogassa a nagyon nagy méretű indexek alacsony számát.

1. [Szolgáltatás létrehozása számlázandó szinten](search-create-service-portal.md):

    + Ha nem biztos benne, hogy a tervezett terhelésről van szó, az alapszintű vagy S1.
    + Ha tudja, hogy nagy léptékű indexelési és lekérdezési terheléssel fog rendelkezni, indítsa el a magas, S2-es vagy akár S3-as értékeket is.
    + Ha nagy mennyiségű adatmennyiséget indexel, és a lekérdezés terhelése viszonylag alacsony, mint egy belső üzleti alkalmazás esetében, az optimalizált tárolóval kezdődik az L1 vagy az L2.

1. [Hozzon létre egy kezdeti indexet](search-what-is-an-index.md) annak meghatározására, hogy a forrásadatok hogyan fordíthatók le egy indexre. Ez az egyetlen módszer az index méretének becslésére.

1. A portálon [megfigyelheti a tárterületet, a szolgáltatási korlátokat, a lekérdezési kötetet és a késést](search-monitor-usage.md) . A portálon másodpercenként megjelennek a lekérdezések, a szabályozott lekérdezések és a keresési késések. Ezeknek az értékeknek a segítségével eldöntheti, hogy a megfelelő szintet választotta-e. 

Az index száma és mérete ugyanilyen fontos az elemzéshez. Ennek az az oka, hogy a maximális korlátot a tárterület (partíciók) teljes kihasználtsága vagy az erőforrások (indexek, indexelő stb.) maximális korlátai (amelyik előbb bekövetkezik) alapján éri el. A portál segítségével nyomon követheti mindkettőt, és megjelenítheti az aktuális használatot és a maximális korlátokat az Áttekintés oldalon.

> [!NOTE]
> A tárolási követelmények akkor tölthetők le, ha a dokumentumok nem tartalmaznak külső adatfájlokat. Ideális esetben a dokumentumok csak azokat az adattartalmakat tartalmazzák, amelyekre szüksége van a keresési élményhez. A bináris adatkeresés nem kereshető, és külön kell tárolni (lehet, hogy egy Azure Table vagy blob Storage-tárolóban). Ezután fel kell venni egy mezőt az indexbe, hogy a külső értékekre mutató URL-hivatkozást lehessen tárolni. Az egyes dokumentumok maximális mérete 16 MB (vagy kevesebb, ha egy kérelemben egyszerre több dokumentumot tölt fel). További információ: [szolgáltatási korlátozások az Azure Cognitive Searchban](search-limits-quotas-capacity.md).
>

**Mennyiségi megfontolások lekérdezése**

A másodpercenkénti lekérdezések (QPS-k) fontos mérőszámot jelentenek a teljesítmény finomhangolása során, de ez általában csak a szintet veszi figyelembe, ha kezdettől fogva nagy lekérdezési kötetre számíthat.

A standard szintű csomagok replikákat és partíciókat is biztosíthatnak. A lekérdezési fordulat növeléséhez replikákat adhat hozzá a terheléselosztáshoz, vagy hozzáadhat partíciókat a párhuzamos feldolgozáshoz. Ezt követően a szolgáltatás üzembe helyezése után beállíthatja a teljesítményt.

Ha kezdettől fogva magas fenntartható lekérdezési köteteket vár, érdemes megfontolnia a magasabb színvonalú, nagyobb teljesítményű hardverek által támogatott szintet. Ezután offline állapotba helyezheti a partíciókat és a replikákat, vagy átválthat egy alacsonyabb szintű szolgáltatásra, ha ezek a lekérdezési kötetek nem történnek. A lekérdezési teljesítmény kiszámításával kapcsolatos további információkért lásd: [Azure Cognitive Search teljesítmény és optimalizálás](search-performance-optimization.md).

A tárolásra optimalizált csomagok nagy mennyiségű adatszámítási feladatokhoz hasznosak, és a lekérdezési késésre vonatkozó követelmények kevésbé fontosak lesznek. Továbbra is használjon további replikákat a terheléselosztáshoz és a párhuzamos feldolgozáshoz szükséges további partíciókhoz. Ezt követően a szolgáltatás üzembe helyezése után beállíthatja a teljesítményt.

**Szolgáltatási szintű szerződések**

Az ingyenes szint és az előzetes verzió funkciói nem biztosítanak [szolgáltatói szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Minden számlázható szinten a SLA-kat akkor kell végrehajtani, ha elegendő redundancia van kiépítve a szolgáltatáshoz. Legalább két replikát kell megadnia a Query (olvasási) SLA-hoz. Három vagy több replikával kell rendelkeznie a lekérdezéshez és az indexeléshez (írható-olvasható) SLA-hoz. A partíciók száma nem befolyásolja a SLA-kat.

## <a name="tips-for-tier-evaluation"></a>Tippek a rétegek kiértékeléséhez

+ Lekérdezések használatának engedélyezése a mérőszámok számára, valamint adatok gyűjtése használati minták köré (a lekérdezések munkaidőn kívüli, az indexelés a munkaidőn kívüli órákban). Ezekkel az információkkal tájékoztathatja a szolgáltatás kiépítési döntéseit. Bár az óránkénti vagy napi lépésszám nem praktikus, dinamikusan módosíthatja a partíciókat és az erőforrásokat a lekérdezési kötetek tervezett változásainak megfelelően. A nem tervezett, de tartós módosításokat is elvégezheti, ha a szintek elég sokáig tartanak a beavatkozáshoz.

+ Ne feledje, hogy a kiépítés alatti egyetlen hátránya, hogy előfordulhat, hogy le kell bontania egy szolgáltatást, ha a tényleges követelmények nagyobbak az előrejelzéseknél. A szolgáltatás megszakadásának elkerülése érdekében hozzon létre egy új szolgáltatást egy magasabb szintű szinten, és futtassa azt egymás mellett, amíg az összes alkalmazás és kérelem meg nem célozza az új végpontot.

## <a name="next-steps"></a>Következő lépések

Kezdje egy ingyenes csomaggal, és hozzon létre egy kezdeti indexet az adatok egy részhalmazának használatával, hogy megértse a jellemzőit. Az Azure Cognitive Search adatstruktúrája fordított index struktúra. A fordított index méretét és összetettségét a tartalom határozza meg. Ne feledje, hogy a nagyon redundáns tartalom általában kisebb indexet eredményez, mint a nagymértékben szabálytalan tartalom. Így az adatkészlet mérete helyett a tartalom jellemzői határozzák meg az index tárolási követelményeit.

Az index méretének kezdeti becslése után hozzon létre [egy számlázható szolgáltatást](search-create-service-portal.md) a cikkben ismertetett szinteken: alapszintű, standard vagy Storage – optimalizált. Lazítson az adatméretezés során felmerülő bármilyen mesterséges korlátozással, és [építse újra az indexet](search-howto-reindex.md) , hogy tartalmazza a kereshető összes adathalmazt.

A szükséges teljesítmény és skálázás érdekében szükség szerint ossza ki a [partíciókat és a replikákat](search-capacity-planning.md) .

Ha a teljesítmény és a kapacitás rendben van, elkészült. Ellenkező esetben hozzon létre újra egy keresési szolgáltatást egy másik szinten, amely szorosabban igazodik az igényeihez.

> [!NOTE]
> Ha kérdése van, tegye közzé a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) , vagy [forduljon az Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/).
