---
title: Válasszon árképzési szintet vagy SKU-t Azure Search Service-Azure Search
description: 'Azure Search a következő SKU-ban lehet kiépíteni: Az ingyenes, az alapszintű és a standard, valamint a standard szint számos erőforrás-konfigurációban és kapacitási szinten érhető el.'
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: d93f8c61511dd1d3fc2bfd253fa7a21857f67ed6
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563371"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Válasszon árképzési szintet Azure Search

Azure Search szolgáltatás létrehozásakor a rendszer egy [erőforrást hoz létre](search-create-service-portal.md) a szolgáltatás élettartamára rögzített árképzési szinten (vagy SKU-ban). A rétegek közé tartoznak az ingyenes, az alapszintű, a standard és a Storage optimalizált funkciók. A standard és a Storage optimalizált szolgáltatás számos konfigurációval és kapacitással érhető el.

A legtöbb ügyfél az ingyenes szintjével kezdődik, így kiértékelheti a szolgáltatást. Az értékelés után a rendszer közösen létrehoz egy második szolgáltatást a magasabb szintű fejlesztési és éles környezetekben való üzembe helyezéshez.

Bár az összes szinten, beleértve az ingyenes szintet is, általában a szolgáltatás paritását kínálja, a nagyobb méretű munkaterhelések magasabb szintű kapacitást is igénybe vehetnek. Például a [mesterséges intelligenciával való gazdagodás Cognitive Services](cognitive-search-concept-intro.md) olyan hosszan futó képességekkel rendelkezik, amelyek időtúllépést mutatnak az ingyenes szolgáltatáson, kivéve, ha az adatkészlet kicsi.

> [!NOTE] 
> A szolgáltatás paritásának kivétele [Indexelő](search-indexer-overview.md), amely az S3 HD-ben nem érhető el.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Elérhető rétegek

A rétegek tükrözik a szolgáltatást üzemeltető hardver jellemzőit (a funkciók helyett), és a következőket különböztetik meg:

+ A létrehozható indexek és indexelő mennyisége
+ Partíciók mérete és sebessége (fizikai tárterület)

A kiválasztott rétegek határozzák meg a számlázható sebességet. A következő képernyőkép a Azure Portal megjeleníti az elérhető csomagokat, mínusz a díjszabás (amely a Portálon és a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található. Az **ingyenes**, az alapszintű és a **standard** a leggyakoribb szintek.

Az **ingyenes** szolgáltatás egy korlátozott keresési szolgáltatást hoz létre egy fürtön, más előfizetőkkel megosztva. Elvégezheti a kisméretű projekteket, például a gyors üzembe helyezést és az oktatóanyagokat, de nem méretezheti a szolgáltatást, vagy nem futtathat jelentős számítási feladatokat. Az alapszintű és a **standard** a leggyakrabban használt számlázó rétegek, amelyek alapértelmezett értéke a **standard** .

![A Azure Search árképzési szintjei](media/search-sku-tier/tiers.png "A Azure Search árképzési szintjei")

Egyes rétegek bizonyos típusú munkákra vannak optimalizálva. Például a **Standard 3 nagy sűrűségű (S3 HD)** az S3 *üzemeltetési módja* , ahol a mögöttes hardver nagy számú kisebb indexre van optimalizálva, és bérlős forgatókönyvekhez készült. Az S3 HD azonos egységenkénti díjszabással rendelkezik, de a hardver nagy számú kisebb index esetén a gyors fájlokra van optimalizálva.

A **tárolásra optimalizált** csomagok nagyobb tárolókapacitást biztosítanak, mint a standard szinteken a TB-onként alacsonyabb áron. Az elsődleges kompromisszum nagyobb lekérdezési késés, amelyet az adott alkalmazásra vonatkozó követelmények érvényesítéséhez kell érvényesíteni.  Ha többet szeretne megtudni a rétegek teljesítményével kapcsolatos megfontolásokról, tekintse meg a [teljesítmény-és optimalizálási szempontokat](search-performance-optimization.md).

További információt a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/), a [szolgáltatási korlátok Azure Search](search-limits-quotas-capacity.md) cikkben, valamint a portál lapon talál a szolgáltatás kiépítés során.

## <a name="billable-events"></a>Számlázható események

Azure Searchra épülő megoldás a következő módokon veheti igénybe a költségeket:

