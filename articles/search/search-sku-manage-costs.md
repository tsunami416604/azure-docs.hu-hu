---
title: Kapacitás és költségek becslése
titleSuffix: Azure Cognitive Search
description: Tekintse át a kapacitás kiszámításának és a keresési szolgáltatások költségeinek, például az Azure infrastruktúrájának és eszközeinek, valamint az erőforrások hatékonyabb használatával kapcsolatos ajánlott eljárásokat ismertető útmutatót.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.openlocfilehash: d48ae71a979a2d0f1457b0cefa8a98a02710dd96
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577686"
---
# <a name="how-to-estimate-capacity-and-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search-szolgáltatás kapacitásának és költségeinek becslése

Az Azure Cognitive Search kapacitásának megtervezése részeként az alábbi útmutató segítségével csökkentheti a költségeket, vagy hatékonyabban kezelheti a költségeket:

+ Hozza létre az összes erőforrást ugyanabban a régióban, vagy a lehető legkevesebb régióban, hogy csökkentse vagy eltávolítsa a sávszélességgel kapcsolatos díjakat.

+ Összevonja az összes szolgáltatást egyetlen erőforráscsoport, például az Azure Cognitive Search, Cognitive Services és minden más, a megoldásban használt Azure-szolgáltatás között. A Azure Portal keresse meg az erőforráscsoportot, és használja a **Cost Management** -parancsokat, hogy betekintést kapjon a tényleges és a tervezett költségekbe.

+ Tekintse át az Azure-webalkalmazást az előtér-alkalmazáshoz, hogy a kérések és válaszok az adatközpont határain belül maradjanak.

+ Vertikális felskálázás az erőforrás-igényes műveletekhez, például az indexeléshez, majd a rendszeres lekérdezési feladatokhoz tartozó leállások újraigazítása. Az Azure Cognitive Search minimális konfigurációjának megkezdése (egy partícióból és egy replikából álló egy SU), majd a felhasználói tevékenység figyelése olyan használati minták azonosításához, amelyek nagyobb kapacitást jelezhetnek. Ha kiszámítható minta van, lehetséges, hogy szinkronizálni tudja a méretezést a tevékenységgel (ezt a kódot kell írnia az automatizáláshoz).

A számlázható események, a számlázási képletek és a számlázható díjak az [árképzési szintek kiválasztása](search-sku-tier.md)című részben találhatók. Emellett megtekintheti a [számlázással és a költséghatékonysággal](../cost-management-billing/cost-management-billing-overview.md) kapcsolatos beépített eszközöket és szolgáltatásokat.

A keresési szolgáltatás ideiglenes leállítása nem lehetséges. A dedikált erőforrások mindig működőképesek, és a szolgáltatás élettartama szempontjából kizárólagos használatra vannak kiosztva. A szolgáltatás törlése végleges, és a hozzá tartozó adatai is törlődnek.

