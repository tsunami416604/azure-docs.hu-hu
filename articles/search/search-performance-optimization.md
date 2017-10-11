---
title: "Az Azure Search teljesítmény- és optimalizálási szempontok |} Microsoft Docs"
description: "Azure Search teljesítmény hangolására és optimális méretezési konfigurálása"
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: f4e371fc16bc57e6963f1ec51c0ea864fa568f0c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Az Azure Search teljesítmény- és optimalizálási kapcsolatos szempontok
Egy remek keresési élményt biztosít az a kulcs sikeres sok Mobile és webes alkalmazásokhoz. Ingatlan, a car Piacterek online katalógusok használni gyors keresés és a vonatkozó eredményeket hatással lesz a felhasználói élmény. Ez a dokumentum azt tapasztalja, ajánlott eljárások a legtöbbet hozhatja ki Azure Search, különösen ha speciális célú méretezhetőségi kifinomult követelményeit beszélő hogyan támogatja a súgót, vagy egyéni rangsorolási készült.  Emellett ez a dokumentum belső ismerteti, és ismerteti a különböző módszer valós felhasználói alkalmazásokban hatékonyan működjön.

## <a name="performance-and-scale-tuning-for-search-services"></a>Teljesítmény és méretezés hangolása keresési szolgáltatások
Azt minden segítségével keresőprogramok például a Bing és Google és a nagy teljesítményt biztosítanak.  Ennek eredményeképpen, amikor az ügyfelek használja a keresés-t vagy a mobilalkalmazás, azok fog várhatóan hasonló teljesítményt nyújt.  A keresési teljesítmény optimalizálása, amikor a legjobb módszer egyik késés, amely a lekérdezés idejét befejeződését, majd adja vissza az eredményeket összpontosítanak.  Optimalizálja a keresési késés esetén fontos, hogy:

1. Egy tipikus keresési kérő célként megadott várakozási ideje (vagy maximális időtartama) gyorsabban befejezéséhez mentse.
2. Hozzon létre, és a keresési szolgáltatás várakozási ideje sebesség mérésére reális adatkészletben szemben a valódi munkaterhelés teszteléséhez.
3. Kevés lekérdezések / másodperc (QPS) kezdődnie, és továbbra is a teszt végrehajtása, csak a meghatározott cél várakozási alá csökken a lekérdezés-késleltetés számának növeléséhez.  Ez az egy fontos teljesítményteszt használata során az alkalmazás növekedésével a lépték tervezéséhez nyújtanak segítséget.
4. Ahol lehetséges, felhasználhatja a HTTP-kapcsolatoknál.  Ha az Azure Search .NET SDK-t használ, ez azt jelenti, hogy szabad újra egy példányát, vagy [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) példányt, és a REST API-t használ, ha egyetlen HttpClient szabad újra.

Ezek létrehozása során a munkaterhelések tesztelése, van néhány szem előtt tartani az Azure Search jellemzői:

1. Lehetőség leküldéses, lekérdezi egy időben sok keresési, hogy az Azure Search szolgáltatás elérhető erőforrások túl sok a rendszer.  Ha ez történik, megjelenik a HTTP 503-as válaszkódot.  Emiatt tanácsos a search kérelemmel késés díjszabás különbségeit megjelenítéséhez, amikor további keresési kérelmeket különböző tartományait elindítása.
2. Az Azure Search tartalom feltöltését átfogó teljesítményét és az Azure Search szolgáltatás várakozási ideje hatással van.  Ha várhatóan küldhet adatokat, amíg a felhasználók keresést hajt végre, fontos a tesztek munkaterhelés figyelembe venni.
3. Nem minden keresési lekérdezés teljesítmény mértékben hajt végre.  Például egy dokumentum keresési vagy keresési javaslat általában végrehajtják gyorsabb, mint az értékkorlátozás és a szűrők jelentős számú lekérdezés.  Érdemes a különböző lekérdezések a várt figyelembe a tesztek fejlesztéskor igénybe vehet.  
4. A search kérelemmel mértékben eltérő változata fontos, mert ugyanaz a search kérelemmel folyamatosan hajtható végre, ha az adatok gyorsítótárazása megkezdik teljesítmény hely nagyobb, mint előfordulhat, hogy több különböző lekérdezéssel állítva, akkor győződjön.

> [!NOTE]
> [A Visual Studio terhelés tesztelés](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) valóban módon hajtsa végre a javasolt teszteli, mert a HTTP-kérések végrehajtásához esetén egyébként használnia az Azure Search lekérdezések végrehajtása, és lehetővé teszi a kérelmek párhuzamos folyamatkezelést biztosítja.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Azure Search szolgáltatást a lekérdezési díjszabás méretezés és szabályozva kérelmek
Túl sok szabályozottan halmozott kérelmet kap, illetve haladhatja meg a célként megadott várakozási ideje díjszabás egy nagyobb lekérdezés terhelését a meggyőződhet, csökkenti a késést sebességét az alábbi két módszer egyikével:

1. **Növelje a replikák:** replika olyan, mint az Azure Search kérések történő terheléselosztásához a több példány szemben így adatok másolatát.  Összes az terheléselosztás és a replikák között adatainak replikálása az Azure Search kezeli, és módosíthatja a szolgáltatás bármikor lefoglalt replikák száma.  Legfeljebb 12 replikák Standard keresési szolgáltatást, és egy egyszerű keresés szolgáltatásban 3 replikák foglalhatja. Replikák lehet beállítani származhatnak a [Azure-portálon](search-create-service-portal.md) vagy [PowerShell](search-manage-powershell.md).
2. **Növelje keresési réteg:** az Azure Search érkezik egy [a rétegek száma](https://azure.microsoft.com/pricing/details/search/) , valamint ezek a rétegek egyes különböző teljesítményszintet.  Bizonyos esetekben előfordulhat, hogy a réteg nem adhatók meg elég alacsony késési igényű díjszabás, akkor is, ha a replikákat a rendszer kimenő kihasználtságában sok lekérdezések.  Ebben az esetben érdemes figyelembe venni, ami a magasabb keresési rétegek, például az Azure Search S3 réteghez, amelynek kiválóan alkalmas a dokumentumok és a rendkívül nagy lekérdezés munkaterhelés nagy számú forgatókönyvek közül.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Azure Search lassú egyes lekérdezések skálázás
Miért késés díjszabás lassú lehet akkor egy egyetlen lekérdezés túl sokáig tart.  Ebben az esetben replikák hozzáadása nem növeli a késés sebességét.  Ebben az esetben két módon érhető el:

1. **Növelje a partíciók** partíció egy olyan mechanizmus az adatok között további erőforrásokat a felosztás.  Emiatt egy második partíció hozzáadásakor az adatok beolvasása felosztása két.  Egy harmadik partíciót az index felosztja a három stb.  Ennek is hatása az, hogy bizonyos esetekben lassú lekérdezések gyorsabban fog miatt számítás a párhuzamos folyamatkezelést biztosítja.  Van néhány példa ahol úgy találtuk, ez működnek jól rendkívül alacsony szelektivitás lekérdezések tartalmazó lekérdezések párhuzamos folyamatkezelést biztosítja.  Ez sok dokumentum megfelelő, vagy ha értékkorlátozás kell megadni. érintett keresztül nagyszámú dokumentumok lekérdezések áll.  Mivel nincs szükség a dokumentumok alkalmazhatóságát pontozása céljából, és a számok dokumentumok száma számítási számos, további partíciók hozzáadása segítséget nyújtanak további számítási.  
   
   Legfeljebb 12 Standard keresési szolgáltatást a partíciók száma és az egyszerű keresés szolgáltatásban 1 partíció lehet.  Partíciók lehet beállítani származhatnak a [Azure-portálon](search-create-service-portal.md) vagy [PowerShell](search-manage-powershell.md).
2. **Korlát nagy számosságot mezők:** egy kategorizálható vagy a szűrhető mezője, amely jelentős mennyiségű egyedi értékkel rendelkezik, és emiatt tart, az erőforrásokat a számítási eredmények keresztül sok nagy számosságot mezőt tartalmaz.   Például termék azonosító vagy leírás mező beállítása kategorizálható szűrhető tenné a nagy számosságot mert dokumentumhoz dokumentum értékei a legtöbb egyedi. Ahol lehetséges, nagy számosságot mezők számának korlátozása.
3. **Növelje keresési réteg:** áthelyezése legfeljebb egy magasabb szintű Azure Search használható lehet egy másik lassú lekérdezések teljesítményének javítása érdekében.  Minden magasabb réteg emellett gyorsabb CPU, illetve több memóriával, amely pozitív hatással lehet a lekérdezések teljesítményét.

## <a name="scaling-for-availability"></a>Rendelkezésre állási csoportok skálázási módjának
Replikák nem csak lekérdezés késés csökkentése érdekében, de lehetővé teszi a magas rendelkezésre állásra.  Az egy replikához számíthat rendszeres leállás miatt a kiszolgáló újraindítása után a szoftverfrissítések vagy egyéb karbantartási események esedékes.  Emiatt fontos figyelembe venni, ha az alkalmazás által magas rendelkezésre állású keresések (lekérdezések), továbbá az írási műveletek (indexelési események).  Az Azure Search szolgáltatásiszint-szerződés beállítások a következő attribútumokkal fizetett keresési ajánlatok kínálja:

* a csak olvasható munkaterhelések (lekérdezések) magas rendelkezésre állás 2-replikával
* a magas rendelkezésre állás írható-olvasható munkaterhelések (lekérdezések és az indexelés) 3 vagy több replikák

A további részletekért tekintse meg a [Azure Search szolgáltatásiszint-megállapodás](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Mivel a replikákat az adatok másolatát, több replika segítségével Azure Search újraindítások és karbantartási egy másodpéldány elleni számítógépre, miközben lehetővé teszi lekérdezések továbbra is hajtható végre, szemben a többi replikához egyszerre.  Ezért is szüksége lesz kell figyelembe venni, hogyan üzemszünet hatással lehet, hogy most már rendelkezik az adatok egy kisebb példányán végrehajtandó lekérdezéseket.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Földrajzilag elosztott munkaterhelések méretezés és földrajzi redundancia
Földrajzilag elosztott munkaterheléseknél találja, hogy az adatközpont, amelyen az Azure Search szolgáltatás található távol lévő felhasználók rendelkeznek-e a késés nagyobb sebesség.  Ezért fontos gyakran több keresési szolgáltatások rendelkeznie azokban a régiókban, amelyek ezekre a felhasználókra szorosabb közelében.  Az Azure Search jelenleg nem biztosít egy földrajzi replikálása Azure Search-indexek automatikus módszerrel régiók között, de néhány technikák használható használhat, amelyek a folyamat egyszerű megvalósítása és kezelése. Ezek a következő néhány szakaszokban lévő eljárásokat.

Keresési szolgáltatások egy földrajzilag elosztott csoportját célja, hogy rendelkezik elérhető két vagy több indexek két vagy több régiókban, ahol a felhasználó továbbítja az Azure Search szolgáltatás, amely a legkisebb mértékű késleltetést biztosítja az ebben a példában látható módon:

   ![Kereszt-régiói lapja][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Adatok szinkronban tartása több Azure Search szolgáltatásban
Két lehetőség, hogy az elosztott keresési szolgáltatások szinkronban álló segítségével a [Azure Search-indexelőt](search-indexer-overview.md) vagy a leküldéses API (más néven a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Az Azure Search indexelő
Az Azure Search-indexelőt használ, ha már importálás adatváltozásokat például az Azure SQL Database vagy az Azure Cosmos DB központi adattárolót. Amikor létrehoz egy új keresés szolgáltatás, akkor egyszerűen is létrehozhat egy új Azure Search-indexelőt, hogy a szolgáltatás, amely a ugyanazon adattár mutat. Ha a módosításokat az adattárba lépnek akkor lesz majd indexelve által a különböző indexelők.  

Íme egy példa mi néz ki az architektúra.

   ![Az elosztott indexelő és kombinációja egyetlen adatforrás][2]

### <a name="push-api"></a>Leküldéses API
Ha az Azure Search-leküldéses API használatával az [az Azure Search-index a tartalom frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index), megtarthatja a különböző keresési szolgáltatások szinkronban módosítások összes keresőszolgáltatást küldésével, amikor szükség egy frissítésre.  Ennek során fontos, ügyeljen arra, hogy az esetek, amelyekben egy keresési szolgáltatás frissítése sikertelen, és egy vagy több frissítés sikeres kezelésére.

## <a name="leveraging-azure-traffic-manager"></a>Az Azure Traffic Manager kihasználva
[Az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi a több földrajzi található webhelyeket, amelyeket majd üzemelnek több Azure Search szolgáltatás kérelmek.  Egy a Traffic Manager előnye, hogy azt is mintavételi Azure Search győződjön meg arról, hogy az elérhető, és átirányíthatja felhasználók alternatív keresési szolgáltatások leállás esetén.  Ezenkívül ha a search kérelmek használatával az Azure webhelyek útválasztáson Azure Traffic Manager lehetővé teszi, hogy egyenleg esetben, ha a webhely mentése betöltésére, de nem az Azure Search.  Íme egy példa, amely kihasználja a Traffic Manager milyen architektúrája.

   ![Kereszt-lapján régiói, a központi Traffic Managerrel][3]

## <a name="monitoring-performance"></a>A teljesítmény figyelése
Az Azure Search segítségével elvégezheti a elemzése és a szolgáltatás teljesítményének figyeléséhez [keresési forgalom Analytics (STA)](search-traffic-analytics.md). Keresztül STA opcionálisan jelentkezhet az egyes keresési műveleteket, valamint a összesített metrikák majd elemzés érdekében dolgoz fel vagy a Power BI ábrázolt Azure Storage-fiók.  STA metrikákat használ, például a lekérdezések vagy lekérdezési válaszidőt átlagos száma teljesítménystatisztikáit tekintheti meg.  Emellett a műveleti naplózás lehetővé teszi elemezze a megadott keresési műveletek részleteit.

STA megérteni, hogy az Azure Search szempontjából késés díjszabás hasznos eszköze.  Mivel a lekérdezés Teljesítményelemzési értékek, a rendszer naplózza a lekérdezés idejét (az erre vonatkozó kérés érkezik történő elküldésekor az idő) az Azure Search teljes körűen feldolgozandó alapulnak, le is tudja ennek segítségével állapítsa meg, ha késési problémák vannak az Azure Search szolgáltatás ügyféloldali vagy elemek a szolgáltatás, például hálózati késés ide.  

## <a name="next-steps"></a>Következő lépések
További információt a árképzési szinteket, és a szolgáltatások korlátok mindegyik, lásd: [szolgáltatási korlátait, az Azure Search](search-limits-quotas-capacity.md).

Látogasson el [kapacitástervezés](search-capacity-planning.md) partícióazonosító és másodpéldány kombinációk tájékozódhat.

A(z) további a teljesítményre, és tekintse meg a cikkben szereplő optimalizálásokat megvalósításának néhány bemutatók tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
