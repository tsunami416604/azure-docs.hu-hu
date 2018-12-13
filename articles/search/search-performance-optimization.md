---
title: Az Azure Search teljesítmény és optimalizálás szempontok – Azure Search
description: Ismerje meg a technikák és ajánlott eljárások az Azure Search teljesítményének hangolása és optimális méretezési csoport konfigurálása.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 0a98e7f05e766d47a5ea9293409a74a6fafbf837
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310242"
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Az Azure Search teljesítmény és optimalizálás szempontok
Remek keresési funkciókat az egy kulcs sikeres számos mobil és webes alkalmazásokat. Ingatlan, az autó piacterein online katalógusokkal, a alkalmazni gyors keresési és a vonatkozó eredményeket hatással lesz a vásárlói élményt. Ez a dokumentum célja azt tapasztalja, bevált gyakorlatokat, a legtöbbet hozhassa ki Azure Search, különösen a speciális alkalmazási kifinomult követelményekkel, a skálázhatóság érdekében a többnyelvű támogatási segítséget, vagy egyéni ennek a területnek.  Ez a dokumentum ezenkívül internals ismerteti, és ismerteti a megközelítést, amely a való életből vett vásárlói alkalmazások hatékonyan működjön.

## <a name="performance-and-scale-tuning-for-search-services"></a>Teljesítmény és méretezés finomhangolás keresési szolgáltatásokat
Azt az összes segítségével például a Bing és a Google és kínálnak a nagy teljesítményű keresőmotorok.  Ennek eredményeképpen ha ügyfelek az keresési webes vagy mobilalkalmazásaiba, elvárják lesz hasonló teljesítményt nyújt.  A keresés teljesítmény optimalizálása, a legjobb megközelítések egyik késés, a lekérdezés befejezéséhez és az eredményeket idő azaz összpontosíthat.  Optimalizálja a keresési késés esetén fontos, hogy:

1. Válasszon egy cél késés (vagy maximális időtartama), amely egy tipikus keresési kérelem végrehajtásához vesz igénybe.
2. Hozzon létre és tesztelheti a számítási feladatok valódi ellen a keresési szolgáltatás egy realisztikus adatkészlet késés díjszabása méréséhez.
3. Indítsa el a lekérdezések másodpercenkénti (lekérdezési QPS) kevés, és továbbra is a teszt végrehajtása, csak a meghatározott célhoz késés alá csökken a Lekérdezések késése számának növelését.  Ez a fontos számításiteljesítmény-mérési megtervezéséhez a méretezhető az alkalmazás növekedésével a használat.
4. Amikor csak lehetséges, újból felhasználhatja a HTTP-kapcsolatoknál.  Ha az Azure Search .NET SDK használ, ez azt jelenti, egy példány szabad újra, vagy [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) példány, és a REST API-t használja, ha egyetlen HttpClient kell újra felhasználhatja.

Ezek létrehozása során tesztelheti a számítási feladatokat, vannak bizonyos figyelembe kell venni az Azure Search jellemzői:

1. Lehetőség a leküldéses így számos keresési lekérdezések egy időben, hogy az Azure Search szolgáltatásban elérhető erőforrások túl sok a rendszer.  Ha ez történik, látni fogja a HTTP 503-as válaszkódot.  Emiatt tanácsos kezdeni, tekintse meg a késés árfolyamok különbségeket, hozzáadhat további keresési kérelmek számára keresési kérések különböző tartományok.
2. Az Azure Search-tartalom feltöltése hatással lesz az általános teljesítmény és az Azure Search szolgáltatás késését.  Ha várhatóan küldhet adatokat, amíg a felhasználók végez keresést, fontos ezt a munkafolyamatot, figyelembe vennie a tesztekben.
3. Nem minden keresési lekérdezés elvégzi az azonos szinten teljesítmény.  Például egy dokumentum lookup vagy keresési javaslat általában végez gyorsabb, mint egy lekérdezés jelentős számú értékkorlátozással és szűrőket.  Érdemes igénybe vehet a különböző lekérdezéseket, a várt figyelembe a tesztek készítése során.  
4. Keresési kérelmek változata fontos, mert ha folyamatosan hajtsa végre az azonos keresési kéréseket, az adatok gyorsítótárazása indul el, hogy a hely jobb, mint, több különböző lekérdezéssel be teljesítmény.

> [!NOTE]
> [A Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) a teljesítményteszt végrehajtásához nagyon jó lehetőséget teszteli, mert Ön a HTTP-kérések végrehajtásához, az Azure Search-lekérdezéseket végrehajtó esetén lenne szükség, és lehetővé teszi a párhuzamos feldolgozás kérelmek.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Méretezés az Azure Search lekérdezési arányát és a kérelmek szabályozva
Amikor azért küldtük Önnek, túl sok szabályozott kérelmeinek száma, vagy a cél késés tarifa szerint egy nagyobb lekérdezési terhelése meghaladja, megtekintheti a két módszer egyikével mértékű késés csökkentése:

1. **Növelje a replikák:**  A replika olyan, mint az adatokat, így eloszthatja a kérelmeket a több példányban ellen az Azure Search egy példányát.  Az összes terheléselosztási funkciók és az Azure Search által kezelt replikák közötti adatreplikáció, és megváltoztathatja a hozzárendelt a szolgáltatáshoz bármikor replikák száma.  A szabványos keresőszolgáltatásokba 12 replikákat és a egy egyszerű keresés szolgáltatásban 3 replika legfeljebb foglalhat le. Replikák lehet beállítani az a [az Azure portal](search-create-service-portal.md) vagy [PowerShell](search-manage-powershell.md).
2. **Növelje a keresés szint:**  Az Azure Search érhető el egy [rétegek száma](https://azure.microsoft.com/pricing/details/search/) és az egyes szintek különböző teljesítményszintet is garantálja.  Bizonyos esetekben előfordulhat, hogy az a szint nem adhatók meg a kellően közel valós idejű díjait számítjuk fel akkor is, ha a replikák vannak maximumot sok lekérdezéseket.  Ebben az esetben érdemes figyelembe venni, kihasználva az Azure Search S3 szint, amely jól, dokumentumokat és a rendkívül nagy lekérdezési számítási feladatok nagy számú forgatókönyvek például a magasabb szintű keresési csomagok valamelyikére.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Méretezés az Azure Search az egyes lassú lekérdezések
Miért késés díjak lassú lehet, a másik ok az egyetlen lekérdezés túl sokáig tart.  Ebben az esetben replikák hozzáadásával nem növeli a késés díjait.  Ebben az esetben két lehetőség van érhető el:

1. **Növelje a partíciók** partíció egy mechanizmust vágását meghatározó az adatok között további erőforrásokat.  Ezért amikor hozzáad egy második partíció adatait lekérdezi ketté bontása.  Egy harmadik partíciót az index felosztja a három stb.  Ez is hatása az, hogy bizonyos esetekben a lassú lekérdezések gyorsabban fog a párhuzamos számítási feldolgozás miatt.  Nincsenek néhány példát, ahol úgy találtuk, ez ezerszer rendkívül jól működnek a lekérdezések, amelyek alacsony szelektivitás lekérdezéseket.  Ez több, a lekérdezések, amely megfelel a legtöbb dokumentumot, vagy ha jellemzőkezelés kell biztosít nagy számú dokumentumok számát.  Mivel a nagy mennyiségű számítási pontszámot rendelni az alkalmazhatóságát, a dokumentumok vagy a számok dokumentumok számát szükséges, további partíciók hozzáadásával segíthet adja meg a további számítási.  
   
   A szabványos keresőszolgáltatásokba 12 partícióra maximum és az egyszerű keresés szolgáltatás 1 partíció lehet.  A partíciók lehet beállítani az a [az Azure portal](search-create-service-portal.md) vagy [PowerShell](search-manage-powershell.md).
2. **Korlát nagy Számosságú mezők:** Egy nagy számosságú mező áll egy kategorizálható vagy szűrhető mező, amely jelentős mennyiségű egyedi értékkel rendelkezik, és ennek eredményeképpen a sok erőforrást eredmények számítási keresztül tart.   Például termék azonosító vagy leírás mező beállítása szűrhető és kategorizálható biztosítja, a nagy számosságú mivel a legtöbb dokumentumot a dokumentumból értékek egyediek. Amikor csak lehetséges, korlátozza a nagy számosságú mezők számát.
3. **Növelje a keresés szint:**  Akár áthelyezése Azure Search a magasabb szintű lehet egy másik módja a lassú lekérdezések teljesítményének javítása érdekében.  Minden egyes magasabb szintű csomagra is biztosít, gyorsabb Processzor- és több memória, amelyek pozitív hatással lehet a lekérdezés teljesítményére.

## <a name="scaling-for-availability"></a>Méretezés a rendelkezésre állás érdekében
Replikák nem csak lekérdezési késés csökkentése érdekében, de lehetővé teszi a magas rendelkezésre állás érdekében.  Egyetlen replikával számíthat rendszeres leállás miatt a kiszolgáló újraindítása után a szoftverfrissítések vagy más, amelyet a rendszer karbantartási események esetében.  Ennek eredményeképpen fontos figyelembe venni, ha az alkalmazás magas rendelkezésre állású kereséseinek (lekérdezések) igényel, valamint írási műveletek (indexelési események).  Az Azure Search a következő attribútumokkal keresési fizetős ajánlatok a különféle SLA-lehetőségeket kínálja:

* a magas rendelkezésre állás csak olvasható munkaterhelések (lekérdezések) 2 replika
* a magas rendelkezésre állású olvasási és írási számítási feladatok (lekérdezések és az indexelés) 3 vagy több replikával

Ezzel kapcsolatban további információért látogasson el a [Azure Search szolgáltatásiszint-szerződés](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Mivel a replikákat az adatok másolatát, több replikával rendelkező lehetővé teszi, hogy az Azure machine újraindítások és a egy replika elleni karbantartási egyszerre, miközben lehetővé teszi, hogy a többi replikák hajtható végre lekérdezéseket.  Éppen ezért is kell figyelembe venni, hogyan az üzemszünet hatással lehet, hogy most már rendelkezik egy kisebb az adatok másolatát hajtható végre a lekérdezéseket.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Földrajzilag elosztott számítási feladatok skálázása, és adja meg a georedundancia
Földrajzilag elosztott számítási feladatokhoz láthatja, hogy az adatközpont, ahol az Azure Search szolgáltatás üzemel távol található felhasználók rendelkeznek-e a nagyobb késést arányt.  Ezért fontos gyakran ezek a felhasználók szorosabb közelében lévő régióban több keresési szolgáltatás rendelkezik.  Az Azure Search jelenleg nem biztosít egy automatizált módszer az Azure Search-indexek geo-replikálása régiók között, de vannak az egyes módszereket, amelyek segítségével, amely megkönnyíti a folyamat egyszerű megvalósítása és kezelése. Ezek a következő néhány szakaszban leírt.

A keresési szolgáltatások egy földrajzilag elosztott halmaza célja, hogy van két vagy több index elérhető két vagy több régióban, ahol a felhasználó a rendszer átirányítja az Azure Search-szolgáltatás, amely a legkisebb késéssel biztosít az ebben a példában látható módon:

   ![Kereszt-szolgáltatások régió szerint lapján][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Több Azure Search szolgáltatás gondoskodik az adatok szinkronizálása
Az elosztott keresési szolgáltatások szinkronban figyelőkből segítségével gondoskodik a két lehetőség van a [Azure Search-indexelő](search-indexer-overview.md) vagy a Push API (más néven a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Az Azure Search-indexelő
Az Azure Search-indexelőt használja, ha már importálás adatok például az Azure SQL Database vagy Azure Cosmos DB központi adattárolót. Amikor létrehoz egy új keresési szolgáltatás, akkor egyszerűen is létrehozhat egy új Azure keresési indexelő, hogy a szolgáltatás, amely ugyanezt az adattárat mutat. Ezzel a módszerrel, amikor új módosításokat az adattár jut, majd indexelését által a különböző indexelők.  

Íme egy példa, mi az architektúra láthatóhoz hasonló.

   ![Egyetlen adatforrást, és elosztott indexelő szolgáltatás kombinációk][2]

### <a name="push-api"></a>A Push API
Ha az Azure Search Push API-t használ [tartalom az Azure Search-index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index), megtarthatja a különféle keresési szolgáltatásokat szinkronban módosítások összes keresőszolgáltatást küldésével, minden alkalommal, amikor szükség egy frissítésre.  Ezzel esetén fontos, hogy esetekben, amikor egy keresési szolgáltatás frissítése nem sikerül, és a egy vagy több frissítés sikeres kezeléséhez.

## <a name="leveraging-azure-traffic-manager"></a>Az Azure Traffic Manager kihasználva
[Az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy több földrajzi található webhelyeket, amelyeket majd élvezik több Azure Search szolgáltatás átirányíthatja a kéréseket.  Egy, a Traffic Manager előnye, hogy akkor is elérhető legyen, és más keresési szolgáltatások üzemszünet irányíthatja a felhasználókat az Azure Search mintavételi.  Emellett keresési kérelmek keresztül az Azure webhelyek vannak útválasztás, ha az Azure Traffic Manager lehetővé teszi, hogy egyenleg esetben, ha a webhely mentése kapcsolódó, de nem az Azure Search.  Íme egy példa, mely az architektúra, amely a Traffic Manager.

   ![Kereszt-lapján-szolgáltatások régiónként, központi Traffic Managerrel][3]

## <a name="monitoring-performance"></a>Teljesítmény figyelése
Az Azure Search lehetőséget biztosít az elemezheti és figyelheti a teljesítmény, a szolgáltatás keretében [keresési Traffic Analytics (STA)](search-traffic-analytics.md). Keresztül STA igény szerint bejelentkezhet az egyéni keresési műveletek, valamint az összesített mérőszámok majd elemzés érdekében dolgoz fel vagy webalkalmazásban jelennek meg a Power bi-ban az Azure Storage-fiókba.  ELŐKÉS mérőszámok segítségével, például a lekérdezések vagy a lekérdezések válaszidejét átlagos száma teljesítménystatisztikáit tekintheti meg.  Emellett a művelet naplózási lehetővé teszi a keresési műveletek részletek feltárásához.

ELŐKÉS egy olyan értékes eszköz késés díjakat az adott Azure Search szempontjából megértéséhez.  A lekérdezési teljesítmény-mérőszámok naplózott egy lekérdezést az Azure Search szolgáltatásban (az az idő, amikor küldésük kérik fel) teljes feldolgozást idő alapulnak, mivel is tudja annak meghatározására, hogy amennyiben késési problémák az Azure Search szolgáltatás oldalán vagy a tudni használja ezt Ide-vel a szolgáltatást, például hálózati késés.  

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
