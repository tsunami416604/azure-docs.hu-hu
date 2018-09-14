---
title: Azure Search szolgáltatás tarifacsomag vagy SKU kiválasztása |} A Microsoft Docs
description: 'Az Azure Search bővítheti, Termékváltozatokban: ingyenes, alapszintű és standard szintű, ahol Standard érhető el a különböző erőforrás-konfigurációk és a kapacitás szintek.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: 140daf4903c64d734182545cd4dc58db60274852
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576120"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Válasszon egy tarifacsomagot az Azure Search

Az Azure Search szolgáltatásban egy [szolgáltatást annak üzembe helyezése](search-create-service-portal.md) egy adott tarifacsomag vagy SKU. Lehetőségek a következők **ingyenes**, **alapszintű**, vagy **Standard**, ahol **Standard** érhető el a konfigurációkat és a kapacitást. 

Ez a cikk célja segítségével válassza ki a szintet. Kiegészíti a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/search/) és [Szolgáltatáskorlátok](search-limits-quotas-capacity.md) számlázási fogalmakat és a különböző szinteken társított felhasználási mintákkal hírösszefoglalójának tartalmazó oldalt. Azt is javasolja, iteratív megközelítés megértésében, melyik szint a legmegfelelőbb az igényeinek. 

Rétegek kapacitás-funkciók nem határozza meg. Ha egy réteg kapacitása elemről kiderül, hogy túl alacsony, szüksége lesz egy új szolgáltatás kiszámlázza a magasabb kiépítéséhez, majd [töltse be újra az indexek](search-howto-reindex.md). Nincs nem ugyanazt a szolgáltatást a Termékváltozat a a másikra a helyszíni frissítését.

A szolgáltatás rendelkezésre állási nem elsődleges szint veszi figyelembe. Minden szinten, beleértve a **ingyenes** csomag, ajánlat funkcióparitás S3HD vonatkozó kivételével. Azonban az indexelés és a resource korlátozások hatékonyan korlátozhatja a szolgáltatás használatának mértékét. Ha például [cognitive search](cognitive-search-concept-intro.md) indexelő rendelkezik hosszú ideig futó képességeit, hogy időtúllépés egy ingyenes szolgáltatás, ha az adatkészlet történik, nagyon kicsi.