+ A szolgáltatás alapdíja minimális konfiguráció alapján
+ Növekményes díj a felskálázáskor (replikák vagy partíciók hozzáadása)
+ A kimenő adatforgalom sávszélesség-díja
+ Cognitive Services erőforrásokat hasznosító kognitív keresés

### <a name="service-costs"></a>Szolgáltatási költségek

A költségek elkerülése érdekében a virtuális gépekkel vagy más olyan erőforrásokkal szemben, amelyek "szünetelnek", a Azure Search szolgáltatás mindig elérhető a kizárólagos használatra szánt hardveren. Ennek megfelelően a szolgáltatás létrehozása olyan számlázható esemény, amely a szolgáltatás létrehozásakor kezdődik, és a szolgáltatás törlésekor lejár. 

A minimális díj az első keresési egység (az egyik replika x egy partíció). Ez a minimális érték a szolgáltatás élettartamára van javítva, mert a szolgáltatás nem futhat ennél a konfigurációnál kisebb mértékben. A minimumon túl egymástól függetlenül is hozzáadhat replikákat és partíciókat. A kapacitás replikák és partíciók révén történő növekményes növekedése a következő képlet alapján növeli a számla mennyiségét: [(replikák x Partitions x Rate)](#search-units), ahol a felszámított díj a kiválasztott árképzési szinttől függ.

Ha megbecsüli egy keresési megoldás költségét, ne feledje, hogy a díjszabás és a kapacitás nem lineáris. (A kapacitás megkettőzése meghaladja a költségeket.) A képlet működéséről a következő témakörben talál példát: [replikák és partíciók](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)lefoglalása.

### <a name="bandwidth-charges"></a>Sávszélességgel kapcsolatos díjak

A [Azure Search indexelő](search-indexer-overview.md) használata a szolgáltatások helyétől függően a számlázásra is hatással lehet. Ha a Azure Search szolgáltatást ugyanabban a régióban hozza létre, ahol az adatok szerepelnek, az adatok teljes mértékben eltávolíthatók. Íme néhány információ a sávszélesség- [díjszabási lapról](https://azure.microsoft.com/pricing/details/bandwidth/):

+ A Microsoft nem számít fel semmilyen, az Azure-szolgáltatásba beérkező, illetve a Azure Searchból kimenő adatokért.
+ A többszolgáltatásos megoldásokban nem számítunk fel díjat a vezeték nélküli adatforgalomért, ha az összes szolgáltatás ugyanabban a régióban található.

A kimenő adatokra akkor számítunk fel díjat, ha a szolgáltatások különböző régiókban találhatók. Ezek a díjak valójában nem részei a Azure Search számlájának. Itt említik, mert ha adatok vagy mesterséges intelligenciával rendelkező indexelő használatával kéri le az adatok különböző régiókban való lekérését, akkor a teljes számlán látható költségeket fogja látni.

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>Kognitív keresési AI-gazdagítás Cognitive Services

A [Cognitive Servicesekkel való mesterséges intelligenciával](cognitive-search-concept-intro.md)kapcsolatban érdemes megtervezni [egy számlázható Azure Cognitive Services-erőforrás csatolását](cognitive-search-attach-cognitive-services.md)a Azure Search azonos régióban, az utólagos elszámolású feldolgozás S0 díjszabási szintjénél. Nincs rögzített díj a Cognitive Services csatolásával kapcsolatban. Csak a szükséges feldolgozásért kell fizetnie.

| Művelet | Számlázási hatás |
|-----------|----------------|
| Dokumentum repedése, szöveg kinyerése | Free |
| A dokumentumok repedése, a képek kinyerése | A számlázás a dokumentumokból kinyert képek száma alapján történik. Az [Indexelő konfigurációjában](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)a **imageAction** az a paraméter, amely a képek kinyerését váltja ki. Ha a **imageAction** értéke "None" (alapértelmezett), nem számítunk fel díjat a képek kinyeréséhez. A képek kinyerésének díja a Azure Search [díjszabását ismertető](https://azure.microsoft.com/pricing/details/search/) oldalon található.|
| [Előre elkészített kognitív képességek](cognitive-search-predefined-skills.md) | A számlázás ugyanolyan sebességgel történik, mint ha a feladatot a Cognitive Services közvetlen használatával végezte el. |
| Egyéni készségek | Az egyéni képességek az Ön által megadott funkciók. Az egyéni képességek használatának díja teljes mértékben attól függ, hogy az egyéni kód más mért szolgáltatásokat hív-e meg. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Számlázási képlet (R x P = SU)

A Azure Search műveletekkel kapcsolatos legfontosabb számlázási koncepció a *keresési egység* (su). Mivel a Azure Search az indexeléshez és a lekérdezésekhez tartozó replikák és partíciók függ, nem kell csupán egyet vagy a másikat számláznia. Ehelyett a számlázás a kettő összetett példányán alapul.

A SU a szolgáltatás által használt *replikák* és *partíciók* terméke: **(R x P = su)** .

Minden szolgáltatás egy SU-val kezdődik (az egyik replikát egy partícióval szorozva) a minimális értékkel. Bármely szolgáltatás esetében a maximális érték 36 SUs. A maximális érték több módon is elérhető: 6 partíció x 6 replika, vagy 3 partíció x 12 replika, például:. Gyakori, hogy a teljes kapacitásnál kevesebbet használ (például egy 3 replika, 3 partíciós szolgáltatás, amely 9 SUs-ként lett kiszámlázva). Tekintse meg a [partíciós és replika kombinációk](search-capacity-planning.md#chart) diagramot az érvényes kombinációk esetében.

A számlázási díj óradíja/SU. Az egyes szintek fokozatosan magasabb arányban jelennek meg. A magasabb szintek nagyobb és gyorsabb partíciókkal rendelkeznek, és ez az adott szinten a legmagasabb szintű óradíjat is segíti. Az egyes szintek díjszabását a [díjszabás részletei](https://azure.microsoft.com/pricing/details/search/) lapon tekintheti meg.

A legtöbb ügyfél csak egy részét a teljes kapacitás online állapotba helyezi, a többi pedig tartalékban tartja. A számlázáshoz a SU-képlet alapján kiszámított partíciók és replikák száma határozza meg, hogy mit fizet óránként.

## <a name="how-to-manage-and-reduce-costs"></a>A költségek kezelése és csökkentése

A következő javaslatok mellett látogasson el a [számlázásra és a Cost Managementre](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Hozza létre az összes erőforrást ugyanabban a régióban, vagy a lehető legkevesebb régióban, hogy csökkentse vagy eltávolítsa a sávszélességgel kapcsolatos díjakat.

- Összevonja az összes szolgáltatást egy erőforráscsoporthoz, például Azure Search, Cognitive Services és minden más, a megoldásban használt Azure-szolgáltatáshoz. A Azure Portal keresse meg az erőforráscsoportot, és használja a **Cost Management** -parancsokat, hogy betekintést kapjon a tényleges és a tervezett költségekbe.

- Tekintse át az Azure-webalkalmazást az előtér-alkalmazáshoz, hogy a kérések és válaszok az adatközpont határain belül maradjanak.

- Vertikális felskálázás az erőforrás-igényes műveletekhez, például az indexeléshez, majd a rendszeres lekérdezési feladatokhoz tartozó leállások újraigazítása. Kezdje a Azure Search minimális konfigurációjával (egy partícióból és egy replikából), majd figyelje a felhasználói tevékenységeket a használati minták azonosítására, amelyek nagyobb kapacitást jelezhetnek. Ha kiszámítható minta van, lehetséges, hogy szinkronizálni tudja a méretezést a tevékenységgel (ezt a kódot kell írnia az automatizáláshoz).

Nem állíthatja le a keresési szolgáltatást a számla csökkentése érdekében. A dedikált erőforrások mindig működőképesek, és a szolgáltatás élettartama szempontjából kizárólagos használatra vannak kiosztva. A szolgáltatás szempontjából a számla csökkentése egyetlen módja annak, hogy csökkentse a replikákat és a partíciókat olyan szintre, amely továbbra is elfogadható teljesítményt és [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)-megfelelőséget biztosít, vagy egy alacsonyabb szintű szolgáltatást hoz létre (az S1 óradíjak alacsonyabbak, mint az S2 vagy az S3). Feltételezve, hogy kiépíti a szolgáltatást a betöltési kivetítések alsó végére, ha kibővíti a szolgáltatást, létrehozhat egy második nagyobb rétegű szolgáltatást, újraépítheti az indexeket a második szolgáltatásban, majd törölheti az elsőt.

## <a name="how-to-evaluate-capacity-requirements"></a>A kapacitásra vonatkozó követelmények kiértékelése

Azure Search a kapacitást replikák és *partíciók*strukturálják.

+ A replikák a keresési szolgáltatás példányai. Mindegyik replika egyetlen elosztott terhelésű másolatot üzemeltet az indexből. Egy hat replikával rendelkező szolgáltatás például hat másolatot tartalmaz a szolgáltatásba betöltött összes indexről.

+ A partíciók az indexeket és a kereshető adattárolók automatikus felosztását végzik. Az indexet két partíció osztja szét, három partíciót pedig a harmadik felé, és így tovább. A kapacitás szempontjából a *partíció mérete* az elsődleges megkülönböztető funkció a rétegek között.

> [!NOTE]
> Minden standard és Storage optimalizált csomag támogatja a [replikák és partíciók rugalmas kombinációját](search-capacity-planning.md#chart) , így [optimalizálhatja a rendszer sebességét vagy tárhelyét](search-performance-optimization.md) az egyensúly megváltoztatásával. Az alapszintű csomag legfeljebb három replikát biztosít a magas rendelkezésre álláshoz, de csak egy partícióval rendelkezik. Az ingyenes szintek nem biztosítanak dedikált erőforrásokat: a számítási erőforrásokat több előfizető is megosztja.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Kapacitás kiértékelése

A szolgáltatás üzemeltetésének kapacitása és költségei közvetlenül kapcsolódnak egymáshoz. A rétegek két szintre korlátozzák a korlátozásokat: a tárolást és az erőforrásokat. Érdemes meggondolni, mivel a határértékek közül az első a hatályos korlát.

Az üzleti követelmények általában a szükséges indexek számát írják elő. Előfordulhat például, hogy globális indexre van szüksége a dokumentumok nagyméretű tárházához. Vagy előfordulhat, hogy több indexre van szüksége a régió, az alkalmazás vagy az üzleti Niche alapján.

Az indexek méretének meghatározásához [létre kell egyet](search-create-index-portal.md). A Azure Search adatstruktúrája elsősorban egy [fordított index](https://en.wikipedia.org/wiki/Inverted_index) -struktúra, amely különböző tulajdonságokkal rendelkezik, mint a forrásadatok. A fordított indexek esetében a méretet és az összetettséget a tartalom határozza meg, nem feltétlenül a betáplált adatmennyiség. Egy nagy redundanciával rendelkező nagyméretű adatforrás kisebb indexet eredményezhet, mint egy nagy mértékben változó tartalmat tartalmazó kisebb adathalmaz. Így ritkán lehet az index méretét az eredeti adatkészlet méretétől függően kikövetkeztetni.

> [!NOTE] 
> Annak ellenére, hogy az indexek és a tárolás jövőbeli igényeit is megbecsülik, érdemes meggondolni. Ha a rétegek kapacitása túl alacsonyra vált, egy új szolgáltatást kell kiépíteni egy magasabb szintű szinten, majd újra kell töltenie [az indexeket](search-howto-reindex.md). A szolgáltatás egyik SKU-ról egy másikra történő frissítése nem történik meg.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>1\. lépés: Durva becslések fejlesztése az ingyenes szintet használva

A kapacitás becslésének egyik megközelítése az ingyenes szinten való kezdés. Ne feledje, hogy az ingyenes szolgáltatás legfeljebb három indexet kínál, 50 MB tárterületet és 2 perc indexelési időt. Az ilyen megkötésekkel a tervezett indexek méretének becslése is kihívást jelenthet. Az alábbi megközelítéssel elvégezheti a következőket:

+ [Hozzon létre egy ingyenes szolgáltatást](search-create-service-portal.md).
+ Készítsen elő egy kisméretű, reprezentatív adatkészletet (például 5 000-dokumentumok és 10 százalékos minta mérete).
+ [Hozzon létre egy kezdeti indexet](search-create-index-portal.md) , és jegyezze fel a méretét a portálon (például 30 MB).

Ha a minta a teljes adatforrás 10 százalékát képviseli, a 30 MB-os index körülbelül 300 MB-ra változik, ha az összes dokumentum indexelve van. Ezt az előzetes számot felfegyverezve megduplázhatja ezt a költségvetést két index (fejlesztés és termelés) esetén. Ez összesen 600 MB tárolási követelményt biztosít. Ezt a követelményt az alapszintű csomag könnyen kielégíti, ezért érdemes elindulni.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>2\. lépés: Finomított becslések kifejlesztése számlázandó rétegek használatával

Egyes ügyfelek inkább olyan dedikált erőforrásokkal kezdenek dolgozni, amelyek nagyobb mintavételi és feldolgozási időt tudnak igénybe venni, majd a fejlesztés során reális becsléseket készítenek a mennyiség, a méret és a lekérdezési kötetek indexéről. A szolgáltatás kezdetben a legjobb becslés alapján lett kiépítve. Ezután a fejlesztési projekt futamideje szerint a csapatok általában azt tudják, hogy a meglévő szolgáltatás a tervezett éles számítási feladatokhoz képest vagy kapacitás alatt van-e.

1. [Tekintse át a szolgáltatási korlátokat az egyes szinteknél](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) annak megállapításához, hogy az alsóbb szintű csomagok támogatni tudják-e a szükséges indexek számát. Az alapszintű, S1 és S2 szinten az index korlátai 15, 50 és 200. A tárolási optimalizált csomagok korlátja 10 index, mert a rendszer úgy van kialakítva, hogy támogassa a nagyon nagy méretű indexek alacsony számát.

1. [Szolgáltatás létrehozása számlázandó szinten](search-create-service-portal.md):

    + Ha a tanulási görbe elején van, akkor az alapszintű vagy S1-es verzióra kell kezdenie.
    + Ha tudja, hogy nagy léptékű indexelési és lekérdezési terheléssel fog rendelkezni, indítsa el a magas, S2-es vagy akár S3-as értékeket is.
    + Ha nagy mennyiségű adatmennyiséget indexel, és a lekérdezés terhelése viszonylag alacsony, mint egy belső üzleti alkalmazás esetében, az optimalizált tárolóval kezdődik az L1 vagy az L2.

1. [Hozzon létre egy kezdeti indexet](search-create-index-portal.md) annak meghatározására, hogy a forrásadatok hogyan fordíthatók le egy indexre. Ez az egyetlen módszer az index méretének becslésére.

1. A portálon megfigyelheti a [tárterületet, a szolgáltatási korlátokat, a lekérdezési kötetet és](search-monitor-usage.md) a késést. A portálon másodpercenként megjelennek a lekérdezések, a szabályozott lekérdezések és a keresési késések. Ezeknek az értékeknek a segítségével eldöntheti, hogy a megfelelő szintet választotta-e. A [keresési forgalom elemzésének](search-traffic-analytics.md)engedélyezésével is konfigurálhatja az értékek, például az átkattintási elemzések mélyreható figyelését.

Az index száma és mérete ugyanilyen fontos az elemzéshez. Ennek az az oka, hogy a maximális korlátot a tárterület (partíciók) teljes kihasználtsága vagy az erőforrások (indexek, indexelő stb.) maximális korlátai (amelyik előbb bekövetkezik) alapján éri el. A portál segítségével nyomon követheti mindkettőt, és megjelenítheti az aktuális használatot és a maximális korlátokat az Áttekintés oldalon.

> [!NOTE]
> A tárolási követelmények akkor tölthetők le, ha a dokumentumok nem tartalmaznak külső adatfájlokat. Ideális esetben a dokumentumok csak azokat az adattartalmakat tartalmazzák, amelyekre szüksége van a keresési élményhez. A bináris adatkeresés nem kereshető, és külön kell tárolni (lehet, hogy egy Azure Table vagy blob Storage-tárolóban). Ezután fel kell venni egy mezőt az indexbe, hogy a külső értékekre mutató URL-hivatkozást lehessen tárolni. Az egyes dokumentumok maximális mérete 16 MB (vagy kevesebb, ha egy kérelemben egyszerre több dokumentumot tölt fel). További információ: [Azure Search szolgáltatási korlátai](search-limits-quotas-capacity.md).
>

**Mennyiségi megfontolások lekérdezése**

A másodpercenkénti lekérdezések (QPS-k) fontos mérőszámot jelentenek a teljesítmény finomhangolása során, de ez általában csak a szintet veszi figyelembe, ha kezdettől fogva nagy lekérdezési kötetre számíthat.

A standard szintű csomagok replikákat és partíciókat is biztosíthatnak. A lekérdezési fordulat növeléséhez replikákat adhat hozzá a terheléselosztáshoz, vagy hozzáadhat partíciókat a párhuzamos feldolgozáshoz. Ezt követően a szolgáltatás üzembe helyezése után beállíthatja a teljesítményt.

Ha kezdettől fogva magas fenntartható lekérdezési köteteket vár, érdemes megfontolnia a magasabb színvonalú, nagyobb teljesítményű hardverek által támogatott szintet. Ezután offline állapotba helyezheti a partíciókat és a replikákat, vagy átválthat egy alacsonyabb szintű szolgáltatásra, ha ezek a lekérdezési kötetek nem történnek. A lekérdezési teljesítmény számításával kapcsolatos további információkért lásd: [Azure Search teljesítmény és optimalizálás](search-performance-optimization.md).

A tárolásra optimalizált csomagok nagy mennyiségű adatszámítási feladatokhoz hasznosak, és a lekérdezési késésre vonatkozó követelmények kevésbé fontosak lesznek. Továbbra is használjon további replikákat a terheléselosztáshoz és a párhuzamos feldolgozáshoz szükséges további partíciókhoz. Ezt követően a szolgáltatás üzembe helyezése után beállíthatja a teljesítményt.

**Szolgáltatási szintű szerződések**

Az ingyenes szint és az előzetes verzió funkciói nem biztosítanak [szolgáltatói szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Minden számlázható szinten a SLA-kat akkor kell végrehajtani, ha elegendő redundancia van kiépítve a szolgáltatáshoz. Legalább két replikát kell megadnia a Query (olvasási) SLA-hoz. Három vagy több replikával kell rendelkeznie a lekérdezéshez és az indexeléshez (írható-olvasható) SLA-hoz. A partíciók száma nem befolyásolja a SLA-kat.

## <a name="tips-for-tier-evaluation"></a>Tippek a rétegek kiértékeléséhez

+ Ismerje meg, hogyan hozhat létre hatékony indexeket, és megtudhatja, hogy mely frissítési módszerek a legkevésbé befolyásolják. A [Search Traffic Analytics](search-traffic-analytics.md) használatával betekintést nyerhet a lekérdezési tevékenységre.

+ Lekérdezések használatának engedélyezése a mérőszámok számára, valamint adatok gyűjtése használati minták köré (a lekérdezések munkaidőn kívüli, az indexelés a munkaidőn kívüli órákban). Ezekkel az információkkal tájékoztathatja a szolgáltatás kiépítési döntéseit. Bár az óránkénti vagy napi lépésszám nem praktikus, dinamikusan módosíthatja a partíciókat és az erőforrásokat a lekérdezési kötetek tervezett változásainak megfelelően. A nem tervezett, de tartós módosításokat is elvégezheti, ha a szintek elég sokáig tartanak a beavatkozáshoz.

+ Ne feledje, hogy a kiépítés egyetlen hátránya, hogy előfordulhat, hogy le kell bontania egy szolgáltatást, ha a tényleges követelmények nagyobbak az előrejelzéseknél. A szolgáltatás megszakadásának elkerülése érdekében hozzon létre egy új szolgáltatást ugyanabban az előfizetésben egy magasabb szinten, és futtassa azt egymás mellett, amíg az összes alkalmazás és kérelem meg nem célozza az új végpontot.

## <a name="next-steps"></a>További lépések

Kezdje egy ingyenes csomaggal, és hozzon létre egy kezdeti indexet az adatok egy részhalmazának használatával, hogy megértse a jellemzőit. Azure Search adatstruktúrája fordított index-struktúra. A fordított index méretét és összetettségét a tartalom határozza meg. Ne feledje, hogy a nagyon redundáns tartalom általában kisebb indexet eredményez, mint a nagymértékben szabálytalan tartalom. Így az adatkészlet mérete helyett a tartalom jellemzői határozzák meg az index tárolási követelményeit.

Az index méretének kezdeti becslése után hozzon létre [egy számlázható szolgáltatást](search-create-service-portal.md) a cikkben ismertetett szintek egyikén: Az alapszintű, a standard vagy a Storage optimalizált. Lazítson az adatméretezés során felmerülő bármilyen mesterséges korlátozással, és [építse újra](search-howto-reindex.md) az indexet, hogy tartalmazza a kereshető összes adathalmazt.

A szükséges teljesítmény és skálázás érdekében szükség szerint ossza ki a [partíciókat és](search-capacity-planning.md) a replikákat.

Ha a teljesítmény és a kapacitás rendben van, elkészült. Ellenkező esetben hozzon létre újra egy keresési szolgáltatást egy másik szinten, amely szorosabban igazodik az igényeihez.

> [!NOTE]
> Ha kérdése van, tegye közzé a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) , vagy [forduljon az Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/).
