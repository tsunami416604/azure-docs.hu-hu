---
title: Üzembe helyezés stratégiákat és ajánlott eljárások a teljesítmény – Azure Search optimalizálása
description: Ismerje meg a technikák és ajánlott eljárások az Azure Search teljesítményének hangolása és optimális méretezési csoport konfigurálása.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404467"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Üzembe helyezés stratégiákat és ajánlott eljárások az Azure Search teljesítményének optimalizálása

Ez a cikk ismerteti a skálázhatóság és rendelkezésre állás kifinomult követelményei speciális forgatókönyvek esetén ajánlott eljárásokat. 

## <a name="develop-baseline-numbers"></a>Alapkonfiguráció számok fejlesztése
A keresés teljesítmény optimalizálása, kell összpontosítania lekérdezés végrehajtási idő csökkentése. Ehhez egy tipikus lekérdezési terhelése néz ismernie kell. Az alábbi irányelvek segítségével eredeti lekérdezés számok érkeznek.

1. Válasszon egy cél késés (vagy maximális időtartama), amely egy tipikus keresési kérelem végrehajtásához vesz igénybe.
2. Hozzon létre és tesztelheti a számítási feladatok valódi ellen a keresési szolgáltatás egy realisztikus adatkészlet késés díjszabása méréséhez.
3. Indítsa el a lekérdezések másodpercenkénti (lekérdezési QPS) száma kevés, és a fokozatosan növelje a számot, a teszt végrehajtása, csak a meghatározott célhoz késés alá csökken a Lekérdezések késése. Ez a fontos számításiteljesítmény-mérési megtervezéséhez a méretezhető az alkalmazás növekedésével a használat.
4. Amikor csak lehetséges, újból felhasználhatja a HTTP-kapcsolatoknál. Ha az Azure Search .NET SDK használ, ez azt jelenti, egy példány szabad újra, vagy [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) példány, és a REST API-t használja, ha egyetlen HttpClient kell újra felhasználhatja.
5. Lekérdezési kérelmek az anyag eltérőek, ezért az index különböző részeinek keresztül történik, hogy a keresés. Változat fontos, mert ha folyamatosan hajtsa végre az azonos keresési kéréseket, az adatok gyorsítótárazása indul el, hogy a hely jobb, mint, több különböző lekérdezéssel be teljesítmény.
6. Eltérő lehet a lekérdezésekre vonatkozó kérelmek szerkezete, annak érdekében, hogy különböző típusú lekérdezések. Nem minden keresési lekérdezést hajt végre ugyanazon a szinten. Például egy dokumentum lookup vagy keresési javaslat, általában gyorsabb, mint egy lekérdezés jelentős számú értékkorlátozással és szűrőket. Teszt összeállítás ki kell terjednie több olyan lekérdezést, nagyjából azonos arány, éles környezetben alakul.  

Ezek létrehozása során tesztelheti a számítási feladatokat, vannak bizonyos figyelembe kell venni az Azure Search jellemzői:

+ Lehet túlterhelni a szolgáltatás túl sok keresési lekérdezések egy időben küldésével. Ha ez történik, látni fogja a HTTP 503-as válaszkódot. Egy 503-as elkerülheti a tesztelés során, kezdje meg a keresési kérelmek megtekintéséhez késés árfolyamok különbségeket, hozzáadhat további keresési kérelmek számának különféle tartományok.

+ Az Azure Search nem futtatható az indexelési feladatok a háttérben. Ha a szolgáltatás elvégzi a lekérdezés és a számítási feladatok indexeléséhez egyidejűleg, vegye figyelembe ezt vagy bemutatása indexelési feladatokat, a lekérdezés teszteket, vagy a csúcsidőn kívüli során az indexelési feladatok futó opciók megismerését.

> [!NOTE]
> [A Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) a teljesítményteszt végrehajtásához nagyon jó lehetőséget teszteli, mert Ön a HTTP-kérések végrehajtásához, az Azure Search-lekérdezéseket végrehajtó esetén lenne szükség, és lehetővé teszi a párhuzamos feldolgozás kérelmek.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>A lekérdezési kötet méretezése és szabályozott kérelmeinek száma
Amikor azért küldtük Önnek, túl sok szabályozott kérelmeinek száma, illetve a cél késés tarifa szerint egy nagyobb lekérdezési terhelése meghaladja, megtekintheti a két módszer egyikével mértékű késés csökkentése:

