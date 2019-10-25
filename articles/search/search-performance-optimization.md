---
title: Központi telepítési stratégiák és ajánlott eljárások a teljesítmény optimalizálása érdekében
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Azure Cognitive Search teljesítményének finomhangolásához és az optimális méretezés konfigurálásához szükséges technikákat és ajánlott eljárásokat.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 15557a437732ee15c3c6dada7b2d9fe1d397dc5a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793423"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-cognitive-search"></a>Üzembe helyezési stratégiák és ajánlott eljárások az Azure-Cognitive Search teljesítményének optimalizálásához

Ez a cikk az ajánlott eljárásokat mutatja be a méretezhetőség és a rendelkezésre állás kifinomult követelményeit biztosító speciális forgatókönyvek esetében. 

## <a name="develop-baseline-numbers"></a>Alapterv-számok fejlesztése
A keresési teljesítmény optimalizálásakor a lekérdezés végrehajtási idejének csökkentését kell összpontosítania. Ehhez ismernie kell, hogy egy tipikus lekérdezési terhelés hogyan néz ki. Az alábbi irányelvek segítséget nyújtanak az alapkonfiguráció-lekérdezési számok megérkezéséhez.

1. Válassza ki a cél késését (vagy a maximális időtartamot), amelyet egy tipikus keresési kérelem végrehajtásához el kell végezni.
2. Valós számítási feladatok létrehozása és tesztelése reális adatkészlettel a keresési szolgáltatáshoz a késési arányok méréséhez.
3. Kezdje a másodpercenkénti lekérdezésekkel (QPS), és fokozatosan növelje a tesztben végrehajtott számot, amíg a lekérdezés késése a megadott cél késés alá nem csökken. Ez egy fontos viszonyítási alap, amely segít a méretezés megtervezésében, mivel alkalmazása növekszik a használat során.
4. Ahol csak lehet, használja újra a HTTP-kapcsolatokat. Ha az Azure Cognitive Search .NET SDK-t használja, ez azt jelenti, hogy fel kell használni egy példány-vagy [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) -példányt, és ha a REST API használja, akkor egyetlen HttpClient kell felhasználnia.
5. A lekérdezési kérések lényegének változása, hogy a Keresés az index különböző részein történjen. A változás azért fontos, mert ha folyamatosan hajtja végre ugyanazokat a keresési kéréseket, az adatgyorsítótárazás megkezdi, hogy a teljesítmény jobban nézzen ki, mint a több különböző lekérdezési készlet.
6. A lekérdezési kérelmek szerkezete változó, hogy különböző típusú lekérdezéseket kapjon. Nem minden keresési lekérdezés ugyanazon a szinten végezhető el. Például egy dokumentum keresési vagy keresési javaslata általában gyorsabb, mint egy jelentős számú dimenzióval és szűrőkkel rendelkező lekérdezés. A tesztelési összeállításnak különböző lekérdezéseket kell tartalmaznia, nagyjából azonos arányban, ahogy az éles környezetben várható.  

A tesztelési feladatok létrehozásakor az Azure Cognitive Search néhány jellemzője van:

+ A szolgáltatás túlterhelést okoz, ha egyszerre több keresési lekérdezést küld. Ebben az esetben a HTTP 503-válasz kódokat fogja látni. Ha a tesztelés során a 503-es számú keresési kérést szeretné elkerülni, a különböző keresési kérelmekkel kapcsolatos különbségeket a késési arányok között tekintheti meg.

+ Az Azure Cognitive Search nem futtat indexelési feladatokat a háttérben. Ha a szolgáltatás párhuzamosan kezeli a lekérdezési és indexelési feladatokat, ezt vegye figyelembe az indexelési feladatok bevezetésével a lekérdezési tesztekben, vagy az indexelési feladatok futtatásának időpontjában való futási lehetőségek feltárásával.

> [!NOTE]
> A [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) egy igazán jó módszer a teljesítményteszt-tesztek végrehajtásához, mivel lehetővé teszi a HTTP-kérések végrehajtását, mivel az Azure-Cognitive Searchon végzett lekérdezések végrehajtásához szükséges, és lehetővé teszi a párhuzamos.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Nagy mennyiségű lekérdezési és szabályozott kérelmek skálázása
Ha túl sok szabályozott kérelmet kap, vagy túllépi a megnövelt lekérdezési késési arányt, a késleltetési arányt a következő két módszer egyikével lehet csökkenteni:

