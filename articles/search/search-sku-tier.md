---
title: Válassza ki az árképzési szint vagy SKU Azure Search szolgáltatáshoz |} Microsoft Docs
description: 'Az Azure Search is üzembe ezek termékváltozatok: ingyenes, a Basic és standard szintű, ahol Standard lehetőség a különböző erőforrás-konfigurációk és kapacitása szintjének.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/12/2018
ms.author: heidist
ms.openlocfilehash: bbf535c5b446fd654331374d29c106b6e43d55f5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Válasszon egy tarifacsomagot az Azure Search

Az Azure Search egy [szolgáltatás kiépítve](search-create-service-portal.md) egy adott árképzési szint vagy a Termékváltozat. A választható lehetőségek **szabad**, **alapvető**, vagy **szabványos**, ahol **szabványos** több konfigurációk és kapacitások érhető el. 

Ez a cikk célja segítségével válassza ki a réteg. Az kiegészíti a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/search/) és [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md) egy kivonatoló számlázási fogalmakat és felhasználási minták különböző rétegek társított lapot. Milyen ajánlott szinten az igényeinek megfelelő megértéséhez iteratív megközelítéssel is javasol. 

Rétegek kapacitását, és nem szolgáltatások meghatározása. Ha a réteg kapacitása elemről kiderül, hogy túl alacsony, szüksége lesz egy új szolgáltatás, a magasabb szintű használható kiépítése, majd [töltse be újra az indexek](search-howto-reindex.md). Nincs nem ugyanazt a szolgáltatást a egyik Termékváltozatáról egy másikra helybeni frissítését.

A szolgáltatás rendelkezésre állásával nincs elsődleges réteg veszi figyelembe. Minden csomagban, beleértve a **szabad** réteg, szolgáltatásparitást kivételével S3HD vonatkozó ajánlatot. Azonban indexelő és erőforrás-korlátozások hatékonyan tudja csökkenthetjük a funkciók használata. Például [kognitív keresési](cognitive-search-concept-intro.md) indexelő rendelkezik hosszú futású ismeretek adott túllépi az időkorlátot egy ingyenes szolgáltatás kivéve, ha az adatkészlet nagyon rövid történik.

