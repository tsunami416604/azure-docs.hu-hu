---
title: Az Azure Search szolgáltatás – Azure Search tarifacsomag vagy SKU kiválasztása
description: 'Az Azure Search Termékváltozatokban lévő építhető ki: Ingyenes, alapszintű, és a Standard és standard szintű különböző erőforrás-konfigurációk és kapacitás szintek érhető el.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539278"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Válasszon egy tarifacsomagot az Azure Search

Amikor létrehoz egy Azure Search szolgáltatást egy [erőforrás létrehozása](search-create-service-portal.md) tarifacsomag vagy SKU, amely a szolgáltatás teljes élettartama rögzített címen. A szolgáltatásszintek ingyenes, alapszintű, Standard és tárolásra optimalizált. Standard és a különböző konfigurációkat, és a kapacitások tárolásra optimalizált érhetők el.

A legtöbb ügyfél indítsa el az ingyenes szinttel, azokat is kiértékelheti, hogy a szolgáltatás. Majd frissítse a fejlesztési és éles környezetekben a magasabb szintű egyik. Az ingyenes, beleértve az erőforrás-igényes cognitive Search azokat az összes útmutatóink és oktatóanyagaink segítségével hajthatja végre.

> [!NOTE]
> A Microsoft jelenleg biztosít a tárolásra optimalizált szolgáltatásszintek, teszteléséhez és a kísérleti,. a cél az, hogy visszajelzéseket kedvezményes áron egy előzetes verzióban érhető el. Végső díjszabás lesz bejelentve később, amikor ezek a rétegek általánosan elérhetők. Azt javasoljuk, hogy az éles környezetben ezek a rétegek tanúsítványokkal szemben.

Rétegek helyett az szolgáltatást (szolgáltatásokat) üzemeltető a hardver jellemzőit tükrözik, és vannak különbözteti meg:

+ Indexeket hozhat létre száma.
+ Méretétől és sebességétől partíciók (fizikai tároló).

Bár minden szinten, beleértve az ingyenes szint általánosan funkcióparitás ajánl fel, nagyobb méretű számítási feladatokat a magasabb szintű szüksége lehet diktálni. Ha például [mesterséges indexeléshez a Cognitive Services](cognitive-search-concept-intro.md) rendelkezik hosszú ideig futó képességeit, hogy időtúllépés egy ingyenes szolgáltatás, ha az adatkészlet nem kisebb.

> [!NOTE] 
> Funkcióparitás kivételt [indexelők](search-indexer-overview.md), amelyek nem állnak rendelkezésre az S3 HD.
>

A szinten belül is [módosíthatja a replika és a partíció erőforrások](search-capacity-planning.md) növelheti vagy csökkentheti a méretezési csoport. Kezdje egy vagy két, az egyes sikerült, és átmenetileg emelni a indexelési számítási nagy számítási teljesítménnyel. Lehetővé teszi a szinten belüli erőforrásszintek hangolása növeli a rugalmasságot, de is némileg megnehezíti az elemzést. Előfordulhat, hogy talál-e egy alacsonyabb szintre a további erőforrások/replikákkal rendelkező kínál jobb érték és a teljesítmény, mint a magasabb szintű kevesebb erőforrást a kísérletezést. Mikor és miért módosíthatja lenne a kapacitás kapcsolatos további információkért lásd: [teljesítmény és optimalizálás szempontok](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Az Azure Search csomag

A következő táblázat felsorolja az elérhető tarifacsomag. Talál további információt a különböző szinteken lévő a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/search/), a a [korlátozások az Azure Search szolgáltatás](search-limits-quotas-capacity.md) című cikket, és a portál lapon, ha egy szolgáltatás kiépítése.

