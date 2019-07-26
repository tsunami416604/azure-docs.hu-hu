---
title: Központi telepítési stratégiák és ajánlott eljárások a teljesítmény optimalizálása érdekében – Azure Search
description: Ismerje meg az Azure Search teljesítményének finomhangolásához és az optimális méretezés konfigurálásához szükséges technikákat és ajánlott eljárásokat.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: a4578e26df5a6c29e80a0bbd2e0a30725e3733ee
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370641"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Központi telepítési stratégiák és ajánlott eljárások a teljesítmény optimalizálásához Azure Search

Ez a cikk az ajánlott eljárásokat mutatja be a méretezhetőség és a rendelkezésre állás kifinomult követelményeit biztosító speciális forgatókönyvek esetében. 

## <a name="develop-baseline-numbers"></a>Alapterv-számok fejlesztése
A keresési teljesítmény optimalizálásakor a lekérdezés végrehajtási idejének csökkentését kell összpontosítania. Ehhez ismernie kell, hogy egy tipikus lekérdezési terhelés hogyan néz ki. Az alábbi irányelvek segítséget nyújtanak az alapkonfiguráció-lekérdezési számok megérkezéséhez.

1. Válassza ki a cél késését (vagy a maximális időtartamot), amelyet egy tipikus keresési kérelem végrehajtásához el kell végezni.
2. Valós számítási feladatok létrehozása és tesztelése reális adatkészlettel a keresési szolgáltatáshoz a késési arányok méréséhez.
3. Kezdje a másodpercenkénti lekérdezésekkel (QPS), és fokozatosan növelje a tesztben végrehajtott számot, amíg a lekérdezés késése a megadott cél késés alá nem csökken. Ez egy fontos viszonyítási alap, amely segít a méretezés megtervezésében, mivel alkalmazása növekszik a használat során.
4. Ahol csak lehet, használja újra a HTTP-kapcsolatokat. Ha a Azure Search .NET SDK-t használja, ez azt jelenti, hogy fel kell használni egy példány-vagy [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) -példányt, és ha a REST API használja, akkor egyetlen HttpClient kell felhasználnia.
5. A lekérdezési kérések lényegének változása, hogy a Keresés az index különböző részein történjen. A változás azért fontos, mert ha folyamatosan hajtja végre ugyanazokat a keresési kéréseket, az adatgyorsítótárazás megkezdi, hogy a teljesítmény jobban nézzen ki, mint a több különböző lekérdezési készlet.
6. A lekérdezési kérelmek szerkezete változó, hogy különböző típusú lekérdezéseket kapjon. Nem minden keresési lekérdezés ugyanazon a szinten végezhető el. Például egy dokumentum keresési vagy keresési javaslata általában gyorsabb, mint egy jelentős számú dimenzióval és szűrőkkel rendelkező lekérdezés. A tesztelési összeállításnak különböző lekérdezéseket kell tartalmaznia, nagyjából azonos arányban, ahogy az éles környezetben várható.  

A tesztelési feladatok létrehozásakor a Azure Search néhány jellemzője van:

+ A szolgáltatás túlterhelést okoz, ha egyszerre több keresési lekérdezést küld. Ebben az esetben a HTTP 503-válasz kódokat fogja látni. Ha a tesztelés során a 503-es számú keresési kérést szeretné elkerülni, a különböző keresési kérelmekkel kapcsolatos különbségeket a késési arányok között tekintheti meg.

+ Azure Search nem futtat indexelési feladatokat a háttérben. Ha a szolgáltatás párhuzamosan kezeli a lekérdezési és indexelési feladatokat, ezt vegye figyelembe az indexelési feladatok bevezetésével a lekérdezési tesztekben, vagy az indexelési feladatok futtatásának időpontjában való futási lehetőségek feltárásával.

> [!NOTE]
> A [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) egy igazán jó módszer a teljesítményteszt-tesztek végrehajtásához, mivel lehetővé teszi a HTTP-kérések végrehajtását, ahogy a Azure Search és a kérelmek párhuzamos való végrehajtásához szükség lenne.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Nagy mennyiségű lekérdezési és szabályozott kérelmek skálázása
Ha túl sok szabályozott kérelmet kap, vagy túllépi a megnövelt lekérdezési késési arányt, a késleltetési arányt a következő két módszer egyikével lehet csökkenteni:

1. **Replikák bővítése:**  A replika olyan, mint az adatai másolata, amely lehetővé teszi, hogy a Azure Search a több példányra irányuló kérelmek terheléselosztását.  A rendszer a replikák közötti összes terheléselosztást és replikálást Azure Search felügyeli, és a szolgáltatáshoz lefoglalt replikák számát bármikor módosíthatja.  Egy standard keresési szolgáltatásban legfeljebb 12 replikát, egy alapszintű keresési szolgáltatásban pedig 3 replikát foglalhat le. A replikák a [Azure Portal](search-create-service-portal.md) vagy a [PowerShell](search-manage-powershell.md)használatával módosíthatók.
2. **Keresési szintek bővítése:**  Azure Search [számos rétegben](https://azure.microsoft.com/pricing/details/search/) érhető el, és ezek a szintek különböző szintű teljesítményt biztosítanak.  Bizonyos esetekben előfordulhat, hogy annyi lekérdezése van, hogy az Ön által használt szintje nem tud megfelelően alacsony késési sebességet biztosítani, még akkor is, ha a replikák maxed ki. Ebben az esetben érdemes fontolóra venni az egyik magasabb szintű keresési szintet, például a Azure Search S3 szintet, amely nagy mennyiségű dokumentummal és rendkívül nagy lekérdezési számítási feladatokkal rendelkező forgatókönyvekhez alkalmas.

## <a name="scaling-for-slow-individual-queries"></a>Méretezés lassú egyéni lekérdezésekhez
A nagy késési arányok egy másik oka, hogy egy lekérdezés túl sok időt vesz igénybe. Ebben az esetben a replikák hozzáadása nem fog segíteni. Két lehetséges lehetőség, amely segíthet a következőkben:

1. **Partíciók bővítése** A partíciók olyan mechanizmusok, amelyekkel az adatait további erőforrásokon keresztül feloszthatja. Egy második partíció hozzáadása két értékre osztja fel az adatfelosztást, egy harmadik partíció három, és így tovább osztja fel őket. Az egyik pozitív mellékhatás az, hogy a lassabb lekérdezések időnként gyorsabban futnak párhuzamos számítástechnika miatt. Megjegyezték, hogy az alacsony szelektivitású lekérdezések, például a sok dokumentumnak megfelelő lekérdezések, illetve a nagy számú dokumentumra vonatkozó párhuzamos. Mivel a dokumentumok relevanciájának kiszámításához, illetve a dokumentumok számának megszámlálásához jelentős számítási követelmény szükséges, a további partíciók hozzáadásával a lekérdezések gyorsabban elvégezhető.  
   
   A standard keresési szolgáltatásban legfeljebb 12 partíció lehet, az alapszintű keresési szolgáltatásban pedig 1 partíció található.  A partíciók a [Azure Portal](search-create-service-portal.md) vagy a [PowerShell](search-manage-powershell.md)használatával módosíthatók.

2. **Magas kardinális mezők korlátozása:** A magas kardinális mező egy olyan, jól látható vagy szűrhető mezőből áll, amely jelentős számú egyedi értékkel rendelkezik, és ennek eredményeképpen jelentős erőforrásokat használ a számítási eredmények alapján. Például ha a termék-azonosító vagy a Description mező úgy van beállítva, hogy a felszűrhető/szűrhető, a dokumentum a dokumentumból származó értékek nagy részét egyedinek számítja. Ahol csak lehetséges, korlátozza a magas kardinális mezők számát.

3. **Keresési szintek bővítése:**  A lassú lekérdezések teljesítményének növelése érdekében akár magasabb Azure Searchi szintet is megadhat. Minden magasabb szinten gyorsabb processzorok és több memória áll rendelkezésre, mindkettőnek pozitív hatása van a lekérdezési teljesítményre.

## <a name="scaling-for-availability"></a>Méretezés a rendelkezésre álláshoz
A replikák nem csupán csökkentik a lekérdezési késést, de lehetővé teszik a magas rendelkezésre állást is. Egyetlen replika esetén rendszeres állásidőt kell várnia, mert a kiszolgáló újraindul a szoftverfrissítések vagy egyéb karbantartási események után.  Ennek eredményeképpen fontos figyelembe venni, hogy az alkalmazás magas rendelkezésre állást igényel-e a keresésekhez (lekérdezésekhez) és írásokhoz (az indexelési eseményekhez). A Azure Search a következő attribútumokkal rendelkező összes fizetős keresési ajánlat SLA-beállításait kínálja:

* 2 replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)
* 3 vagy több replika az írási és olvasási feladatok (lekérdezések és indexelés) magas rendelkezésre állásához

