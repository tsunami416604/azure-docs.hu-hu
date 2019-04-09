---
title: Az Azure Search szolgáltatás – Azure Search tarifacsomag vagy SKU kiválasztása
description: 'Az Azure Search Termékváltozatokban is üzembe helyezve: Ingyenes, alapszintű és standard szintű, ahol Standard érhető el a különböző erőforrás-konfigurációk és a kapacitás szintek.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: da8c8adacfead598a8dec6280cf3518fb7b31f49
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270952"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Válasszon egy tarifacsomagot az Azure Search

Az Azure Search szolgáltatásban egy [erőforrás létrehozása](search-create-service-portal.md) tarifacsomag vagy SKU, amely a szolgáltatás teljes élettartama rögzített címen. Szolgáltatásszintek **ingyenes**, **alapszintű**, **Standard**, vagy **tárolásra optimalizált**.  **Standard szintű** és **tárolásra optimalizált** érhetők el a konfigurációkat és a kapacitások több. 

A legtöbb ügyfél kezdje a **ingyenes** tesztelési csomagot, és ezután végezni a fejlesztési és éles üzemelő példányokhoz magasabb fizetős csomagok valamelyikére. Az elvégezhető összes útmutatóink és oktatóanyagaink segítségével a **ingyenes** szint, beleértve a nagy erőforrásigényű cognitive search.

> [!NOTE]
> Tárolásra optimalizált szolgáltatásszintjét jelenleg előzetes verzióként érhető el egy vizsgálat és a Kísérletezési céljából a cél az, hogy visszajelzéseket kedvezményes áron. Végső díjszabás lesz bejelentve később, amikor ezek a rétegek általánosan elérhetők. Azt javasoljuk, hogy az éles környezetben ezek a rétegek tanúsítványokkal szemben.

Rétegek helyett az szolgáltatást (szolgáltatásokat) üzemeltető a hardver jellemzőit tükrözik, és vannak különbözteti meg:

+ Indexeket hozhat létre száma
+ Méretétől és sebességétől partíciók (fizikai tároló)

Bár minden szint esetében, beleértve a **ingyenes** réteg, általában kínálnak funkcióparitás, nagyobb méretű számítási feladatokat is szabályozzák a magasabb szintű követelményei. Ha például [mesterséges indexeléshez a Cognitive Services](cognitive-search-concept-intro.md) rendelkezik hosszú ideig futó képességeit, hogy időtúllépés egy ingyenes szolgáltatás, ha az adatkészlet kicsi történik.

> [!NOTE] 
> Funkcióparitás kivételt [indexelők](search-indexer-overview.md), amely nem áll rendelkezésre a S3HD.
>

A szinten belül is [módosíthatja a replika és a partíció erőforrások](search-capacity-planning.md) növelheti vagy csökkentheti a méretezési csoport. Egy vagy két, az egyes kezdődhet, és átmenetileg emelni a indexelési számítási nagy számítási teljesítménnyel. Lehetővé teszi a szinten belüli erőforrásszintek hangolása növeli a rugalmasságot, de is némileg megnehezíti az elemzést. Előfordulhat, hogy talál-e egy alacsonyabb szintre magasabb erőforrások/replikákkal rendelkező kínál a nagyobb érték és a teljesítmény, mint az alacsonyabb szabályozásával magasabb szintre kísérletezést. Mikor és miért módosíthatja lenne a kapacitás kapcsolatos további információkért lásd: [teljesítmény és optimalizálás szempontok](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Az Azure Search csomag

A következő táblázat felsorolja az elérhető tarifacsomag. Egyéb információforrások szint közé tartozik a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/search/), [szolgáltatás és adat korlátok](search-limits-quotas-capacity.md), és a egy szolgáltatást üzembe helyezésekor a portál oldala.