1. **Replikák bővítése:**  A replika olyan, mint az adatai másolata, amely lehetővé teszi, hogy az Azure Cognitive Search a több példányra irányuló kérések terheléselosztását.  Az Azure Cognitive Search felügyeli az összes replika terheléselosztását és replikálását, és a szolgáltatáshoz lefoglalt replikák számát bármikor módosíthatja.  Egy standard keresési szolgáltatásban legfeljebb 12 replikát, egy alapszintű keresési szolgáltatásban pedig 3 replikát foglalhat le. A replikák a [Azure Portal](search-create-service-portal.md) vagy a [PowerShell](search-manage-powershell.md)használatával módosíthatók.
2. **Keresési szintek bővítése:**  Az Azure Cognitive Search [számos rétegben](https://azure.microsoft.com/pricing/details/search/) érhető el, és ezek a szintek különböző szintű teljesítményt biztosítanak.  Bizonyos esetekben előfordulhat, hogy annyi lekérdezése van, hogy az Ön által használt szintje nem tud megfelelően alacsony késési sebességet biztosítani, még akkor is, ha a replikák maxed ki. Ebben az esetben érdemes lehet olyan magasabb keresési szintek egyikét használni, mint például az Azure Cognitive Search S3-réteg, amely nagy mennyiségű dokumentummal és rendkívül nagy lekérdezési számítási feladatokkal rendelkező forgatókönyvekhez alkalmas.

## <a name="scaling-for-slow-individual-queries"></a>Méretezés lassú egyéni lekérdezésekhez
A nagy késési arányok egy másik oka, hogy egy lekérdezés túl sok időt vesz igénybe. Ebben az esetben a replikák hozzáadása nem fog segíteni. Két lehetséges lehetőség, amely segíthet a következőkben:

1. **Partíciók bővítése** A partíciók olyan mechanizmusok, amelyekkel az adatait további erőforrásokon keresztül feloszthatja. Egy második partíció hozzáadása két értékre osztja fel az adatfelosztást, egy harmadik partíció három, és így tovább osztja fel őket. Az egyik pozitív mellékhatás az, hogy a lassabb lekérdezések időnként gyorsabban futnak párhuzamos számítástechnika miatt. Megjegyezték, hogy az alacsony szelektivitású lekérdezések, például a sok dokumentumnak megfelelő lekérdezések, illetve a nagy számú dokumentumra vonatkozó párhuzamos. Mivel a dokumentumok relevanciájának kiszámításához, illetve a dokumentumok számának megszámlálásához jelentős számítási követelmény szükséges, a további partíciók hozzáadásával a lekérdezések gyorsabban elvégezhető.  
   
   A standard keresési szolgáltatásban legfeljebb 12 partíció lehet, az alapszintű keresési szolgáltatásban pedig 1 partíció található.  A partíciók a [Azure Portal](search-create-service-portal.md) vagy a [PowerShell](search-manage-powershell.md)használatával módosíthatók.

2. **Magas kardinális mezők korlátozása:** A magas kardinális mező egy olyan, jól látható vagy szűrhető mezőből áll, amely jelentős számú egyedi értékkel rendelkezik, és ennek eredményeképpen jelentős erőforrásokat használ a számítási eredmények alapján. Például ha a termék-azonosító vagy a Description mező úgy van beállítva, hogy a felszűrhető/szűrhető, a dokumentum a dokumentumból származó értékek nagy részét egyedinek számítja. Ahol csak lehetséges, korlátozza a magas kardinális mezők számát.

3. **Keresési szintek bővítése:**  Akár egy magasabb szintű Azure Cognitive Search-szintet is használhat, így javíthatja a lassú lekérdezések teljesítményét. Minden magasabb szinten gyorsabb processzorok és több memória áll rendelkezésre, mindkettőnek pozitív hatása van a lekérdezési teljesítményre.

## <a name="scaling-for-availability"></a>Méretezés a rendelkezésre álláshoz
A replikák nem csupán csökkentik a lekérdezési késést, de lehetővé teszik a magas rendelkezésre állást is. Egyetlen replika esetén rendszeres állásidőt kell várnia, mert a kiszolgáló újraindul a szoftverfrissítések vagy egyéb karbantartási események után.  Ennek eredményeképpen fontos figyelembe venni, hogy az alkalmazás magas rendelkezésre állást igényel-e a keresésekhez (lekérdezésekhez) és írásokhoz (az indexelési eseményekhez). Az Azure Cognitive Search SLA-beállításokat biztosít az összes fizetős keresési ajánlathoz a következő attribútumokkal:

* 2 replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)
* 3 vagy több replika az írási és olvasási feladatok (lekérdezések és indexelés) magas rendelkezésre állásához