A szolgáltatás szempontjából a számla csökkentése egyetlen módja annak, hogy csökkentse a replikákat és a partíciókat olyan szintre, amely továbbra is elfogadható teljesítményt és [SLA-megfelelőséget](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biztosít, vagy egy alacsonyabb szintű szolgáltatást hoz létre (az S1 óradíjak alacsonyabbak, mint az S2 vagy az S3). Feltételezve, hogy kiépíti a szolgáltatást a betöltési kivetítések alsó végére, ha kibővíti a szolgáltatást, létrehozhat egy második nagyobb rétegű szolgáltatást, újraépítheti az indexeket a második szolgáltatásban, majd törölheti az elsőt.

## <a name="how-to-evaluate-capacity-requirements"></a>A kapacitásra vonatkozó követelmények kiértékelése

Az Azure Cognitive Search a kapacitást *replikák* és *partíciók* strukturálják.

+ A replikák a keresési szolgáltatás példányai. Mindegyik replika egyetlen elosztott terhelésű másolatot üzemeltet az indexből. Egy hat replikával rendelkező szolgáltatás például hat másolatot tartalmaz a szolgáltatásba betöltött összes indexről.

+ A partíciók az indexeket és a kereshető adattárolók automatikus felosztását végzik. Az indexet két partíció osztja szét, három partíciót pedig a harmadik felé, és így tovább. A kapacitás szempontjából a *partíció mérete* az elsődleges megkülönböztető funkció a rétegek között.

> [!NOTE]
> Minden standard és Storage optimalizált csomag támogatja a [replikák és partíciók rugalmas kombinációját](search-capacity-planning.md#chart) , így [optimalizálhatja a rendszer sebességét vagy tárhelyét](search-performance-optimization.md) az egyensúly megváltoztatásával. Az alapszintű csomag legfeljebb három replikát biztosít a magas rendelkezésre álláshoz, de csak egy partícióval rendelkezik. Az ingyenes szintek nem biztosítanak dedikált erőforrásokat: a számítási erőforrásokat több előfizető is megosztja.

### <a name="evaluating-capacity"></a>Kapacitás kiértékelése

A szolgáltatás üzemeltetésének kapacitása és költségei. A szintek két szintre korlátozzák a korlátozásokat: tárolás és tartalom (például indexek száma). Érdemes meggondolni, mivel a határértékek közül az első a hatályos korlát.

Az üzleti követelmények általában a szükséges indexek számát írják elő. Előfordulhat például, hogy globális indexre van szüksége a dokumentumok nagyméretű tárházához. Vagy előfordulhat, hogy több indexre van szüksége a régió, az alkalmazás vagy az üzleti Niche alapján.

Az indexek méretének meghatározásához [létre kell egyet](search-what-is-an-index.md). A mérete az importált és az index konfiguráción alapul, például az, hogy engedélyezi-e a javaslatokat, a szűrést és a rendezést.

A teljes szöveges kereséshez az elsődleges adatstruktúra egy [fordított index](https://en.wikipedia.org/wiki/Inverted_index) -struktúra, amely különböző tulajdonságokkal rendelkezik, mint a forrásadatok. A fordított indexek esetében a méretet és az összetettséget a tartalom határozza meg, nem feltétlenül a betáplált adatmennyiség. Egy nagy redundanciával rendelkező nagyméretű adatforrás kisebb indexet eredményezhet, mint egy nagy mértékben változó tartalmat tartalmazó kisebb adathalmaz. Így ritkán lehet az index méretét az eredeti adatkészlet méretétől függően kikövetkeztetni.

> [!NOTE] 
> Annak ellenére, hogy az indexek és a tárolás jövőbeli igényeit is megbecsülik, érdemes meggondolni. Ha a rétegek kapacitása túl alacsonyra vált, egy új szolgáltatást kell kiépíteni egy magasabb szintű szinten, majd [újra kell töltenie az indexeket](search-howto-reindex.md). A szolgáltatás egyik rétegből a másikba való helyben történő frissítése nem történik meg.
>

## <a name="estimate-with-the-free-tier"></a>Becslés az ingyenes szintjével

A kapacitás becslésének egyik megközelítése az ingyenes szinten való kezdés. Ne feledje, hogy az ingyenes szolgáltatás legfeljebb három indexet kínál, 50 MB tárterületet és 2 perc indexelési időt. A megszorítások alapján megbecsülheti a tervezett indexek méretét, de ezek a lépések a következők:

+ [Hozzon létre egy ingyenes szolgáltatást](search-create-service-portal.md).
+ Készítsen elő egy kis, reprezentatív adatkészletet.
+ [Hozzon létre egy kezdeti indexet a portálon](search-get-started-portal.md) , és jegyezze fel a méretét. A funkciók és attribútumok hatással vannak a tárterületre. Például a javaslatok hozzáadásával (keresési típusú lekérdezések) növelheti a tárolási követelményeket. Ha ugyanazt az adatkészletet használja, előfordulhat, hogy egy index több verzióját is létrehozhatja az egyes mezők különböző attribútumaival, hogy megtekintse, hogyan változnak a tárolási követelmények. További információkért lásd: [alapszintű index létrehozása "tárolási vonzatok"](search-what-is-an-index.md#index-size).

A durva becsléssel megduplázhatja ezt a költségvetést két index (fejlesztés és éles környezet) esetében, majd a szintet ennek megfelelően kiválaszthatja.

## <a name="estimate-with-a-billable-tier"></a>Becslés számlázható szintjével

A dedikált erőforrások nagyobb mintavételezési és feldolgozási időt biztosítanak a fejlesztés során az index mennyiségének, méretének és lekérdezési köteteinek reális becsléséhez. Néhány ügyfél azonnal egy számlázható réteggel ugrik, majd újraértékeli a fejlesztési projekt futamideje alatt.

1. [Tekintse át a szolgáltatási korlátokat az egyes szinteknél](./search-limits-quotas-capacity.md#index-limits) annak megállapításához, hogy az alsóbb szintű csomagok támogatni tudják-e a szükséges indexek számát. Az alapszintű, S1 és S2 szinten az index korlátai 15, 50 és 200. A tárolási optimalizált csomagok korlátja 10 index, mert a rendszer úgy van kialakítva, hogy támogassa a nagyon nagy méretű indexek alacsony számát.

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

**Szolgáltatásiszint-szerződések**

Az ingyenes szint és az előzetes verzió funkciói nem biztosítanak [szolgáltatói szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Minden számlázható szinten a SLA-kat akkor kell végrehajtani, ha elegendő redundancia van kiépítve a szolgáltatáshoz. Legalább két replikát kell megadnia a Query (olvasási) SLA-hoz. Három vagy több replikával kell rendelkeznie a lekérdezéshez és az indexeléshez (írható-olvasható) SLA-hoz. A partíciók száma nem befolyásolja a SLA-kat.

## <a name="tips-for-tier-evaluation"></a>Tippek a rétegek kiértékeléséhez

+ Lekérdezések használatának engedélyezése a mérőszámok számára, valamint adatok gyűjtése használati minták köré (a lekérdezések munkaidőn kívüli, az indexelés a munkaidőn kívüli órákban). Ezekkel az információkkal tájékoztathatja a szolgáltatás kiépítési döntéseit. Bár az óránkénti vagy napi lépésszám nem praktikus, dinamikusan módosíthatja a partíciókat és az erőforrásokat a lekérdezési kötetek tervezett változásainak megfelelően. A nem tervezett, de tartós módosításokat is elvégezheti, ha a szintek elég sokáig tartanak a beavatkozáshoz.

+ Ne feledje, hogy a kiépítés alatti egyetlen hátránya, hogy előfordulhat, hogy le kell bontania egy szolgáltatást, ha a tényleges követelmények nagyobbak az előrejelzéseknél. A szolgáltatás megszakadásának elkerülése érdekében hozzon létre egy új szolgáltatást egy magasabb szintű szinten, és futtassa azt egymás mellett, amíg az összes alkalmazás és kérelem meg nem célozza az új végpontot.

## <a name="next-steps"></a>További lépések

Kezdje egy ingyenes csomaggal, és hozzon létre egy kezdeti indexet az adatok egy részhalmazának használatával, hogy megértse a jellemzőit. Az Azure Cognitive Search adatstruktúrája fordított index struktúra. A fordított index méretét és összetettségét a tartalom határozza meg. Ne feledje, hogy a nagyon redundáns tartalom általában kisebb indexet eredményez, mint a nagymértékben szabálytalan tartalom. Így az adatkészlet mérete helyett a tartalom jellemzői határozzák meg az index tárolási követelményeit.

Az index méretének kezdeti becslése után hozzon létre [egy számlázható szolgáltatást](search-create-service-portal.md) a cikkben ismertetett szinteken: alapszintű, standard vagy Storage – optimalizált. Lazítson az adatméretezés során felmerülő bármilyen mesterséges korlátozással, és [építse újra az indexet](search-howto-reindex.md) , hogy tartalmazza a kereshető összes adathalmazt.

A szükséges teljesítmény és skálázás érdekében szükség szerint ossza ki a [partíciókat és a replikákat](search-capacity-planning.md) .

Ha a teljesítmény és a kapacitás rendben van, elkészült. Ellenkező esetben hozzon létre újra egy keresési szolgáltatást egy másik szinten, amely szorosabban igazodik az igényeihez.

> [!NOTE]
> Ha kérdése van, tegye közzé a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) , vagy [forduljon az Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/).