|Szint | Kapacitás |
|-----|-------------|
|Ingyenes | Más előfizetőkkel megosztott. Nem méretezhető, legfeljebb 3 indexszel és 50 MB tárhely. |
|Alapszintű | Dedikált számítási erőforrások a kisebb léptékű termelési számítási feladatokhoz. 2 GB egy partíciót, és legfeljebb három replika. |
|Standard 1 (S1 esetén) | Az S1 a további tárolási és feldolgozási kapacitással, minden szinten dedikált gépek mentése. Partíció mérete 25 GB/partíció (legfeljebb 300 GB szolgáltatásonként) s1 esetén. |
|Standard szintű 2 (S2) | Hasonló az S1 szintre, de a 100 GB/partíció (maximális 1,2 TB szolgáltatásonként) |
|Standard 3 (S3 esetén) | 200 GB/partíció (maximális 2,4 TB szolgáltatásonként) |
|Standard 3 nagy sűrűségű (S3-HD) | Nagy sűrűségű van egy *üzemeltetési mód* S3. Az alapul szolgáló hardver sok kisebb indexek, több-bérlős forgatókönyvek számára van optimalizálva. S3-HD S3, de a hardver optimalizált sok kisebb indexeket a gyors olvasást az azonos egységenkénti díj rendelkezik.|
|Tárolásra optimalizált 1 (1) | 1 TB-os partíciónként (legfeljebb 12 TB szolgáltatásonként) |
|Tárolásra optimalizált 2 (L2) | 2 TB-os partíciónként (legfeljebb 24 TB szolgáltatásonként) |