További részletekért látogasson el a [Azure Search szolgáltatói szerződésre](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Mivel a replikák az adatai másolatai, több replikával Azure Search a számítógép újraindítását és karbantartását egy replikával, miközben lehetővé teszi a lekérdezés végrehajtásának folytatását más replikák esetében. Ezzel szemben, ha elvégzi a replikákat, a lekérdezési teljesítmény romlása merül fel, feltéve, hogy ezek a replikák egy kihasználatlan erőforrás.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Méretezés a földrajzilag elosztott számítási feladatokhoz és a Geo-redundancia
Földrajzilag elosztott számítási feladatokhoz a Azure Search adatközpont-üzemeltetőtől távol lévő felhasználóknak magasabb a késési aránya. Az egyik megoldás az, hogy több keresési szolgáltatást kell kiépíteni a régiókban, és ezek a felhasználók közelebb vannak egymáshoz. A Azure Search jelenleg nem biztosít automatizált módszert a régiók közötti Azure Search indexek földrajzi replikálására, de vannak olyan technikák, amelyek segítségével egyszerűen megvalósítható és kezelhető. Ezeket a következő néhány szakaszban ismertetjük.

A földrajzi eloszlású keresési szolgáltatások célja, hogy legalább két, két vagy több olyan régióban legyen elérhető index, amelyben a felhasználó a Azure Search szolgáltatáshoz van irányítva, amely a legkisebb késést biztosítja a jelen példában látható módon:

   ![Szolgáltatások közötti, régiónként][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Az adatszinkronizálás több Azure Search szolgáltatás között
Két lehetőség van az elosztott keresési szolgáltatások szinkronizálására, amely a [Azure Search indexelő](search-indexer-overview.md) vagy a leküldéses API (más néven a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)) használatát jelenti.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexelő használata több szolgáltatás tartalmának frissítéséhez

Ha már használja az indexelő az egyik szolgáltatáson, a második indexelő is konfigurálható egy másik szolgáltatáson, hogy ugyanazt az adatforrás-objektumot használja, az adatok ugyanabból a helyről való kihúzásával. Az egyes régiókban található szolgáltatások saját indexelő és egy célként megadott indextel rendelkeznek (a keresési index nincs megosztva, ami azt jelenti, hogy az adatok duplikálva vannak), de minden indexelő ugyanarra az adatforrásra hivatkozik.

Itt látható egy magas szintű vizualizáció, hogy az architektúra milyen módon fog kinézni.

   ![Egyetlen adatforrás elosztott indexelő és szolgáltatási kombinációkkal][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>A REST API-k használata a tartalmi frissítések több szolgáltatásban való leküldéséhez
Ha a Azure Search REST APIt használja a [Azure Search indexben lévő tartalom](https://docs.microsoft.com/rest/api/searchservice/update-index)leküldéséhez, a különböző keresési szolgáltatásokat szinkronizálhatja, ha az összes keresési szolgáltatás módosítását kéri, amikor frissítésre van szükség. Ügyeljen arra, hogy a kódban olyan eseteket kezeljen, amelyekben az egyik keresési szolgáltatás frissítése meghiúsul, de a többi keresési szolgáltatás sikeres.

## <a name="leverage-azure-traffic-manager"></a>Az Azure Traffic Manager kihasználása
Az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy több Azure Search-szolgáltatás által támogatott, több földrajzi helyen lévő webhelyre irányítsa át a kérelmeket. Az Traffic Manager az egyik előnye, hogy képes Azure Search a mintavételre, hogy elérhető legyen, és átirányítsa a felhasználókat az alternatív keresési szolgáltatásokra leállás esetén. Emellett, ha az Azure-webhelyeken keresztül irányítja a keresési kérelmeket, az Azure Traffic Manager lehetővé teszi a terheléselosztási esetek terhelését, ha a webhely nem Azure Search. Íme egy példa arra, hogy milyen architektúrát használ a Traffic Manager.

   ![Szolgáltatások – régiók közötti, központi Traffic Manager][3]

## <a name="monitor-performance"></a>Teljesítmény figyelése
Azure Search lehetővé teszi a szolgáltatás teljesítményének elemzését és figyelését a [Search Traffic Analytics](search-traffic-analytics.md)használatával. Ha engedélyezi ezt a funkciót, és felveszi a rendszerállapotot az ügyfélalkalmazás számára, megadhatja az egyes keresési műveleteket, valamint az összesített mérőszámokat egy olyan Azure Storage-fiókba, amely az elemzéshez és a Power BIban való megjelenítéshez használható fel. A metrikák ily módon rögzítik a teljesítménnyel kapcsolatos statisztikákat, például a lekérdezések átlagos számát vagy a lekérdezési válaszidőt. Emellett a művelet naplózása lehetővé teszi az adott keresési műveletek részleteinek részletezését.

A Traffic Analytics hasznos a késési arányok Azure Search perspektívából való megismeréséhez. Mivel a lekérdezési teljesítmény mérőszámai azon időpontokon alapulnak, amikor egy lekérdezés teljes feldolgozásra kerül Azure Searchban (a kérelem elküldésekor szükséges idő alapján), ezzel megállapíthatja, hogy a késéssel kapcsolatos problémák a Azure Search szolgáltatás oldaláról vagy kimenetéről származnak-e. a szolgáltatás ide, például a hálózati késéstől.  

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a díjszabási csomagokról és a szolgáltatásokra vonatkozó korlátozásokról, tekintse meg a [Azure Search szolgáltatási korlátozásait](search-limits-quotas-capacity.md).

A particionálási és a replika-kombinációkkal kapcsolatos további információkért látogasson el a [kapacitás](search-capacity-planning.md) megtervezésére.

Ha többet szeretne megtudni a teljesítményről és a jelen cikkben ismertetett optimalizációk megvalósításával kapcsolatos néhány bemutatóról, tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