|Szint | Kapacitás |
|-----|-------------|
|Ingyenes | Más előfizetőkkel megosztott. Nem méretezhető. Legfeljebb három indexre, és 50 MB tárhelyet. |
|Alapszintű | Dedikált számítási erőforrások a kisebb léptékű termelési számítási feladatokhoz. Egy 2 GB-os partíciót, és legfeljebb három replika. |
|Standard 1 (S1 esetén) | S1 és magasabb, dedikált gépek további tárolási és feldolgozási kapacitással, minden szinten. S1 a partíció mérete 25 GB/partíció (legfeljebb 300 GB szolgáltatásonként). |
|Standard szintű 2 (S2) | Hasonló az S1 szintre, de 100 GB-os partíciók (és 1,2 TB-os szolgáltatásonként). |
|Standard 3 (S3 esetén) | 200 GB-os partíciók (legfeljebb 2,4 TB-os szolgáltatásonként). |
|Standard 3 nagy sűrűségű (HD S3 szintű) | Nagy sűrűségű van egy *üzemeltetési mód* S3. Az alapul szolgáló hardver kisebb indexek sok van optimalizálva, és több-bérlős forgatókönyvek esetén javasolt. S3 HD három S3, de a hardver optimalizált sok kisebb indexeket a gyors olvasást az azonos egységenkénti díj rendelkezik.|
|Tárolásra optimalizált 1 (1) | 1 TB-os partíciók (legfeljebb 12 TB szolgáltatásonként). |
|Tárolásra optimalizált 2 (L2) | 2 TB-os partíciók (legfeljebb 24 TB szolgáltatásonként). |