További részletekért látogasson el az [Azure Cognitive Search szolgáltatói szerződésra](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Mivel a replikák több replikával rendelkeznek, az Azure Cognitive Search a számítógép újraindítását és karbantartását is lehetővé teszi egy replikán, miközben lehetővé teszi a lekérdezés-végrehajtást más replikák folytatásához. Ezzel szemben, ha elvégzi a replikákat, a lekérdezési teljesítmény romlása merül fel, feltéve, hogy ezek a replikák egy kihasználatlan erőforrás.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Méretezés a földrajzilag elosztott számítási feladatokhoz és a Geo-redundancia
Földrajzilag elosztott számítási feladatokhoz az Azure Cognitive Search üzemeltető adatközponttól távol lévő felhasználóknak magasabb a késési aránya. Az egyik megoldás az, hogy több keresési szolgáltatást kell kiépíteni a régiókban, és ezek a felhasználók közelebb vannak egymáshoz. Az Azure Cognitive Search jelenleg nem biztosít automatizált módszert az Azure Cognitive Search indexek földrajzi replikálására a régiók között, de vannak olyan technikák is, amelyek a folyamat egyszerű megvalósításához és kezeléséhez használhatók. Ezeket a következő néhány szakaszban ismertetjük.

A földrajzi eloszlású keresési szolgáltatások célja, hogy legalább két, két vagy több olyan régióban legyen elérhető index, amelyben a felhasználó az Azure Cognitive Search szolgáltatáshoz van irányítva, amely a legkisebb késést biztosítja az alábbi példában látható módon:

   ![Szolgáltatások közötti, régiónként][1]

### <a name="keeping-data-in-sync-across-multiple-azure-cognitive-search-services"></a>Az adatszinkronizálás több Azure Cognitive Search-szolgáltatás között
Az elosztott keresési szolgáltatások szinkronizálásának két lehetősége van, amelyek az [azure Cognitive Search indexelő](search-indexer-overview.md) vagy a leküldéses API (más néven [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)) használatával állnak.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexelő használata több szolgáltatás tartalmának frissítéséhez

Ha már használja az indexelő az egyik szolgáltatáson, a második indexelő is konfigurálható egy másik szolgáltatáson, hogy ugyanazt az adatforrás-objektumot használja, az adatok ugyanabból a helyről való kihúzásával. Az egyes régiókban található szolgáltatások saját indexelő és egy célként megadott indextel rendelkeznek (a keresési index nincs megosztva, ami azt jelenti, hogy az adatok duplikálva vannak), de minden indexelő ugyanarra az adatforrásra hivatkozik.

Itt látható egy magas szintű vizualizáció, hogy az architektúra milyen módon fog kinézni.

   ![Egyetlen adatforrás elosztott indexelő és szolgáltatási kombinációkkal][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>A REST API-k használata a tartalmi frissítések több szolgáltatásban való leküldéséhez
Ha az Azure Cognitive Search REST APIt használja az [azure Cognitive Search indexben lévő tartalmak leküldéséhez](https://docs.microsoft.com/rest/api/searchservice/update-index), a különböző keresési szolgáltatásokat szinkronizálhatja, ha az összes keresési szolgáltatás módosítását kéri, amikor frissítésre van szükség. Ügyeljen arra, hogy a kódban olyan eseteket kezeljen, amelyekben az egyik keresési szolgáltatás frissítése meghiúsul, de a többi keresési szolgáltatás sikeres.

## <a name="leverage-azure-traffic-manager"></a>Az Azure Traffic Manager kihasználása
Az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy a kérelmeket több, több keresési szolgáltatás által támogatott földrajzi helyen lévő webhelyre irányítsa. Az Traffic Manager egyik előnye, hogy az Azure-Cognitive Search mintavétele lehetővé teszi, hogy elérhető legyen, és a felhasználók átirányítása alternatív keresési szolgáltatásokra leállás esetén. Emellett, ha az Azure-webhelyeken keresztül irányítja a keresési kérelmeket, az Azure Traffic Manager lehetővé teszi a terheléselosztási esetek terhelését, ha a webhely fel van töltve, de nem az Azure Cognitive Search. Íme egy példa arra, hogy milyen architektúrát használ a Traffic Manager.

   ![Szolgáltatások – régiók közötti, központi Traffic Manager][3]

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a díjszabási csomagokról és a szolgáltatásokra vonatkozó korlátozásokról, tekintse meg a [szolgáltatási korlátok az Azure Cognitive Search-ban](search-limits-quotas-capacity.md)című témakört.

A particionálási és a replika-kombinációkkal kapcsolatos további információkért látogasson el a [kapacitás megtervezésére](search-capacity-planning.md) .

Ha többet szeretne megtudni a teljesítményről és a jelen cikkben ismertetett optimalizációk megvalósításával kapcsolatos néhány bemutatóról, tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