> [!NOTE] 
> A tárolásra optimalizált rétegei kedvezőbb áron / TB, mint a Standard szint nagyobb tárolási kapacitás. Az elsődleges egyensúlyt a lekérdezés nagyobb késést, amelynek ellenőrizni kell az alkalmazás specifikus igényei.  Ez a szint a teljesítménnyel kapcsolatos megfontolások kapcsolatos további információkért lásd: [teljesítmény és optimalizálás szempontok](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>A számlázás működése

Az Azure Search szolgáltatásban az Azure Search szolgáltatásban a személy költségei három módja van, és a rögzített és változó összetevőből áll. Ez a szakasz a megvizsgálja az egyes számlázási összetevők.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Core szolgáltatási költségei (rögzített és változó)

A szolgáltatás magát a minimális díj megfizetése alól az első keresési egység (1 replika x 1 partíció), és ez a mennyiség állandó, a szolgáltatás teljes élettartama, mert a szolgáltatás bármit kevesebb, mint ez a konfiguráció nem futtatható. 

Az alábbi képernyőképen / alapegység jelzi a Basic, S1 és az ingyenes (S2, S3, L1 és L2 nem jelennek meg). Ha létrehozott egy **alapszintű**, **Standard**, vagy **tárolásra optimalizált** service, a havi költségek lenne átlagos az érték, amely megjelenik az *ár-1*és *ár-2* jelölik. Egység költségek lépjen az egyes szintek mert a számítási teljesítmény és tárterület-kapacitás nagyobb egyes egymást követő rétegben.

![/ Alapegység](./media/search-sku-tier/per-unit-pricing.png "/ alapegység")

További replikák és partíciók olyan bővítménye, a kezdeti díja. Egy keresési szolgáltatás, a minimális konfigurációs egy replika és a partíció szükséges. A minimális túli hozzáadhat replikák és partíciók egymástól függetlenül. Például hozzáadhat csak a replikákat és a csak a partíciókat. 

További replikák és partíciók alapján lesznek kiszámlázva egy [képlet](#search-units). A költségek, amelyek nem lineáris (kétszeresére matematikában költsége nagyobb kapacitás). Példa bemutatja, hogyan a képlet működését, lásd: ["Útmutató a replikák és partíciók lefoglalni"](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="2-data-egress-charges-during-indexing"></a>2. Kimenő adatforgalom díját az indexelés során

Felhasználása [Azure Search-indexelők](search-indexer-overview.md) hatása attól függően, hol találhatók a szolgáltatások számlázási eredményezhet. Kimenő adatforgalom díját kiküszöbölheti az teljes mértékben, ha az Azure Search szolgáltatás hoz létre ugyanabban a régióban, mint az adatokat.

+ A díjfizetés nélkül használhatók az Azure-ban bármely szolgáltatás a bejövő adatokat.

+ A díjfizetés nélkül használhatók az Azure Search kimenő adatokat.

+ Nem számítunk fel díjat, az adatok és az SQL DB, Cosmos, Blob storage a kimenő fájlok (bejövő az Azure Search) mindaddig, amíg az összes szolgáltatás ugyanabban a régióban vannak.

+ A kimenő adatok vagy fájlok díjakat számítjuk Ha storage és az Azure Search különböző régiókban találhatók.

Útválasztás az adatok Azure-régióban, látni fogja a sávszélesség díjai a számlán, az erőforrásokhoz. Ezek a díjak nem részei az Azure Search számla, de itt azok szerepelnek, mert ha indexelők használatával kérje le az adatok vagy fájlok a hálózaton keresztül, látni fogja, hogy a díj a a teljes számlája összegét.

Ha az indexelők nem használ, nem számítunk sávszélesség fel díjat. 

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. AI-bővített indexelés kognitív szolgáltatások használata

A [mesterséges indexeléshez a Cognitive Services](cognitive-search-concept-intro.md) csak, kép kibontása során dokumentumfeltörést alapján számítjuk fel a dokumentumok kinyert képek számát. Szövegkinyerés jelenleg díjmentes. Más végrehajtott információbeolvasás, természetes nyelvi feldolgozás, például a alapuló [beépített kognitív képességeket](cognitive-search-predefined-skills.md) Cognitive Services-erőforrás terhelve van. Végrehajtott információbeolvasás számlázása megegyező módon, ha végrehajtotta a Cognitive Services használatával közvetlenül a feladatot.

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>A számlázás alapját a keresési egységek

Az Azure Search-műveletek, a legfontosabb számlázási megértéséhez koncepciójuk egy *keresési egység* (SU). Azure Search a replikák és partíciók indexelést és lekérdezést is függ, mivel csak az egyik vagy másik számlázási nincs értelme. Ehelyett mindkét összetett számlázási alapul. 

SU, a termék *replika* és *partíciók* a szolgáltatás által használt: **`(R X P = SU)`**

Mindegyik szolgáltatás egy SU (egy másodpéldány egy partíciót megszorozza) kezdődik, az a minimális. Bármely szolgáltatás a maximális mérete 36 SUS-t, amely többféle módon is megvalósítható: 6 partíciók x 6 replikák, illetve 3 partíció x 12 replikákat, hogy néhányat említsünk. Kevesebb, mint a teljes kapacitás szokás. Például egy 3-replika, a 3-partition szolgáltatás 9 SUS-t számít. Áttekintheti [ezen a diagramon](search-capacity-planning.md#chart) érvényes kombinációk egyetlen pillantással megtekintheti.

A számlázási sebessége **óránkénti száma SU**, minden egyes kellene fokozatosan magasabb szinten. A magasabb szintű nagyobb és gyorsabb partíciókat, hozzájárul a magasabb, a teljes óránkénti elszámolása kapható. Értékeli az egyes szintek találhatók [díjszabása](https://azure.microsoft.com/pricing/details/search/). 

A legtöbb ügyfél használata csak egy része online, a teljes kapacitás, a többi tulajdonában foglalás. A számlázási tekintetében, a partíciókat és -replikákat online, számított képlettel a SU, amely meghatározza a ténylegesen kell fizetnie óránként hozza a száma.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Kép kinyerése kognitív keresés díjszabása

Ha egy folyamat indexelés kognitív keresés-fájlokból a képek kibontására, díjkötelesek művelet az Azure Search számla. A paraméter, amely elindítja a lemezkép kinyerési **imageAction** a egy [az indexelő konfigurációjának](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Ha **imageAction** értéke None (alapértelmezett), nem számítunk fel díjat a lemezkép kinyerési.

A díjszabás változhat, de mindig dokumentációja a a [díjszabása](https://azure.microsoft.com/pricing/details/search/) oldal az Azure search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>A számlázás a kognitív keresés beépített képességek

-Felderítési bővítést folyamat beállításakor minden [beépített képességek](cognitive-search-predefined-skills.md) a folyamatban használt gépi tanulási modellek alapulnak. Ezek a modellek a Cognitive Services által biztosított. Használati minták indexelés során történik ugyanez a díjszabás, mintha közvetlenül az erőforrás kérte.

Tegyük fel, hogy egy folyamatot, optikai karakterfelismerés (OCR) álló elleni beolvasott kép JPEG-fájlok, ahol az eredményül kapott szöveg leküldéssel kerülnek az Azure Search-index a szabad formátumú keresési lekérdezések esetében. Az indexelési folyamat magában foglalja az indexelőt a [OCR szakértelem](cognitive-search-skill-ocr.md), és a szakértelem lehet, hogy [Cognitive Services-erőforrás csatlakoztatva](cognitive-search-attach-cognitive-services.md). Az indexelő futtatásakor díjak OCR végrehajtási Cognitive erőforrások számlán jelennek meg.

## <a name="tips-for-reducing-costs"></a>Tippek a költségek csökkentése

Nem lehet leállítani a szolgáltatást, hogy csökkentse a számla. Dedikált erőforrások működési 24-7, a kizárólagos használatára, a szolgáltatás teljes élettartama lefoglalva. Csak úgy csökkentheti a számla a replikákat és partíciókat, hogy továbbra is a elfogadható teljesítményt biztosít alacsony szintű csökkentésével és [SLA megfelelőségi](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

A költségek csökkentésére egy szintje alacsonyabb óránkénti egy szintre megválasztása. S1 óradíjak alacsonyabbak, mint az S2 vagy S3 szintű díjszabás szerint. Feltételezve, hogy a terhelés leképezések alacsonyabb végén célzó, ha Ön a szolgáltatás elszaporodó szolgáltatás hozza létre, akkor sikerült hozzon létre egy második nagyobb többrétegű service, a második szolgáltatás az indexek újraépítése, és törölje az elsőt. 

Ha elvégezte a kapacitástervezés helyi kiszolgálókon, tudja, hogy, hogy az általános "megvásárlása", hogy a tervezett növekedést is kezelni tudja. De a szolgáltatás egy felhőszolgáltatással, akkor is szerezni a költségmegtakarítást további agresszív mert, sok mindent megváltoztathat egy adott beszerzési. Egy újabb többrétegű szolgáltatás mindig váltson, ha a jelenlegi előfizetéssel nem elegendő.

### <a name="capacity-drill-down"></a>Kapacitás kibontása

Az Azure Search szolgáltatásban kapacitás van strukturálva, *replikák* és *partíciók*. 

+ Replika a keresési szolgáltatás példányainak, ahol minden egyes replikának üzemeltet-e az index egy elosztott terhelésű példánya. Például a szolgáltatás, amely 6 replika, a service-ben betöltött minden egyes indexnek 6 példányban. 

+ A partíciók indexek tárolja, és automatikusan a kereshető adatok felosztása: két partíció harmadának ossza fel az indexet a felét, három partíció, és így tovább. Kapacitás tekintetében *mérete particionálása* funkciója az elsődleges megkülönböztető szolgáltatásszinten.

> [!NOTE]
> Az összes **Standard** és **tárolásra optimalizált** támogatási szintekkel [rugalmas kombinációk replika és a partíciók](search-capacity-planning.md#chart) segítségével [a rendszer a sebesség-súlyozási vagy tárolási](search-performance-optimization.md) az egyenleg módosításával. **Alapszintű** kínál fel három replika készül, magas rendelkezésre állású azonban csak egy partíciót tartalmaz. **Ingyenes** szinten nem kínál dedikált erőforrásokat: számítástechnika több előfizető által megosztott erőforrások.

### <a name="more-about-service-limits"></a>További információ a szolgáltatásokra vonatkozó korlátozások

-Szolgáltatások állomás erőforrások, például indexek, indexelők és így tovább. Az egyes csomagok közzétételével [szolgáltatási korlátozásaival](search-limits-quotas-capacity.md) hozhat létre erőforrások mennyisége. Egy korlát indexek (és más objektumok) száma mint ilyen, a második megkülönböztető funkció az szolgáltatásszinten. Az Áttekintés a portálon az egyes lehetőségek, vegye figyelembe a korlátlan számú indexet. További erőforrások, például az indexelők, adatforrásokat és szakértelmével, vannak rögzítve, és index korlátok.

## <a name="consumption-patterns"></a>Felhasználási mintákkal

A legtöbb ügyfél kezdje a **ingyenes** szolgáltatás, amely azok határozatlan ideig megőrizni, és válassza ki a a **Standard** vagy **tárolásra optimalizált** rétegek súlyos fejlesztési vagy az éles üzemi környezetekhez. 

![Az Azure search-csomagok](./media/search-sku-tier/tiers.png "tarifacsomagok az Azure search")

Mindkét végén **alapszintű** és **S3 HD** léteznek, fontos, de a szokásostól eltérő felhasználási mintákkal. **Alapszintű** kisméretű éles számítási feladatokra van: SLA-t, kínál dedikált erőforrások, a magas rendelkezésre állást, de a szerény tárolására, 2 GB-os összesen feltöltve. Ez a szint volt fejthetők vissza az ügyfelek számára, egységesen magas kihasználtságú rendelkezésre álló kapacitás alapján. A távolságban végén **S3 HD** van a számítási feladatok jellemzően ISV-k, a partnerek, [több-bérlős megoldások](search-modeling-multitenant-saas-applications.md), vagy a nagy számú kis indexek hívása konfigurációra. Gyakran magától értetődő mikor **alapszintű** vagy **S3 HD** szintje a megfelelő méretezése, de ha azt szeretné, hogy a megerősítő könyvelés [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [lépjen kapcsolatba az Azure Támogatási](https://azure.microsoft.com/support/options/) további útmutatást tartalmaz.

A leggyakrabban használt standard csomagokhoz fókusz pótlása **S1-S3** egy haladását, a kapacitás, a partíció méretét a nincsenek pontokkal és a maximális értékeket az indexek, indexelők és corollary erőforrások számát szintjei vannak:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partíció mérete|  25 GB | 100 GB | 200 GB |  |  |  |  |
| index és indexelő korlátok| 50 | 200 | 200 |  |  |  |  |

**S1** közös döntés, amikor dedikált erőforrásokat, és több partíciót megtagadná válnak. Legfeljebb 12 partícióra partícióval 25 GB-ot, a szolgáltatási korlátja **S1** 300 GB-os teljes van Ha maximalizálja a partíciók replikáit keresztül (lásd: [lefoglalni a partíciókat és -replikákat](search-capacity-planning.md#chart) esetében több elosztott terhelésű összetételű.)

Portál és díjszabási oldal helyezze a fókuszt a partíció méretét és a storage, de az egyes szinteken az összes számítási képességeket (lemez kapacitása, a processzorok sebességét) ára a költségráfordításokkal egyenes arányban növelhető. Egy **S2** replika: gyorsabb, mint a **S1**, és **S3** gyorsabb, mint **S2**. **S3** rétegek felosztása aránytalanul gyorsabb i/o-általában lineáris számítási díjak mintát. Ha várhatóan a szűk i/o- **S3** biztosít, mint az alacsonyabb rétegek sokkal több IOPS.

**S3** és **S3 HD** biztonsági azonos nagy kapacitású infrastruktúrával, de egyes egy eléri a maximális korlátot különböző módon. **S3** célozza meg, nagyon nagy méretű indexek kevesebb. Mint ilyen, a maximális kapacitásnak az erőforrás kötött (az egyes szolgáltatások 2,4 TB). **S3 HD** nagy számú kis indexek célozza. 1000 indexet, **S3 HD** eléri a teljesítménye korlátait index megkötések formájában. Ha Ön egy **S3 HD** vevő akinek van szüksége a több mint 1000 indexek, forduljon a Microsoft Support információkat a következő lépésekre.

> [!NOTE]
> Korábban a dokumentum korlátok veszi figyelembe volt, de már nem alkalmazható az új szolgáltatások verziókat. További információ a feltételek alapján, amely dokumentumot korlátok továbbra is érvényben maradnak: [szolgáltatási korlátozásaival: korlátait dokumentálja](search-limits-quotas-capacity.md#document-limits).
>

Optimalizált tárolási rétegek, **L1-L2**, a nagy mennyiségű adat követelményeknek, de a végfelhasználók számára, amikor minimálisra csökkentik a Lekérdezések késése nem áll a legmagasabb prioritást száma viszonylag alacsony alkalmazásokhoz ideálisak.  

|  | L1 | 2. |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partíció mérete|  1 TB | 2 TB |  |  |  |  |  |
| index és indexelő korlátok| 10 | 10 |  |  |  |  |  |

*L2* kétszer az általános tárolási kapacitását kínál egy *L1*.  Válassza ki a réteg úgy gondolja, hogy az index kell maximális adatmennyiség alapján.  A *L1* 12 TB legfeljebb 1 TB-os lépésekben szint particionálja az be méretezési közben a *L2* növelheti partíciónként akár 24 TB legfeljebb 2 TB-osra bővül.

## <a name="evaluate-capacity"></a>Kapacitás kiértékelése

Kapacitás és a szolgáltatás futtatásának költségeit, nyissa meg kézzel az aktuális. Rétegek készletként két szinten (tárolási és erőforrások), így gondolja mindkét mert amelyiket először eléri az érvényes korlátot. 

Üzleti igények általában kell indexek száma miatt. Például egy globális egy nagy adattárból a dokumentumok indexének, vagy akár több index, régió, alkalmazás vagy üzleti jelentethet alapján.

Az index mérete határozza meg, hogy kell [hozzon létre egyet](search-create-index-portal.md). Az Azure Search szolgáltatásban az adatok struktúrája elsősorban egy [index fordított](https://en.wikipedia.org/wiki/Inverted_index), amely más paraméterekkel rendelkezik, mint a forrásadatokat. Egy fordított index mérete és összetettsége határozza meg a tartalmat, nem feltétlenül egyezik bele adatcsatorna mennyisége. Egy nagy méretű adatforrás nagy redundanciával egy kisebb indexet, mint egy kisebb adatkészletet nagymértékben változó tartalmat tartalmazó eredményezhet. Mint ilyen akkor csak ritkán lehet index mérete az eredeti készlet mérete alapján következtetnek ki.

> [!NOTE] 
> Bár a jövőbeli igényeit, az indexeket és a storage becslése esetlegességét is gondolja, érdemes ezzel. Ha egy réteg kapacitása elemről kiderül, hogy túl alacsony, szüksége lesz egy új szolgáltatás kiszámlázza a magasabb kiépítéséhez, majd [töltse be újra az indexek](search-howto-reindex.md). Nincs nem ugyanazt a szolgáltatást a Termékváltozat a a másikra a helyszíni frissítését.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>1. lépés: Az ingyenes csomag használatával durva becslést fejlesztése

Egy megoldás kapacitásának becsléséhez a következővel kell kezdődnie, a **ingyenes** szint. Emlékeztetnek arra, hogy a **ingyenes** szolgáltatást kínál, legfeljebb 3 index, 50 MB tárhelyet, és az indexelési idő 2 perc. Ezek a megkötések az előre jelzett index méret becsléséhez kihívást jelenthet, de a következő példa bemutatja egy módszert:

+ [Hozzon létre egy ingyenes szolgáltatás](search-create-service-portal.md)
+ Egy kis méretű, képviselő adatkészlet előkészítése (ötezer dokumentumokat és a tíz százalékos mintaméret feltételezik)
+ [Az első index létrehozása](search-create-index-portal.md) , és jegyezze fel annak méretét a portálon (feltételezik 30 MB)

Feltéve, a minta képviselő, mind a teljes adatforrásra tíz százalékát, egy 30 MB index lesz kb. 300 MB Ha indexelt összes dokumentumot. Előzetes szám képességekkel, double 600 MB-os a tárolási követelmények összesen (fejlesztési és éles környezetben), két indexeket a költségvetéshez összeg lehet. Ez egyszerűen feltételfüggvényt a **alapszintű** tier, így hiba lép érvénybe.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>2. lépés: Fejlesztés a számlázható szint szerint kalkuláljuk használatával állít elő a feldolgozott becslése

Egyes ügyfelek inkább a dedikált erőforrások, amelyek nagyobb mintavételi és feldolgozási időt, és ezután fejlesztése index mennyisége, mérete és a lekérdezés kötetek valósághű becslése a fejlesztés során történő indításához. Kezdetben a szolgáltatás ki van építve a legjobb-találgatásos becslés alapján, és majd, az alkalmazásfejlesztési projekt kiforrottá, teams általában tudja, hogy a meglévő szolgáltatás felett vagy alatt várható éles számítási kapacitást. 

1. [Tekintse át a szolgáltatásokra vonatkozó korlátozások minden egyes rétegben](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) e alacsonyabb rétegek támogathat kell indexek mennyiségének meghatározásához. Között a **alapszintű**-**S1**-**S2** szinteken, index korlátok el a 15-50 – 200 jelölik.  A **tárolásra optimalizált** csomag esetében 10 indexek mivel ez egy nagyon nagy méretű indexek alacsony számának támogatásához Tervező.

1. [Hozzon létre egy szolgáltatás a számlázható szint szerint kalkuláljuk](search-create-service-portal.md):

    + Indítsa el a alacsony **alapszintű** vagy **S1** Ha Ön a elsajátítható elején.
    + Indítsa el a magas **S2** vagy akár **S3**, ha nagy méretű indexelést és lekérdezést terhelések magától értetődő.
    + Tárolásra optimalizált, címen **L1** vagy **L2**, ha meg vannak indexelő nagy mennyiségű adatot, és lekérdezési terhelés viszonylag alacsony, például belső üzleti alkalmazás.

1. [Az első index létrehozása](search-create-index-portal.md) meghatározni a forrásadatok hogyan fordítja le az indexbe. Ez az az egyetlen módszer, ha meg szeretné becsülni az index mérete.

1. [Figyelheti a storage, szolgáltatási korlátai, lekérdezés mennyiségi és késési](search-monitor-usage.md) a portálon. A portálon, olyan lekérdezést mutat be, valamint a második, szabályozott lekérdezések és keresési késés; az összes, amelynek segítségével eldöntheti, hogy ha választotta-e a megfelelő szint. Azokat a portál mérőszámai, kivéve részletes figyelésének beállításához, átkattintós elemzés, például engedélyezésével [forgalmi elemzések keresése](search-traffic-analytics.md). 

Index számát és méretét alkalmazhatják az elemzés, mert maximális korlát elérésekor keresztül teljes kihasználtság (partíciók) tárolás vagy korlátozásainak erőforrásokon (indexek, indexelők és így tovább), amelyiket hamarabb elérik. A portál segítségével nyomon követheti, mindkettő, bemutató aktuális használatát és a maximális korlát egymás mellett az Áttekintés oldalon.

> [!NOTE]
> Tárolási követelmények túlterhelt felfújt lehet, ha a dokumentumok a felesleges adatokat tartalmaznak. Ideális esetben a dokumentumok csak a keresési élmény szükséges adatokat tartalmazzák. A bináris adatok nem kereshető, és kell tárolni külön (például egy Azure table- vagy blob storage) mezőt az indexben URL-cím hivatkozni kell a külső adatok tárolásához. Egy adott dokumentumot maximális mérete 16 MB (vagy kevesebb, mint ha Ön egy kérelem több dokumentumok feltöltésével tömeges). [Korlátozások az Azure Search szolgáltatás](search-limits-quotas-capacity.md) tartalmaz további információkat.
>

**Lekérdezés kötet kapcsolatos szempontok**

Lekérdezések másodpercenkénti (lekérdezési QPS) azt jelzi, amely kiemeli átveszi az során teljesítményhangolás, de általában nem szint veszi figyelembe, ha a kezdetben a lekérdezési kötet várt.

A standard szint az egyensúlyt a replikák közvetíti partíciók, további replikán keresztül kiszolgálnia lekérdezést támogató betöltésére, terheléselosztás és további partíciók párhuzamos feldolgozásra. A teljesítmény finomhangolása a szolgáltatás kiépítése után.

Ügyfelek, akik várhatóan erős tartósan lekérdezés kötetek kezdettől fogva érdemes magasabb **Standard** rétegek, nagyobb teljesítményű hardver által támogatott. Ezután offline állapotba helyezése partíciókat és -replikákat, vagy még váltson egy alacsonyabb szintű szolgáltatást, ha a lekérdezés köteteket tényleges táblává alakíthatóak sikertelen. A lekérdezések átviteli sebességére kiszámításáról további információkért lásd: [Azure Search-teljesítmény és optimalizálás](search-performance-optimization.md).

A tárolási rétegek felé nagy adatszámítási támogató további általános index rendelkezésre álló tárhelyet, amelyeknél némileg Könnyített lekérdezés késésre vonatkozó követelmény letisztult optimalizálva.  További replikák továbbra is jól használható betöltésére, terheléselosztás és további partíciók párhuzamos feldolgozásra. A teljesítmény finomhangolása a szolgáltatás kiépítése után.

**Szolgáltatásiszint-szerződések**

A **ingyenes** szint és az előzetes funkciók nem tartoznak [szolgáltatói szerződések (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Összes számlázható szint, az SLA-k lépnek érvénybe, ha a szolgáltatás üzembe helyezésekor megfelelő redundanciát. Két vagy több replikát is szükséges a lekérdezés (olvasás) SLA-t. Három vagy több replikát is szükséges a lekérdezési és indexelési (olvasás / írás) SLA-t. A partíciók számát, nem egy SLA-t veszi figyelembe. 

## <a name="tips-for-tier-evaluation"></a>Tippek a réteg kiértékelése

+ Ismerje meg, hogyan hozhat létre hatékony indexeket, és mely frissítési módszerek a legalacsonyabb impactful. Javasoljuk, hogy [forgalmi elemzések keresése](search-traffic-analytics.md) az üzletvitelt a lekérdezés tevékenység számára.

+ Lehetővé teszi a mérőszámok körül lekérdezéseket hozhat létre és használati minták (lekérdezések munkaidőben, amikor kevesen indexelő) adatokat gyűjthet, és tájékoztatja a jövőbeli szolgáltatás kialakítási döntéseket ezen adatok segítségével. Az óránkénti vagy napi kiadása ütemben történik, nem praktikus, míg a partíciók és erőforrások kezelésére lekérdezési kötetekről, vagy nem tervezett módosításokat, de a tartós módosítások Ha szintek tartsa elég hosszú garantálja a műveletek dinamikusan módosíthatja.

+ Ne feledje, hogy a csak a kiépítés alatt hátránya, hogy szükség lehet a üzemen egy szolgáltatás, ha a tényleges követelmények nagyobb a becsült. A szolgáltatáskimaradás elkerülése érdekében lenne hozzon létre egy új, magasabb szintű ugyanabban az előfizetésben, és azt egymás mellett futtassa addig, amíg az összes alkalmazás és a cél az új végpont.

## <a name="next-steps"></a>További lépések

Kezdje egy **ingyenes** csomagot, és teljesítményjellemzőit megérteni az adatok egy részhalmazának használata kezdeti indexet hozhat létre. Az Azure Search szolgáltatásban az adatok struktúrája fordított index, amennyiben ez a méret és tartalom fordított index összetettsége határozza meg. Ne feledje, hogy magas redundáns tartalom általában egy kisebb indexet, mint a magas szabálytalan tartalom eredményez. Mint ilyen akkor az adatkészlet, amely meghatározza, hogy index tárhellyel kapcsolatos követelmények mérete helyett a tartalom jellemzőit.

Index mérete, a kezdeti ötlet után [kiépítése egy számlázható szolgáltatás létrejöttét](search-create-service-portal.md) az ebben a cikkben tárgyalt vagy csomagok valamelyikére **alapszintű**, **Standard**, vagy **Tárolásra optimalizált** szint. Az adatok méretezés mesterséges korlátozások enyhítése és [az index újraépítése](search-howto-reindex.md) minden rájuk ténylegesen kívánt adatokat.

[Foglaljon le a partíciókat és -replikákat](search-capacity-planning.md) a teljesítmény és méretezés van szüksége, igény szerint.

Ha a teljesítmény és kapacitás rendben, akkor kell elvégezni. Ellenkező esetben újra hozzon létre egy keresési szolgáltatás egy másik csomagban, amely több szorosan igényeinek megfelel.

> [!NOTE]
> További segítséget nyújt a kérdésekre, közzététel [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [Azure támogatási](https://azure.microsoft.com/support/options/).