1. **Növelje a replikák:**  A replika olyan, mint az adatokat, így eloszthatja a kérelmeket a több példányban ellen az Azure Search egy példányát.  Az összes terheléselosztási funkciók és az Azure Search által kezelt replikák közötti adatreplikáció, és megváltoztathatja a hozzárendelt a szolgáltatáshoz bármikor replikák száma.  A szabványos keresőszolgáltatásokba 12 replikákat és a egy egyszerű keresés szolgáltatásban 3 replika legfeljebb foglalhat le. Replikák lehet beállítani az a [az Azure portal](search-create-service-portal.md) vagy [PowerShell](search-manage-powershell.md).
2. **Növelje a keresés szint:**  Az Azure Search érhető el egy [rétegek száma](https://azure.microsoft.com/pricing/details/search/) és az egyes szintek különböző teljesítményszintet is garantálja.  Bizonyos esetekben előfordulhat, hogy az a szint nem adhatók meg a kellően közel valós idejű díjait számítjuk fel akkor is, ha a replikák vannak maximumot sok lekérdezéseket. Ebben az esetben érdemes figyelembe venni, kihasználva az Azure Search S3 szint, amely jól használható a dokumentumokat és a rendkívül nagy lekérdezési számítási feladatok nagy számú forgatókönyvek például a magasabb szintű keresési csomagok valamelyikére.

## <a name="scaling-for-slow-individual-queries"></a>Lassú az egyes lekérdezések méretezése
Nagy késésű díjszabás egy másik oka egy egyetlen lekérdezés túl sokáig tart. Replikák hozzáadásával ebben az esetben nem segít. Két lehetőség lehetőségeket, melyek segíthetnek a következők:

1. **Növelje a partíciók** partíció egy mechanizmust vágását meghatározó az adatok között további erőforrásokat. A második partíció hozzáadása felosztja a adatok két, a harmadik partíció osztja fel a három, és így tovább. Egy pozitív mellékhatása, hogy lassabb lekérdezések néha gyorsabban párhuzamos számítások miatt. Hogy észrevették ezerszer alacsony szelektivitás lekérdezéseknél, például a lekérdezések, amelyek megfelelnek a sok dokumentumok vagy értékkorlátozással counts biztosít a nagy számú dokumentumot keresztül. Mivel jelentős számítási szükséges a dokumentumok alkalmazhatóságát pontszám, vagy a dokumentumok, további partíciók hozzáadásával számok száma segít a lekérdezések gyorsabb befejezéséhez.  
   
   A szabványos keresőszolgáltatásokba 12 partícióra maximum és az egyszerű keresés szolgáltatás 1 partíció lehet.  A partíciók lehet beállítani az a [az Azure portal](search-create-service-portal.md) vagy [PowerShell](search-manage-powershell.md).

2. **Korlát nagy Számosságú mezők:** Egy nagy számosságú mező áll egy kategorizálható vagy szűrhető mező, amely jelentős mennyiségű egyedi értékkel rendelkezik, és ennek eredményeképpen eredmények kiszámításakor jelentős erőforrásokat használ fel. Például termék azonosító vagy leírás mező szűrhető és kategorizálható beállítása számolja nagy számosságú, mivel a legtöbb dokumentumot a dokumentumból értékek egyediek. Amikor csak lehetséges, korlátozza a nagy számosságú mezők számát.

3. **Növelje a keresés szint:**  Akár áthelyezése Azure Search a magasabb szintű lehet egy másik módja a lassú lekérdezések teljesítményének javítása érdekében. Minden egyes magasabb szintre biztosít gyorsabb processzorokat és memóriát és, amelyek pozitív hatással a lekérdezési teljesítmény.

## <a name="scaling-for-availability"></a>Méretezés a rendelkezésre állás érdekében
Replikák nem csak lekérdezési késés csökkentése érdekében, de lehetővé teszi a magas rendelkezésre állás érdekében. Egyetlen replikával számíthat rendszeres leállás miatt a kiszolgáló újraindítása után a szoftverfrissítések vagy más, amelyet a rendszer karbantartási események esetében.  Ennek eredményeképpen fontos figyelembe venni, ha az alkalmazás magas rendelkezésre állású kereséseinek (lekérdezések) igényel, valamint írási műveletek (indexelési események). Az Azure Search a következő attribútumokkal keresési fizetős ajánlatok a különféle SLA-lehetőségeket kínálja:

* a magas rendelkezésre állás csak olvasható munkaterhelések (lekérdezések) 2 replika
* a magas rendelkezésre állású olvasási és írási számítási feladatok (lekérdezések és az indexelés) 3 vagy több replikával

Ezzel kapcsolatban további információért látogasson el a [Azure Search szolgáltatásiszint-szerződés](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Mivel a replikákat az adatok másolatát, több replikával rendelkező lehetővé teszi, hogy az Azure újraindítások és a egy replika elleni karbantartási machine, miközben lehetővé teszi más replikákon folytatja a lekérdezés végrehajtása. Ezzel szemben ha azonnal replikákat, fog díjak lekérdezési teljesítmény romlását, feltéve, hogy ezeket a replikákat egy kevésbé használt erőforrások is.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Földrajzilag elosztott számítási feladatok és a georedundancia skálázása
Földrajzilag elosztott számítási feladatokhoz a felhasználók, akik az Azure Search üzemeltető adatközpont távol található magasabb késés érvényes lesz. Egy megoldás, hogy ezek a felhasználók szorosabb közelében régiókban több keresési szolgáltatások kiépítése. Az Azure Search jelenleg nem biztosít egy automatizált módszer az Azure Search-indexek geo-replikálása régiók között, de vannak az egyes módszereket, amelyek segítségével, amely megkönnyíti a folyamat egyszerű megvalósítása és kezelése. Ezek a következő néhány szakaszban leírt.

A keresési szolgáltatások egy földrajzilag elosztott halmaza célja, hogy van két vagy több index elérhető két vagy több régióban, ahol a felhasználó irányítja a rendszer az Azure Search-szolgáltatás, amely a legkisebb késéssel biztosít az ebben a példában látható módon:

   ![Kereszt-szolgáltatások régió szerint lapján][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Több Azure Search szolgáltatás gondoskodik az adatok szinkronizálása
Az elosztott keresési szolgáltatások szinkronban figyelőkből segítségével gondoskodik a két lehetőség van a [Azure Search-indexelő](search-indexer-overview.md) vagy a Push API (más néven a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexelők használata több szolgáltatást a tartalom frissítése

Ha már használja egy szolgáltatás indexelő, konfigurálhatja egy második indexelő egy második szolgáltatást, hogy az ugyanazon adatforrás-objektum, használja a kiindulásként adatokat ugyanarról a helyről. Az egyes régiókban mindegyikük rendelkezik a saját indexelő és a egy célindex (a keresési forrásgyűjteményébe nincsenek megosztva, ami azt jelenti, hogy adatok duplikálódnak), azonban minden indexelő hivatkozik ugyanazon az adatforráson.

Íme egy magas szintű vizualizációt, mi az architektúra módon jelenik meg.

   ![Egyetlen adatforrást, és elosztott indexelő szolgáltatás kombinációk][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Több szolgáltatást a tartalom frissítések terjesztése a REST API-k használata
Ha az Azure Search REST API-t használ [tartalom leküldése az Azure Search-index](https://docs.microsoft.com/rest/api/searchservice/update-index), megtarthatja a különféle keresési szolgáltatásokat szinkronban módosítások összes keresőszolgáltatást küldésével, minden alkalommal, amikor szükség egy frissítésre. A kódban ügyeljen arra, hogy esetekben, amikor egy keresési szolgáltatás frissítése nem sikerül, de más keresési szolgáltatásokat nem tud kezelni.

## <a name="leverage-azure-traffic-manager"></a>Használja ki az Azure Traffic Manager
[Az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy több földrajzi található webhelyeket, amelyeket majd élvezik több Azure Search szolgáltatás átirányíthatja a kéréseket. Egy, a Traffic Manager előnye, hogy akkor is elérhető legyen, és más keresési szolgáltatások üzemszünet irányíthatja a felhasználókat az Azure Search mintavételi. Emellett keresési kérelmek keresztül az Azure webhelyek vannak útválasztás, ha az Azure Traffic Manager lehetővé teszi, hogy egyenleg esetben, ha a webhely mentése kapcsolódó, de nem az Azure Search. Íme egy példa, mely az architektúra, amely a Traffic Manager.

   ![Kereszt-lapján-szolgáltatások régiónként, központi Traffic Managerrel][3]

## <a name="monitor-performance"></a>Teljesítmény figyelése
Az Azure Search lehetőséget biztosít az elemezheti és figyelheti a teljesítmény, a szolgáltatás keretében [forgalmi elemzések keresése](search-traffic-analytics.md). Ha engedélyezi ezt a funkciót, és az ügyfélalkalmazás üzemállapot-, igény szerint bejelentkezhet az egyéni keresési műveletek, valamint az összesített mérőszámok majd elemzés érdekében dolgoz fel vagy webalkalmazásban jelennek meg a Power bi-ban az Azure Storage-fiókba. Ezzel a módszerrel metrikák rögzíti adja meg a teljesítmény statisztikáit, például a lekérdezések vagy a lekérdezések válaszidejét átlagos száma. Emellett a művelet naplózási lehetővé teszi a keresési műveletek részletek feltárásához.

A TRAFFIC analytics hasznos annak megértéséhez, késés díjakat az adott Azure Search szempontjából. A lekérdezési teljesítmény-mérőszámok naplózott egy lekérdezést az Azure Search szolgáltatásban (az az idő, amikor küldésük kérik fel) teljes feldolgozást idő alapulnak, mivel is tudja annak meghatározására, hogy amennyiben késési problémák az Azure Search szolgáltatás oldalán vagy a tudni használja ezt Ide-vel a szolgáltatást, például hálózati késés.  

## <a name="next-steps"></a>További lépések
Árképzési szintek és a szolgáltatások korlátait kapcsolatos további tudnivalókért mindegyik lásd [korlátozások az Azure Search szolgáltatás](search-limits-quotas-capacity.md).

Látogasson el [kapacitástervezés](search-capacity-planning.md) partíció és a replika kombinációk tájékozódhat.

További részletezést, a teljesítményre, és ebben a cikkben tárgyalt optimalizálásokat megvalósításának egyes bemutatók megtekintéséhez tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