> [!TIP]
> A legtöbb ügyfél indítsa el a **szabad** értékelésre réteg, majd a majd átmenettel **szabványos** fejlesztéshez. A réteg kiválasztása után és [egy keresési szolgáltatás kiépítése](search-create-service-portal.md), is [növelje a replika és a partíció számát](search-capacity-planning.md) teljesítményhangolás a. További információ a időpontját és okát módosíthatja volna a kapacitás: [teljesítmény- és optimalizálási szempontok](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Számlázással kapcsolatos fogalmak

Meg kell ismernie a réteghez kijelölést fogalmak kapacitás definíciókat, a szolgáltatásra vonatkozó korlátozások és a szolgáltatási egységek tartalmaznak. 

### <a name="capacity"></a>Kapacitás

Kapacitás épül *replikák* és *partíciók*. Replikák a keresési szolgáltatás példánya, ahol minden egyes replikának üzemeltet egy elosztott terhelésű példányt az index. Például egy szolgáltatás 6 replikával rendelkezik minden szolgáltatási betöltött index 6 másolatát. Partíciók indexek tárolja, és automatikusan osztani a kereshető adatokat: két partíció ossza fel a index fele, három partíciók hajtogatja, és így tovább. Tekintetében kapacitás *mérete partícióazonosító* egy, az elsődleges ezeknek funkció rétegek között.

> [!NOTE]
> Minden **szabványos** támogatási tiers [rugalmas kombinációk replika és a partíciók](search-capacity-planning.md#chart) , hogy [a rendszert a sebesség, vagy tárolási súlyozási](search-performance-optimization.md) az egyenleg módosításával. **Alapszintű** be három replikák kínál a magas rendelkezésre állású azonban csak a partíció van. **Szabad** rétegek nem adja meg a dedikált erőforrások: számítástechnikai több szabad szolgáltatás által megosztott erőforrások.

### <a name="limits"></a>Korlátok

Szolgáltatások gazdagép-erőforrások, például indexekkel, indexelők és így tovább. Minden egyes réteg ró [szolgáltatási korlátait](search-limits-quotas-capacity.md) hozhat létre erőforrások mennyisége. Indexek (és egyéb objektumok) számára a cap mint ilyen, a második ezeknek a szolgáltatás az rétegek között. Az Áttekintés a portálon lehetőségekről, vegye figyelembe az indexek száma vonatkozó korlátozások. További erőforrások, például az indexelők, az adatforrások és skillsets, index határértékekhez van rögzítve.

### <a name="search-units"></a>Keresési egységek

A legfontosabb számlázási fogalom megértéséhez egy *keresési egység* (SU), amely a számlázási egység az Azure Search. Azure Search függ a replikák és a partíciók függvény, mert egy vagy a másik számlázási nem értelmezhető. Ehelyett a számlázási együtt összetett alapul. Formulaically, egy SU a termék replika-és a szolgáltatás által használt partíciók: (R X P = SU). Legalább 1 SU (egy másodpéldány egy partíciót megszorozza) kezdődik, minden szolgáltatás lenne, de egy modell modell egy 3-replika, a 3-partíció szolgáltatás 9 SUs terheli. 

Bár az egyes rétegek fokozatosan nagyobb kapacitást biztosít, helyezheti egy része online, a teljes kapacitás tartalék a többi okozó. Számlázási tekintetében a partíciók és online, számított képlettel a SU, határozza meg, hogy mi ténylegesen kell fizetnie kapcsolása replikák száma is.

Számlázási ez óránként / SU, és minden réteg, hogy a különböző mértékben. Az egyes rétegek található minősíti [díjszabás](https://azure.microsoft.com/pricing/details/search/).

## <a name="consumption-patterns"></a>Felhasználási minták

A legtöbb ügyfél indítsa el a **szabad** szolgáltatás, amely vezetnek határozatlan ideig, és válassza ki a a **szabványos** rétegek súlyos fejlesztési és éles munkaterhelésekhez. 

![Az Azure search rétegek](./media/search-sku-tier/tiers.png "tarifacsomagok az Azure search")

A kezdési, és a **alapvető** és **S3 HD** fontos, de nem típusos felhasználási minták léteznek. **Alapszintű** kis termelési számítási feladatokhoz van: SLA-t, kínál dedikált erőforrások, a magas rendelkezésre állású, de a mérsékelt tárolási, 2 GB összesen feltöltve. A réteg lett fejthetők vissza az ügyfelek számára következetesen túlterhelt elérhető kapacitás a. A szélen végén **S3 HD** van munkaterhelések ISV-k, a jellemző partnerek, [több-bérlős megoldások](search-modeling-multitenant-saas-applications.md), vagy hívja a sok kisméretű indexek beállításra. Általában célszerű magától értetődő mikor **alapvető** vagy **S3 HD** érvényben lévő korlát miatt a megfelelő illeszkedő, de ha azt szeretné, hogy megerősítési könyvelés [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [lépjen kapcsolatba az Azure Támogatási](https://azure.microsoft.com/support/options/) kapcsolatos további iránymutatásért.

Fókusz túl a gyakrabban használt szabványos rétegek **S1-S3** egy előmenetel növekvő kapacitását, és nincsenek pontok partícióméret és maximális értékeket indexek, az indexelők és az corollary erőforrások levő szintjei vannak:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| A partícióméret|  25 GB | 100 GB | 250 GB |  |  |  |  |
| index és indexelő korlátok| 50 | 200 | 200 |  |  |  |  |

**S1** akkor közös választani, amikor több partíciót és a dedikált erőforrások áttelepítésére. Partíciókat 25 GB, legfeljebb 12 partíciók, az egyes-szolgáltatás a korlátot **S1** esetén 300 GB-os teljes méretűre partíciók replikák keresztül (lásd: [partíciókat és a replikák lefoglalni](search-capacity-planning.md#chart) a több elosztott terhelésű összetételű.)

Portál és az árazás lapok elhelyezése a fókusz partícióméret és a tárolási, de az egyes rétegekhez minden számítási képességeket (lemez kapacitását, sebességét, a CPU-k) ár lineárisan nő. Egy **S2** replikája gyorsabb, mint a **S1**, és **S3** gyorsabb, mint **S2**. **S3** rétegek törés az általában lineáris számítási árképzési mintát aránytalanul gyorsabb I/O. Ha a szűk i/o várhatóan egy **S3** lehetővé teszi az alacsonyabb rétegek mint sokkal magasabb iops-értéket.

**S3** és **S3 HD** biztonsági azonos nagy kapacitású infrastruktúra, de egyes egy eléri a maximális korlátját különböző módon. **S3** nagyon nagy indexek kevesebb célozza. Mint ilyen, a maximális korlátját az erőforrás-kötött (minden egyes szolgáltatás 2,4 TB). **S3 HD** nagyon kis indexek nagy számú célozza. A 1000 indexek **S3 HD** eléri az index megkötések formájában teljes kapacitásukkal működjenek. Ha Ön egy **S3 HD** ügyfél számára igényel 1000-nél több indexek, forduljon a Microsoft Support információt arról, hogyan.

> [!NOTE]
> Korábban a dokumentum korlátok szempont volt, de kiépítése után január 2018 legtöbb Azure Search szolgáltatás már nem alkalmazhatók. További információ a feltételek, amelyekre a dokumentum korlátok továbbra is érvényben maradnak: [szolgáltatási korlátait: dokumentum korlátok](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Értékelje ki a kapacitás

Kapacitás és a szolgáltatás futtatásával járó költségek nyissa meg kézzel az aktuális. Rétegek maximálásához két szintje (tárolás és erőforrások), használati korlátait, gondolja mindkét mivel bármelyik először elérni még a hatékony korlátot. 

Üzleti igények általában szüksége lesz indexek száma miatt. Például a dokumentumok nagy tárház egy globális index, vagy régió, alkalmazás vagy üzleti árkategóriájú alapján akár több index.

Határozza meg az index méretét, be kell [egy build](search-create-index-portal.md). Az adatok szerkezete, az Azure Search elsősorban van egy [index fordított](https://en.wikipedia.org/wiki/Inverted_index), mint forrásadatok más tulajdonságokkal rendelkező. Egy fordított index méretét és összetettségét határozza meg a tartalmat, nem feltétlenül bele adatcsatorna mennyisége. Egy magas változó tartalmat tartalmazó kisebb adatkészlet-nál kisebb index nagy redundanciával nagy adatforrás vezethet.  Mint ilyen akkor ritkán lehet célszámítógéppel index mérete az eredeti készlet mérete alapján.

### <a name="preliminary-estimates-using-the-free-tier"></a>Ingyenes szint használatával előzetes becslése

A kapacitása becsléséhez egy megoldás, kezdje a **szabad** réteg. Visszahívása, amely a **szabad** szolgáltatás nyújt 3 indexek, tárolási 50 MB, és az indexelési idő 2 perc. Ez kihívást jelenthet becslésére egy tervezett index mérete ezen korlátozásokkal rendelkező, de a következő példában látható módon egy módszert:

+ [Ingyenes szolgáltatás létrehozása](search-create-service-portal.md)
+ Készítse elő a kisméretű, reprezentatív adatkészletet (azt feltételezik, ötezer dokumentumok és kerülő tíz százalékát mintaméret)
+ [Kezdeti indexének](search-create-index-portal.md) meg és jegyezze fel a portálon annak méretét (feltételezik 30 MB)

Feltéve, a minta képviselője és a teljes adatforrás kerülő tíz százalékát, egy 30 MB index lesz kb. 300 MB Ha indexelt összes dokumentumot. Volt képes ez az előzetes érték felvértezve, akkor előfordulhat, hogy kettős terv két indexek (fejlesztési és éles), a tárolási követelmények 600 MB összesen mennyiség. Ez egyszerűen feltételfüggvényt a **alapvető** réteg, így nincs kezdenie.

### <a name="advanced-estimates-using-a-billable-tier"></a>Speciális becslése számlázható réteg használatával

Egyes ügyfelek előnyben részesítik a dedikált erőforrások, amely nagyobb mintavételi és feldolgozási időt, és azután kialakítani a fejlesztés során index mennyisége, a mérete, a lekérdezés kötetek reális becslése elindítása. Kezdetben szolgáltatás ki van építve, a legjobb-becslés becsült alapján, és ezután az alkalmazásfejlesztési projekt Miután kiforrottá válik, csapatok általában tudja, hogy a meglévő service felett vagy alatt kapacitás tervezett termelési számítási feladatokhoz. 

1. [Tekintse át az egyes rétegek, a szolgáltatásra vonatkozó korlátozások](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) annak meghatározásához, hogy alacsonyabb rétegek támogathat kell indexek mennyiségét. Között a **alapvető**-**S1**- **S2** rétegek, index korlátok 15-50-200, illetve.

1. [Hozzon létre egy szolgáltatás számlázható réteg](search-create-service-portal.md):

    + Kevés a Start **alapvető** vagy **S1** Ha Ön a tanulási görbére elején.
    + Indítsa el a magas **S2** vagy akár **S3**, ha a felügyeleti teendők központjaként indexelő és a lekérdezés terhelések magától értetődő.

1. [Kezdeti indexének](search-create-index-portal.md) annak meghatározásához, hogy hogyan forrásadatok az eszköz az index. Ez az index méretének becslése az egyetlen lehetőség.

1. [Figyelje a tárolási, a szolgáltatásra vonatkozó korlátozások, a lekérdezés kötet és a késleltetés](search-monitor-usage.md) a portálon. A portál elsajátíthatja, hogy lekérdezések száma második, a szabályozottan halmozott lekérdezések és a keresési késés; Ezek mindegyike segítségével eldöntheti, hogy ha vannak, a megfelelő rétegben. Portál metrikák vezérelt figyelését be tudja állítani a részletes, átkattintós elemzés, például engedélyezésével [keresési forgalom analytics](search-traffic-analytics.md). 

Index számát és méretét nincsenek egyaránt kapcsolódó elemzések végrehajtását, mert a legmagasabb eléri a teljes kihasználtság tárolás (partíciók) vagy felső határai erőforrások (indexek indexelők és így tovább), amelyik előbb következik be. A portál segítségével nyomon követheti, mindkettő megjelenítő aktuális használatát, és a legmagasabb párhuzamos Áttekintés lap.

> [!NOTE]
> Tárolási követelmények túlzott felfújt lehet, ha a dokumentumok oda nem tartozó adatokat tartalmaznak. Ideális esetben a dokumentumok csak a keresési élményt biztosít a szükséges adatokat tartalmazzák. Bináris adatok nem kereshető és kell tárolni külön-külön (lehet, hogy az Azure tábla vagy a blob storage) mezőt a index URL-cím hivatkozni kell a külső adatok tárolásához. Egy adott dokumentumot maximális mérete 16 MB (vagy ha kevesebb mint egy kérelem több dokumentum feltöltése tömeges). [Szolgáltatási korlátait, az Azure Search](search-limits-quotas-capacity.md) további részleteket tartalmaz.
>

**Lekérdezés kötet kapcsolatos szempontok**

Lekérdezések / másodperc (QPS) tulajdonságaival képezhet során teljesítményhangolás metrika, de általában nem réteg veszi figyelembe kivéve, ha nagyon nagy lekérdezés kötet kezdetben várt.

Replikák egyensúly kézbesíthet a szabványos réteg mindegyikét a partíciók esetében további replikák keresztül kiszolgálnia lekérdezés támogató feltöltését a terheléselosztás és a további partíciókat a párhuzamos feldolgozást. A teljesítmény hangolására a szolgáltatás kiépítése után.

Erős várt ügyfelünk kötetek kezdettől fogva figyelembe kell vennie a magasabb szinteken nagyobb teljesítményű hardver által támogatott lekérdezési megtartását. Majd partíciókat és a replikák offline állapotba, vagy egy alacsonyabb szint szolgáltatás, még akkor is vált, ha a lekérdezés köteteket materializálni sikertelen. Lekérdezés teljesítmény kiszámítására további információkért lásd: [Azure Search teljesítmény- és optimalizálási](search-performance-optimization.md).


**Szolgáltatásszint-szerződések**

A **szabad** szint és az előzetes funkciók nem rendelkeznek [szolgáltatási szintek (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Az összes számlázható rétegek SLA-k érvénybe, ha a szolgáltatás megfelelő redundancia kiépítése. Két vagy több replikák szükségesek (olvasás) lekérdezés SLA-t. Három vagy több replikák lekérdezés és az indexelés (írásra) SLA szükségesek. A partíciók számának nincs egy SLA-t szempont. 

## <a name="tips-for-tier-evaluation"></a>Tippek a réteg kiértékelése

+ Megtudhatja, hogyan hozhat létre hatékony indexek, és melyik frissítési módszereit, amelyek a legalacsonyabb impactful. Ajánlott [keresési forgalom analytics](search-traffic-analytics.md) a végértékeket szerzett lekérdezés tevékenység.

+ Metrikák körül lekérdezések összeállításához és használati minták (lekérdezések munkaidőben, amikor kevesen indexelő) körül adatgyűjtés engedélyezése, és ezen adatok segítségével kialakítási döntések jövőbeli szolgáltatás tájékoztatja. Amíg nem gyakorlati óránként vagy naponta szinten, partíciókat és a tervezett módosítások vannak a lekérdezést, vagy nem tervezett, de a tartós módosítások alkalmazásához, ha szintek tartsa hosszúságúra, hogy indokolják műveletek erőforrásokat dinamikusan módosíthatja.

+ Ne feledje, hogy a kiépítés csak hátránya, hogy előfordulhat, hogy rendelkezik szakadjon el le a szolgáltatást, ha a tényleges követelmények nagyobbak, mint hogy becsült. Szolgáltatás megszűnésének megelőzése érdekében ehhez hozzon létre egy új, magasabb szintű használható ugyanazt az előfizetést, és futtassa párhuzamos mindaddig, amíg az alkalmazások és a kérelmek az új végpont cél.

## <a name="next-steps"></a>További lépések

Indítsa el a **szabad** réteg és a kezdeti indexének használatával az adatok egy részét megértéséhez jellemzőit. Az adatok szerkezete, az Azure Search fordított index, ahol méret és fordított index összetettsége tartalom határozza meg. Ne feledje, hogy magas redundáns tartalom általában egy magas szabálytalan tartalom-nál kisebb index eredményez. Így is, amely meghatározza egy index tárhellyel kapcsolatos követelmények az adatkészlet méretének helyett tartalom jellemzőit.

Egy kezdeti meghatározni, hogy az index mérete, ha [számlázható szolgáltatás kiépítése](search-create-service-portal.md) egyik ebben a cikkben ismertetett vagy rétegek **alapvető** vagy egy **szabványos** réteg. Az adatok megfelelő részhalmazát bármely mesterséges megkötések enyhíteni és [az index újraépítése](search-howto-reindex.md) az összes kereshető kell ténylegesen kívánt adatokat.

[Partíciók és replikák lefoglalni](search-capacity-planning.md) szükség esetén a teljesítmény és méretezés van szüksége.

Ha a teljesítmény és kapacitás rendben, akkor elkészült. Ellenkező esetben hozza létre a keresési szolgáltatást, egy másik réteghez, amely több igényeinek leginkább igazodik.

> [!NOTE]
> További segítségre van szüksége a kérdésekre, továbbá hogy [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) vagy [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/).