> [!TIP]
> A legtöbb ügyfél kezdje a **ingyenes** tesztelési csomagot, és ezután végezni a **Standard** fejlesztéséhez. Egy szint kiválasztása után, és [egy keresési szolgáltatás kiépítése](search-create-service-portal.md), is [növelheti a replika és a partíció számát](search-capacity-planning.md) a teljesítmény finomhangolásának. Mikor és miért módosíthatja lenne a kapacitás kapcsolatos további információkért lásd: [teljesítmény és optimalizálás szempontok](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Számlázással kapcsolatos fogalmak

Fogalmak a kiválasztott csomag ismernie kell például a kapacitás definíciók, szolgáltatási korlátai és szolgáltatási egységek. 

### <a name="capacity"></a>Kapacitás

Kapacitás van strukturálva, *replikák* és *partíciók*. 

+ Replika a keresési szolgáltatás példányainak, ahol minden egyes replikának üzemeltet-e az index egy elosztott terhelésű példánya. Például a szolgáltatás, amely 6 replika, a service-ben betöltött minden egyes indexnek 6 példányban. 

+ A partíciók indexek tárolja, és automatikusan a kereshető adatok felosztása: két partíció harmadának ossza fel az indexet a felét, három partíció, és így tovább. Kapacitás tekintetében *mérete particionálása* funkciója az elsődleges megkülönböztető szolgáltatásszinten.

> [!NOTE]
> Az összes **Standard** támogatási szintekkel [rugalmas kombinációk replika és a partíciók](search-capacity-planning.md#chart) segítségével [sebessége vagy a tárolás a rendszer-súlyozási](search-performance-optimization.md) az egyenleg módosításával. **Alapszintű** kínál fel három replika készül, magas rendelkezésre állású azonban csak egy partíciót tartalmaz. **Ingyenes** szinten nem kínál dedikált erőforrásokat: számítástechnika több ingyenes szolgáltatások által megosztott erőforrások.

### <a name="search-units"></a>Keresési egységek

A legfontosabb számlázási fogalom megértéséhez egy *keresési egység* (SU), azaz a számlázási egység az Azure search. Azure Search a replikák és partíciók függvényt is függ, mivel egy vagy egyéb számlázási nem bírnak. Ehelyett mindkét összetett számlázási alapul. Formulaically, egy a SU, amely a replika és a egy szolgáltatás által használt partíciók: (az R-X P = SU). Legalább 1 SU (egy másodpéldány egy partíciót megszorozza) kezdődik minden szolgáltatás, de realisztikusabb modell lehet, hogy egy 3 és a replikakiszolgáló, a 3-partition service 9 SUS-t számít. 

Bár az egyes szintek fokozatosan nagyobb kapacitást biztosít, közzétehet egy online, a teljes kapacitás része a többi tulajdonában foglalás. A számlázás tekintetében, a partíciókat és -replikákat, hogy tenné online, számított képlettel a SU, amely meghatározza a ténylegesen kell fizetnie a száma.

Számlázási ez óránként / SU, az egyes szintek különböző arány kellene. Értékeli az egyes szintek találhatók [díjszabása](https://azure.microsoft.com/pricing/details/search/).

### <a name="limits"></a>Korlátok

-Szolgáltatások állomás erőforrások, például indexek, indexelők és így tovább. Az egyes csomagok közzétételével [szolgáltatási korlátozásaival](search-limits-quotas-capacity.md) hozhat létre erőforrások mennyisége. Egy korlát indexek (és más objektumok) száma mint ilyen, a második megkülönböztető funkció az szolgáltatásszinten. Az Áttekintés a portálon az egyes lehetőségek, vegye figyelembe a korlátlan számú indexet. További erőforrások, például az indexelők, adatforrásokat és szakértelmével, vannak rögzítve, és index korlátok.

## <a name="consumption-patterns"></a>Felhasználási mintákkal

A legtöbb ügyfél kezdje a **ingyenes** szolgáltatás, amely azok határozatlan ideig megőrizni, és válassza ki a a **Standard** rétegek súlyos fejlesztési vagy éles üzemi számítási feladatokhoz. 

![Az Azure search-csomagok](./media/search-sku-tier/tiers.png "tarifacsomagok az Azure search")

Mindkét végén **alapszintű** és **S3 HD** léteznek, fontos, de a szokásostól eltérő felhasználási mintákkal. **Alapszintű** kisméretű éles számítási feladatokra van: SLA-t, kínál dedikált erőforrások, a magas rendelkezésre állást, de a szerény tárolására, 2 GB-os összesen feltöltve. Ez a szint volt fejthetők vissza az ügyfelek számára, egységesen magas kihasználtságú rendelkezésre álló kapacitás alapján. A távolságban végén **S3 HD** van a számítási feladatok jellemzően ISV-k, a partnerek, [több-bérlős megoldások](search-modeling-multitenant-saas-applications.md), vagy a nagy számú kis indexek hívása konfigurációra. Gyakran magától értetődő mikor **alapszintű** vagy **S3 HD** szintje a megfelelő méretezése, de ha azt szeretné, hogy a megerősítő könyvelés [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [lépjen kapcsolatba az Azure Támogatási](https://azure.microsoft.com/support/options/) további útmutatást tartalmaz.

A leggyakrabban használt standard csomagokhoz fókusz pótlása **S1-S3** egy haladását, a kapacitás, a partíció méretét a nincsenek pontokkal és a maximális értékeket az indexek, indexelők és corollary erőforrások számát szintjei vannak:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partíció mérete|  25 GB | 100 GB | 250 GB |  |  |  |  |
| index és indexelő korlátok| 50 | 200 | 200 |  |  |  |  |

**S1** közös döntés, amikor dedikált erőforrásokat, és több partíciót megtagadná válnak. Legfeljebb 12 partícióra partícióval 25 GB-ot, a szolgáltatási korlátja **S1** 300 GB-os teljes van Ha maximalizálja a partíciók replikáit keresztül (lásd: [lefoglalni a partíciókat és -replikákat](search-capacity-planning.md#chart) esetében több elosztott terhelésű összetételű.)

Portál és díjszabási oldal helyezze a fókuszt a partíció méretét és a storage, de az egyes szinteken az összes számítási képességeket (lemez kapacitása, a processzorok sebességét) ára a költségráfordításokkal egyenes arányban növelhető. Egy **S2** replika: gyorsabb, mint a **S1**, és **S3** gyorsabb, mint **S2**. **S3** rétegek felosztása aránytalanul gyorsabb i/o-általában lineáris számítási díjak mintát. Ha várhatóan a szűk i/o- **S3** biztosít, mint az alacsonyabb rétegek sokkal több IOPS.

**S3** és **S3 HD** biztonsági azonos nagy kapacitású infrastruktúrával, de egyes egy eléri a maximális korlátot különböző módon. **S3** célozza meg, nagyon nagy méretű indexek kevesebb. Mint ilyen, a maximális kapacitásnak az erőforrás kötött (az egyes szolgáltatások 2,4 TB). **S3 HD** nagy számú kis indexek célozza. 1000 indexet, **S3 HD** eléri a teljesítménye korlátait index megkötések formájában. Ha Ön egy **S3 HD** vevő akinek van szüksége a több mint 1000 indexek, forduljon a Microsoft Support információkat a következő lépésekre.

> [!NOTE]
> Korábban a dokumentum korlátok veszi figyelembe volt, de már nem alkalmazható a legtöbb Azure Search szolgáltatás 2018 január után létesített verziókat. Amelynek a dokumentum korlátozások is érvényesek feltételekkel kapcsolatos további információkért lásd: [szolgáltatási korlátozásaival: korlátait dokumentálja](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Kapacitás kiértékelése

Kapacitás és a szolgáltatás futtatásának költségeit, nyissa meg kézzel az aktuális. Rétegek készletként két szinten (tárolási és erőforrások), így gondolja mindkét mert amelyiket először eléri az érvényes korlátot. 

Üzleti igények általában kell indexek száma miatt. Például egy globális egy nagy adattárból a dokumentumok indexének, vagy akár több index, régió, alkalmazás vagy üzleti jelentethet alapján.

Az index mérete határozza meg, hogy kell [hozzon létre egyet](search-create-index-portal.md). Az Azure Search szolgáltatásban az adatok struktúrája elsősorban egy [index fordított](https://en.wikipedia.org/wiki/Inverted_index), amely más paraméterekkel rendelkezik, mint a forrásadatokat. Egy fordított index mérete és összetettsége határozza meg a tartalmat, nem feltétlenül egyezik bele adatcsatorna mennyisége. Egy nagy méretű adatforrás nagy redundanciával egy kisebb indexet, mint egy kisebb adatkészletet nagymértékben változó tartalmat tartalmazó eredményezhet.  Mint ilyen akkor csak ritkán lehet index mérete az eredeti készlet mérete alapján következtetnek ki.

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>1. lépés: Az ingyenes csomag használatával durva becslést fejlesztése

Egy megoldás kapacitásának becsléséhez a következővel kell kezdődnie, a **ingyenes** szint. Emlékeztetnek arra, hogy a **ingyenes** szolgáltatást kínál, legfeljebb 3 index, 50 MB tárhelyet, és az indexelési idő 2 perc. Ezek a megkötések az előre jelzett index méret becsléséhez kihívást jelenthet, de a következő példa bemutatja egy módszert:

+ [Hozzon létre egy ingyenes szolgáltatás](search-create-service-portal.md)
+ Egy kis méretű, képviselő adatkészlet előkészítése (ötezer dokumentumokat és a tíz százalékos mintaméret feltételezik)
+ [Az első index létrehozása](search-create-index-portal.md) , és jegyezze fel annak méretét a portálon (feltételezik 30 MB)

Feltéve, a minta képviselő, mind a teljes adatforrásra tíz százalékát, egy 30 MB index lesz kb. 300 MB Ha indexelt összes dokumentumot. Előzetes szám képességekkel, double 600 MB-os a tárolási követelmények összesen (fejlesztési és éles környezetben), két indexeket a költségvetéshez összeg lehet. Ez egyszerűen feltételfüggvényt a **alapszintű** tier, így hiba lép érvénybe.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>2. lépés: Fejlesztés a számlázható szint szerint kalkuláljuk használatával állít elő a feldolgozott becslése

Egyes ügyfelek inkább a dedikált erőforrások, amelyek nagyobb mintavételi és feldolgozási időt, és ezután fejlesztése index mennyisége, mérete és a lekérdezés kötetek valósághű becslése a fejlesztés során történő indításához. Kezdetben a szolgáltatás ki van építve a legjobb-találgatásos becslés alapján, és majd, az alkalmazásfejlesztési projekt kiforrottá, teams általában tudja, hogy a meglévő szolgáltatás felett vagy alatt várható éles számítási kapacitást. 

1. [Tekintse át a szolgáltatásokra vonatkozó korlátozások minden egyes rétegben](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) e alacsonyabb rétegek támogathat kell indexek mennyiségének meghatározásához. Között a **alapszintű**-**S1**- **S2** szinteken, index korlátok el a 15-50 – 200 jelölik.

1. [Hozzon létre egy szolgáltatás a számlázható szint szerint kalkuláljuk](search-create-service-portal.md):

    + Indítsa el a alacsony **alapszintű** vagy **S1** Ha Ön a elsajátítható elején.
    + Indítsa el a magas **S2** vagy akár **S3**, ha nagy méretű indexelést és lekérdezést terhelések magától értetődő.

1. [Az első index létrehozása](search-create-index-portal.md) meghatározni a forrásadatok hogyan fordítja le az indexbe. Ez az az egyetlen módszer, ha meg szeretné becsülni az index mérete.

1. [Figyelheti a storage, szolgáltatási korlátai, lekérdezés mennyiségi és késési](search-monitor-usage.md) a portálon. A portálon, olyan lekérdezést mutat be, valamint a második, szabályozott lekérdezések és keresési késés; összes segítségével eldöntheti, hogy ha vannak, a megfelelő szint. Azokat a portál mérőszámai, kivéve részletes figyelésének beállításához, átkattintós elemzés, például engedélyezésével [forgalmi elemzések keresése](search-traffic-analytics.md). 

Index számát és méretét alkalmazhatják az elemzés, mert maximális korlát elérésekor keresztül teljes kihasználtság (partíciók) tárolás vagy korlátozásainak erőforrásokon (indexek, indexelők és így tovább), amelyiket hamarabb elérik. A portál segítségével nyomon követheti, mindkettő, bemutató aktuális használatát és a maximális korlát egymás mellett az Áttekintés oldalon.

> [!NOTE]
> Tárolási követelmények túlterhelt felfújt lehet, ha a dokumentumok a felesleges adatokat tartalmaznak. Ideális esetben a dokumentumok csak a keresési élmény szükséges adatokat tartalmazzák. A bináris adatok nem kereshető, és kell tárolni külön (például egy Azure table- vagy blob storage) mezőt az indexben URL-cím hivatkozni kell a külső adatok tárolásához. Egy adott dokumentumot maximális mérete 16 MB (vagy kevesebb, mint ha Ön egy kérelem több dokumentumok feltöltésével tömeges). [Korlátozások az Azure Search szolgáltatás](search-limits-quotas-capacity.md) tartalmaz további információkat.
>

**Lekérdezés kötet kapcsolatos szempontok**

Lekérdezések másodpercenkénti (lekérdezési QPS) azt jelzi, amely kiemeli átveszi az során teljesítményhangolás, de általában nem szint veszi figyelembe, ha a kezdetben várhatóan nagyon jó lekérdezési kötet.

Replikák az egyensúlyt a standard csomag összes közvetíti partíciók, további replikán keresztül kiszolgálnia lekérdezést támogató betöltésére, terheléselosztás és további partíciók párhuzamos feldolgozásra. A teljesítmény finomhangolása a szolgáltatás kiépítése után.

Ügyfelek, akik strong kezdettől fogva kötetek érdemes megfontolnia a magasabb szintű, nagyobb teljesítményű hardver által támogatott lekérdezési megtartását. Ezután offline állapotba helyezése partíciókat és -replikákat, vagy még váltson egy alacsonyabb szintű szolgáltatást, ha a lekérdezés köteteket tényleges táblává alakíthatóak sikertelen. A lekérdezések átviteli sebességére kiszámításáról további információkért lásd: [Azure Search-teljesítmény és optimalizálás](search-performance-optimization.md).


**Szolgáltatásiszint-szerződések**

A **ingyenes** szint és az előzetes funkciók nem tartoznak [szolgáltatói szerződések (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Összes számlázható szint, az SLA-k lépnek érvénybe, ha a szolgáltatás üzembe helyezésekor megfelelő redundanciát. Két vagy több replikát is szükséges a lekérdezés (olvasás) SLA-t. Három vagy több replikát is szükséges a lekérdezési és indexelési (olvasás / írás) SLA-t. A partíciók számát, nem egy SLA-t veszi figyelembe. 

## <a name="tips-for-tier-evaluation"></a>Tippek a réteg kiértékelése

+ Ismerje meg, hogyan hozhat létre hatékony indexeket, és mely frissítési módszerek a legalacsonyabb impactful. Javasoljuk, hogy [forgalmi elemzések keresése](search-traffic-analytics.md) az üzletvitelt a lekérdezés tevékenység számára.

+ Lehetővé teszi a mérőszámok körül lekérdezéseket hozhat létre és használati minták (lekérdezések munkaidőben, amikor kevesen indexelő) adatokat gyűjthet, és tájékoztatja a jövőbeli szolgáltatás kialakítási döntéseket ezen adatok segítségével. Amíg nem gyakorlati óránkénti vagy napi szinten, a partíciók és erőforrások kezelésére lekérdezési kötetekről, vagy nem tervezett módosításokat, de a tartós módosítások Ha szintek tartsa elég hosszú garantálja a műveletek dinamikusan módosíthatja.

+ Ne feledje, hogy a csak a kiépítés alatt hátránya, hogy szükség lehet a üzemen egy szolgáltatás, ha a tényleges követelmények nagyobb a becsült. A szolgáltatáskimaradás elkerülése érdekében lenne hozzon létre egy új, magasabb szintű ugyanabban az előfizetésben, és azt egymás mellett futtassa addig, amíg az összes alkalmazás és a cél az új végpont.

## <a name="next-steps"></a>További lépések

Kezdje egy **ingyenes** csomagot, és teljesítményjellemzőit megérteni az adatok egy részhalmazának használata kezdeti indexet hozhat létre. Az Azure Search szolgáltatásban az adatok struktúrája fordított index, amennyiben ez a méret és tartalom fordított index összetettsége határozza meg. Ne feledje, hogy magas redundáns tartalom általában egy kisebb indexet, mint a magas szabálytalan tartalom eredményez. Mint ilyen akkor az adatkészlet, amely meghatározza, hogy index tárhellyel kapcsolatos követelmények mérete helyett a tartalom jellemzőit.

Index mérete, a kezdeti ötlet után [kiépítése egy számlázható szolgáltatás létrejöttét](search-create-service-portal.md) az ebben a cikkben tárgyalt vagy csomagok valamelyikére **alapszintű** vagy egy **Standard** szint. Az adatok részhalmazainak mesterséges korlátozások enyhítése és [az index újraépítése](search-howto-reindex.md) minden rájuk ténylegesen kívánt adatokat.

[Foglaljon le a partíciókat és -replikákat](search-capacity-planning.md) a teljesítmény és méretezés van szüksége, igény szerint.

Ha a teljesítmény és kapacitás rendben, akkor kell elvégezni. Ellenkező esetben újra hozzon létre egy keresési szolgáltatás egy másik csomagban, amely több szorosan igényeinek megfelel.

> [!NOTE]
> További segítséget nyújt a kérdésekre, közzététel [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [Azure támogatási](https://azure.microsoft.com/support/options/).