> [!NOTE] 
> A tárolásra optimalizált rétegei kedvezőbb áron / TB, mint a Standard szint nagyobb tárolási kapacitás. Az elsődleges egyensúlyt a lekérdezés nagyobb késést, amelynek ellenőrizni kell az alkalmazás specifikus igényei.  Ez a réteg a teljesítménnyel kapcsolatos megfontolások kapcsolatos további információkért lásd: [teljesítmény és optimalizálás szempontok](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>A számlázás működése

Háromféleképpen költségek az Azure Search szolgáltatásban, és a rögzített és változó összetevőből áll. Ez a szakasz ismerteti a számlázási három összetevővel: alapvető szolgáltatási költségei, a kimenő adatforgalom díját és a mesterséges Intelligencia bővített indexelés.

### <a name="core-service-costs-fixed-and-variable"></a>Core szolgáltatási költségei (rögzített és változó)

A szolgáltatás magát a minimális díj megfizetése alól az első keresési egység (1 replika x 1 partíció). Ez a minimum a szolgáltatás teljes élettartama fix, mivel a szolgáltatás bármit kevesebb, mint ez a konfiguráció nem futtatható.

A minimális túl is hozzáadhat replikák és partíciók egymástól függetlenül. Ha például csak a replikákat és a csak a partíciókat is hozzáadhat. A replikák és partíciók kapacitás növekményes növekedése a változó költség összetevő alkotják.

Alapján számoljuk el egy [képlet (replikák partíciók x x mértéke)](#search-units). A sebesség kell fizetnie, válassza ki a tarifacsomagot függ.

Az alábbi képernyőképen egységenkénti díjszabás jelzi a Basic, S1 és az ingyenes. (S2, S3, L1 és L2 nem jelennek meg.) Ha létrehoz egy egyszerű szolgáltatást, a havi költségek az érték, amely megjelenik az fog átlagos *ár-1*. Egy Standard szolgáltatáshoz, az a havi költségek az érték, amely megjelenik az átlagos fog *ár-2*. Az egyes szintek növelje egység költségeket, mert a számítási teljesítmény és tárterület-kapacitás nagyobb egyes egymást követő rétegben. Az Azure Search díjszabása érhetők el a [díjszabását ismertető oldalt az Azure Search](https://azure.microsoft.com/pricing/details/search/).

![Egységenkénti díjszabás](./media/search-sku-tier/per-unit-pricing.png "egységenkénti díjszabása")

Becslésekor a keresési megoldások költségeit, tartsa szem előtt, díjszabás, és a kapacitás nem lineáris. (Kapacitás kétszeresére több mint megduplázódik költségeinek.) Példa bemutatja, hogyan a képlet működését, lásd: [lefoglalása a replikák és partíciók](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>A számlázás alapját a keresési egységek

Az Azure Search műveletek megismeréséhez legfontosabb számlázási elképzelés az, a *keresési egység* (SU). Azure Search a replikák és partíciók indexelést és lekérdezést is függ, mivel csak az egyik vagy másik számlázási nincs értelme. Ehelyett mindkét összetett számlázási alapul.

SU, a termék a *replikák* és *partíciók* a szolgáltatás által használt: **(Az R x P = SU)** .

Mindegyik szolgáltatás egy SU (egy másodpéldány egy partíciót megszorozza) kezdődik, az a minimális. A maximális bármely szolgáltatás 36 su mérete. Ez a maximális érték többféle módon érhető el: 6 partíciók x 6 replikák, illetve 3 partíció x 12 replikái példa. Szokás kevesebb, mint a teljes kapacitás (például egy 3-replika, a 3-partition service 9 SUS-t számít). Tekintse meg a [partíció és a replika kombinációk](search-capacity-planning.md#chart) érvényes kombinációk diagramot.

Számlázási díj óránként / SU van. Minden szint tartalmaz fokozatosan magasabb. A magasabb szintű nagyobb és gyorsabb partícióval rendelkező származnak, és ez hozzájárul a magasabb, a teljes óránkénti elszámolása. Megtekintheti az egyes szintek a díjszabás a [díjszabás](https://azure.microsoft.com/pricing/details/search/) lap.

A legtöbb ügyfél használata csak egy része online, a teljes kapacitás, a többi tulajdonában foglalás. A számlázást, a partíciókat és -replikákat, amely online állapotba helyezése száma által a SU képletet, meghatározza, hogy óránként fizet.

### <a name="data-egress-charges-during-indexing"></a>Kimenő adatforgalom díját az indexelés során

Használatával [Azure Search-indexelők](search-indexer-overview.md) előfordulhat, hogy miként befolyásolják a számlázást, a szolgáltatások helyétől függően. Kimenő adatforgalom díját kiküszöbölheti az teljes mértékben, ha az Azure Search szolgáltatás hoz létre ugyanabban a régióban, mint az adatokat. Az itt következő információk a a [díjszabását ismertető lapon sávszélesség](https://azure.microsoft.com/pricing/details/bandwidth/):

+ A Microsoft nem számít fel díjat a bejövő adatokat bármely szolgáltatás az Azure-ban vagy a kimenő adatok az Azure Search.

+ A többcélú megoldások nem adatokat a hálózaton áthaladó ugyanabban a régióban összes szolgáltatások esetén számítunk fel díjat.

Díjak terhelik kimenő különböző régiókban lévő szolgáltatások esetén. Ezek a díjak nem az Azure Search számla valójában egy részét. Ezek még itt említett mert használata adatok és AI-bővített indexelők segítségével szerez adatokat különböző régiókban, látni fogja a költségek megjelennek a teljes számlája összegét.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>AI-bővített indexelés kognitív szolgáltatásokkal

A [mesterséges indexeléshez a Cognitive Services](cognitive-search-concept-intro.md), meg kell terveznie a számlázható Azure Cognitive Services-erőforrás, ugyanabban a régióban, az Azure Search, a használatalapú fizetéses feldolgozási S0 tarifacsomagjának csatolni. Nincs társított csatolása a Cognitive Services rögzített költség nélkül. Csak kell fizetnie kell a feldolgozása.

Kép kinyerési dokumentumfeltörést során egy Azure Search díj. A dokumentumok kinyert lemezképek száma alapján számlázzuk ki. Szövegkinyerés jelenleg díjmentes.

Más végrehajtott információbeolvasás, természetes nyelvi feldolgozás, például a alapuló [beépített kognitív képességeket](cognitive-search-predefined-skills.md) és a Cognitive Services-erőforrás terhelve. Ezek a számlázás megegyező módon, ha végrehajtotta a feladat közvetlenül a Cognitive Services használatával. További információkért lásd: [a képességek alkalmazási lehetőségét a Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Kép kinyerése kognitív keresés díjszabása

Képek kinyerése egy folyamatot az indexelés kognitív keresés a fájlokat, ha meg kell fizetnie, hogy a művelet az Azure Search számla. Az egy [az indexelő konfigurációjának](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** az a paraméter, amely elindítja a lemezkép kinyerése. Ha **imageAction** értéke "none" (alapértelmezett), a lemezkép kinyerési nem kell fizetnie.

A díjszabása változhat. Dokumentációja a a [díjszabás](https://azure.microsoft.com/pricing/details/search/) oldal az Azure search.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>A számlázás a kognitív keresés beépített képességek

-Felderítési bővítést folyamat beállításakor minden [beépített képességek](cognitive-search-predefined-skills.md) a folyamatban használt gépi tanulási modellek alapulnak. Ezek a modellek a Cognitive Services által biztosított. Ezek a modellek az indexelés során használatakor kell fizetnie megegyező lenne, ha a kért erőforrás közvetlenül.

Tegyük fel például, van egy folyamatot, amely alapján a beolvasott JPEG-fájlok optikai karakterfelismerés (OCR) használja, és az eredményül kapott szöveg leküldéssel kerülnek az Azure Search-index a szabad formátumú keresési lekérdezések esetében. Az indexelési folyamat magában foglalja az indexelőt a [OCR szakértelem](cognitive-search-skill-ocr.md), és a szakértelem lehet, hogy [Cognitive Services-erőforrás csatlakoztatva](cognitive-search-attach-cognitive-services.md). Az indexelő futtatásakor OCR végrehajtási kell fizetni a Cognitive erőforrások számlán jelenik meg.

## <a name="tips-for-reducing-costs"></a>Tippek a költségek csökkentése

Nem lehet leállítani a szolgáltatást, hogy csökkentse a számlán. Dedikált erőforrások mindig működnek működési, a szolgáltatás teljes élettartama a kizárólagos használatra. Csak úgy csökkentheti a számlán, hogy csökkentheti a replikák és partíciók egy szint, amely továbbra is a elfogadható teljesítményt nyújt és [SLA megfelelőségi](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Csökkentheti a költségeket az egyik lehetőség, hogy az alacsonyabb óránkénti szintek kiválasztásának. S1 óradíjak alacsonyabbak, mint az S2 vagy S3 szintű díjszabás szerint. Feltételezve, hogy a terhelés leképezések alacsonyabb végén a szolgáltatás üzembe helyezése, ha elszaporodó a szolgáltatást, akkor is hozzon létre egy második nagyobb többrétegű service, a második szolgáltatásban az indexek újraépítése, és ezután törölje az elsőt.

A helyszíni kiszolgálók a kapacitástervezés hajtotta végre, ha tudja, hogy az általános "megvásárlása", a tervezett növekedést is kezelni tudja. Szolgáltatás egy felhőszolgáltatással akkor is szerezni a költségmegtakarítást további agresszív mivel meg mindent megváltoztathat egy adott beszerzési. Bármikor átválthat egy nagyobb többrétegű Service, ha a jelenlegi előfizetéssel nem elegendő.

### <a name="capacity"></a>Kapacitás

Az Azure Search szolgáltatásban kapacitás van strukturálva, *replikák* és *partíciók*.

+ Replikák a keresési szolgáltatás példányai. Minden egyes replikának egy indexet egy elosztott terhelésű példánya üzemelteti. Például a szolgáltatás, amely hat, a service-ben betöltött minden egyes indexnek hat példányban.

+ A partíciók indexek tárolja, és automatikusan a kereshető adatok felosztása. Két partíció felosztani az index felére, három partíció azt fel harmadának, és így tovább. Kapacitás tekintetében *partíció mérete* elsődleges megkülönböztető szolgáltatása rétegek között.

> [!NOTE]
> Támogatja az összes Standard és a tárolásra optimalizált szinten [replikák és partíciók rugalmas kombinációit](search-capacity-planning.md#chart) így [a rendszer sebessége vagy a tárolás optimalizálása](search-performance-optimization.md) az egyenleg módosításával. Az alapszintű csomag legfeljebb három replika készül a magas rendelkezésre álláshoz kínál, de csak egyetlen partícióval rendelkezik. Ingyenes szintje nem ad meg dedikált erőforrásokat: számítástechnika több előfizető által megosztott erőforrások.

### <a name="more-about-service-limits"></a>További információ a szolgáltatásokra vonatkozó korlátozások

Gazdagép-erőforrások, például indexek és indexelők szolgáltatásokat. Az egyes csomagok közzétételével [szolgáltatási korlátozásaival](search-limits-quotas-capacity.md) hozhat létre erőforrásokat száma. Ezért az indexek (és más objektumok) maximális száma funkciója a második megkülönböztető rétegek között. Az Áttekintés a portálon az egyes lehetőségek, vegye figyelembe az indexek száma korlátlan. További erőforrások, például az indexelők, adatforrásokat és szakértelmével, kerüljenek az index korlátok.

## <a name="consumption-patterns"></a>Felhasználási mintákkal

A legtöbb ügyfél kezdje az ingyenes szolgáltatás, és tartsa határozatlan ideig, és válassza ki a Standard vagy a tárolásra optimalizált rétegek súlyos fejlesztési vagy éles üzemi számítási feladatokhoz.

![Az Azure Search tarifacsomagok](./media/search-sku-tier/tiers.png "Azure Search-tarifacsomag")

A nagy és kis véget ér, a Basic és S3 HD el fontos, de a szokásostól eltérő felhasználási mintákkal. Alapszintű van, kis méretű éles számítási feladatok esetében. SLA-k, a dedikált erőforrások és a magas rendelkezésre állást kínál, de az élcsomóponton tárolására, 2 GB-os összesen feltöltve biztosít. Ezen a szinten olyan ügyfelek számára, egységesen a rendelkezésre álló kapacitás underutilize volt kategóriában. A magas végén S3 HD számítási feladatok jellemzően ISV-k, a partnerek, [több-bérlős megoldások](search-modeling-multitenant-saas-applications.md), vagy minden olyan konfiguráció, amely meghívja ezt a nagy számú kis indexeket. Gyakran törlése, ha alapszintű vagy S3 HD a megfelelő szint. Ha azt szeretné, hogy a jóváhagyás, könyvelés [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [lépjen kapcsolatba az Azure-támogatás](https://azure.microsoft.com/support/options/) útmutatást.

A leggyakrabban használt keresztül S3 szintű, S1 standard csomagokhoz kapacitás szintjei fejlődését alkotják. Partíció mérete nincsenek pontokat és korlátlan számú indexek, indexelők és corollary erőforrások vannak:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partíció mérete|  25 GB | 100 GB | 200 GB |  |  |  |  |
| index és indexelő korlátok| 50 | 200 | 200 |  |  |  |  |

S1 esetén az ügyfelek, amelyek dedikált erőforrásokat, és több partíciót kell közös választani. S1 kínál a partíciók 25 GB-ot, és legfeljebb 12 partícióra, amely egy szolgáltatás korlát 300 GB-os, ha a partíciók replikáit keresztül maximalizálása érdekében. (Lásd: [lefoglalni a partíciókat és -replikákat](search-capacity-planning.md#chart) több elosztott terhelésű értékesítjük.)

A portál és díjszabási oldalakon helyezze a fókuszt a partíció méretét és a storage, de az egyes szinteken az összes számítási képességeket (lemez kapacitása, a processzorok sebességét) általában költségráfordításokkal egyenes arányban növekszik az ár. S2 replikájának S1 gyorsabban, és S3 gyorsabb, mint az S2. S3 szinttel aránytalanul gyorsabb i/o-szünet a lineáris számítási díjak mintát. Ha várhatóan kell a szűk I/O, vegye figyelembe, hogy sokkal több IOPS-S3, mint az alacsonyabb rétegek kap kérheti.

S3 és az S3 HD élvezik azonos nagy kapacitású infrastruktúrával, de különböző módon elérnék a maximális korlát. S3 nagyon nagy méretű indexek, kevesebb célozza meg, így a maximális kapacitásnak az erőforrás-korlátja (az egyes szolgáltatások 2,4 TB). S3 HD három nagy számú kis indexek célozza. 1000 indexek S3 HD eléri a teljesítménye korlátait index megkötések formájában. Ha már S3 HD-ügyfelek, és több mint 1000 indexek van szüksége, forduljon a Microsoft Support információ a folytatásról.

> [!NOTE]
> A dokumentum korlátok egyszerre veszi figyelembe, de azok a továbbiakban nem alkalmazható az új szolgáltatásokat. Amely dokumentumot korlátok továbbra is érvényesek lesznek feltételekkel kapcsolatos további információkért lásd: [korlátait dokumentálja](search-limits-quotas-capacity.md#document-limits).
>

Optimalizált tárterületekkel, L1 és L2, alkalmazásokhoz ideálisak nagy mennyiségű adat követelményeknek, de a viszonylag kevés végfelhasználók számára, amikor minimálisra csökkentik a Lekérdezések késése nem a legmagasabb prioritást.  

|  | L1 | 2\. |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partíció mérete|  1 TB | 2 TB |  |  |  |  |  |
| index és indexelő korlátok| 10 | 10 |  |  |  |  |  |

L2 kétszer teljes tárolási kapacitásának L1 kínál.  Válassza ki a réteg maximális adatmennyiség úgy gondolja, hogy az index kell alapján. A L1 szint partíciók vertikális felskálázása 12 TB legfeljebb 1 TB-os lépésekben. L2 partíciók növelése partíciónként akár 24 TB legfeljebb 2 TB-osra bővül.

## <a name="evaluating-capacity"></a>Kapacitás kiértékelése

Kapacitás és a szolgáltatás futtatásával járó költségek közvetlenül kapcsolódnak. Rétegek készletként alatt két szinttel: storage és az erőforrásokat. Gondolja mindkét mert előbb eléri korlát átlépése érvényes korlátot.

Üzleti igények általában kell indexek száma miatt. Például szüksége lehet egy globális index egy nagy adattárból a dokumentumok számára. Vagy előfordulhat, hogy a régió, alkalmazás vagy üzleti jelentethet több index van szüksége.

Az index mérete határozza meg, hogy kell [hozzon létre egyet](search-create-index-portal.md). Az Azure Search szolgáltatásban az adatok struktúrája elsősorban egy [index fordított](https://en.wikipedia.org/wiki/Inverted_index) struktúra, amely a forrásadatok eltérő jellemzői. Egy fordított index mérete és összetettsége határozza meg tartalmat, nem feltétlenül, amely akkor bele adatok mennyisége alapján. Magas redundanciával nagy adatforrásként egy kisebb adatkészletet, amelyek rendkívül változó tartalom található, mint egy kisebb index eredményezhet. Ezért csak ritkán lehetőség index mérete az eredeti adathalmazból mérete alapján következtetnek ki.

> [!NOTE] 
> Annak ellenére, hogy a jövőbeli igényeit, az indexeket és a storage becslése esetlegességét is gondolja, érdemes ezzel. Ha egy réteg kapacitása elemről kiderül, hogy túl alacsony, a magasabb szintű új szolgáltatások kiépítése kell, majd [töltse be újra az indexek](search-howto-reindex.md). Nincs nem a helyszíni frissítését egy másikra a Termékváltozat-szolgáltatásával.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>1\. lépés: Durva becslést fejlesztése az ingyenes csomag használatával

Kapacitásának becsléséhez egyik lehetőség, hogy indítsa el az ingyenes szinttel. Ne feledje, hogy az ingyenes szolgáltatást kínál legfeljebb három indexre, 50 MB tárhelyet, és az indexelési idő 2 perc. Ez egy előre jelzett index mérete, ezek a korlátozások a becslésbe kihívást jelenthet. Itt látható, amely elvégezhető:

+ [Hozzon létre egy ingyenes szolgáltatás](search-create-service-portal.md).
+ Készítsen elő egy kisebb, jellemző adatkészletet (például 5000 dokumentumok és mintavételi méret 10 %-os).
+ [Az első index létrehozása](search-create-index-portal.md) , és jegyezze fel annak méretét a portálon (például 30 MB).

Ha a minta helyi képviselőjéhez, és 10 százaléka, a teljes adatforrásra, egy 30 MB-os index KB. 300 MB válik, ha az összes dokumentum indexelt. Előzetes szám képességekkel, double (fejlesztési és éles környezetek) két indexeket a költségvetéshez összeg lehet. Ez lehetővé teszi a tárolási követelmények 600 MB összesen. Ez a követelmény egyszerűen teljesül által az alapszintű csomag, így hiba lép érvénybe.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>2\. lépés: A becslések finomított fejlesztése a számlázható szint szerint kalkuláljuk használatával

Egyes ügyfelek inkább a dedikált erőforrások, amelyek nagyobb mintavételi és feldolgozási időt és majd fejlesztése index mennyisége, mérete és a lekérdezés kötetek valósághű becslése a fejlesztés során történő indításához. Kezdetben a szolgáltatás a legjobb-találgatásos becslés alapján van kiépítve. Ezt követően az alkalmazásfejlesztési projekt kiforrottá, mivel csapatok általában tudja, hogy a meglévő szolgáltatás felett vagy alatt várható éles számítási kapacitást.

1. [Tekintse át a szolgáltatásokra vonatkozó korlátozások minden egyes rétegben](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) meghatározni, hogy alacsonyabb szinten kell indexek száma is támogatják-e. Az alap, S1 és S2 csomag index korlátok: 15, 50 és 200, illetve. A tárolásra optimalizált szint, legfeljebb 10 indexeket, mert a feladata, hogy támogatja a nagyon nagy méretű indexek száma alacsony.

1. [Hozzon létre egy szolgáltatás a számlázható szint szerint kalkuláljuk](search-create-service-portal.md):

    + Indítsa el a alacsony, alapszintű vagy az S1, ha Ön a elsajátítható elején.
    + Indítsa el a magas, S2 vagy S3 még akkor is, ha tudja, hogy rendelkezik a nagy méretű indexelést és lekérdezést terhelések lesz.
    + Indítsa el a tárolásra optimalizált L1 vagy L2, ha, indexelés még nagy mennyiségű adatot, és lekérdezési terhelés viszonylag alacsony, mint a belső üzleti alkalmazás.

1. [Az első index létrehozása](search-create-index-portal.md) meghatározni a forrásadatok hogyan fordítja le az indexbe. Ez az az egyetlen módszer, ha meg szeretné becsülni az index mérete.

1. [Figyelheti a storage, szolgáltatási korlátai, lekérdezés mennyiségi és késési](search-monitor-usage.md) a portálon. A portál megjeleníti a lekérdezések száma, a második, szabályozott lekérdezések és a keresési késés. Ezeket az értékeket minden segítségével eldöntheti, hogy ha választotta-e a megfelelő szint. Is részletes figyelésének beállításához értékek átkattintós elemzési például engedélyezésével [forgalmi elemzések keresése](search-traffic-analytics.md).

Index számát és méretét is ugyanilyen fontos, hogy az elemzést. Ennek az az oka a maximális korlát elérésekor keresztül teljes kihasználtság (partíciók) tárolás vagy korlátozásainak erőforrásokon (indexek, indexelők és így tovább), amelyiket hamarabb. A portál segítségével nyomon követheti, mindkettő, bemutató aktuális használatát és a maximális korlát egymás mellett az Áttekintés oldalon.

> [!NOTE]
> Ha a dokumentumok tartalmazzák a felesleges adatokat is kell növelve a tárhellyel kapcsolatos követelmények. Ideális esetben dokumentumok csak a keresési funkció szükséges adatokat tartalmazzák. A bináris adatok nem kereshető, és (esetleg a az Azure tábla- vagy blob storage) külön-külön kell tárolni. Egy mezőt hozzá kell majd az index URL-cím hivatkozni kell a külső adatok tárolásához. Egy adott dokumentumot maximális mérete 16 MB (vagy kevesebb, mint ha Ön egy kérelem több dokumentumok feltöltésével tömeges). További információkért lásd: [korlátozások az Azure Search szolgáltatás](search-limits-quotas-capacity.md).
>

**Lekérdezés kötet kapcsolatos szempontok**

Lekérdezések másodpercenkénti (lekérdezési QPS) egy fontos mérőszám során teljesítményhangolás, de azt általában csak szint veszi figyelembe Ha kezdetben a lekérdezési kötet várható.

A Standard szint az egyensúlyt a replikákat és partíciókat tud biztosítani. Terheléselosztás replikák hozzáadásával növelheti a lekérdezés válaszidejű, optimalizált vagy párhuzamos feldolgozáshoz partíciók hozzáadásával. Majd hangolhassa a teljesítmény a szolgáltatás kiépítése után.

Ha tartós a lekérdezési kötetek kezdettől fogva, érdemes magasabb Standard szinteken, nagyobb teljesítményű hardver által támogatott. Ezután offline állapotba helyezése partíciókat és -replikákat, vagy még váltson egy alacsonyabb szintű szolgáltatást, ha a lekérdezés köteteket nem következik be. A lekérdezések átviteli sebességére kiszámításáról további információkért lásd: [Azure Search-teljesítmény és optimalizálás](search-performance-optimization.md).

A tárolásra optimalizált szint nagy mennyiségű adat számítási feladatok, ha a lekérdezés késleltetési követelményekkel kevésbé fontos több index a teljes rendelkezésre álló tárhely támogató hasznosak. Továbbra is használja további replikák terhelés terheléselosztás és további partíciók párhuzamos feldolgozásra. Majd hangolhassa a teljesítmény a szolgáltatás kiépítése után.

**Szolgáltatásiszint-szerződések**

Az ingyenes szint és az előzetes funkciók nem ad meg [szolgáltatásiszint-szerződések (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Összes számlázható szint, az SLA-k lépnek érvénybe, ha a szolgáltatás üzembe helyezésekor megfelelő redundanciát. Két vagy több replikával rendelkeznek a lekérdezés (olvasás) SLA-k kell. Három vagy több replikával rendelkeznek a lekérdezési és indexelési (olvasás / írás) SLA-k kell. A partíciók számának nincs hatással az SLA-kat.

## <a name="tips-for-tier-evaluation"></a>Tippek a réteg kiértékelése

+ Ismerje meg, hogyan hozhat létre hatékony indexeket, és ismerje meg, milyen frissítési módszerek rendelkezik a legkevésbé érintik. Használat [forgalmi elemzések keresése](search-traffic-analytics.md) következtetések levonásához lekérdezés tevékenység.

+ Metrikák körül lekérdezéseket hozhat létre és a használati minták (lekérdezések munkaidőben, amikor kevesen indexelő) körül adatokat gyűjteni. Ezen adatok segítségével tájékoztatja a szolgáltatás üzembe helyezési döntéseket hozhat. Bár nem célszerű, az óránkénti vagy napi kiadása ütemben történik, partíciók és alkalmazzák a a tervezett módosításokat lekérdezés kötetek erőforrásokat dinamikusan módosíthatja. Nem tervezett, de a tartós módosítások is úgy tud megfelelni, ha szintek tartsa elég hosszú garantálja a műveletek.

+ Ne feledje, hogy a csak a underprovisioning hátránya, hogy előfordulhat, hogy a tényleges követelmények nagyobbak, mint az előrejelzéseket Ha egy szolgáltatás lebontására. A szolgáltatáskimaradás elkerülése érdekében lenne hozzon létre egy új, magasabb szintű ugyanabban az előfizetésben, és azt egymás mellett futtassa addig, amíg az összes alkalmazás és a cél az új végpont.

## <a name="next-steps"></a>További lépések

Indítsa el az ingyenes csomagot, és a egy kezdeti index létrehozása az adatok egy részhalmazának teljesítményjellemzőit megértéséhez használatával. Az adatok struktúrája az Azure Search egy fordított index struktúra. A mérete és a egy fordított index összetettsége tartalom határozza meg. Ne feledje, hogy magas redundáns tartalom általában egy kisebb indexet, mint a magas szabálytalan tartalom eredményez. Így az adatkészlet méretét, hanem tartalom jellemzők index tárolási követelmények meghatározása.

Az index mérete kezdeti becsült Miután [kiépítése egy számlázható szolgáltatás létrejöttét](search-create-service-portal.md) az a cikkben leírtak szerint a csomagok valamelyikére: Alapszintű, Standard vagy a tárolásra optimalizált. Az adatok méretezés mesterséges korlátozások enyhítése és [az index újraépítése](search-howto-reindex.md) az adatokkal, amelyeket szeretne kereshetővé válik.

[Foglaljon le a partíciókat és -replikákat](search-capacity-planning.md) a teljesítmény és méretezés van szüksége, igény szerint.

Ha a teljesítmény és kapacitás rendben, akkor végzett. Ellenkező esetben újra hozzon létre egy keresési szolgáltatás egy másik csomagban, amely több szorosan igényeinek megfelel.

> [!NOTE]
> Ha kérdése van, küldése a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [lépjen kapcsolatba az Azure-támogatás](https://azure.microsoft.com/support/options/).
